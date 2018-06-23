---
title: Datorn Vision API JavaScript kursen | Microsoft Docs
description: Utforska en grundläggande JavaScript-app som använder datorn Vision API i kognitiva Microsoft-tjänster. Utföra OCR, skapa miniatyrbilder och arbeta med visuella funktioner i en bild.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 09/19/2017
ms.author: kefre
ms.openlocfilehash: 89bdc0524e07c1cb6a1473e0a52791fe20271e06
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351912"
---
# <a name="computer-vision-api-javascript-tutorial"></a>Datorn Vision API JavaScript självstudiekursen

Den här kursen visar funktionerna i Microsoft kognitiva tjänster datorn Vision REST API.

Utforska ett JavaScript-program som använder datorn Vision REST API för att utföra OCR (OCR), skapa smart beskäras miniatyrer plus identifiera, kategorisera taggen och beskriver visual funktioner, inklusive ansikten, i en bild. Det här exemplet kan du skicka en bild-URL för analys eller bearbetning. Du kan använda det här exemplet med öppen källkod som en mall för att skapa din egen app i JavaScript för att använda datorn Vision REST API.

JavaScript formuläret programmet har redan skrivits, men datorn Vision fungerar inte. I kursen får du lägger till kod specifika dator Vision REST API för att slutföra programmets funktioner.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Den här självstudiekursen har utvecklats med en enkel textredigerare.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Prenumerera på datorn Vision API och få en nyckel för prenumeration 

