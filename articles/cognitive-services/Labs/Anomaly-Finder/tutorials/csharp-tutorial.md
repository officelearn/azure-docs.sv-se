---
title: Avvikelseidentifiering identifiering av C#-app – Microsoft Cognitive Services | Microsoft Docs
description: 'Utforska en C#-app som använder API: T för Avvikelseidentifiering identifiering i Microsoft Cognitive Services. Skicka ursprungliga datapunkter till API: et och få det förväntade värdet och avvikelseidentifiering punkter.'
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: c6d33de70fd5b7c03b7af6b85e8ebc567dd2f925
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502516"
---
# <a name="anomaly-detection-c-application"></a>Avvikelseidentifiering identifiering av C#-program

Utforska ett grundläggande Windows-program som använder API: T för Avvikelseidentifiering för att identifiera avvikelser från angivna indata. I exempel skickar time series-data till API: T för Avvikelseidentifiering identifiering med din prenumerationsnyckel och sedan hämtar alla avvikelser punkter och förväntat värde för varje datapunkt från API: et.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Exemplet har utvecklats för .NET Framework med hjälp av [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på identifiering av avvikelser och få en prenumerationsnyckel 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Hämta och använda exemplet

Du kan klona Avvikelseidentifiering-exempelprogram till din dator från [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Installera i exempel

Öppna Sample\AnomalyDetectionSample.sln i ditt GitHub-skrivbord.

<a name="Step2"></a>
### <a name="build-the-example"></a>Skapa exemplet

Tryck på Ctrl + Skift + B, eller på Build på menyfliksområdet-menyn och välj sedan skapa lösning.

<a name="Step3"></a>
### <a name="run-the-example"></a>Kör exempel

1. När bygget har slutförts, trycker du på **F5** eller klicka på **starta** på menyn menyfliksområdet för att köra exemplet.
2. Leta upp fönstret Avvikelseidentifiering användaren gränssnitt med textrutan redigera läsa ”{your_subscription_key}”.
3. Ersätt filen request.json, som innehåller exempeldata, med dina egna data och klicka sedan på ”Skicka”. Microsoft tar emot de data du ladda upp och använda dem för att identifiera eventuella avvikelseidentifiering punkter mellan sedan. De data som du läser in sparas inte i Microsofts server. För att identifiera avvikelser punkten igen, behöver du överföra data igen.
4. Om data är bra, märker Identifieringsresultat avvikelseidentifiering i fältet ”svar”. Om något fel inträffar, visas information om felet i fältet svar.

<a name="Review"></a>
### <a name="read-the-result"></a>Läsa

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Granska och lär dig

Nu när du har ett program som körs kan vi ska gå igenom hur exempelappen kan integreras med Cognitive Services-teknik. Det här steget gör det enklare att antingen fortsätta att bygga på den här appen eller utveckla din egen app med hjälp av Microsoft för Avvikelseidentifiering.

Den här exempel-appen kan använda av identifiering av avvikelser Restful API slutpunkt.

Granska hur Restful API: et hämtar används i exempelprogrammet kan vi titta på ett kodstycke från **AnomalyDetectionClient.cs**. Filen innehåller koden kommentarer som du anger ”NYCKELN EXEMPLET kod börjar här” och ”nyckel EXEMPLET kod slutar här” för att hjälpa dig att hitta den kod som kodfragment reproduceras nedan.

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
**Request(...)**  Under kodfragment visar hur du använder HttlClient skicka in din prenumeration nyckeln och data pekar på slutpunkten för API: T för Avvikelseidentifiering identifiering.

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
