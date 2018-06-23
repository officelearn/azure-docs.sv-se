---
title: Avvikelseidentifiering Javascript-app - kognitiva Microsoft-tjänster | Microsoft Docs
description: 'Utforska en Javascript-webbapp som använder API: et för Avvikelseidentifiering identifiering i kognitiva Microsoft-tjänster. Skicka ursprungliga datapunkter-API: et och få ett förväntat värde och avvikelseidentifiering punkter.'
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 42c3941a05efe8b74f818cd99f3606b3073892a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353337"
---
# <a name="anomaly-detection-javascript-application"></a>Avvikelseidentifiering identifiering Javascript-programmet

Utforska ett webbprogram som använder Avvikelseidentifiering identifiering REST API för att upptäcka avvikelser. Exemplet skickar serien tidsdata Avvikelseidentifiering identifiering-API: et med din prenumeration nyckel och hämtar alla avvikelseidentifiering punkter och det förväntade värdet för varje datapunkt från API: et.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Den här självstudiekursen har utvecklats med en enkel textredigerare.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på Avvikelseidentifiering och få en nyckel för prenumeration 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Hämta och använda exempel

Den här självstudiekursen innehåller två scenarier för identifiering av avvikelse tid serie data. Nu sätter vi igång.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Hämta projektet självstudiekursen

Klona den [kognitiva tjänster JavaScript-vägledning för identifiering av Avvikelseidentifiering](https://github.com/MicrosoftAnomalyDetection/javascript-sample), eller hämta ZIP-filen och extrahera till en tom katalog.

<a name="Step2"></a>
### <a name="run-the-example"></a>Kör exemplet

Det finns två scenarier som du kan försöka exemplet.
1. Placera din **prenumeration nyckeln** i nyckelfältet prenumeration på identifiera funktionen på anomalydetection.html.
2. Placera avvikelseidentifiering identifiering API-slutpunkt och kontrollera att du använder rätt region i prenumerationen Region.
3. Öppna den **anomalydetection.html** fil i en webbläsare.

**Scenario 1 identifiera varje vecka tid series-data**
1. I Period fältet indata period **7**. 
2. Ersätta exempeldata med din veckovisa tid seriens datapunkter (Json) i fältet punkter eller använda exempeldata direkt.
3. Klicka på knappen Avvikelseidentifiering och kontrollera resultatet i textrutan rätt svar.

**Scenario 2 identifiera tid series-data utan punkt**
1. Lämna tomt i Period perioden arkiverats förutsätter att du inte vet period i tidsserien.
2. Med samma tid seriedata som scenario 1.
3. Klicka på knappen Avvikelseidentifiering och kontrollera fältet Period i textrutan rätt svar.

<a name="Step3"></a>
### <a name="read-the-result"></a>Läsa resultatet

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Granska och lära dig

Nu kan du få ett program som körs. Nu ska vi se hur appen exempel integreras med kognitiva Services-teknik. Det här steget gör det enklare att fortsätta bygger på den här appen eller utveckla egna app med Microsoft Avvikelseidentifiering.
Det här exemplet app väljer Avvikelseidentifiering identifiering Restful-API slutpunkt.
Granska hur Restful-API används i exempelprogrammet kan vi att titta på ett kodstycke från anomalydetection.html.
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
