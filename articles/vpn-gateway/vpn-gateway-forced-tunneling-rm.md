---
title: Configure o túnel forçado para conexões site-a-site
description: Como redirecionar ou 'forçar' todo o tráfego direcionado à Internet para sua localização local.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: fc35654403bbe1375d4188476b11fd0453f74345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244622"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurar o túnel forçado usando o modelo de implantação do Azure Resource Manager

O túnel forçado permite redirecionar ou "forçar" todo o tráfego direcionado para a Internet de volta para seu local por meio de um túnel VPN de Site a Site para inspeção e auditoria. Esse é um requisito crítico de segurança para a maioria das políticas de TI empresariais. Sem o túnel forçado, o tráfego direcionado para Internet de suas VMs no Azure sempre percorrerão da infraestrutura de rede do Azure diretamente para a Internet, sem a opção para permitir que você inspecione ou audite o tráfego. O acesso não autorizado à Internet pode levar à divulgação de informações ou outros tipos de violações de segurança.



[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Este artigo o guia pela configuração de túnel forçado para redes virtuais criadas usando o modelo de implantação do Resource Manager. O túnel forçado pode ser configurado usando o PowerShell, não por meio do portal. Se você quiser configurar o túnel forçado para o modelo de implantação clássica, selecione o artigo clássico na lista suspensa abaixo:

> [!div class="op_single_selector"]
> * [PowerShell - clássico](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Sobre túnel forçado

O diagrama a seguir ilustra o funcionamento do túnel forçado. 

![Túnel forçado](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

No exemplo acima, a sub-rede Front-end não é um túnel forçado. As cargas de trabalho na sub-rede do front-end podem continuar a aceitar e a responder diretamente às solicitações de clientes da Internet. As sub-redes de Camada intermediária e Back-end são túneis forçados. As conexões de saída dessas duas sub-redes com a Internet serão forçadas ou redirecionadas de volta ao site local por meio de túneis de VPN S2S.

Isso permite que você restrinja e inspecione o acesso à Internet de suas máquinas virtuais ou serviços de nuvem no Azure, continuando a habilitar a arquitetura de serviço de várias camadas necessária. Se não houver cargas de trabalho voltadas para a Internet em suas redes virtuais, você também poderá aplicar o túnel forçado às redes virtuais inteiras.

## <a name="requirements-and-considerations"></a>Requisitos e considerações

O túnel forçado no Azure é configurado por meio de rotas de rede virtual definidas pelo usuário. Redirecionar o tráfego para um site local é expressado como uma Rota Padrão para o gateway de VPN do Azure. Para saber mais sobre rotas definidas pelo usuário e redes virtuais, confira [Encaminhamento IP e rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md).

* Cada sub-rede de rede virtual tem uma tabela de roteamento interna do sistema. A tabela de roteamento do sistema tem estes três grupos de rotas:
  
  * **Rotas locais de Rede Virtual:** diretamente para as VMs de destino na mesma rede virtual.
  * **Rotas locais:** para o gateway de VPN do Azure.
  * **Rota padrão:** diretamente para a Internet. Os pacotes destinados para os endereços IP privados não cobertos pelas duas rotas anteriores serão removidos.
* Este procedimento usa rotas definidas pelo usuário (UDR) a fim de criar uma tabela de roteamento para adicionar uma rota padrão e associar a tabela de roteamento às sub-redes de VNet para habilitar o túnel forçado nessas sub-redes.
* O túnel forçado deve ser associado a uma Rede Virtual que tenha um Gateway de VPN de roteamento. Você precisa definir um "site padrão" entre sites locais entre locais conectado à rede virtual. Além disso, o dispositivo VPN local deve ser configurado usando 0.0.0.0/0 como seletores de tráfego. 
* O túnel forçado do ExpressRoute não é configurado por meio deste mecanismo, mas é habilitado por meio do anúncio de uma rota padrão por meio de sessões de emparelhamento via protocolo BGP do ExpressRoute. Para saber mais, veja a [documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Visão geral de configuração

O procedimento a seguir o ajudará a criar um grupo de recursos e uma rede virtual. Em seguida, você criará um gateway de VPN e configurará um túnel forçado. Nesse procedimento, a rede virtual "MultiTier-VNet" tem três sub-redes: ‘Frontend’, ‘Midtier’ e ‘Backend’, com quatro conexões entre locais: ‘DefaultSiteHQ’ e três Branches.

As etapas do procedimento definem ‘DefaultSiteHQ’ como a conexão de site padrão para o túnel forçado e configuram as sub-redes Midtier e Back-end para usarem túnel forçado.

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets do PowerShell.

> [!IMPORTANT]
> É necessário instalar a versão mais recente dos cmdlets do PowerShell. Caso contrário, você poderá receber erros de validação ao executar alguns dos cmdlets.
>
>

### <a name="to-log-in"></a>Para fazer logon

[!INCLUDE [To log in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Configurar o túnel forçado

> [!NOTE]
> Você pode ver avisos dizendo que "O tipo de objeto de saída deste cmdlet será modificado em uma versão futura". Esse comportamento é esperado e você pode ignorar esses avisos com segurança.
>
>


1. Crie um grupos de recursos.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Crie uma rede virtual e especifique as sub-redes.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Crie os gateways de rede local.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Crie uma regra e uma tabela de rotas.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Associe a tabela de rotas criada acima às sub-redes Mid-Tier e Back-end.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Crie o gateway de rede virtual. Essa etapa de criação e configuração do gateway leva cerca de 45 minutos ou mais para ser concluída, pois você está criando e configurando o gateway. Se você vir erros ValidateSet relacionados ao valor do GatewaySKU, verifique se você instalou a [versão mais recente dos cmdlets do PowerShell](#before). A versão mais recente dos cmdlets do PowerShell contém os novos valores validados para os SKUs de gateway mais recentes.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Atribua um site padrão ao gateway de rede virtual. O **-GatewayDefaultSite** é o parâmetro cmdlet que permite a configuração de roteamento forçada funcionar, portanto, atente-se para configurar corretamente essa configuração. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Estabeleça as conexões VPN Site a Site.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
