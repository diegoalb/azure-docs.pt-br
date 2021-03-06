---
title: Replicar VMs do Azure executando espaços de armazenamento direto com a recuperação do site do Azure
description: Saiba como replicar as VMs do Azure executando espaços de armazenamento diretos usando o Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 9f394fa8d618c97d74a47ff6e42a002f177cf7d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973661"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replicar VMs do Azure executando espaços de armazenamento direto para outra região

Este artigo descreve como habilitar a recuperação de desastre de VMs do Azure executando espaços de armazenamento diretos.

>[!NOTE]
>Somente os pontos de recuperação com controle de falhas são compatíveis com clusters de espaços de armazenamento diretos.
>

[Os espaços de armazenamento direto (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) são armazenamentos definidos por software, o que fornece uma maneira de criar [clusters de hóspedes](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) no Azure.  Um cluster de hóspedes no Microsoft Azure é um cluster de failover composto por VMs IaaS. Ele permite que as cargas de trabalho de VM hospedadas falhem entre os clusters de hóspedes, alcançando uma SLA de maior disponibilidade para aplicativos do que uma única VM azure pode fornecer. É útil em cenários onde um VM hospeda um aplicativo crítico como SQL ou servidor de arquivos de saída de escala.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Recuperação de desastres com espaços de armazenamento direto

Em um cenário típico, você pode ter um cluster convidado de máquinas virtuais no Azure para maior resiliência do aplicativo, assim como no servidor de arquivos de escala horizontal. Embora isso possa fornecer maior disponibilidade ao aplicativo, é conveniente que você proteja esses aplicativos usando o Site Recovery contra falhas em qualquer nível de região. O Site Recovery replica os dados de uma região para outra região do Azure e traz o cluster na região de recuperação de desastre em um evento de failover.

O diagrama abaixo mostra um cluster failover Azure VM de dois nós usando espaços de armazenamento direto.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Duas Máquinas Virtuais do Azure em um cluster de failover do Windows, sendo que cada máquina virtual tem dois ou mais discos de dados.
- O S2D sincroniza os dados no disco de dados e apresenta o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento é apresentado como um CSV (volume compartilhado clusterizado) para o cluster de failover.
- O cluster de Failover usará o CSV para as unidades de dados.

**Considerações sobre recuperação de desastres**

1. Quando você estiver configurando uma [testemunha de nuvem](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) para o cluster, mantenha a testemunha na região da Recuperação de Desastre.
2. Se você pretende fazer failover das máquinas virtuais para a sub-rede na região de Recuperação de Desastre, que é diferente da região de origem, o endereço IP do cluster precisa ser alterado após o failover.  Para alterar o IP do cluster, você precisa usar o script do [plano de recuperação do site.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Exemplo de script](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) para executar o comando dentro da VM usando a extensão de script personalizado 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Habilitar o Site Recovery para o cluster S2D:

1. Dentro do cofre dos serviços de recuperação, clique em "+replicar"
1. Selecione todos os nós no cluster e torne-os parte de um [Grupo de consistência de várias VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Selecione a política de replicação com a consistência do aplicativo desativada* (apenas o suporte a controle de falhas está disponível)
1. Habilitar a replicação

   ![proteção storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Vá para os itens replicados e você poderá ver o status de ambas as máquinas virtuais.
3. Ambas as máquinas virtuais estão sendo protegidas e também são mostradas como parte do grupo de consistência de várias VMs.

   ![proteção storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação dá suporte à sequência de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Quando você criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [Criar um plano de recuperação usando o Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais aos grupos de failover

1.  Crie um plano de recuperação adicionando as máquinas virtuais.
2.  Clique em 'Personalizar' para agrupar as VMs. Por padrão, todas as VMs são parte do 'Grupo 1'.


### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Talvez seja necessário fazer algumas operações nas máquinas virtuais do Azure após o failover ou durante o teste de failover para que seus aplicativos funcionem corretamente. É possível automatizar algumas operações pós-failover. Por exemplo, aqui estamos anexando balanceador de carga e alterando IP de cluster.


### <a name="failover-of-the-virtual-machines"></a>Failover das máquinas virtuais 
Ambos os nós das VMs precisam ser reprovados usando o [plano de recuperação](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) de site 

![proteção storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Execute um teste de failover
1.  No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
2.  Selecione o plano de recuperação que você criou.
3.  Selecione **Failover de Teste**.
4.  Para iniciar o processo de failover de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5.  Quando o ambiente secundário está ativo, faça validações.
6.  Quando as validações forem concluídas, para limpar o ambiente de failover, selecione **Limpar failover de teste**.

Para obter mais informações, consulte [Failover de teste para Azure no Site Recovery ](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar um failover

1.  No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
2.  Selecione o plano de recuperação que você criou para aplicativos SAP.
3.  Selecione **Failover**.
4.  Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [Failover no Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Próximas etapas

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) sobre como executar o failback.
