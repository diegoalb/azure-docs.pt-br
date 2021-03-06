---
title: Gerenciar módulos na Automação Azure
description: Este artigo descreve como gerenciar módulos no Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 605d1bc72406a9aeecc9273f9bd2d7fd2b30ab11
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618703"
---
# <a name="manage-modules-in-azure-automation"></a>Gerenciar módulos na Automação Azure

Você pode importar módulos PowerShell para o Azure Automation para disponibilizar seus cmdlets em runbooks e seus recursos DSC disponíveis nas configurações do DSC. Nos bastidores, a Azure Automation armazena esses módulos. No trabalho de runbook e no tempo de execução do trabalho de compilação do DSC, a Automação os carrega nas caixas de areia da Azure Automation, onde os runbooks executam e as configurações do DSC compilam. Todos os recursos DSC em módulos também são colocados automaticamente no servidor de pull de DSC de Automação. As máquinas podem puxá-las quando aplicam configurações DSC.

Os módulos usados no Azure Automation podem ser módulos personalizados que você criou, módulos da Galeria PowerShell ou os módulos AzureRM e Az para o Azure. Quando você cria uma conta de Automação, alguns módulos são importados por padrão.

## <a name="default-modules"></a>Módulos padrão

A tabela a seguir lista módulos importados por padrão quando uma conta de Automação é criada. A automação pode importar versões mais recentes desses módulos. No entanto, você não pode remover a versão original de sua conta de Automação mesmo se você excluir uma versão mais recente.

|Nome do módulo|Versão|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1,1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="internal-cmdlets"></a>Cmdlets internos

A tabela abaixo lista cmdlets no módulo interno `Orchestrator.AssetManagement.Cmdlets` que é importado para cada conta de Automação. Esses cmdlets são acessíveis em seus runbooks e configurações de DSC e permitem que você interaja com ativos dentro de sua conta de Automação. Além disso, os cmdlets internos permitem que você recupere segredos de variáveis criptografadas, credenciais e conexões criptografadas. Os cmdlets Do Azure PowerShell não são capazes de recuperar esses segredos. Esses cmdlets não exigem que você se conecte implicitamente ao Azure ao usá-los, como ao usar uma Conta Run As para autenticar o Azure.

>[!NOTE]
>Esses cmdlets internos estão disponíveis em um Trabalhador de Runbook Híbrido do Windows, mas não em um Executão Híbrido Linux. Use os cmdlets correspondentes do módulo [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) ou [Az](../az-modules.md) para runbooks em execução diretamente no computador ou contra recursos em seu ambiente. 

|Nome|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Trabalho de automação de espera|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="importing-modules"></a>Módulos de importação

Existem várias maneiras de importar um módulo para sua conta de Automação. As seções a seguir mostram as diferentes maneiras de importar um módulo.

> [!NOTE]
> O tamanho máximo do caminho para um arquivo em um módulo usado no Azure Automation é de 140 caracteres. A automação não pode importar um arquivo com tamanho de caminho `Import-Module`acima de 140 caracteres na sessão PowerShell com .

### <a name="import-modules-in-azure-portal"></a>Módulos de importação no portal Azure

Para importar um módulo no portal Azure:

1. Abra sua conta de Automação.
2. Selecione **módulos** em **Recursos Compartilhados**.
3. Clique **em Adicionar um módulo**. 
4. Selecione o arquivo **.zip** que contém o módulo.
5. Clique em **OK** para iniciar o processo de importação.

### <a name="import-modules-using-powershell"></a>Módulos de importação usando powershell

