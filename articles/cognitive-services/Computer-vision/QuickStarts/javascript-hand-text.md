---
title: 'Snabb start: Visuellt innehåll 2,0 och 2,1 – extrahera skriven text – REST, Java Script'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten extraherar du utskriven och handskriven text från en bild med hjälp av API för visuellt innehåll med Java Script.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 907b3ead8e39268bc73604599bd2c37b18ddfa21
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83676103"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-rest-api-and-javascript"></a>Snabb start: extrahera utskrift och handskriven text med hjälp av Visuellt innehåll REST API och Java Script

I den här snabb starten ska du extrahera tryckt och/eller handskriven text från en bild med hjälp av Visuellt innehåll REST API. Med resultat metoderna [batch Läs](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) och [Läs åtgärd](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) kan du identifiera text i en bild och extrahera identifierade tecken i en maskin läsnings bar tecken ström. API: et avgör vilken igenkännings modell som ska användas för varje textrad, så den stöder bilder med både utskrift och handskriven text.

Den här funktionen är tillgänglig i både ett v 2.1-API och en v 3.0-API för offentlig för hands version. Jämfört med v 2.1 har 3,0 API:

* Förbättrad precision
* Förtroende poäng för ord
* Stöd för både spanska och engelska med ytterligare `language` parameter
* Ett annat utdataformat

Välj fliken nedan för den version som du använder.

#### <a name="version-2"></a>[Version 2](#tab/version-2)

> [!IMPORTANT]
> Läs metoden för [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) körs asynkront. Den här metoden returnerar inte någon information i en svarsbrödtext. I stället returnerar batch-metoden en URI i värdet för `Operation-Location` fältet svars huvud. Du kan sedan anropa denna URI, som representerar API för [Läs åtgärds resultat](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) , för att både kontrol lera statusen och returnera resultatet från anropet av Läs metoden för batch.

#### <a name="version-3-public-preview"></a>[Version 3 (offentlig för hands version)](#tab/version-3)

> [!IMPORTANT]
> Läs metoden för [batch](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d986960601faab4bf452005) körs asynkront. Den här metoden returnerar inte någon information i en svarsbrödtext. I stället returnerar batch-metoden en URI i värdet för `Operation-Location` fältet svars huvud. Du kan sedan anropa denna URI, som representerar API för [Läs åtgärds resultat](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d9869604be85dee480c8750) , för att både kontrol lera statusen och returnera resultatet från anropet av Läs metoden för batch.

---

## <a name="prerequisites"></a>Krav

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnads fri utvärderings nyckel från [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på visuellt innehåll och hämta din nyckel. Spara prenumerations nyckeln och slut punkts-URL: en på en tillfällig plats.

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

#### <a name="version-2"></a>[Version 2](#tab/version-2)

Så här skapar du och kör exemplet:

1. Skapa en fil med namnet _Get-text. html_, öppna den i en text redigerare och kopiera följande kod till den.
1. Du kan också ersätta värdet på `value` attributet för `inputImage` kontrollen med URL: en till en annan bild som du vill extrahera text från.
1. Öppna ett webbläsarfönster.
1. Dra och släpp filen till webbläsarfönstret i webbläsaren.
1. När webb sidan visas i webbläsaren klistrar du in din prenumerations nyckel och slut punkts-URL i lämpliga inmatade rutor.
1. Välj knappen **Läs avbildning** .

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // Fetch your Computer Vision key and endpoint for this sample.
        var subscriptionKey = document.getElementById("subscriptionKey").value;
        var endpoint = document.getElementById("endpointUrl").value;
        
        var uriBase = endpoint + "vision/v2.1/read/core/asyncBatchAnalyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase,

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Subscription key: 
<input type="text" name="subscriptionKey" id="subscriptionKey"
    value="" /> 
Endpoint URL:
<input type="text" name="endpointUrl" id="endpointUrl"
    value="" />
<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

#### <a name="version-3-public-preview"></a>[Version 3 (offentlig för hands version)](#tab/version-3)

Så här skapar du och kör exemplet:

1. Kopiera följande kod till en textredigerare.
1. Du kan också ersätta värdet på `value` attributet för `inputImage` kontrollen med URL: en till en annan bild som du vill extrahera text från.
1. Spara koden som en fil med tillägget `.html`. Till exempel `get-text.html`.
1. Öppna ett webbläsarfönster.
1. När webb sidan visas i webbläsaren fyller du i de nödvändiga parametrarna och klickar på knappen **Läs avbildning** .

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        let subscriptionKey = document.getElementById("key").value;
        let endpoint = document.getElementById("endpoint").value;
        if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }
        let language = document.getElementById("language").value;
        
        var uriBase = endpoint + "/vision/v3.0-preview/read/analyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        const params = {
            language: language,
        };

        const searchParams = new URLSearchParams(params);

        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase + "?" + searchParams.toString(),

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: JSON.stringify({url: sourceImageUrl}),
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Endpoint: 
<input type="text" name="endpoint" id="endpoint" value="" style="width: 300px;"/>
<div style="margin: 20px;">Example: https://westus2.api.cognitive.microsoft.com</div>
Subscription Key:    
<input type="text" name="key" id="key" value="" style="width: 300px;"/>
<br><br>
Language: 
<input type="text" name="language" id="language"
    value="en" />
