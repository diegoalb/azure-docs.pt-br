---
title: Configure o login e faça login com uma conta do WeChat
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do WeChat em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c53210939358255b20d0e976df9c4bff88580a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184427"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do WeChat usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Criar um aplicativo WeChat

Para usar uma conta do WeChat como provedor de identidade no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo em seu inquilino que o represente. Se você ainda não tem uma conta no WeChat, você pode obter informações em [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrar um aplicativo WeChat

1. Faça login [https://open.weixin.qq.com/](https://open.weixin.qq.com/) com suas credenciais do WeChat.
1. Escolha **管理中心** (Centro de gerenciamento).
1. Siga as etapas para registrar um novo aplicativo.
1. Insira `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **授权回调域** (URL de retorno de chamada). Por exemplo, se o nome do locatário é contoso, defina a URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Copie a **ID DO APLICATIVO** e a **CHAVE DO APLICATIVO**. Você precisará delas para adicionar o provedor de identidade ao locatário.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurar o WeChat como um provedor de identidade em seu locatário

1. Faça login no [portal Azure](https://portal.azure.com/) como o administrador global do seu inquilino Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e selecione **o WeChat (Preview)**.
1. Digite um **nome**. Por exemplo, *WeChat*.
1. Para o **ID do cliente,** digite o ID do aplicativo WeChat que você criou anteriormente.
1. Para o **segredo do Cliente,** digite a CHAVE DO APP que você gravou.
1. Selecione **Salvar**.
