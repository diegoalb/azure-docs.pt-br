---
title: Não é possível conectar as Máquinas Virtuais do Microsoft Azure porque o NIC foi desabilitado | Microsoft Docs
description: Saiba como solucionar um problema no qual o RDP falha porque o NIC está desabilitado na VM do Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 315974e4995630eb3af055ac0e1c44f7d8dd0737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918233"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Não é possível conectar área de trabalho remota em uma VM porque o adaptador de rede está desabilitado

Este artigo explica como resolver um problema em que não é possível fazer uma conexão de Área de Trabalho Remota a Máquinas Virtuais do Windows (VMs), se a interface de rede for desabilitada.


## <a name="symptoms"></a>Sintomas

Não é possível fazer uma conexão RDP ou qualquer outro tipo de conexão com qualquer outra porta para uma VM no Azure porque o adaptador de rede na VM está desabilitado.

## <a name="solution"></a>Solução

Antes de seguir essas etapas, tire um instantâneo do disco do SO da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para habilitar o adaptador de rede da VM, use o Controle serial ou [redefina o adaptador de rede](#reset-network-interface) da VM.

### <a name="use-serial-control"></a>Usar o Controle serial

1. Conecte-se ao [Console Serial e abra a instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se o Console Serial não estiver habilitado em sua VM, consulte [a interface de rede redefinida](#reset-network-interface).
2. Verifique o estado do adaptador de rede:

        netsh interface show interface

    Anote o nome do adaptador de rede desabilitado.

3. Habilite o adaptador de rede:

        netsh interface set interface name="interface Name" admin=enabled

    Por exemplo, se o adaptador de rede nomear "Ethernet 2", execute o seguinte comando:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Verifique o estado do adaptador de rede novamente para certificar-se de que o adaptador de rede está habilitado.

        netsh interface show interface

    Não é necessário reiniciar a VM nesse momento. A VM retornará acessível.

5.  Conecte a VM e verifique se o problema foi resolvido.

## <a name="reset-network-interface"></a>Redefinir o adaptador de rede

Para reiniciar a interface de rede, altere o endereço IP para outro endereço IP que está disponível na Sub-rede. Para fazer isso, use o portal do Azure ou Azure PowerShell. Para obter mais informações, consulte [Redefinir Adaptador de Rede](reset-network-interface.md).
