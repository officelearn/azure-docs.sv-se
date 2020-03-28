---
title: 'Snabbstart: Skapa en webbapp som startar Immersive Reader med C #'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten skapar du en webbapp från grunden och lägger till Immersive Reader API-funktionen.
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
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845245"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Snabbstart: Skapa en webbapp som startar Immersive Reader (C#)

[Den uppslukande läsaren](https://www.onenote.com/learningtools) är ett inkluderande utformat verktyg som implementerar beprövade tekniker för att förbättra läsförståelsen.

I den här snabbstarten skapar du en webbapp från grunden och integrerar Immersive Reader med hjälp av Immersive Reader SDK. Ett fullständigt fungerande exempel på den här snabbstarten finns [här](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* En Immersive Reader-resurs som konfigurerats för Azure Active Directory-autentisering. Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att komma igång. Du behöver några av de värden som skapas här när du konfigurerar exempelprojektegenskaperna. Spara utdata från sessionen i en textfil för framtida referens.

## <a name="create-a-web-app-project"></a>Skapa ett webbappprojekt

Skapa ett nytt projekt i Visual Studio med mallen ASP.NET Core Web Application med inbyggd Model-View-Controller och ASP.NET Core 2.1. Namnge projektet "QuickstartSampleWebApp".

![Nytt projekt](./media/quickstart-csharp/1-createproject.png)

![Konfigurera nytt projekt](./media/quickstart-csharp/2-configureproject.png)

![Nytt ASP.NET core-webbprogram](./media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Konfigurera autentisering

### <a name="configure-authentication-values"></a>Konfigurera autentiseringsvärden

Högerklicka på projektet i _Lösningsutforskaren_ och välj **Hantera användarhemligheter**. Detta kommer att öppna en fil som heter _secrets.json_. Den här filen är inte incheckad i källkontrollen. Läs mer [här](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows). Ersätt innehållet i _secrets.json_ med följande och ange de värden som angavs när du skapade Immersive Reader-resursen.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Lägg till paketet Microsoft.IdentityModel.Clients.ActiveDirectory NuGet

Följande kod använder objekt från **paketet Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet, så du måste lägga till en referens till paketet i projektet.

Öppna NuGet Package Manager Console från **Verktyg -> NuGet Package Manager -> Package Manager Console** och kör följande kommando:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>Uppdatera styrenheten för att hämta token 

Öppna _Controllers\HomeController.cs_och lägg till följande kod efter _att du har använt-satser_ högst upp i filen.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Nu konfigurerar vi styrenheten för att hämta Azure AD-värdena från _secrets.json_. Lägg till följande kod högst upp ```public class HomeController : Controller {```i klassen _HomeController._

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

## <a name="add-sample-content"></a>Lägga till exempelinnehåll
Öppna först _Vyer\Delad\Layout.cshtml_. Lägg till ```</head>```följande kod före raden:

```html
@RenderSection("Styles", required: false)
```

Nu lägger vi till exempelinnehåll i den här webbappen. Öppna _vyer\Home\Index.cshtml_ och ersätt all automatiskt genererad kod med det här exemplet:

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

Observera att all text **lang** har ett lang-attribut, som beskriver textens språk. Det här attributet hjälper Immersive Reader att tillhandahålla relevanta språk- och grammatikfunktioner.

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>Lägg till JavaScript för att hantera lanseringen av Immersive Reader

Immersive Reader-biblioteket innehåller funktioner som att starta immersive Reader och återge Immersive Reader-knappar. Läs mer [här](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference).

Lägg till följande kod längst ned i _Views\Home\Index.cshtml:_

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

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Välj Felsökning > **Starta felsökning på**menyraden eller tryck på **F5** för att starta programmet.

I din webbläsare bör du se:

![Exempelapp](./media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Starta den uppslukande läsaren

När du klickar på knappen "Uppslukande läsare" ser du Immersive Reader lanseras med innehållet på sidan.

![Avancerad läsare](./media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Nästa steg

* Visa [snabbstarten Node.js](./quickstart-nodejs.md) för att se vad mer du kan göra med Immersive Reader SDK med Node.js
* Visa [Python-självstudien](./tutorial-python.md) för att se vad mer du kan göra med Immersive Reader SDK med Python
* Visa [iOS-självstudien](./tutorial-ios-picture-immersive-reader.md) för att se vad mer du kan göra med Immersive Reader SDK med Swift
* Utforska [den uppslukande läsaren SDK](https://github.com/microsoft/immersive-reader-sdk) och [Immersive Reader SDK-referensen](./reference.md)