<div style="margin: 20px;">Accepted values are &quot;en&quot; and &quot;es&quot;</div>

<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

---

## <a name="examine-the-response"></a>Granska svaret

Ett svar som anger att åtgärden lyckades returneras i JSON. Exempelwebbsidan tolkar och visar ett lyckat svar i webbläsarfönstret liknar följande exempel:

#### <a name="version-2"></a>[Version 2](#tab/version-2)

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

#### <a name="version-3-public-preview"></a>[Version 3 (offentlig för hands version)](#tab/version-3)

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-02-11T20:56:33Z",
  "lastUpdatedDateTime": "2020-02-11T20:56:34Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8206,
        "width": 800,
        "height": 154,
        "unit": "pixel",
        "lines": [
          {
            "language": "en",
            "boundingBox": [
              6,
              4,
              774,
              14,
              773,
              61,
              5,
              49
            ],
            "text": "The quick brown fox jumps over the lazy",
            "words": [
              {
                "boundingBox": [
                  14,
                  5,
                  76,
                  6,
                  74,
                  49,
                  12,
                  48
                ],
                "text": "The",
                "confidence": 0.83
              },
              {
                "boundingBox": [
                  84,
                  6,
                  182,
                  7,
                  180,
                  51,
                  82,
                  49
                ],
                "text": "quick",
                "confidence": 0.762
              },
              {
                "boundingBox": [
                  191,
                  7,
                  312,
                  9,
                  309,
                  54,
                  189,
                  51
                ],
                "text": "brown",
                "confidence": 0.67
              },
              {
                "boundingBox": [
                  320,
                  9,
                  382,
                  10,
                  379,
                  55,
                  317,
                  54
                ],
                "text": "fox",
                "confidence": 0.849
              },
              {
                "boundingBox": [
                  390,
                  10,
                  497,
                  11,
                  493,
                  57,
                  387,
                  55
                ],
                "text": "jumps",
                "confidence": 0.703
              },
              {
                "boundingBox": [
                  506,
                  11,
                  596,
                  12,
                  591,
                  59,
                  502,
                  57
                ],
                "text": "over",
                "confidence": 0.799
              },
              {
                "boundingBox": [
                  604,
                  12,
                  666,
                  13,
                  661,
                  60,
                  600,
                  59
                ],
                "text": "the",
                "confidence": 0.923
              },
              {
                "boundingBox": [
                  674,
                  13,
                  773,
                  14,
                  768,
                  62,
                  670,
                  60
                ],
                "text": "lazy",
                "confidence": 0.863
              }
            ]
          },
          {
            "language": "en",
            "boundingBox": [
              5,
              53,
              79,
              56,
              77,
              95,
              4,
              92
            ],
            "text": "dog",
            "words": [
              {
                "boundingBox": [
                  6,
                  53,
                  74,
                  56,
                  72,
                  95,
                  5,
                  92
                ],
                "text": "dog",
                "confidence": 0.418
              }
            ]
          },
          {
            "language": "en",
            "boundingBox": [
              0,
              90,
              787,
              95,
              787,
              145,
              0,
              136
            ],
            "text": "Pack my box with five dozen liquor jugs",
            "words": [
              {
                "boundingBox": [
                  1,
                  96,
                  79,
                  93,
                  79,
                  135,
                  0,
                  136
                ],
                "text": "Pack",
                "confidence": 0.835
              },
              {
                "boundingBox": [
                  87,
                  93,
                  151,
                  92,
                  151,
                  135,
                  87,
                  135
                ],
                "text": "my",
                "confidence": 0.88
              },
              {
                "boundingBox": [
                  162,
                  92,
                  226,
                  91,
                  225,
                  135,
                  161,
                  135
                ],
                "text": "box",
                "confidence": 0.301
              },
              {
                "boundingBox": [
                  234,
                  91,
                  335,
                  90,
                  335,
                  135,
                  233,
                  135
                ],
                "text": "with",
                "confidence": 0.959
              },
              {
                "boundingBox": [
                  346,
                  91,
                  418,
                  91,
                  417,
                  136,
                  345,
                  135
                ],
                "text": "five",
                "confidence": 0.489
              },
              {
                "boundingBox": [
                  426,
                  91,
                  527,
                  93,
                  527,
                  138,
                  425,
                  136
                ],
                "text": "dozen",
                "confidence": 0.727
              },
              {
                "boundingBox": [
                  554,
                  94,
                  687,
                  98,
                  687,
                  143,
                  553,
                  139
                ],
                "text": "liquor",
                "confidence": 0.377
              },
              {
                "boundingBox": [
                  701,
                  99,
                  787,
                  103,
                  787,
                  146,
                  700,
                  143
                ],
                "text": "jugs",
                "confidence": 0.693
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

## <a name="next-steps"></a>Nästa steg

Utforska ett JavaScript-program som använder Visuellt innehåll för att utföra optisk tecken igenkänning (OCR). Skapa Smart-beskurna miniatyrer; identifiera, kategorisera, tagga och beskriv visuella funktioner i bilder. 

> [!div class="nextstepaction"]
> [Självstudie: API för visuellt innehåll med JavaScript](../Tutorials/javascript-tutorial.md)

* Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