Innan du skapar exemplet måste du prenumerera på datorn Vision API som ingår i kognitiva Microsoft-tjänster. Prenumerationen och information om nyckelhantering finns [prenumerationer](https://azure.microsoft.com/try/cognitive-services/). Både de primära och sekundära nycklarna är giltiga i den här kursen. 

## <a name="download-the-tutorial-project"></a>Hämta projektet självstudiekursen

Klona den [kognitiva Services JavaScript datorn Vision kursen](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial), eller hämta ZIP-filen och extrahera till en tom katalog.

Om du föredrar att använda färdiga kursen med alla självstudiekursen koden som lagts till kan du använda filerna i den **slutförd** mapp.

## <a name="add-the-tutorial-code"></a>Lägg till självstudiekursen kod

JavaScript-programmet har konfigurerats med sex .html-filer, en för varje funktion. Visar en annan funktion i datorn Vision för varje fil (analysera OCR osv). Sex självstudiekursen avsnitt har inte beroenden, så du kan lägga till självstudiekursen koden till en fil, alla sex filer eller ett par av filer. Och du kan lägga till självstudiekursen koden till filer i valfri ordning.

Nu sätter vi igång.

## <a name="analyze-an-image"></a>Analysera en bild

Funktionen Analysera i datorn Vision analyserar en bild för fler än 2 000 okänt objekt, levande människor, landskap och åtgärder. När analysen är klar, returnerar analysera ett JSON-objekt som beskriver avbildningen med beskrivande taggar, färg analys, beskrivningar och mycket mer.

Utför följande steg för att slutföra funktionen Analysera i självstudiekursen programmet:

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>Analysera steg 1: Lägg till händelsekod för hanteraren för knappen formulär

Öppna den **analyze.html** filen i en textredigerare och leta upp den **analyzeButtonClick** funktionen längst ned i filen.

Den **analyzeButtonClick** händelsehanterare rensar formuläret, visar bilden som anges i URL-Adressen, anropar sedan den **AnalyzeImage** funktion för att analysera avbildningen.

Kopiera och klistra in följande kod i den **analyzeButtonClick** funktion.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>Analysera steg 2: Lägg till Omslutning för REST API-anrop

Den **AnalyzeImage** funktionen radbryts REST API-anropet för att analysera en bild. Vid en lyckad RETUR formaterad JSON analysen ska visas i den angivna textområde och etiketten ska visas i det angivna intervallet.

Kopiera och klistra in den **AnalyzeImage** fungerar kod precis under den **analyzeButtonClick** funktion.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="analyze-step-3-run-the-application"></a>Analysera steg 3: kör programmet

Spara den **analyze.html** filen och öppna den i en webbläsare. Placera din prenumeration nyckel till den **prenumeration nyckeln** fältet och kontrollera att du använder rätt region i **prenumeration Region**. Ange en URL till en bild för att analysera och klicka sedan på den **analysera avbildningen** för att analysera en avbildning och visa resultatet.

## <a name="recognize-a-landmark"></a>Identifiera en Landmärke

Funktionen Landmärke i datorn Vision analyserar en bild för naturlig och artificiell landmärken, till exempel Berg eller berömda byggnader. När analysen är klar, returnerar Landmärke ett JSON-objekt som identifierar landmärken hittades i avbildningen.

Utför följande steg för att slutföra funktionen Landmärke i självstudiekursen programmet:

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>Landmärke steg 1: Lägg till händelsekod för hanteraren för knappen formulär

Öppna den **landmark.html** filen i en textredigerare och leta upp den **landmarkButtonClick** funktionen längst ned i filen.

Den **landmarkButtonClick** händelsehanterare rensar formuläret, visar bilden som anges i URL-Adressen, anropar sedan den **IdentifyLandmarks** funktion för att analysera avbildningen.

Kopiera och klistra in följande kod i den **landmarkButtonClick** funktion.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>Landmärke steg 2: Lägg till Omslutning för REST API-anrop

Den **IdentifyLandmarks** funktionen radbryts REST API-anropet för att analysera en bild. Vid en lyckad RETUR formaterad JSON analysen ska visas i den angivna textområde och etiketten ska visas i det angivna intervallet.

Kopiera och klistra in den **IdentifyLandmarks** fungerar kod precis under den **landmarkButtonClick** funktion.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="landmark-step-3-run-the-application"></a>Landmärke steg 3: kör programmet

Spara den **landmark.html** filen och öppna den i en webbläsare. Placera din prenumeration nyckel till den **prenumeration nyckeln** fältet och kontrollera att du använder rätt region i **prenumeration Region**. Ange en URL till en bild för att analysera och klicka sedan på den **analysera avbildningen** för att analysera en avbildning och visa resultatet.

## <a name="recognize-celebrities"></a>Identifiera kändisar

Funktionen Kändisars i datorn Vision analyserar en avbildning för berömda personer. När analysen är klar, returnerar Kändisars ett JSON-objekt som identifierar Kändisars hittades i avbildningen.

Utför följande steg för att slutföra funktionen Kändisars i självstudiekursen programmet:

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>Kändisars steg 1: Lägg till händelsekod för hanteraren för knappen formulär

Öppna den **celebrities.html** filen i en textredigerare och leta upp den **celebritiesButtonClick** funktionen längst ned i filen.

Den **celebritiesButtonClick** händelsehanterare rensar formuläret, visar bilden som anges i URL-Adressen, anropar sedan den **IdentifyCelebrities** funktion för att analysera avbildningen.

Kopiera och klistra in följande kod i den **celebritiesButtonClick** funktion.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>Kändisars steg 2: Lägg till Omslutning för REST API-anrop

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="celebrities-step-3-run-the-application"></a>Kändisars steg 3: kör programmet

Spara den **celebrities.html** filen och öppna den i en webbläsare. Placera din prenumeration nyckel till den **prenumeration nyckeln** fältet och kontrollera att du använder rätt region i **prenumeration Region**. Ange en URL till en bild för att analysera och klicka sedan på den **analysera avbildningen** för att analysera en avbildning och visa resultatet.

## <a name="intelligently-generate-a-thumbnail"></a>Intelligent Generera en miniatyrbild

Funktionen miniatyren i datorn Vision genererar en miniatyrbild från en avbildning. Med hjälp av den **Smart Beskär** funktionen funktionen miniatyr identifierar intresseområde i en avbildning och center miniatyren i det här området för att skapa fler aesthetically tilltalande miniatyrbilder.

Utför följande steg för att slutföra funktionen miniatyren i självstudiekursen programmet:

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>Miniatyrer steg 1: Lägg till händelsekod för hanteraren för knappen formulär

Öppna den **thumbnail.html** filen i en textredigerare och leta upp den **thumbnailButtonClick** funktionen längst ned i filen.

Den **thumbnailButtonClick** händelsehanterare rensar formuläret, visar bilden som anges i URL-Adressen, anropar sedan den **getThumbnail** funktionen två gånger för att skapa två miniatyrer, en smart beskäras och en utan smart Beskär.

Kopiera och klistra in följande kod i den **thumbnailButtonClick** funktion.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>Miniatyrer steg 2: Lägg till Omslutning för REST API-anrop

Den **getThumbnail** funktionen radbryts REST API-anropet för att analysera en bild. Vid en lyckad RETUR visas miniatyren i det angivna img-elementet.

Kopiera och klistra in följande **getThumbnail** funktion att de bara under den **thumbnailButtonClick** funktion.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

### <a name="thumbnail-step-3-run-the-application"></a>Miniatyrer steg 3: kör programmet

Spara den **thumbnail.html** filen och öppna den i en webbläsare. Placera din prenumeration nyckel till den **prenumeration nyckeln** fältet och kontrollera att du använder rätt region i **prenumeration Region**. Ange en URL till en bild för att analysera och klicka sedan på den **generera miniatyrbilder** för att analysera en avbildning och visa resultatet.

## <a name="read-printed-text-ocr"></a>Läs ut text (OCR)

Funktionen OCR (OCR) i datorn Vision analyserar en avbildning av tryckt text. När analysen är klar returnerar OCR ett JSON-objekt som innehåller texten och platsen för texten i bilden.

Utför följande steg för att slutföra funktionen OCR i självstudiekursen programmet:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>OCR steg 1: Lägg till händelsekod för hanteraren för knappen formulär

Öppna den **ocr.html** filen i en textredigerare och leta upp den **ocrButtonClick** funktionen längst ned i filen.

Den **ocrButtonClick** händelsehanterare rensar formuläret, visar bilden som anges i URL-Adressen, anropar sedan den **ReadOcrImage** funktion för att analysera avbildningen.

Kopiera och klistra in följande kod i den **ocrButtonClick** funktion.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>OCR steg 2: Lägg till Omslutning för REST API-anrop

Den **ReadOcrImage** funktionen radbryts REST API-anropet för att analysera en bild. Returnerar formaterad JSON som beskriver texten vid en lyckad och platsen för texten som ska visas i den angivna textområde.

Kopiera och klistra in följande **ReadOcrImage** funktion att de bara under den **ocrButtonClick** funktion.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

### <a name="ocr-step-3-run-the-application"></a>OCR steg 3: kör programmet

Spara den **ocr.html** filen och öppna den i en webbläsare. Placera din prenumeration nyckel till den **prenumeration nyckeln** fältet och kontrollera att du använder rätt region i **prenumeration Region**. Ange en URL till en bild för att läsa och klicka sedan på den **Läs avbildningen** för att analysera en avbildning och visa resultatet.

## <a name="read-handwritten-text-handwriting-recognition"></a>Läsa handskriven text (handskriftsigenkänning)

Funktionen handskriftsigenkänning i datorn Vision analyserar en avbildning av handskriven text. När analysen är klar returnerar handskriftsigenkänning ett JSON-objekt som innehåller texten och platsen för texten i bilden.

Utför följande steg för att slutföra funktionen handskriftsigenkänning i självstudiekursen programmet:

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>Handskriftsigenkänning Recognition steg 1: Lägg till händelsekod för hanteraren för knappen formulär

Öppna den **handwriting.html** filen i en textredigerare och leta upp den **handwritingButtonClick** funktionen längst ned i filen.

Den **handwritingButtonClick** händelsehanterare rensar formuläret, visar bilden som anges i URL-Adressen, anropar sedan den **HandwritingImage** funktion för att analysera avbildningen.

Kopiera och klistra in följande kod i den **handwritingButtonClick** funktion.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>Handskriftsigenkänning Recognition steg 2: Lägg till Omslutning för REST API-anrop

Den **ReadHandwrittenImage** funktionen radbryts två REST API-anrop som behövs för att analysera en bild. Eftersom handskriftsigenkänning är en tidskrävande process, används en process i två steg. Det första anropet skickar bilden för bearbetning. Det andra anropet hämtar identifierade texten när bearbetningen är klar.

Efter texten som har hämtats ska formaterad JSON som beskriver texten och platsen för texten som visas i den angivna textområde.

Kopiera och klistra in följande **ReadHandwrittenImage** funktion att de bara under den **handwritingButtonClick** funktion.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requrires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

### <a name="handwriting-recognition-step-3-run-the-application"></a>Handskriftsigenkänning Recognition steg 3: kör programmet

Spara den **handwriting.html** filen och öppna den i en webbläsare. Placera din prenumeration nyckel till den **prenumeration nyckeln** fältet och kontrollera att du använder rätt region i **prenumeration Region**. Ange en URL till en bild för att läsa och klicka sedan på den **Läs avbildningen** för att analysera en avbildning och visa resultatet.

## <a name="next-steps"></a>Nästa steg

- [Datorn Vision API C&#35; självstudiekursen](CSharpTutorial.md)
- [Datorn Vision API Python självstudiekursen](PythonTutorial.md)
