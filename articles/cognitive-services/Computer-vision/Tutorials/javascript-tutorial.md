---
title: 'Självstudie: API för visuellt innehåll med JavaScript'
titlesuffix: Azure Cognitive Services
description: Utforska en grundläggande JavaScript-app som använder API för visuellt innehåll i Azure Cognitive Services. Utför OCR, skapa miniatyrer och arbeta med visuella funktioner i en bild.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: kefre
ms.openlocfilehash: 6dc6eec729fc1be3f0a859834597bf2d5785d9bc
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984933"
---
# <a name="tutorial-computer-vision-api-javascript"></a>Självstudie: API för visuellt innehåll med JavaScript

Den här självstudien visar funktionerna i Azure Cognitive Services REST API för visuellt innehåll.

Utforska ett JavaScript-program som använder REST API för visuellt innehåll för att utföra optisk teckenigenkänning (OCR), skapa miniatyrbilder med smart beskärning och identifiera, kategorisera, tagga och beskriva visuella funktioner, inklusive ansikten i en bild. Det här exemplet låter dig skicka en bild-URL för analys eller bearbetning. Du kan använda det här exemplet med öppen källkod som en mall för att skapa din egen app i JavaScript och använda REST API för visuellt innehåll.

JavaScript-formulärprogrammet har redan skrivits, men har inga funktioner för visuellt innehåll. I den här självstudien får du lägga till den specifika koden för REST API för visuellt innehåll för att slutföra programmets funktioner.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="platform-requirements"></a>Plattformskrav

Den här självstudien har utvecklats med en enkel textredigerare.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Prenumerera på API för visuellt innehåll och få en prenumerationsnyckel 

