---
title: 'Início Rápido: Criar um aplicativo Web que inicia a Leitura Avançada com C#'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você cria um aplicativo Web do zero e adiciona a funcionalidade da API de Leitura Avançada.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 8dd8459922caa9f765d59bc28fbf050b86834b46
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845253"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Início Rápido: Criar um aplicativo Web que inicia a Leitura Avançada (C#)

A [Leitura Avançada](https://www.onenote.com/learningtools) é uma ferramenta projetada de forma inclusiva que implementa técnicas comprovadas para melhorar a compreensão da leitura.

Neste Início Rápido, você cria um aplicativo Web do zero e integra a Leitura Avançada usando o SDK de Leitura Avançada. Um exemplo de funcionamento completo deste Início Rápido está disponível [aqui](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Um recurso de Leitura Avançada configurado para autenticação do Azure Active Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para a configuração. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do projeto de exemplo. Salve a saída da sessão em um arquivo de texto para referência futura.

## <a name="create-a-web-app-project"></a>Criar um projeto do aplicativo Web

Crie um projeto no Visual Studio, usando o modelo de aplicativo Web ASP.NET Core com Model-View-Controller interno e o ASP.NET Core 2.1. Dê ao projeto o nome "QuickstartSampleWebApp".

![Novo Projeto](./media/quickstart-csharp/1-createproject.png)

![Configurar novo projeto](./media/quickstart-csharp/2-configureproject.png)

![Novo aplicativo Web ASP.NET Core](./media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Configurar a autenticação

### <a name="configure-authentication-values"></a>Configurar os valores de autenticação

Clique com botão direito do mouse no projeto no _Gerenciador de Soluções_ e escolha **Gerenciar Segredos do Usuário**. Isso abrirá um arquivo chamado _secrets.json_. Não foi feito o check-in do arquivo no controle do código-fonte. Saiba mais [aqui](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows). Substitua o conteúdo de _secrets.json_ pelo seguinte, fornecendo os valores fornecidos quando você criou o recurso de Leitura Avançada.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Adicione o pacote NuGet Microsoft.IdentityModel.Clients.ActiveDirectory

O código a seguir usa objetos do pacote NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**; portanto, você precisará adicionar uma referência a esse pacote no projeto.

Abra o Console do Gerenciador de Pacotes NuGet em **Ferramentas -> Gerenciador de Pacotes NuGet-> Console do Gerenciador de Pacotes** e execute o seguinte comando:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>Atualize o controlador para adquirir o token 

Abra _Controllers\HomeController.cs_ e adicione o código a seguir após as instruções _using_ na parte superior do arquivo.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Agora, configuraremos o controlador para obter os valores do Azure AD de _secrets.json_. Na parte superior da classe _HomeController_, depois de ```public class HomeController : Controller {```, adicione o código a seguir.

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Adicionar o conteúdo de exemplo
Primeiro, abra _Views\Shared\Layout.cshtml_. Antes da linha ```</head>```, adicione o código a seguir:

```html
@RenderSection("Styles", required: false)
```

Agora, vamos adicionar conteúdo de exemplo a este aplicativo Web. Abra _Views\Home\Index.cshtml_ e substitua todo o código gerado automaticamente por este exemplo:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

Observe que todo o texto tem um atributo **lang**, que descreve os idiomas do texto. Esse atributo ajuda a Leitura Avançada a fornecer recursos relevantes de idioma e gramática.

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>Adicionar JavaScript para tratar da inicialização da Leitura Avançada

A biblioteca da Leitura Avançada fornece funcionalidades como iniciar a Leitura Avançada e renderizar botões da Leitura Avançada. Saiba mais [aqui](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference).

Na parte inferior de _Views\Home\Index.cshtml_, adicione o seguinte código:

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Na barra de menus, selecione **Depurar > Iniciar Depuração** ou pressione **F5** para iniciar o aplicativo.

Em seu navegador, você deverá ver:

![Aplicativo de exemplo](./media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Iniciar a Leitura Avançada

Ao clicar no botão "Leitura Avançada", você verá a Leitura Avançada iniciada com o conteúdo na página.

![Leitura Avançada](./media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Próximas etapas

* Confira o [Início rápido do Node.js](./quickstart-nodejs.md) para ver o que mais você pode fazer com o SDK de Leitura Avançada usando Node.js
* Confira o [tutorial do Python](./tutorial-python.md) para ver o que mais você pode fazer com o SDK de Leitura Avançada usando Python
* Confira o [tutorial do iOS](./tutorial-ios-picture-immersive-reader.md) para ver o que mais você pode fazer com o SDK de Leitura Avançada usando Swift
* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](./reference.md)