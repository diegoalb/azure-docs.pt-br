---
title: Versão de Cliente e Servidor SDK
description: Lista de SDKs do cliente e compatibilidade com versões do servidor SDK para Serviços Móveis e Aplicativos Móveis Azure.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: a9ba442c00ec2498139ee34a1ff7497c98f17ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293479"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Controle de versão de cliente e servidor em Aplicativos Móveis e Serviços Móveis

A versão mais recente dos Serviços Móveis do Azure é o recurso **Aplicativos Móveis** do Serviço de Aplicativo do Azure.

Os SDKs de cliente e servidor de Aplicativos Móveis são baseados originalmente nos Serviços Móveis, mas *não* são compatíveis entre si.
Ou seja, você deve usar um SDK de cliente de *Aplicativos Móveis* com SDK de servidor de *Aplicativos Móveis* e da mesma forma para *Serviços Móveis*. Esse contrato é imposto por meio de um valor de cabeçalho especial usado pelos SDKs de cliente e servidor, `ZUMO-API-VERSION`.

Observação: sempre que este documento se refere a um back-end de *Serviços Móveis* , ele não necessariamente precisa estar hospedados nos Serviços Móveis. Agora é possível migrar um serviço móvel para ser executado no App Service sem nenhuma alteração de código, mas o serviço ainda estaria usando versões SDK *de serviços móveis.*

## <a name="header-specification"></a>Especificação de cabeçalho
A chave `ZUMO-API-VERSION` pode ser especificada no cabeçalho HTTP ou na cadeia de consulta. O valor é uma cadeia de caracteres de versão no formulário **x.y.z**.

Por exemplo: 

`GET https://service.azurewebsites.net/tables/TodoItem`

CABEÇALHOS: ZUMO-API-VERSION: 2.0.0

`POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0`

## <a name="opting-out-of-version-checking"></a>Recusando a verificação de versão
Você pode recusar a verificação de versão definindo um valor **verdadeiro** para a configuração do aplicativo **MS_SkipVersionCheck**. Especifique isso no seu web.config ou na seção Configurações do Aplicativo do Portal do Azure.

> [!NOTE]
> Há algumas alterações de comportamento entre os Serviços Móveis e os Aplicativos Móveis, especialmente nas áreas de sincronização offline, autenticação e notificações por push. Você deve recusar verificação somente após o teste completo para garantir que essas alterações de comportamento não interrompam a funcionalidade do aplicativo.

## <a name="azure-mobile-apps-client-and-server"></a><a name="2.0.0"></a>Servidor e cliente de Aplicativos Móveis do Azure
### <a name="mobile-apps-client-sdks"></a><a name="MobileAppsClients"></a>SDKs de *clientes de aplicativos* móveis
A verificação de versão foi introduzida começando com as seguintes versões do SDK do cliente para **Aplicativos Móveis do Azure**:

| Plataforma cliente | Versão | Valor de cabeçalho de versão |
| --- | --- | --- |
| Cliente gerenciado (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>SDKs de servidor dos *Aplicativos* Móveis
A verificação de versão está incluída nas seguintes versões do SDK do servidor:

| Plataforma servidor | . | Cabeçalho de versão aceito |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento dos back-ends de Aplicativos Móveis
| ZUMO-API-VERSION | Valor de MS_SkipVersionCheck | Resposta |
| --- | --- | --- |
| x.y.z ou Null |True |200 - OK |
| Nulo |Falso/não especificado |400 - solicitação inválida |
| 1.x.y |Falso/não especificado |400 - solicitação inválida |
| 2.0.0-2.x.y |Falso/não especificado |200 - OK |
| 3.0.0-3.x.y |Falso/não especificado |400 - solicitação inválida |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