Innan du skapar exemplet, måste du prenumerera på API för visuellt innehåll som ingår i Azure Cognitive Services. Information om prenumeration och nyckelhantering finns i [Prenumerationer](https://azure.microsoft.com/try/cognitive-services/). Både de primära och sekundära nycklarna kan användas i den här självstudien. 

## <a name="download-the-tutorial-project"></a>Hämta självstudieprojektet

Klona [Cognitive Services-självstudien för visuellt innehåll med JavaScript](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial), eller hämta ZIP-filen och extrahera den till en tom katalog.

Om du föredrar att använda den färdiga självstudien med alla självstudiekoder tillagda så kan du använda filerna i mappen **Slutförd**.

## <a name="add-the-tutorial-code"></a>Lägg till självstudiekoden

JavaScript-programmet har konfigurerats med sex .html-filer, en för varje funktion. Varje fil visar en annan funktion för visuellt innehåll (analysera OCR, osv). De sex självstudiedelarna har inga beroenden så du kan lägga till självstudiekoden på en fil, alla sex filer eller bara ett par filer. Och du kan lägga till självstudiekoden i filerna i valfri ordning.

Då sätter vi igång.

## <a name="analyze-an-image"></a>Analysera en bild

Funktionen Analysera i API för visuellt innehåll analyserar en bild efter fler än 2 000 identifierbara objekt, levande varelser, landskap och åtgärder. När analysen är klar, returnerar analysera ett JSON-objekt som beskriver bilden med beskrivande taggar, färganalys, beskrivningar och mer.

Utför följande steg för att slutföra analyserafunktionen i självstudieprogrammet:

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>Analysera steg 1: Lägg till händelsehanterarkoden för formulär-knappen

Öppna filen **analyze.html** i en textredigerare och leta upp funktionen **analyzeButtonClick** i slutet av filen.

Händelsehanterarfunktionen **analyzeButtonClick** rensar formuläret, visar den bild som angetts i URL:en och anropar sedan funktionen **AnalyzeImage** för att analysera bilden.

Kopiera och klistra in följande kod till funktionen **analyzeButtonClick**.

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

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>Analysera steg 2: Lägg till adaptern för REST API-anropet

Funktionen **AnalyzeImage** omsluter REST API-anropet för att analysera en bild. Vid en lyckad retur, visas den formaterade JSON-analysen i det angivna textområdet och beskrivningen visas i det angivna intervallet.

Kopiera och klistra in funktionskoden **AnalyzeImage** under funktionen **analyzeButtonClick**.

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

Spara filen **analyze.html** och öppna den i en webbläsare. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Ange en URL för en bild som ska analyseras och klicka sedan på knappen **Analysera bild** för att analysera bilden och se resultatet.

## <a name="recognize-a-landmark"></a>Identifiera ett landmärke

Funktionen landmärken i visuellt innehåll analyserar en bild efter naturliga och konstgjorda landmärken, till exempel berg eller berömda byggnader. När analysen är klar, returnerar Landmärke ett JSON-objekt som identifierar landmärken i bilden.

Utför följande steg för att slutföra funktionen Landmärken i självstudiekursen:

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>Landmärke steg 1: Lägg till händelsehanterarkoden för formulärknappen

Öppna filen **landmark.html** i en textredigerare och leta upp funktionen **landmarkButtonClick** i slutet av filen.

Händelsehanterarfunktionen **landmarkButtonClick** rensar formuläret, visar den bild som angetts i URL-Adressen och anropar sedan **IdentifyLandmarks**-funktionen för att analysera bilden.

Kopiera och klistra in följande kod i funktionen **landmarkButtonClick**.

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

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>Landmärke steg 2: Lägg till adaptern för REST API-anropet

Funktionen **IdentifyLandmarks** omsluter REST API-anropet för att analysera en bild. Vid en lyckad retur, visas den formaterade JSON-analysen i det angivna textområdet och beskrivningen visas i det angivna intervallet.

Kopiera och klistra in funktionskoden **IdentifyLandmarks** under funktionen **landmarkButtonClick**.

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

Spara filen **landmark.html** och öppna den i en webbläsare. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Ange en URL för en bild som ska analyseras och klicka sedan på knappen **Analysera bild** för att analysera bilden och se resultatet.

## <a name="recognize-celebrities"></a>Identifiera kändisar

Funktionen kändisar i visuellt innehåll analyserar en bild efter kända personer. När analysen är klar, returnerar Kändisar ett JSON-objekt som identifierar de kändisar som identifierats i bilden.

Utför följande steg för att slutföra funktionen Kändisar i självstudiekursen:

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>Kändisar steg 1: Lägg till händelsehanterarkoden för formulärknappen

Öppna filen **celebrities.html** i en textredigerare och leta upp funktionen **celebritiesButtonClick** i slutet av filen.

Händelsehanterarfunktionen **celebritiesButtonClick** rensar formuläret, visar den bild som angetts i URL:en och anropar sedan **IdentifyCelebrities**-funktionen för att analysera bilden.

Kopiera och klistra in följande kod i **celebritiesButtonClick**-funktionen.

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

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>Kändisar steg 2: Lägg till adaptern för REST API-anropet

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

### <a name="celebrities-step-3-run-the-application"></a>Kändisar steg 3: kör programmet

Spara filen **celebrities.html** och öppna den i en webbläsare. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Ange en URL för en bild som ska analyseras och klicka sedan på knappen **Analysera bild** för att analysera bilden och se resultatet.

## <a name="intelligently-generate-a-thumbnail"></a>Generera en miniatyrbild

Miniatyrfunktionen i visuellt innehåll skapar en miniatyrbild från en bild. Med hjälp av funktionen **Smart beskärning** så identifierar miniatyrfunktionen intresseområdet i en bild och centrerar miniatyrbilden i det här området att skapa mer estetiskt tilltalande miniatyrbilder.

Utför följande steg för att slutföra miniatyrfunktionen i självstudiekursen:

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>Miniatyr steg 1: Lägg till händelsehanterarkoden för formulärknappen

Öppna filen **thumbnail.html** i en textredigerare och leta upp funktionen **thumbnailButtonClick** i slutet av filen.

Händelsehanterarfunktionen **thumbnailButtonClick** rensar formuläret, visar den bild som angetts i URL:en och anropar sedan **getThumbnail**-funktionen två gånger för att skapa två miniatyrer, en med smart beskärning och en utan.

Kopiera och klistra in följande kod i **thumbnailButtonClick**-funktionen.

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

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>Miniatyr steg 2: Lägg till adaptern för REST API-anropet

Funktionen **getThumbnail** omsluter REST API-anropet för att analysera en bild. Vid en lyckad retur så visas miniatyrbilden i det angivna img-elementet.

Kopiera och klistra in följande **getThumbnail**-funktion under **thumbnailButtonClick**-funktionen.

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

### <a name="thumbnail-step-3-run-the-application"></a>Miniatyrbild steg 3: kör programmet

Spara filen **thumbnail.html** och öppna den i en webbläsare. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Ange en URL för en bild som ska analyseras och klicka sedan på knappen **Skapa miniatyrer** för att analysera bilden och se resultatet.

## <a name="read-printed-text-ocr"></a>Läs tryckt text (OCR)

Optisk teckenigenkänning (OCR)-funktionen för visuellt innehåll analyserar en bild av tryckt text. När analysen är klar, returnerar OCR en JSON-objekt som innehåller texten och placeringen av texten i bilden.

Utför följande steg för att slutföra OCR-funktionen i självstudien:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>OCR steg 1: Lägg till händelsehanterarkoden för formulär-knappen

Öppna filen **ocr.html** i en textredigerare och leta upp funktionen **ocrButtonClick** i slutet av filen.

Händelsehanterarfunktionen **ocrButtonClick** rensar formuläret, visar den bild som angetts i URL:en och anropar sedan funktionen **ReadOcrImage** för att analysera bilden.

Kopiera och klistra in följande kod till funktionen **ocrButtonClick**.

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

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>OCR steg 2: Lägg till adaptern för REST API-anropet

Funktionen **ReadOcrImage** omsluter REST API-anropet för att analysera en bild. Vid en lyckad retur, visas formaterad JSON som beskriver texten och dess plats i det angivna textområdet.

Kopiera och klistra in följande **ReadOcrImage**-funktion under **ocrButtonClick**-funktionen.

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

Spara filen **ocr.html** och öppna den i en webbläsare. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Ange en URL till en textbild för att läsa och klicka sedan på knappen **Läs bild** för att analysera en bild och se resultatet.

## <a name="read-handwritten-text-handwriting-recognition"></a>Läs handskriven text (handskriftsigenkänning)

Funktionen handskriftsigenkänning i API för visuellt innehåll analyserar en bild av handskriven text. När analysen är klar returnerar handskriftsigenkänning ett JSON-objekt som innehåller texten och placeringen av texten i bilden.

Utför följande steg för att slutföra funktionen handskriftsigenkänning i självstudieprogrammet:

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>Handskriftsigenkänning steg 1: Lägg till händelsehanterarkoden för formulärknappen

Öppna filen **handwriting.html** i en textredigerare och leta upp funktionen **handwritingButtonClick** i slutet av filen.

Händelsehanterarfunktionen **handwritingButtonClick** rensar formuläret, visar den avbildning som angetts i URL-Adressen och anropar sedan **HandwritingImage**-funktionen för att analysera avbildningen.

Kopiera och klistra in följande kod i **handwritingButtonClick**-funktionen.

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

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>Handskriftsigenkänning steg 2: Lägg till adaptern för REST API-anropet

Funktionen **ReadHandwrittenImage** omsluter de två REST API-anrop som behövs för att analysera en bild. Eftersom handskriftsigenkänning är en tidskrävande process så används en tvåstegsprocess. Det första anropet skickar bilden för bearbetning. Det andra anropet hämtar den identifierade texten när bearbetningen är klar.

Efter att texten hämtas, visas formaterad JSON som beskriver texten och dess plats i det angivna textområdet.

Kopiera och klistra in följande **ReadHandwrittenImage**-funktion under **handwritingButtonClick**-funktionen.

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

### <a name="handwriting-recognition-step-3-run-the-application"></a>Handskriftsigenkänning steg 3: kör programmet

Spara filen **handwriting.html** och öppna den i en webbläsare. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Ange en URL till en textbild för att läsa och klicka sedan på knappen **Läs bild** för att analysera en bild och se resultatet.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: API för visuellt innehåll med C&#35;](CSharpTutorial.md)
- [Självstudie: API för visuellt innehåll med Python](PythonTutorial.md)