Você pode usar o [cmdlet New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) para importar um módulo para sua conta de Automação. O cmdlet leva uma URL para um pacote .zip do módulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Você também pode usar o mesmo cmdlet para importar um módulo diretamente da Galeria PowerShell. Certifique-se `ModuleName` de `ModuleVersion` pegar e da [Galeria PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzureRmAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```
### <a name="import-modules-from-powershell-gallery"></a>Módulos de importação da Galeria PowerShell

Você pode importar módulos da [Galeria PowerShell](https://www.powershellgallery.com) diretamente da galeria ou da sua conta de Automação.

Para importar um módulo diretamente da Galeria PowerShell:

1. Vá https://www.powershellgallery.com e procure o módulo para importar.
2. Clique **em Implantar no Azure Automation** na guia Automação do **Azure** em **Opções de instalação**. Essa ação abre o portal do Azure. 
3. Na página Importar, selecione sua conta de Automação e clique em **OK**.

![Módulo de importação da Galeria PowerShell](../media/modules/powershell-gallery.png)

Para importar um módulo da Galeria PowerShell diretamente da sua conta de automação:

1. Selecione **módulos** em **Recursos Compartilhados**. 
2. Na página Módulos, clique **em Procurar galeria**e, em seguida, procure na galeria por um módulo. 
3. Selecione o módulo para importar e clique **em Importar**. 
4. Na página Importar, clique em **OK** para iniciar o processo de importação.

![Importação da Galeria PowerShell do portal Azure](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Excluindo módulos

Se você tiver problemas com um módulo ou precisar reverter para uma versão anterior de um módulo, você pode excluí-lo da sua conta de Automação. Você não pode excluir as versões originais dos [módulos padrão importados](#default-modules) quando você cria uma conta de Automação. Se o módulo a ser excluído for uma versão mais recente de um dos [módulos padrão,](#default-modules)ele volta para a versão que foi instalada com sua conta de Automação. Caso contrário, qualquer módulo que você excluir da sua conta de Automação será removido.

### <a name="delete-modules-in-azure-portal"></a>Excluir módulos no portal Azure

Para remover um módulo no portal Azure:

1. Navegue até sua conta de automação e selecione **Módulos** em **Recursos Compartilhados**. 
2. Selecione o módulo que deseja remover. 
3. Na página **Módulo,** **selecione Excluir**. Se este módulo for um dos [módulos padrão,](#default-modules)ele volta para a versão que existia quando a conta de Automação foi criada.

### <a name="delete-modules-using-powershell"></a>Excluir módulos usando o PowerShell

Para remover um módulo através do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```
## <a name="adding-a-connection-type-to-your-module"></a>Adicionando um tipo de conexão ao seu módulo

Você pode fornecer um tipo de [conexão](../automation-connections.md) personalizada para usar em sua conta de Automação adicionando um arquivo de metadados opcional ao seu módulo. Este arquivo especifica um tipo de conexão Azure Automation a ser usado com os cmdlets do módulo em sua conta de Automação. Para conseguir isso, você deve primeiro saber como criar um módulo PowerShell. Veja [como escrever um módulo de script PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Use uma conexão personalizada no portal Azure](../media/modules/connection-create-new.png)

O arquivo especificando propriedades do tipo de conexão é chamado ** &lt;ModuleName&gt;-Automation.json** e é encontrado na pasta de módulo do seu arquivo **compactado .zip.** Este arquivo contém os campos de uma conexão que são necessários para se conectar ao sistema ou serviço que o módulo representa. A configuração permite a criação de um tipo de conexão no Azure Automation. Usando este arquivo, você pode definir os nomes de campo, tipos e se os campos são criptografados ou opcionais para o tipo de conexão do módulo. O exemplo a seguir é um modelo no formato de arquivo **.json** que define as propriedades do nome de usuário e da senha:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="best-practices-for-authoring-modules"></a>Melhores práticas para a autoria de módulos

Recomendamos que você siga as considerações nesta seção ao criar um módulo PowerShell para uso na Azure Automation.

### <a name="version-folder"></a>Pasta de versão

NÃO inclua uma pasta de versão dentro do pacote **.zip** para o seu módulo.  Este problema é menos preocupante para os runbooks, mas causa um problema com o serviço de Configuração do Estado. A Azure Automation cria a pasta de versão automaticamente quando o módulo é distribuído a nós gerenciados pelo DSC. Se existir uma pasta de versão, você acaba com duas instâncias. Aqui está uma estrutura de pasta de exemplo para um módulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informações de ajuda

Inclua uma sinopse, descrição e ajude uri para cada cmdlet em seu módulo. No PowerShell, você pode definir informações de `Get-Help` ajuda para cmdlets usando o cmdlet. O exemplo a seguir mostra como definir uma sinopse e ajudar o URI em um arquivo de módulo **.psm1:**

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Fornecer essas informações mostra `Get-Help` texto de ajuda através do cmdlet no console PowerShell. Este texto também é exibido no portal Azure.

  ![Ajuda do Módulo de Integração](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo de conexão

Se o módulo se conectar a um serviço externo, defina um [tipo de conexão](#adding-a-connection-type-to-your-module). Cada cmdlet no módulo deve aceitar um objeto de conexão (uma instância desse tipo de conexão) como parâmetro. Os usuários mapeiam parâmetros do ativo de conexão aos parâmetros correspondentes do cmdlet cada vez que chamam um cmdlet. Com base no exemplo do runbook acima, ele `ContosoConnection` usa um exemplo de recurso de conexão Contoso chamado para acessar recursos do Contoso e devolver dados do serviço externo.

  No exemplo a seguir, os campos `UserName` `Password` são mapeados para as propriedades e propriedades de um `PSCredential` objeto e, em seguida, passados para o cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Uma maneira mais fácil e melhor de abordar esse comportamento é passando diretamente o objeto de conexão para o cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Você pode habilitar um comportamento semelhante para seus cmdlets, permitindo que eles aceitem um objeto de conexão diretamente como um parâmetro, em vez de apenas campos de conexão para parâmetros. Normalmente, convém ter um conjunto de parâmetros para cada um, para que um usuário que não esteja usando a Automação do Azure possa chamar seus cmdlets sem construir uma tabela de hash para agir como o objeto de conexão. O conjunto `UserAccount`de parâmetros é usado para passar as propriedades do campo de conexão. `ConnectionObject`permite que você passe a conexão diretamente.

### <a name="output-type"></a>Tipo de saída

Defina o tipo de saída para todos os cmdlets do seu módulo. A definição de um tipo de saída para um cmdlet permite o IntelliSense no tempo de criação para ajudar você a determinar as propriedades de saída do cmdlet, para uso durante a criação. Essa prática é especialmente útil durante a autoria gráfica do runbook de Automação, para a qual o conhecimento em tempo de design é fundamental para uma experiência fácil do usuário com o seu módulo.

Adicione `[OutputType([<MyOutputType>])]` `MyOutputType` onde é um tipo válido. Para saber `OutputType`mais sobre , consulte [Sobre funções OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O código a seguir `OutputType` é um exemplo de adição a um cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Tipo de saída do runbook gráfico](../media/modules/runbook-graphical-module-output-type.png)

  Esse comportamento é semelhante à funcionalidade "preenchimento automático" da saída de um cmdlet no ISE do PowerShell sem precisar executá-lo.

  ![IntelliSense POSH](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet

Torne todos os cmdlets do seu módulo apátridas. Vários trabalhos de runbook podem `AppDomain` ser executados simultaneamente no mesmo processo e caixa de areia. Se houver algum estado compartilhado nesses níveis, os empregos podem afetar uns aos outros. Esse comportamento pode levar a problemas intermitentes e difíceis de diagnosticar. Aqui está um exemplo do que NÃO fazer:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Dependência do módulo

Certifique-se de que o módulo está totalmente contido em um pacote com xcopy. Os módulos de automação do Azure são distribuídos às caixas de areia de Automação quando os runbooks são executados. Os módulos devem funcionar independentemente do host que os executa. 

Você deve ser capaz de fechar e mover um pacote de módulo e tê-lo funcionando normalmente quando importado para o ambiente PowerShell de outro host. Para que isso aconteça, certifique-se de que o módulo não dependa de nenhum arquivo fora da pasta do módulo que seja fechado quando o módulo for importado para a Automação Azure. 

Seu módulo não deve depender de nenhuma configuração de registro única em um host. Um exemplo são as configurações feitas quando um produto é instalado. 

Certifique-se de que todos os arquivos do módulo tenham caminhos com menos de 140 caracteres. Quaisquer caminhos com mais de 140 caracteres causam problemas com a importação de runbooks. Se você não seguir essa prática recomendada, o módulo não poderá ser utilizável na Azure Automation.  

### <a name="references-to-azurerm-and-az"></a>Referências a AzureRM e Az

Se fizer referência ao [módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) em seu módulo, certifique-se de que você também não está fazendo referência ao AzureRM. Você não pode usar o módulo Az em conjunto com o módulo AzureRM. Az é suportado em runbooks, mas não é importado por padrão. Para saber mais sobre o módulo Az e considerações a serem consideradas, consulte [o suporte ao módulo AZ na Azure Automation](../az-modules.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de módulos PowerShell, consulte [Writing a Windows PowerShell Module](/powershell/scripting/developer/windows-powershell).
