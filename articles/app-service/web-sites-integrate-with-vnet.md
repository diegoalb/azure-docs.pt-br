---
title: Integrar aplicativos com a Rede Virtual Do Azure
description: Integre aplicativos no Azure App Service com redes virtuais Do Zure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a1a9739c444db2e41d55b8876011c066f2e71ca3
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421382"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integre seu aplicativo com uma rede virtual Do Zure

Este artigo descreve o recurso De Integração VNet do Serviço de Aplicativos Azure e como configurá-lo com aplicativos no [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Com [a Rede Virtual Azure,][VNETOverview]você pode colocar muitos de seus recursos do Azure em uma rede não-internet-routable.

O Azure App Service tem duas variações:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Habilite a integração vnet

1. Vá para a **UI de Rede** no portal app service. Em **VNet Integration,** selecione **Clique aqui para configurar**.

1. Selecione **Adicionar VNet**.

   ![Selecione a Integração VNet][1]

1. A lista de paradas contém todas as redes virtuais do Azure Resource Manager em sua assinatura na mesma região. Abaixo disso está uma lista das redes virtuais do Resource Manager em todas as outras regiões. Selecione a rede virtual com a que deseja integrar.

   ![Selecione a rede virtual][2]

   * Se a rede virtual estiver na mesma região, crie uma nova sub-rede ou selecione uma sub-rede pré-existente vazia.
   * Para selecionar uma rede virtual em outra região, você deve ter um gateway de Rede Virtual provisionado com ponto para site ativado.
   * Para se integrar a uma rede virtual clássica, em vez de selecionar a lista de parada da **Rede Virtual,** selecione **Clique aqui para se conectar a um VNet clássico**. Selecione a rede virtual clássica desejada. A rede virtual de destino já deve ter um gateway de Rede Virtual provisionado com ativação ponto a ponto.

    ![Selecione VNet clássico][3]

Durante a integração, o aplicativo é reiniciado. Quando a integração estiver concluída, você verá detalhes na rede virtual com a qual está integrado.

Depois que seu aplicativo é integrado à sua rede virtual, ele usa o mesmo servidor DNS com o que sua rede virtual está configurada, a menos que seja a Azure DNS Private Zones. Atualmente, você não pode usar a Integração VNet com as Zonas Privadas do Azure DNS.

## <a name="regional-vnet-integration"></a>Integração VNet Regional

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Como funciona a Integração VNet regional

Os aplicativos no Serviço de Aplicativos estão hospedados nas funções dos trabalhadores. Os planos básicos e de preços mais altos são planos de hospedagem dedicados onde não há cargas de trabalho de outros clientes sendo executados nos mesmos trabalhadores. A Integração VNet Regional funciona montando interfaces virtuais com endereços na sub-rede delegada. Como o endereço de partir está em sua rede virtual, ele pode acessar a maioria das coisas dentro ou através de sua rede virtual como uma VM em sua rede virtual faria. A implementação de rede é diferente de executar uma VM em sua rede virtual. É por isso que alguns recursos de rede ainda não estão disponíveis para este recurso.

![Como funciona a Integração VNet regional][5]

Quando a Integração VNet regional é ativada, seu aplicativo faz chamadas de saída para a internet através dos mesmos canais que o normal. Os endereços de saída listados no portal de propriedades do aplicativo são os endereços ainda usados pelo seu aplicativo. O que muda para o seu aplicativo são as chamadas para serviços protegidos por ponto final de serviço, ou os endereços RFC 1918 vão para sua rede virtual. Se WEBSITE_VNET_ROUTE_ALL estiver definido como 1, todo o tráfego de saída pode ser enviado para sua rede virtual.

O recurso suporta apenas uma interface virtual por trabalhador. Uma interface virtual por trabalhador significa um plano regional de Integração VNet por Serviço de Aplicativo. Todos os aplicativos do mesmo plano de Serviço de Aplicativo podem usar a mesma Integração VNet. Se você precisa de um aplicativo para se conectar a uma rede virtual adicional, você precisa criar outro plano de Serviço de Aplicativo. A interface virtual usada não é um recurso ao que os clientes têm acesso direto.

Devido à natureza de como essa tecnologia opera, o tráfego usado com a Integração VNet não aparece nos registros de fluxo do Azure Network Watcher ou do NSG.

## <a name="gateway-required-vnet-integration"></a>Integração VNet necessária ao Gateway

O VNet Integration, necessário ao Gateway, suporta a conexão a uma rede virtual em outra região ou a uma rede virtual clássica. Integração VNet necessária ao Gateway:

* Permite que um aplicativo se conecte a apenas uma rede virtual por vez.
* Permite que até cinco redes virtuais sejam integradas dentro de um plano de Serviço de Aplicativo.
* Permite que a mesma rede virtual seja usada por vários aplicativos em um plano de Serviço de Aplicativo sem afetar o número total que pode ser usado por um plano de Serviço de Aplicativo. Se você tem seis aplicativos usando a mesma rede virtual no mesmo plano de Serviço de Aplicativo, isso conta como uma rede virtual sendo usada.
* Suporta um SLA de 99,9% devido ao SLA no gateway.
* Permite que seus aplicativos usem o DNS com o que a rede virtual está configurada.
* Requer um gateway baseado em rota de rede virtual configurado com uma VPN ponto a ponto sSTP antes que ele possa ser conectado a um aplicativo.

Você não pode usar a integração VNet necessária ao gateway:

* Com aplicativos Linux.
* Com uma rede virtual conectada ao Azure ExpressRoute.
* Para acessar o ponto final do serviço, os recursos garantidos.
* Com um gateway de coexistência que suporta vpns expressroute e point-to-site ou site-to-site.

### <a name="set-up-a-gateway-in-your-virtual-network"></a>Configure um gateway em sua rede virtual ###

Para criar um gateway:

1. [Crie uma sub-rede gateway][creategatewaysubnet] em sua rede virtual.  

1. [Crie o gateway VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Defina os endereços ponto a ponto][setp2saddresses]. Se o gateway não estiver no SKU básico, então o IKEV2 precisará ser desabilitado na configuração ponto a site e o SSTP deverá ser selecionado. O espaço de endereço ponto a ponto deve estar nos blocos de endereços RFC 1918 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Se você criar o gateway para uso com o App Service VNet Integration, você não precisará carregar um certificado. A criação do gateway pode levar 30 minutos. Você não poderá integrar seu aplicativo à sua rede virtual até que o gateway seja provisionado.

### <a name="how-gateway-required-vnet-integration-works"></a>Como funciona a Integração VNet necessária por gateway

A Integração VNet necessária pelo Gateway é construída em cima da tecnologia VPN ponto a ponto. As VPNs ponto a ponto limitam o acesso da rede à máquina virtual que hospeda o aplicativo. Os aplicativos são restritos a enviar tráfego para a internet apenas através de Conexões Híbridas ou através da Integração VNet. Quando seu aplicativo é configurado com o portal para usar a Integração VNet exigida pelo gateway, uma negociação complexa é gerenciada em seu nome para criar e atribuir certificados no gateway e no lado do aplicativo. O resultado é que os trabalhadores usados para hospedar seus aplicativos são capazes de se conectar diretamente ao gateway de rede virtual na rede virtual selecionada.

![Como funciona a Integração VNet necessária por gateway][6]

### <a name="access-on-premises-resources"></a>Acesse os recursos locais

Os aplicativos podem acessar recursos locais integrando-se a redes virtuais que possuem conexões site-a-site. Se você usar a Integração VNet necessária pelo gateway, atualize suas rotas de gateway VPN no local com seus blocos de endereços ponto a ponto. Quando o VPN site a site é configurado pela primeira vez, os scripts usados para configurá-lo devem configurar as rotas adequadamente. Se adicionar os endereços ponto a site depois de criar uma VPN site a site, você precisará atualizar as rotas manualmente. Detalhes sobre como fazer isso variam por gateway e não são descritos aqui. Você não pode ter o BGP configurado com uma conexão VPN site-site.

Nenhuma configuração adicional é necessária para que o recurso regional de Integração VNet chegue através de sua rede virtual aos recursos locais. Você simplesmente precisa conectar sua rede virtual aos recursos locais usando o ExpressRoute ou uma VPN site-to-site.

> [!NOTE]
> O recurso VNet Integration exigido por gateway não integra um aplicativo com uma rede virtual que tenha um gateway ExpressRoute. Mesmo que o gateway ExpressRoute esteja configurado no [modo de coexistência,][VPNERCoex]a Integração VNet não funciona. Se você precisar acessar recursos através de uma conexão ExpressRoute, use o recurso regional VNet Integration ou um [App Service Environment][ASE], que é executado em sua rede virtual.
> 
> 

### <a name="peering"></a>Emparelhamento

Se você usar o peering com a Integração VNet regional, você não precisará fazer nenhuma configuração adicional.

Se você usar a Integração VNet necessária ao gateway com peering, você precisa configurar alguns itens adicionais. Para configurar o emparelhamento para funcionar com o aplicativo:

1. Adicione uma conexão de peering na rede virtual à que seu aplicativo se conecta. Ao adicionar a conexão de peering, **habilite permitir acesso à rede virtual** e selecione Permitir tráfego **encaminhado** e Permitir o trânsito **de gateway**.
1. Adicione uma conexão de peering na rede virtual que está sendo espiada para a rede virtual à que você está conectado. Quando você adicionar a conexão de peering na rede virtual de destino, **habilite permitir acesso à rede virtual** e selecione Permitir tráfego **encaminhado** e **Permitir gateways remotos**.
1. Vá para o **plano de serviço de** > **aplicativo Networking** > **VNet Integration** UI no portal. Selecione a rede virtual à que seu aplicativo se conecta. Na seção de roteamento, adicione o intervalo de endereços da rede virtual que está de olho na rede virtual à rede à que seu aplicativo está conectado.

## <a name="manage-vnet-integration"></a>Gerenciar a integração VNet

Conectar-se e desconectar-se com uma rede virtual está em um nível de aplicativo. As operações que podem afetar a integração do VNet em vários aplicativos estão no nível do plano de serviço do aplicativo. A partir do aplicativo > portal**de integração VNet** **networking,** > você pode obter detalhes em sua rede virtual. Você pode ver informações semelhantes no nível > do plano de serviço do aplicativo no portal de**integração vnet de** serviço**de** >  **aplicativo.**

A única operação que você pode ter na visualização do aplicativo da sua instância de Integração VNet é desconectar seu aplicativo da rede virtual à que está conectado atualmente. Para desconectar seu aplicativo de uma rede virtual, selecione **Desconectar**. Seu aplicativo é reiniciado quando você se desconecta de uma rede virtual. Desconectar não altera sua rede virtual. A sub-rede ou gateway não foi removida. Se você quiser excluir sua rede virtual, primeiro desconecte seu aplicativo da rede virtual e exclua os recursos nela, como gateways.

O plano de serviço de aplicativo VNet Integration UI mostra todas as integrações de rede virtual usadas pelos aplicativos em seu plano de Serviço de Aplicativo. Para ver detalhes em cada rede virtual, selecione a rede virtual em que você está interessado. Existem duas ações que você pode executar aqui para a integração VNet necessária pelo gateway:

* **Rede de sincronização**: A operação de rede de sincronização é usada apenas para o recurso de integração VNet dependente de gateway. A realização de uma operação de rede de sincronização garante que seus certificados e informações de rede estejam em sincronia. Se você adicionar ou alterar o DNS da sua rede virtual, execute uma operação de rede de sincronização. Esta operação reinicia todos os aplicativos que usam esta rede virtual.
* **Adicionar rotas**: Adicionar rotas impulsiona o tráfego de saída em sua rede virtual.

### <a name="gateway-required-vnet-integration-routing"></a>Roteamento de integração VNet necessário por gateway
As rotas definidas em sua rede virtual são usadas para direcionar o tráfego para sua rede virtual a partir do seu aplicativo. Para enviar tráfego de saída adicional para a rede virtual, adicione esses blocos de endereços aqui. Esse recurso só funciona com a Integração VNet necessária ao gateway. As tabelas de rota não afetam o tráfego do aplicativo quando você usa a Integração VNet exigida pelo gateway da maneira que elas fazem com a Integração VNet regional.

### <a name="gateway-required-vnet-integration-certificates"></a>Certificados de integração VNet necessários ao Gateway
Quando a Integração VNet necessária é ativada, há uma troca de certificados necessária para garantir a segurança da conexão. Juntamente com os certificados, há a configuração do DNS, as rotas e outros itens semelhantes que descrevem a rede.

Se os certificados ou informações de rede forem alterados, selecione **Sync Network**. Quando você seleciona **Sync Network,** você causa uma breve interrupção na conectividade entre seu aplicativo e sua rede virtual. Embora seu aplicativo não seja reiniciado, a perda de conectividade pode fazer com que seu site não funcione corretamente.

## <a name="pricing-details"></a>Detalhes de preço
O recurso regional De Integração VNet não tem nenhuma taxa adicional para uso além das taxas de nível de preço do plano de serviço do Aplicativo.

Três cargas estão relacionadas ao uso do recurso de integração VNet exigido pelo gateway:

* **Taxas**de nível de preço do plano de serviço do App : Seus aplicativos precisam estar em um plano de serviço de aplicativo Padrão, Premium ou PremiumV2. Para obter mais informações sobre esses custos, consulte [os preços do App Service][ASPricing].
* **Custos de transferência**de dados : Há uma cobrança pela saída de dados, mesmo que a rede virtual esteja no mesmo data center. Essas taxas são descritas nos [detalhes dos preços da Transferência de Dados][DataPricing].
* **Custos de gateway VPN**: Há um custo para o gateway de rede virtual que é necessário para a VPN ponto a ponto. Para obter mais informações, consulte [os preços dos gateways VPN][VNETPricing].

## <a name="troubleshooting"></a>Solução de problemas

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automação

O suporte à CLI está disponível para integração regional do VNet. Para acessar os seguintes comandos, [instale o Azure CLI][installCLI].

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

Para a integração VNet necessária pelo gateway, você pode integrar o App Service com uma rede virtual Azure usando o PowerShell. Para obter um script pronto para ser executado, consulte [Conecte um aplicativo no Azure App Service a uma rede virtual Do Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
