---
title: Crie uma VM com um endereço IP privado estático - Azure PowerShell
description: Aprenda a criar uma máquina virtual com um endereço IP privado usando o PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1745ca176fac18b4903686cb556670531ee40a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244752"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Crie uma máquina virtual com um endereço IP privado estático usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode criar uma máquina virtual (VM) com um endereço IP privado estático. Atribua um endereço IP privado estático, em vez de um endereço dinâmico, se você quiser selecionar qual endereço de uma sub-rede é atribuído a uma VM. Saiba mais sobre [endereços IP privados estáticos](virtual-network-ip-addresses-overview-arm.md#allocation-method). Para alterar um endereço IP privado atribuído a uma VM existente de dinâmica para estática ou para trabalhar com endereços IP públicos, consulte [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Você pode concluir as etapas a seguir no seu computador local ou usando o Shell de Nuvem do Azure. Para usar seu computador local, verifique se você tem o [Azure PowerShell instalado](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Para usar o Azure Cloud Shell, selecione **Experimente** no canto superior direito de qualquer caixa de comando a seguir. O Cloud Shell entrará no Azure.

1. Se usar o Cloud Shell, vá para a etapa 2. Abra uma sessão de comando e entre no Azure com `Connect-AzAccount`.
2. Crie um grupo de recursos com o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos na região Leste do Azure dos EUA:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Crie uma configuração de sub-rede e uma rede virtual com os comandos [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) e [New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Crie uma interface de rede na rede virtual e atribua um endereço IP privado da sub-rede à interface de rede com os comandos [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) e [New-AzNetworkInterface:](/powershell/module/az.network/new-aznetworkinterface)

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Crie uma configuração de VM com [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)e crie a VM com [New-AzVM](/powershell/module/az.Compute/New-azVM). Quando solicitado, forneça um nome de usuário e senha para ser usado como o login nas credenciais da VM:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Embora você possa adicionar configurações de endereço IP privadas ao sistema operacional, recomendamos não fazê-lo até depois de ler [Adicionar um endereço IP privado a um sistema operacional](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Para acessar a VM da internet, você deve atribuir um endereço IP público à VM. Você também pode alterar uma atribuição dinâmica de endereço IP privado para uma atribuição estática. Para obter detalhes, consulte [Adicionar ou alterar endereços IP](virtual-network-network-interface-addresses.md). Além disso, é recomendável limitar o tráfego de rede à Sua VM associando um grupo de segurança de rede à interface de rede, à sub-rede em que você criou a interface de rede, ou ambos. Para obter detalhes, consulte [Gerenciar grupos de segurança de rede](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e atribuindo um [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) a uma máquina virtual do Azure.
- Saiba mais sobre a criação de máquinas virtuais [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows.](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
