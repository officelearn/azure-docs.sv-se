---
title: 'Självstudier: Avvikelseidentifiering med Javascript'
titlesuffix: Azure Cognitive Services
description: Utforska en Javascript-webbapp som använder API:et för avvikelseidentifiering. Skicka ursprungliga datapunkter till API:et och få det förväntade värdet och avvikelsepunkter.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 9e66b24987b2318f3022404d951fbb911e7b592d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709882"
---
# <a name="tutorial-anomaly-detection-with-javascript-application"></a>Självstudier: Avvikelseidentifiering med Javascript-program

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Utforska ett webbprogram som använder REST API:et för avvikelseidentifiering för att identifiera avvikelser. I exemplet skickas tidsseriedata till API:et för avvikelseidentifiering tillsammans med din prenumerationsnyckel, och därefter hämtas alla avvikelsepunkter och det förväntade värdet för varje datapunkt från API:et.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="platform-requirements"></a>Plattformskrav

Den här självstudien har utvecklats med en enkel textredigerare.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på avvikelseidentifiering och få en prenumerationsnyckel 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Hämta och använda exemplet

Den här självstudien innehåller två scenarier för avvikelseidentifiering med tidsseriedata. Nu sätter vi igång!

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Ladda ned projektet för självstudien

Klona [Cognitive Services-självstudien för avvikelseidentifiering med JavaScript](https://github.com/MicrosoftAnomalyDetection/javascript-sample), eller hämta ZIP-filen och extrahera den till en tom katalog.

<a name="Step2"></a>
### <a name="run-the-example"></a>Kör exemplet

Det finns två scenarier där du kan prova exemplet.
1. Ange din **prenumerationsnyckel** i fältet Prenumerationsnyckel i identifieringsfunktionen på anomalydetection.html.
2. Ange API-slutpunkten för avvikelseidentifiering och kontrollera att du använder rätt region i Subscription Region (prenumerationsregion).
3. Öppna filen **anomalydetection.html** i en webbläsare.

**Scenario 1 identifierar veckovisa tidsseriedata**
1. Ange period **7** i fältet Period. 
2. Ersätt exempeldata med dina veckovisa, tidsseriebaserade datapunkter (Json) i fältet Punkter eller använd exempeldata direkt.
3. Klicka på knappen Avvikelseidentifiering och kontrollera resultatet i den högra textrutan för svar.

**Scenario 2 identifierar tidsseriedata utan period**
1. Lämna perioden tom i Period filed (sparad period) och anta att du inte känner till perioden för tidsserien.
2. Använd samma tidsseriedata som i scenario 1.
3. Klicka på knappen Avvikelseidentifiering och kontrollera fältet Period i den högra textrutan för svar.

<a name="Step3"></a>
### <a name="read-the-result"></a>Avläs resultatet

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Granska och lär

Nu får du ett program som körs. Nu ska vi se hur exempelappen kan integreras med Cognitive Services-teknik. Steget gör att du enklare kan bygga vidare på appen eller utveckla en egen app med hjälp av Microsofts Avvikelseidentifiering.
Den här exempel-appen använder Restful API-slutpunkten för avvikelseidentifiering.
För att se hur Restful API:et används i exempelprogrammet kan vi titta på ett kodstycke från anomalydetection.html.
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
