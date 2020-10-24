---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: afe6562357f1d5558fdb2ac8cb0eb53cd3422d48
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499117"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att känna igen mänskligt tal (kallas ofta tal till text). I den här snabb starten får du lära dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativt tal-till-text-konvertering.

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod kan du läsa [snabb starts exemplen för Java Script](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK för Java Script <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Använd följande instruktioner, beroende på plattform:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbläsare <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Beroende på mål miljön använder du dessutom något av följande:

# <a name="script"></a>[skript](#tab/script)

Hämta och extrahera <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">talet SDK för Java Script <span class="docon docon-navigate-external x-hidden-focus"></span> </a> *microsoft.cognitiveservices.speech.sdk.bundle.js* -filen och placera den i en mapp som är tillgänglig för HTML-filen.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Om du är mål för en webbläsare och använder `<script>` taggen, `sdk` behövs inte prefixet. `sdk`Prefixet är ett alias som används för att namnge `require` modulen.

# <a name="import"></a>[export](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Mer information finns i `import` <a href="https://javascript.info/import-export" target="_blank">Exportera och importera <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

# <a name="require"></a>[innebära](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Mer information om `require` finns i <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Vad kräver <span class="docon docon-navigate-external x-hidden-focus"></span> ? </a>.

---

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering. Skapa en [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) med hjälp av din nyckel och region. Se sidan [region support](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regions-ID.

```javascript
const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Det finns några andra sätt som du kan initiera [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) :

* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

> [!NOTE]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

## <a name="recognize-from-microphone"></a>Identifiera från mikrofonen

Skapa en med hjälp av om du vill känna igen tal med din enhets mikrofon `AudioConfig` `fromDefaultMicrophoneInput()` . Initiera sedan en [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) , och skicka in `speechConfig` och `audioConfig` .

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);

console.log('Speak into your microphone.');
recognizer.recognizeOnceAsync(result => {
    console.log(`RECOGNIZED: Text=${result.text}`);
});
```

Om du vill använda en *speciell* enhet för ljud inspelning måste du ange enhets-ID i `AudioConfig` . Lär dig [hur du hämtar enhets-ID](../../../how-to-select-audio-input-devices.md) : t för din enhet för ljud inspelning.

## <a name="recognize-from-file-nodejs-only"></a>Identifiera från fil (endast Node.js)

Om du vill känna igen tal från en ljudfil i stället för att använda en mikrofon måste du ändå ange en `AudioConfig` . Men när du skapar i [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) stället för att anropa anropar `fromDefaultMicrophoneInput()` du `fromWavFileInput()` och överför fil Sök vägen.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new SpeechRecognizer(speechConfig, audioConfig);

recognizer.recognizeOnceAsync(result => {
    console.log(`RECOGNIZED: Text=${result.text}`);
});
```

## <a name="recognize-speech"></a>Identifiera tal

[Igenkännings klassen](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) för tal-SDK för Java Script visar några metoder som du kan använda för tal igenkänning.

### <a name="single-shot-recognition"></a>Igenkänning av enstaka bild

Ett enda avbilds igenkännings läge identifierar en enskild uttryck asynkront. Slutet på en enskild uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunders ljud bearbetas. Här är ett exempel på en asynkron igenkänning av enstaka bild med [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Du måste skriva kod för att hantera resultatet. I det här exemplet utvärderas [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest&preserve-view=true#reason) :

* Skriver ut resultatet för igenkänning: `ResultReason.RecognizedSpeech`
* Om det inte finns någon igenkännings matchning, informera användaren: `ResultReason.NoMatch`
* Om ett fel påträffas skriver du ut fel meddelandet: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        console.log("    Intent not recognized.");
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

### <a name="continuous-recognition"></a>Kontinuerlig igenkänning

Kontinuerlig igenkänning är lite mer engagerande än igenkänning av enstaka steg. Det kräver att du prenumererar på `Recognizing` -, `Recognized` -och- `Canceled` händelserna för att få igenkännings resultatet. Om du vill stoppa igenkänningen måste du anropa [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync) . Här är ett exempel på hur kontinuerlig igenkänning utförs på en inspelnings fil.

Vi börjar med att definiera indatamängden och initiera en [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) :

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Vi kommer att prenumerera på de händelser som skickas från [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true) .

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizing): Signal för händelser som innehåller mellanliggande igenkännings resultat.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognized): Signal för händelser som innehåller slutgiltiga igenkännings resultat (indikerar ett lyckat igenkännings försök).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#sessionstopped): Signal för händelser som indikerar att en avläsnings session avslutas (åtgärd).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#canceled): Signal för händelser som innehåller avbrutna igenkännings resultat (vilket indikerar ett igenkännings försök som avbrutits som ett resultat eller en direkt uppsägnings förfrågan eller, alternativt, ett transport-eller protokoll haveri).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Med allt konfigurerat kan vi anropa [`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#startcontinuousrecognitionasync) .

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Diktamensläge

När du använder kontinuerlig igenkänning kan du aktivera dikterings bearbetning genom att använda motsvarande "Aktivera diktering"-funktion. Det här läget kommer att göra att tal konfigurations instansen tolkar ord beskrivningar av menings strukturer som interpunktion. Till exempel skulle uttryck "är du bor i stadens frågetecken" tolkas som texten "är du bor i staden?".

Om du vill aktivera dikteringsläget använder du [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#enabledictation--) metoden på din [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Ändra käll språk

En vanlig uppgift för tal igenkänning anger språk för indata (eller källa). Låt oss ta en titt på hur du ändrar indatamängds språk till italienska. Leta upp din kod i koden [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) och Lägg sedan till den här raden direkt under den.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#speechrecognitionlanguage)Egenskapen förväntar sig en språk språks format sträng. Du kan ange valfritt värde i kolumnen **språk** i listan [över språk som stöds.](../../../language-support.md)

## <a name="improve-recognition-accuracy"></a>Förbättra igenkännings precisionen

Det finns ett par sätt att förbättra igenkännings precisionen med tal vi tar en titt på fras listor. Fras listor används för att identifiera kända fraser i ljuddata, t. ex. en persons namn eller en viss plats. Enstaka ord eller fullständiga fraser kan läggas till i en fras lista. Under igenkänning används en post i en fras lista om en exakt matchning för hela frasen ingår i ljudet. Om det inte går att hitta en exakt matchning till frasen går det inte att hitta igenkänning.

> [!IMPORTANT]
> Funktionen fras lista är bara tillgänglig på engelska.

Om du vill använda en fras lista måste du först skapa ett [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true) objekt och sedan lägga till vissa ord och fraser med [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true#addphrase-string-) .

Eventuella ändringar [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true) börjar gälla nästa igenkänning eller efter en åter anslutning till tal-tjänsten.

```javascript
const phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Om du behöver rensa fras listan:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andra alternativ för att förbättra igenkännings precisionen

Fras listor är bara ett alternativ för att förbättra igenkännings precisionen. Du kan även: 

* [Förbättra noggrannheten med anpassat tal](../../../how-to-custom-speech.md)
* [Förbättra noggrannheten med klientmodeller](../../../tutorial-tenant-model.md)
