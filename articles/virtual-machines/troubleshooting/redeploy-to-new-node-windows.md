---
title: Reimplantar máquinas virtuais Windows no Azure | Microsoft Docs
description: Como reimplantar máquinas virtuais Windows no Azure para atenuar problemas de conexão RDP.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 36af0eeb43fb209ed65f950576f2dc9e97ec3633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058634"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Reimplantar uma máquina virtual Windows em um novo nó do Azure
Se você enfrentou dificuldades para solucionar problemas de conexão RDP (Área de Trabalho Remota) ou de acesso ao aplicativo em uma VM (máquina virtual) do Azure baseada em Windows, reimplantar a VM pode ajudar. Ao reimplantar uma VM, o Azure desligará a VM, moverá a VM para um novo nó na infraestrutura do Azure e, em seguida, a ligará novamente mantendo todas as opções de configuração e recursos associados. Este artigo mostra como reimplantar uma VM usando o Azure PowerShell ou o Portal do Azure.

> [!NOTE]
> Depois que você reimplanta uma VM, o disco temporário será perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual serão atualizados. 


## <a name="using-azure-powershell"></a>Usando o PowerShell do Azure
Verifique se você tem o Azure PowerShell 1.x mais recente instalado em seu computador. Para obter mais informações, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

O seguinte exemplo implanta a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Próximas etapas
Você pode encontrar ajuda específica em [Solução de problemas de conexões RDP](troubleshoot-rdp-connection.md) ou [Etapas detalhadas de solução de problemas de RDP](detailed-troubleshoot-rdp.md) se você estiver enfrentando problemas para se conectar à sua VM. Você também pode ler [problemas com a solução de problemas de aplicativo](../windows/troubleshoot-app-connection.md) se não conseguir acessar um aplicativo em execução em sua VM.

