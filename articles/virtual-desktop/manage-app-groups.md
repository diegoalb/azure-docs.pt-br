---
title: Gerenciar grupos de aplicativos para a Área de Trabalho Virtual do Windows – Azure
description: Descreve como configurar locatários da Área de Trabalho Virtual do Windows no Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9a9d92ea525c6b5a64fdf7cc74babdce6a97f923
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127817"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Tutorial: Gerenciar grupos de aplicativos para a Área de Trabalho Virtual do Windows

O grupo de aplicativos padrão criado para um novo pool de hosts da Área de Trabalho Virtual do Windows também publica a área de trabalho completa. Além disso, você pode criar um ou mais grupos de aplicativos do RemoteApp para o pool de hosts. Siga este tutorial para criar um grupo de aplicativos do RemoteApp e publicar aplicativos individuais do menu **Iniciar**.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um grupo do RemoteApp.
> * Permitir acesso a programas do RemoteApps.

Antes de começar, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usar na sessão do PowerShell, caso ainda não tenha feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Criar um grupo do RemoteApp

1. Execute o seguinte cmdlet do PowerShell para criar um grupo de aplicativos vazio do RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Opcional) Para verificar se o grupo de aplicativos foi criado, execute o cmdlet a seguir para ver uma lista de todos os grupos de aplicativos para o pool de hosts.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Execute o cmdlet a seguir para obter uma lista de aplicativos do menu **Iniciar** na imagem de máquina virtual do pool de hosts. Anote os valores de **FilePath**, **IconPath**, **IconIndex** e outras informações importantes do aplicativo que você deseja publicar.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Execute o cmdlet a seguir para instalar o aplicativo com base no `AppAlias`. O `AppAlias` fica visível quando você executa a saída da etapa 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Opcional) Execute o cmdlet a seguir para publicar um novo programa RemoteApp no grupo de aplicativos criado na etapa 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Para verificar se o aplicativo foi publicado, execute o cmdlet a seguir.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Repita as etapas 1 a 5 para cada aplicativo que deseja publicar para esse grupo de aplicativos.
8. Execute o cmdlet a seguir para permitir aos usuários acesso aos programas RemoteApps no grupo de aplicativos.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um grupo de aplicativos, preenchê-lo com os programas RemoteApp e atribuir usuários ao grupo de aplicativos. Para saber como criar um pool de host de validação, consulte o tutorial a seguir. Você pode usar um pool de host de validação para monitorar a atualizações de serviço antes de implantá-las em seu ambiente de produção.

> [!div class="nextstepaction"]
> [Criar um pool de hosts para validar as atualizações de serviço](./create-validation-host-pool.md)
