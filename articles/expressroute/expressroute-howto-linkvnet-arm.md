---
title: 'ExpressRoute: Vincule um VNet a um circuito: Azure PowerShell'
description: Este documento fornece uma visão geral de como vincular as redes virtuais (VNets) aos circuitos de ExpressRoute usando o modelo de implantação do Gerenciador de Recursos e do PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 05/20/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 755b1898ee4cbc32de3a65a6bbc368ecf3eb3acf
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616375"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Conectar uma rede virtual a um circuito do ExpressRoute
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
>

Este artigo ajuda a vincular as VNets (redes virtuais) aos circuitos do Azure ExpressRoute usando o modelo de implantação do Resource Manager e PowerShell. As redes virtuais podem estar na mesma assinatura ou fazer parte de outra assinatura. Este artigo mostra como atualizar um link de rede virtual.

* Você pode vincular até 10 redes virtuais a um circuito de ExpressRoute padrão. Todas as redes virtuais deverão estar na mesma região geopolítica ao usar um circuito de ExpressRoute padrão. 

* Uma única rede virtual pode ser vinculada a até quatro circuitos de ExpressRoute. Siga as etapas neste artigo para criar um novo objeto de conexão para cada circuito de ExpressRoute ao qual você está se conectando. Os circuitos de ExpressRoute podem estar na mesma assinatura, assinaturas diferentes ou uma mistura de ambos.

* Você poderá vincular redes virtuais fora da região geopolítica do circuito do ExpressRoute ou conectar um grande número de redes virtuais ao circuito de ExpressRoute se tiver habilitado o complemento premium do ExpressRoute. Confira as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.


## <a name="before-you-begin"></a>Antes de começar

* Analise os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

* Você deve ter um circuito do ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e para que o circuito seja habilitado pelo provedor de conectividade. 
  * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Veja o artigo [Configurar roteamento](expressroute-howto-routing-arm.md) para obter instruções sobre roteamento. 
  * Verifique se o emparelhamento privado do Azure está configurado e se o emparelhamento BGP entre sua rede e a Microsoft está ativo para que você possa habilitar a conectividade de ponta a ponta.
  * Verifique se tem uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [criar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para ExpressRoute usa o GatewayType “ExpressRoute”, não VPN.

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar uma rede virtual na mesma assinatura a um circuito
Você pode vincular um gateway de rede virtual a um circuito do ExpressRoute usando o cmdlet a seguir. Verifique se o gateway de rede virtual foi criado e se está pronto para vinculação antes de executar o cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectar uma rede virtual em uma assinatura diferente a um circuito
Você pode compartilhar um circuito do ExpressRoute entre várias assinaturas. A figura a seguir mostra um esquema simples de como funciona o compartilhamento de circuitos do ExpressRoute entre várias assinaturas.

Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode usar sua própria assinatura para implantar seus serviços, mas pode compartilhar um único circuito do ExpressRoute para se conectar de volta à respectiva rede local. Um único departamento (neste exemplo: TI) pode ter o circuito do ExpressRoute. Outras assinaturas dentro da organização podem usar o circuito de ExpressRoute.

> [!NOTE]
> As cobranças por conectividade e largura de banda pelo circuito ExpressRoute serão aplicadas ao proprietário da assinatura. Todas as redes virtuais compartilham a mesma largura de banda.
> 
> 

![Conectividade entre assinaturas](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administração – proprietários e usuários do circuito

O “proprietário do circuito” é um usuário avançado autorizado do recurso de circuito do ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas pelos ‘usuários do circuito’. Usuários do circuito são proprietários de gateways de rede virtual que não estão na mesma assinatura que o circuito do ExpressRoute. Usuários do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização faz com que todas as conexões de links sejam excluídas da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito

**Criar uma autorização**

O proprietário do circuito cria uma autorização. Isso resulta na criação de uma chave de autorização que pode ser usada por um usuário do circuito para conectar seus gateways de rede virtual ao circuito do ExpressRoute. Uma autorização é válida apenas para uma conexão.

O seguinte snippet de cmdlet mostra como criar uma autorização:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


A resposta para isso conterá a chave de autorização e o status:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Examinar autorizações**

O proprietário do circuito pode examinar todas as autorizações emitidas em um circuito específico executando o seguinte cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Adicionar autorizações**

O proprietário do circuito pode adicionar autorizações usando o cmdlet a seguir.

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Excluir autorizações**

O proprietário do circuito pode revogar/excluir autorizações usando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operações do usuário do circuito

O usuário do circuito precisa da ID do par e de uma chave de autorização do proprietário do circuito. A chave de autorização é um GUID.

É possível verificar a ID de Par com o seguinte comando:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Resgatar uma autorização de conexão**

O usuário de circuito pode executar o seguinte cmdlet para resgatar uma autorização de vínculo:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Liberar uma autorização de conexão**

É possível liberar uma autorização excluindo a conexão que vincula o circuito do ExpressRoute à rede virtual.

## <a name="modify-a-virtual-network-connection"></a>Modificar uma conexão de rede virtual
Você pode atualizar determinadas propriedades de uma conexão de rede virtual. 

**Atualizar o peso da conexão**

Sua rede virtual pode ser conectada a vários circuitos do ExpressRoute. Você pode receber o mesmo prefixo de mais de um circuito do ExpressRoute. Para escolher à qual conexão enviar o tráfego destinado a esse prefixo, você pode alterar *RoutingWeight* de uma conexão. O tráfego será enviado na conexão com o *RoutingWeight* mais alto.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

O intervalo de *RoutingWeight* é de 0 a 32.000. O valor padrão é 0.

## <a name="configure-expressroute-fastpath"></a>Configure ExpressRoute FastPath 
Você pode ativar [o ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) se o gateway de rede virtual for Ultra Performance ou ErGw3AZ. O FastPath melhora o desempenho do caminho de dados, como pacotes por segundo e conexões por segundo entre sua rede local e sua rede virtual. 

**Configure o FastPath em uma nova conexão**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Atualizando uma conexão existente para habilitar o FastPath**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o ExpressRoute, consulte o [FAQ ExpressRoute](expressroute-faqs.md).
