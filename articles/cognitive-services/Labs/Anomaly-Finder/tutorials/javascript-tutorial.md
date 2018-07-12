---
title: Identifiering av avvikelser Javascript-app – Microsoft Cognitive Services | Microsoft Docs
description: 'Utforska en Javascript-webbapp som använder API: T för Avvikelseidentifiering identifiering i Microsoft Cognitive Services. Skicka ursprungliga datapunkter till API: et och få det förväntade värdet och avvikelseidentifiering punkter.'
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 42c3941a05efe8b74f818cd99f3606b3073892a9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38453177"
---
# <a name="anomaly-detection-javascript-application"></a>Avvikelseidentifiering identifiering av Javascript-programmet

Utforska ett webbprogram som använder den Avvikelseidentifiering REST API: T för att identifiera avvikelser. I exempel skickar time series-data till API: T för Avvikelseidentifiering identifiering med din prenumerationsnyckel och sedan hämtar alla avvikelser punkter och det förväntade värdet för varje datapunkt från API: et.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Den här självstudien har utvecklats med en enkel textredigerare.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på identifiering av avvikelser och få en prenumerationsnyckel 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Hämta och använda exemplet

Den här självstudien innehåller två scenarier för avvikelseidentifiering för time series-data. Nu sätter vi igång.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Ladda ned självstudieprojektet

Klona den [Cognitive Services JavaScript-självstudiekursen för identifiering av avvikelser](https://github.com/MicrosoftAnomalyDetection/javascript-sample), eller hämta ZIP-filen och extrahera det till en tom katalog.

<a name="Step2"></a>
### <a name="run-the-example"></a>Kör exempel

Det finns två scenarier som du kan försöka exemplet.
1. Placera din **prenumerationsnyckel** i fältet Prenumerationsnyckel på identifiera funktionen på anomalydetection.html.
2. Placera avvikelseidentifiering identifiering av API-slutpunkt och kontrollera att du använder rätt region i prenumerationen Region.
3. Öppna den **anomalydetection.html** fil i en webbläsare.

**Scenario 1 identifiera veckovisa time series-data**
1. Period fältet anger perioden **7**. 
2. Ersätta exempeldata med din veckovisa tid datapunkter i serien (Json) i fältet punkter eller använda exempeldata direkt.
3. Klicka på knappen Avvikelseidentifiering och kontrollera resultatet i textrutan för rätt svar.

**Scenario 2 identifiera time series-data utan punkt**
1. Lämna den tom inom perioden arkiverat förutsätter att du inte vet perioden för tidsserier.
2. Använda samma time series-data som scenario 1.
3. Klicka på knappen Avvikelseidentifiering och kontrollera fältet Period i textrutan för rätt svar.

<a name="Step3"></a>
### <a name="read-the-result"></a>Läsa

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Granska och lär dig

Nu kan du få ett program som körs. Nu ska vi se hur exempelappen kan integreras med Cognitive Services-teknik. Det här steget gör det enklare att fortsätta att skapa i den här appen eller utveckla din egen app med hjälp av Microsoft för Avvikelseidentifiering.
Den här exempel-appen kan använda av identifiering av avvikelser Restful API slutpunkt.
Granska hur Restful API: et hämtar används i exempelprogrammet kan vi att titta på ett kodstycke från anomalydetection.html.
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
