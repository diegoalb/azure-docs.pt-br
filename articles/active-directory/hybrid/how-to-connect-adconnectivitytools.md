---
title: 'Azure AD Connect: O que é o módulo do PowerShell ADConnectivityTool | Microsoft Docs'
description: Este documento introduz o novo módulo PowerShell de ADConectividade e como ele pode ser usado para ajudar a solucionar problemas.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5340cd8c802df4ffbe0207b5401d2fee4e207e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "64571120"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Solucionando problemas de conectividade Azure AD com o módulo PowerShell do ADConnectivityTool

A ferramenta ADConnectivity é um módulo do PowerShell que é usado em um dos seguintes:

- Durante a instalação, quando um problema de conectividade de rede impede a validação bem-sucedida das credenciais do Active Directory fornecidas pelo usuário no Assistente.
- Pós-instalação por um usuário que chama as funções de uma sessão do PowerShell.

A ferramenta está localizada em: **C: \ Arquivos de Programas \ Microsoft Active Directory do Azure Conecte \ Tools \ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool durante a instalação

Na página **Conecte seus diretórios**, no Assistente para Conexão do Azure AD, se ocorrer um problema de rede, a ADConnectivityTool usará automaticamente uma de suas funções para determinar o que está acontecendo.  Qualquer um dos itens a seguir pode ser considerado um problema de rede:

- O nome da floresta que o usuário forneceu foi digitado incorretamente ou a floresta não existe 
- A porta UDP 389 é fechada nos controladores de domínio associados à floresta que o usuário forneceu
- As credenciais fornecidas na janela "Conta de floresta do AD" não têm privilégios para recuperar os controladores de domínio associados à floresta de destino
- Qualquer uma das portas TCP 53, 88 ou 389 é fechada nos controladores de domínio associados à floresta que o usuário forneceu 
- UDP 389 e uma porta TCP (ou portas) estão fechadas
- O DNS não pôde ser resolvido para a floresta fornecida e \ ou seus controladores de domínio associados

Sempre que qualquer um desses problemas for encontrado, uma mensagem de erro relacionada será exibida no Assistente AADConnect:


![Erro](media/how-to-connect-adconnectivitytools/error1.png)

Por exemplo, quando estamos tentando adicionar um diretório na tela **Conecte seus diretórios**, o Azure AD Connect precisa verificar isso e espera poder se comunicar com um controlador de domínio pela porta 389.  Se não puder, veremos o erro mostrado na captura de tela acima.  

O que realmente está acontecendo nos bastidores é que o Azure AD Connect está chamando a função `Start-NetworkConnectivityDiagnosisTools`.  Essa função é chamada quando a validação de credenciais falha devido a um problema de conectividade de rede.

Finalmente, um arquivo de log detalhado é gerado sempre que a ferramenta é chamada do assistente. O log está localizado em **C:\ProgramData\AADConnect\ADConnectivityTool-\<data>-\<hora>.log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools pós instalação
Depois que o Azure AD Connect tiver sido instalado, qualquer uma das funções no módulo ADConnectivityTools PowerShell poderá ser usada.  

Você pode encontrar informações de referência sobre as funções na [ADConnectivityTools Reference](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Vamos chamar essa função porque ela pode **somente** ser chamada manualmente depois que o ADConnectivityTool.psm1 for importado para o PowerShell. 

Essa função executa a mesma lógica que o Assistente do Azure AD Connect executa para validar as credenciais do AD fornecidas.  No entanto, fornece uma explicação muito mais detalhada sobre o problema e uma solução sugerida. 

A validação de conectividade consiste nas seguintes etapas:
-   Obter o objeto FQDN do domínio (nome de domínio totalmente qualificado)
-   Valide se, se o usuário selecionou "Criar nova conta do AD", essas credenciais pertencem ao grupo Administradores de empresas
-   Objeto FQDN floresta Get
-   Confirme se pelo menos um domínio associado ao objeto FQDN do Forest obtido anteriormente está acessível
-   Verifique se o nível funcional da floresta é o Windows Server 2003 ou superior.

O usuário poderá adicionar um Diretório se todas essas ações forem executadas com sucesso.

Se o usuário executar essa função depois que um problema for resolvido (ou se nenhum problema existir), a saída indicará que o usuário retorne ao Assistente de Conexão do Azure AD e tente inserir as credenciais novamente.



## <a name="next-steps"></a>Próximas etapas
- [Azure AD Connect: contas e permissões](reference-connect-accounts-permissions.md)
- Instalação expressa[Instalação expressa](how-to-connect-install-express.md)
- [Personalização Instalada](how-to-connect-install-custom.md)
- [Referência do ADConnectivityTools](reference-connect-adconnectivitytools.md)

