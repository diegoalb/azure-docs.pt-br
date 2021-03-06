---
title: Criar uma VM Linux com SQL Server 2017 no Azure | Microsoft Docs
description: Este tutorial mostra como criar uma máquina virtual Linux com SQL Server 2017 no portal do Azure.
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: conceptual
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ba4eed4dcfd6d8e86c21f1ee5214108c44a8c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060241"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Provisionar uma máquina virtual Linux com SQL Server no portal do Azure

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

Neste tutorial de início rápido, você usará o portal do Azure para criar uma máquina virtual do Linux com o SQL Server 2017 instalado.

Neste tutorial, você aprenderá como:

* [Criar uma VM Linux SQL da galeria](#create)
* [Conectar-se à nova VM com SSH](#connect)
* [Alterar a senha SA](#password)
* [Configurar para conexões remotas](#remote)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a>Criar uma VM Linux com o SQL Server instalado

1. Faça login no [portal Azure](https://portal.azure.com/).

1. No painel esquerdo, selecione **Criar um recurso**.

1. No painel **Criar um recurso**, selecione **Computação**.

1. Selecione **Ver todos** ao lado do cabeçalho **Em destaque**.

   ![Ver todas as imagens de VM](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Na caixa de pesquisa, digite **SQL Server 2019**e selecione **Enter** para iniciar a pesquisa.

1. Limite os resultados da pesquisa selecionando **o sistema** > operacional**Redhat**.

    ![Filtro de pesquisa para imagens VM do SQL Server 2019](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Selecione uma imagem Linux do SQL Server 2019 a partir dos resultados da pesquisa. Este tutorial usa **o SQL Server 2019 no RHEL74**.

   > [!TIP]
   > A edição Developer permite o teste ou o desenvolvimento com os recursos da edição Enterprise, mas sem os custos de licenciamento do SQL Server. Você só paga o custo da execução da VM Linux.

1. Selecione **Criar**. 


### <a name="set-up-your-linux-vm"></a>Configurar a VM do Linux

1. Na guia **Informações Básicas**, selecione a **Assinatura** e o **Grupo de Recursos**. 

    ![Janela Básico](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. Em **Nome da máquina virtual**, insira um nome para a nova VM do Linux.
1. Em seguida, digite ou selecione os seguintes valores:
   * **Região**: Selecione a região Azure que é a certa para você.
   * **Opções de disponibilidade**: Escolha a opção de disponibilidade e redundância que é melhor para seus aplicativos e dados.
   * **Tamanho da mudança**: Selecione esta opção para escolher um tamanho de máquina e, quando feito, escolha **Selecionar**. Para saber mais sobre tamanhos de VM, confira [Tamanhos de VM Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

     ![Escolher um tamanho de VM](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Para desenvolvimento e teste funcional, use um tamanho de VM **DS2** ou superior. Para testes de desempenho, use **DS13** ou superior.

   * **Tipo de autenticação**: Selecione **a chave pública SSH**.

     > [!Note]
     > Você tem a opção de usar uma chave pública SSH ou uma senha para autenticação. SSH é mais seguro. Para obter instruções sobre como gerar uma chave SSH, confira [Criar chaves SSH em Linux e Mac para VMs Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

   * **Nome de usuário**: Digite o nome do administrador da VM.
   * **Chave pública SSH**: Digite sua chave pública RSA.
   * **Portas de entrada públicas**: Escolha **Escolher Permitir portas selecionadas** e escolha a porta **SSH (22)** na lista **De seleção de portas de entrada pública.** Neste início rápido, esta etapa é necessária para se conectar e concluir a configuração do SQL Server. Se você quiser se conectar remotamente ao SQL Server, você precisará permitir manualmente o tráfego na porta padrão (1433) usada pelo Microsoft SQL Server para conexões pela Internet após a criação da máquina virtual.

     ![Portas de entrada](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Faça as alterações desejadas nas configurações nas guias adicionais a seguir ou mantenha as configurações padrão.
    * **Discos**
    * **Rede**
    * **Gerenciamento**
    * **Configuração de convidado**
    * **Marcas**

1. Selecione **Revisão + criar**.
1. No painel **Examinar + criar**, selecione **Criar**.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a>Conectar-se à VM Linux

Se você já usa um shell BASH, conecte-se à VM do Azure usando o comando **ssh**. No comando a seguir, substitua o nome de usuário da VM e o endereço IP para se conectar à VM Linux.

```bash
ssh azureadmin@40.55.55.555
```

Você pode encontrar o endereço IP da VM no portal do Azure.

![Endereço IP no portal do Azure](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Se estiver executando no Windows e não tiver um shell Bash, você poderá instalar um cliente SSH, como o PuTTY.

1. [Baixe e instale o PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Execute o PuTTY.

1. Na tela de configuração do PuTTY, insira o endereço IP público da VM.

1. Selecione **Abrir** e insira seu nome de usuário e a senha nos prompts.

Para saber mais sobre como se conectar às VMs Linux, confira [Criar uma VM Linux no Azure usando o Portal](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal).

> [!Note]
> Se for exibido um alerta de segurança do PuTTY indicando que a chave do host do servidor não está sendo armazenada em cache no Registro, escolha uma das opções a seguir. Se você confia nesse host, selecione **Sim** para adicionar a chave ao cache do PuTTY e continuar a conexão. Caso deseje continuar a conexão apenas uma vez, sem adicionar a chave ao cache, selecione **Não**. Se você não confia nesse host, selecione **Cancelar** para abandonar a conexão.

## <a name="change-the-sa-password"></a><a id="password"></a>Alterar a senha SA

A nova máquina virtual instala o SQL Server com uma senha SA aleatória. Redefina essa senha antes de se conectar ao SQL Server com o logon SA.

1. Depois de se conectar à VM Linux, abra um novo terminal de comando.

1. Altere a senha SA com os seguintes comandos:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Insira uma nova senha e confirme-a quando solicitado.

1. Reinicie o serviço SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Adicionar as ferramentas ao caminho (opcional)

Vários [pacotes](sql-server-linux-virtual-machines-overview.md#packages) do SQL Server são instalados por padrão, incluindo o pacote de ferramentas de linha de comando do SQL Server. O pacote de ferramentas contém as ferramentas **sqlcmd** e **bcp**. Para sua conveniência, você pode opcionalmente adicionar o caminho de ferramentas, `/opt/mssql-tools/bin/`, à variável de ambiente **PATH**.

1. Execute os seguintes comandos para modificar o **PATH** para sessões de login e sessões interativas/não-login:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a>Configurar para conexões remotas

Se você precisa se conectar remotamente ao SQL Server na VM do Azure, configure uma regra de entrada no grupo de segurança de rede. A regra permite o tráfego na porta na qual o SQL Server escuta (padrão 1433). As etapas a seguir mostram como usar o portal do Azure nesta etapa.

> [!TIP]
> Se você tiver selecionado a porta de entrada **MS SQL (1433)** nas configurações durante o provisionamento, essas alterações foram feitas para você. Você pode ir para a próxima seção sobre como configurar o firewall.

1. No portal, selecione **Máquinas Virtuais**e selecione sua VM do SQL Server.
1. No painel de navegação à esquerda, em **Configurações**, selecione **Rede**.
1. Na janela Rede, selecione **Adicionar porta de entrada** em **Regras de Porta de Entrada**.

   ![Regras de porta de entrada](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. Na lista **Serviço**, selecione **MS SQL**.

    ![Regra de grupo de segurança MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Clique em **OK** para salvar a regra para a sua VM.

### <a name="open-the-firewall-on-rhel"></a>Abrir o firewall no RHEL

Este tutorial o instruiu a criar uma VM RHEL (Red Hat Enterprise Linux). Se você quiser se conectar remotamente a VMs RHEL, precisará abrir a porta 1433 no firewall do Linux.

1. [Conecte-se](#connect) à VM RHEL.

1. No shell BASH, execute os seguintes comandos:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma máquina virtual com SQL Server 2017 no Azure, pode se conectar localmente com **sqlcmd** para executar consultas Transact-SQL.

Se você configurou a VM do Azure para conexões remotas do SQL Server, você deve conseguir se conectar remotamente. Para obter um exemplo de como se conectar remotamente ao SQL Server no Linux pelo Windows, confira [Usar SSMS no Windows para se conectar ao SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Para se conectar com o Visual Studio Code, confira [Usar o Visual Studio Code para criar e executar scripts Transact-SQL para SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)

Para obter mais informações gerais sobre o SQL Server em Linux, confira [Visão geral do SQL Server 2017 em Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Para saber mais sobre como usar o SQL Server 2017 em máquinas virtuais Linux, confira [Visão geral de máquinas virtuais com SQL Server 2017 no Azure](sql-server-linux-virtual-machines-overview.md).
