---
title: 'Snabbstart: Skapa en webbapp som startar uppslukande läsaren (C#)'
titlesuffix: Azure Cognitive Services
description: I den här snabbstarten får du skapa en webbapp från grunden och lägga till uppslukande läsare-funktioner.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: a4da8d23e78fde9b936bcf9258eec137bcdf9231
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704144"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Snabbstart: Skapa en webbapp som startar uppslukande läsaren (C#)

Den [uppslukande läsare](https://www.onenote.com/learningtools) är ett portintervallet utformade verktyg som implementerar beprövade metoder för att förbättra läsbarheten.

I den här snabbstarten skapa en webbapp från grunden och integrera uppslukande läsaren med hjälp av uppslukande Reader-SDK. En fullständig fungerande exempel på den här snabbstarten finns [här](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples/quickstart-csharp).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* En prenumerationsnyckel för uppslukande läsare. Skaffa en genom att följa [instruktionerna](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

## <a name="create-a-web-app-project"></a>Skapa en webbapp-projekt

Skapa ett nytt projekt i Visual Studio med hjälp av mallen för ASP.NET Core-Webbapp med inbyggd Model-View-Controller.

![Nytt projekt](./media/vswebapp.png)

![Ny ASP.NET Core-Webbapp](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>Hämta en åtkomsttoken

Du behöver din prenumerationsnyckel och slutpunkt för nästa steg. Du hittar din prenumerationsnyckel på sidan nycklar i din uppslukande Reader-resurs i Azure-portalen. Du kan hitta din slutpunkt på sidan Översikt.

Högerklicka på projektet i den _Solution Explorer_ och välj **hantera Användarhemligheter**. Då öppnas en fil med namnet _secrets.json_. Ersätt innehållet i filen med följande, med din prenumerationsnyckel och slutpunkt där det är lämpligt.

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

Öppna _Controllers\HomeController.cs_, och Ersätt den `HomeController` klassen med följande kod.

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="add-sample-content"></a>Lägg till exempel innehåll

Nu lägger vi till exempel innehåll till webbappen. Öppna _Views\Home\Index.cshtml_ och Ersätt den automatiskt genererade koden med det här exemplet:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
<script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
<script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
    }
</script>
}
```

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Från menyraden väljer **Felsök > Starta felsökning**, eller tryck på **F5** att starta programmet.

Du bör se i webbläsaren:

![Exempelapp](./media/quickstart-result.png)

När du klickar på knappen ”uppslukande läsare” visas uppslukande läsaren startas med innehåll på sidan.

![Avancerad läsare](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Nästa steg

* Visa den [självstudien](./tutorial.md) att se vad du kan göra med uppslukande läsare SDK
* Utforska den [uppslukande läsare SDK](https://github.com/Microsoft/immersive-reader-sdk) och [uppslukande läsare SDK-referens](./reference.md)
