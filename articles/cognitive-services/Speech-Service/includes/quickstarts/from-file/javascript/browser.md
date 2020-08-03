---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 34e83fc04942d465f27a5f6f7e1f0ebda46c181b
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406339"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure tal-resurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md)

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod

Nu ska vi lägga till kod som fungerar som en Skeleton för vårt projekt.

```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Microsoft Cognitive Services Speech SDK JavaScript Quickstart</title>
    <meta charset="utf-8" />
    </head>
    <body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
    </body>
    </html>
```
## <a name="add-ui-elements"></a>Lägg till GRÄNSSNITTs element

Nu ska vi lägga till några grundläggande användar gränssnitt för inmatade rutor, referera till Java Script för tal-SDK och ta tag i en autentiseringstoken om det är tillgängligt.

```html  
  <div id="content" style="display:none">
    <table width="100%">
      <tr>
        <td></td>
        <td><h1 style="font-weight:500;">Microsoft Cognitive Services Speech SDK JavaScript Quickstart</h1></td>
      </tr>
      <tr>
        <td align="right"><a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started" target="_blank">Subscription</a>:</td>
        <td><input id="subscriptionKey" type="text" size="40" value="subscription"></td>
      </tr>
      <tr>
        <td align="right">Region</td>
        <td><input id="serviceRegion" type="text" size="40" value="YourServiceRegion"></td>
      </tr>
      <tr>
        <td align="right">File</td>
        <td><input type="file" id="filePicker" accept=".wav" style="display:none" /></td>
      </tr>
      <tr>
        <td></td>
        <td><button id="startRecognizeOnceAsyncButton">Start recognition</button></td>
      </tr>
      <tr>
        <td align="right" valign="top">Results</td>
        <td><textarea id="phraseDiv" style="display: inline-block;width:500px;height:200px"></textarea></td>
      </tr>
    </table>
  </div>

  <script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>

   <script>
  // Note: Replace the URL with a valid endpoint to retrieve
  //       authorization tokens for your subscription.
  var authorizationEndpoint = "token.php";

  function RequestAuthorizationToken() {
    if (authorizationEndpoint) {
      var a = new XMLHttpRequest();
      a.open("GET", authorizationEndpoint);
      a.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
      a.send("");
      a.onload = function() {
          var token = JSON.parse(atob(this.responseText.split(".")[1]));
          serviceRegion.value = token.region;
          authorizationToken = this.responseText;
          subscriptionKey.disabled = true;
          subscriptionKey.value = "using authorization token (hit F5 to refresh)";
          console.log("Got an authorization token: " + token);
      }
    }
  }
  </script>
  
  <script>
    // status fields and start button in UI
    var phraseDiv;
    var startRecognizeOnceAsyncButton;

    // subscription key and region for speech services.
    var subscriptionKey, serviceRegion;
    var authorizationToken;
    var SpeechSDK;
    var recognizer;
    var filePicker;
    var audioFile;

    document.addEventListener("DOMContentLoaded", function () {
      startRecognizeOnceAsyncButton = document.getElementById("startRecognizeOnceAsyncButton");
      subscriptionKey = document.getElementById("subscriptionKey");
      serviceRegion = document.getElementById("serviceRegion");
      phraseDiv = document.getElementById("phraseDiv");
      filePicker = document.getElementById('filePicker');
      
      filePicker.addEventListener("change", function () {
                audioFile = filePicker.files[0];
            });

      startRecognizeOnceAsyncButton.addEventListener("click", function () {
        startRecognizeOnceAsyncButton.disabled = true;
        phraseDiv.innerHTML = "";

      });

      if (!!window.SpeechSDK) {
        SpeechSDK = window.SpeechSDK;
        startRecognizeOnceAsyncButton.disabled = false;

        document.getElementById('content').style.display = 'block';
        document.getElementById('warning').style.display = 'none';

        // in case we have a function for getting an authorization token, call it.
        if (typeof RequestAuthorizationToken === "function") {
            RequestAuthorizationToken();
        }
      }
    });
  </script>
```
 
## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Innan du kan initiera ett `SpeechRecognizer` objekt måste du skapa en konfiguration som använder din prenumerations nyckel och din prenumerations region. Infoga den här koden i- `startRecognizeOnceAsyncButton.addEventListener()` metoden.

> [!NOTE]
> Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.


```JavaScript
        // if we got an authorization token, use the token. Otherwise use the provided subscription key
        var speechConfig;
        if (authorizationToken) {
          speechConfig = SpeechSDK.SpeechConfig.fromAuthorizationToken(authorizationToken, serviceRegion.value);
        } else {
          if (subscriptionKey.value === "" || subscriptionKey.value === "subscription") {
            alert("Please enter your Microsoft Cognitive Services Speech subscription key!");
            return;
          }
          speechConfig = SpeechSDK.SpeechConfig.fromSubscription(subscriptionKey.value, serviceRegion.value);
        }

        speechConfig.speechRecognitionLanguage = "en-US";
```

## <a name="create-an-audio-configuration"></a>Skapa en ljud konfiguration

Nu måste du skapa ett `AudioConfig` objekt som pekar på ljud filen. Infoga den här koden i `startRecognizeOnceAsyncButton.addEventListener()` metoden, precis under din tal konfiguration.

```JavaScript
        var audioConfig  = SpeechSDK.AudioConfig.fromWavFileInput(audioFile);
```

## <a name="initialize-a-speechrecognizer"></a>Initiera en SpeechRecognizer

Nu ska vi skapa `SpeechRecognizer` objektet med de `SpeechConfig` `AudioConfig` objekt som skapades tidigare. Infoga den här koden i- `startRecognizeOnceAsyncButton.addEventListener()` metoden.

```JavaScript
        recognizer = new SpeechSDK.SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-a-phrase"></a>Identifiera en fras

Från `SpeechRecognizer` -objektet kommer du att anropa- `recognizeOnceAsync()` metoden. Med den här metoden kan röst tjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta identifiera tal.

```JavaScript
recognizer.recognizeOnceAsync(
          function (result) {
            startRecognizeOnceAsyncButton.disabled = false;
            phraseDiv.innerHTML += result.text;
            window.console.log(result);

            recognizer.close();
            recognizer = undefined;
          },
          function (err) {
            startRecognizeOnceAsyncButton.disabled = false;
            phraseDiv.innerHTML += err;
            window.console.log(err);

            recognizer.close();
            recognizer = undefined;
          });
```

## <a name="check-your-code"></a>Kontrol lera koden

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-from-file.html)]

## <a name="create-the-token-source-optional"></a>Skapa tokenkälla (valfritt)

Om du vill värdbasera webbsidan på en webbserver kan du ange en tokenkälla för demoappen.
På så sätt lämnar prenumerationen aldrig servern och tillåter att användare använder talfunktioner utan att själva behöva ange en auktoriseringskod.

Skapa en ny fil med namnet `token.php`. I det här exemplet antar vi att webb servern stöder PHP-skript språket med aktive rad vändning. Ange följande kod:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Auktoriseringstoken har en begränsad livslängd.
> Det här förenklade exemplet visar inte hur du uppdaterar auktoriseringstoken automatiskt. Som användare kan du manuellt läsa in sidan igen eller trycka på F5 för att uppdatera.

## <a name="build-and-run-the-sample-locally"></a>Skapa och köra exemplet lokalt

Starta appen genom att dubbelklicka på index.html-filen eller öppna index.html med valfri webbläsare. Ett enkelt grafiskt användargränssnitt visas där du kan ange din prenumerationsnyckel och [region](../../../../regions.md) och utlösa en igenkänning med mikrofonen.

> [!NOTE]
> Den här metoden fungerar inte i Safari-webbläsaren.
> På Safari måste exempel webb sidan finnas på en webb server. Safari tillåter inte att webbplatser läses in från en lokal fil för att använda mikrofonen.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Skapa och köra exemplet via en webbserver

Du kan starta appen genom att öppna valfri webbläsare och leda den till den offentliga URL där mappen finns, ange din [region](../../../../regions.md) och utlösa en igenkänning med mikrofonen. Om det har konfigurerats hämtas en token från tokenkällan.


## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]