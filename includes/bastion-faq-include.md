---
title: incluir arquivo
description: incluir arquivo
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 03/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57a764b62fcda333f042794e176c24c8e6cc5526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80374073"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Quais regiões estão disponíveis?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>É necessário ter um IP público em minha máquina virtual?

Ao conectar-se a uma VM usando o Azure Bastion, NÃO é preciso um IP público na Máquina Virtual do Azure a que você está se conectando. O serviço do Bastion abrirá o RDP/SSH/sessão/conexão para sua máquina virtual usando o IP privado da máquina virtual, dentro de sua rede virtual.

### <a name="is-ipv6-supported"></a>Há suporte para o IPv6?

No momento, não há suporte para o IPv6. O Azure Bastion só dá suporte ao IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Eu preciso de um cliente de RDP ou SSH?

Você não precisa de um cliente de RDP ou SSH para acessar o RDP/SSH para sua máquina virtual do Azure no portal do Azure. Use o [portal do Azure](https://portal.azure.com) para ter acesso de RDP/SSH à sua máquina virtual diretamente no navegador.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>O Azure Bastion exige uma CAL para Serviços de Área de Trabalho Remota para fins administrativos em VMs hospedadas no Azure?
Não, o acesso às VMs do Windows Server pelo Azure Bastion não exige uma [CAL para Serviços de Área de Trabalho Remota](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) quando usado exclusivamente para fins administrativos.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Com quantas sessões de RDP e SSH simultâneas cada Azure Bastion é compatível?
RDP e SSH são protocolos baseados em uso. O alto uso de sessões fará com que o bastion host seja compatível com um número total de sessões menor. Os números abaixo presumem fluxos de trabalho normais do dia a dia.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Eu preciso ter um agente em execução na máquina virtual do Azure?

Não é necessário instalar um agente ou um software no navegador ou na máquina virtual do Azure. O serviço do Bastion é sem agente e não requer software adicional para RDP/SSH.

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Quais navegadores são compatíveis?

Use o navegador Microsoft Edge ou o Google Chrome no Windows. Para o Apple Mac, use o navegador Google Chrome. O Microsoft Edge Chromium também é compatível com o Windows e o Mac, respectivamente.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>É necessário ter alguma função para acessar uma máquina virtual?

Para fazer uma conexão, são necessárias as seguintes funções:

* Função de leitor na máquina virtual
* Função de leitor na placa de interface de rede com endereço IP privado da máquina virtual
* Função de leitor no recurso do Azure Bastion

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Quais são os preços?

Para saber mais, confira a [página de preço](https://aka.ms/BastionHostPricing).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Por que recebo a mensagem de erro "Sua sessão expirou" antes de iniciar a sessão do Bastion?

Uma sessão deve ser iniciada somente por meio do portal do Azure. Entre no portal do Azure e inicie sua sessão novamente. Se você acessar a URL diretamente em outra guia ou sessão do navegador, esse erro será esperado. Ele ajuda a garantir que sua sessão seja mais segura e que ela possa ser acessada somente por meio do portal do Azure.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Quais layouts de teclado têm suporte durante a sessão remota do Bastion?

Atualmente, o Azure Bastion dá suporte ao layout de teclado QWERTY em inglês dentro da VM.  Estamos trabalhando para dar suporte a layouts de teclado de outras localidades.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>As sub-redes do Azure Bastion tem suporte para UDR (roteamento definido pelo usuário)?

Não. As sub-redes do Azure Bastion não tem suporte para UDR.
Para cenários que incluem o Azure Bastion e o Firewall do Azure ou a NVA (solução de virtualização de rede) na mesma rede virtual, você não precisa forçar o tráfego de uma sub-rede do Azure Bastion para o Firewall do Azure, pois a comunicação entre o Azure Bastion e suas VMs é privada. Para saber mais, confira [Acessar VMs por trás do Firewall do Azure com o Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="is-file-transfer-supported-with-azure-bastion-rdp-session"></a><a name="filetransfer"></a>A sessão RDP do Azure Bastion é compatível com transferência de arquivos?

Estamos trabalhando duro para adicionar novos recursos. Atualmente, a transferência de arquivos não é compatível, mas faz parte de nosso roteiro para o futuro. Fique à vontade para compartilhar seus comentários sobre novos recursos na [página Comentários sobre o Azure Bastion](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Como faço para lidar com falhas de implantação?

Examine todas as mensagens de erro e [gere uma solicitação de suporte no portal do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) conforme necessário. As falhas de implantação podem resultar de [Limites, cotas e restrições de assinatura do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Especificamente, os clientes podem encontrar um limite no número de endereços IP públicos permitidos por assinatura que causam falha na implantação do Azure Bastion.
