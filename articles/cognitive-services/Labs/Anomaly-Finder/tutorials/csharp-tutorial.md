---
title: Avvikelseidentifiering identifiering C# app – kognitiva Microsoft-tjänster | Microsoft Docs
description: 'Utforska en C#-appar som använder API: et för Avvikelseidentifiering identifiering i kognitiva Microsoft-tjänster. Skicka ursprungliga datapunkter-API: et och få ett förväntat värde och avvikelseidentifiering punkter.'
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 2e4100fd7d8e85a6b103c31000176aaaeb3d7151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353361"
---
# <a name="anomaly-detection-c-application"></a>Avvikelseidentifiering identifiering C#-program

Utforska ett grundläggande Windows-program som använder Avvikelseidentifiering identifiering API för att identifiera avvikelser från angivna indata. Exemplet skickar serien tidsdata Avvikelseidentifiering identifiering-API: et med din prenumeration nyckel och hämtar alla avvikelseidentifiering punkter och förväntat värde för varje datapunkt från API: et.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Exemplet har utvecklats för .NET Framework med [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på Avvikelseidentifiering och få en nyckel för prenumeration 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Hämta och använda exempel

Du kan också klona Avvikelseidentifiering exempelprogrammet till din dator från [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Installera exemplet

Öppna Sample\AnomalyDetectionSample.sln i ditt GitHub-skrivbord.

<a name="Step2"></a>
### <a name="build-the-example"></a>Skapa exemplet

Tryck på Ctrl + Skift + B, eller klicka på Skapa på menyn menyfliksområdet och välj sedan skapa lösning.

<a name="Step3"></a>
### <a name="run-the-example"></a>Kör exemplet

1. När versionen är klar trycker du på **F5** eller klicka på **starta** på menyn menyfliksområdet om du vill köra exemplet.
2. Leta upp fönstret Avvikelseidentifiering användaren gränssnitt med redigera textrutan läsning ”{your_subscription_key}”.
3. Ersätt filen request.json, som innehåller exempeldata med dina egna data och klicka sedan på ”Skicka”. Microsoft tar emot data du överför och använda dem för att identifiera eventuella avvikelseidentifiering punkter mellan sedan. Du uppdaterade data sparas inte i Microsofts server. För att identifiera punkten avvikelseidentifiering igen, måste du överföra data igen.
4. Om data är bra, hittar du avvikelseidentifiering identifiering resultatet i fältet ”svar”. Om något fel inträffar, visas information om felet i fältet svar.

<a name="Review"></a>
### <a name="read-the-result"></a>Läsa resultatet

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Granska och lära dig

Nu när du har ett program som körs nu ska vi se hur appen exempel integreras med kognitiva Services-teknik. Det här steget gör det enklare att fortsätta skapa på den här appen eller utveckla egna app med Microsoft Avvikelseidentifiering.

Det här exemplet app väljer Avvikelseidentifiering identifiering Restful-API slutpunkt.

Granska hur Restful-API används i exempelprogrammet kan vi titta på ett kodstycke från **AnomalyDetectionClient.cs**. Filen innehåller koden kommentarer som anger ”nyckel exempel kod startar här” och ”nyckel exempel kod slutar här” för att hitta koden kodavsnitt reproduceras nedan.

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
**Request(...)**  Under utdrag visar hur du använder HttlClient skicka din prenumeration nyckeln och data pekar på slutpunkten för API: et för Avvikelseidentifiering identifiering.

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
