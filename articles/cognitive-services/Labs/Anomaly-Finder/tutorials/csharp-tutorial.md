---
title: 'Självstudie: Avvikelseidentifiering, C#'
titlesuffix: Azure Cognitive Services
description: Utforska en C#-app som använder API:et för avvikelseidentifiering. Skicka ursprungliga datapunkter till API:et och få det förväntade värdet och avvikelsepunkter.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: f99ce765c1d9417fd5ca88b49214eca8a3b0bf49
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887657"
---
# <a name="tutorial-anomaly-detection-with-c-application"></a>Självstudie: Självstudie: Avvikelseidentifiering med C#-program

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Utforska ett grundläggande Windows-program som använder API:et för avvikelseidentifiering för att identifiera avvikelser från indatan. I exemplet skickas tidsseriedata till API:et för avvikelseidentifiering tillsammans med din prenumerationsnyckel, och därefter hämtas alla avvikelsepunkter och det förväntade värdet för varje datapunkt från API:et.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="platform-requirements"></a>Plattformskrav

Exemplet har utvecklats för .NET Framework med hjälp av [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på avvikelseidentifiering och få en prenumerationsnyckel 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Hämta och använd exemplet

Du kan klona exempelprogrammet för avvikelseidentifiering till din dator från [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Installera exemplet

Öppna Sample\AnomalyDetectionSample.sln på ditt GitHub-skrivbord.

<a name="Step2"></a>
### <a name="build-the-example"></a>Skapa exemplet

Tryck på Ctrl + Skift + B, eller klicka på Skapa i menyfliksområdet och välj sedan Skapa lösning.

<a name="Step3"></a>
### <a name="run-the-example"></a>Kör exemplet

1. När exemplet har skapats trycker du på **F5** eller klickar på **Start** i menyfliksområdet för att köra exemplet.
2. Leta reda på användargränssnittsfönstret Avvikelseidentifiering med textredigeringsrutan ”{your_subscription_key}”.
3. Ersätt filen request.json, som innehåller exempeldata, med dina egna data och klicka sedan på Skicka. Microsoft tar emot de data du laddar upp och använder dem för att identifiera eventuella avvikelsepunkter mellan dem. De data som du läser in sparas inte i Microsofts server. För att identifiera avvikelsepunkten igen behöver du ladda upp datan igen.
4. Om datan är bra hittar du resultaten av avvikelseidentifieringen i fältet Svar. Om ett fel inträffar visas även information om felet i fältet Svar.

<a name="Review"></a>
### <a name="read-the-result"></a>Avläs resultatet

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Granska och lär

Nu när programmet körs ska vi se hur exempelappen kan integreras med Cognitive Services-teknik. Steget gör att du enklare kan bygga vidare på appen eller utveckla en egen app med hjälp av Microsofts avvikelseidentifiering.

Den här exempelappen använder Restful API-slutpunkten för avvikelseidentifiering.

För att se hur Restful-API:et används i exempelprogrammet kan vi titta på ett kodavsnitt från **AnomalyDetectionClient.cs**. Filen innehåller kodkommentarer som anger ”KEY SAMPLE CODE STARTS HERE” (PRIMÄR EXEMPELKOD STARTAR HÄR) och ”KEY SAMPLE CODE ENDS HERE” (PRIMÄR EXEMPELKOD SLUTAR HÄR) för att hjälpa dig att hitta de kodavsnitt som återges nedan.

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
### <a name="request"></a>**Förfrågan**
Kodavsnittet nedan visar hur du använder HttpClient för att skicka prenumerationsnyckeln och datapunkter till slutpunkten för API:et för avvikelseidentifiering.

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
