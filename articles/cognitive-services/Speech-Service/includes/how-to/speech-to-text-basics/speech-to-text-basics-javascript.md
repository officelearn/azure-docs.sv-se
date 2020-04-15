---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/14/2020
ms.author: dapine
ms.openlocfilehash: 668cf9e831191a5d649f7dd82af03eb637bb0d3a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314215"
---
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har en Azure-konto- och taltjänstprenumeration. Om du inte har ett konto och en prenumeration [provar du taltjänsten utan kostnad](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript <span class="docon docon-navigate-external x-hidden-focus"> </span>Speech SDK </a>. Beroende på din plattform använder du följande instruktioner:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbläsare<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Beroende på målmiljön används dessutom något av följande:

# <a name="import"></a>[Importera](#tab/import)

```javascript
import * as sdk from "microsoft-cognitiveservices-speech-sdk";
```

Mer information `import`om finns i <a href="https://javascript.info/import-export" target="_blank">exportera och importera <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

# <a name="require"></a>[Kräver](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Mer information `require`om finns i <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">vad som krävs? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[-skriptet](#tab/script)

Hämta och extrahera filen <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span> Speech SDK</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* och placera den i en mapp som är tillgänglig för HTML-filen.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Om du riktar in dig på `<script>` en webbläsare och använder taggen. prefixet `sdk` behövs inte. Prefixet `sdk` är ett alias som `import` `require` vi använder för att namnge vår eller modul.

---

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Om du vill anropa taltjänsten med tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest). Den här klassen innehåller information om din prenumeration, till exempel din nyckel och associerade region, slutpunkt, värd eller auktoriseringstoken.

> [!NOTE]
> Oavsett om du utför taligenkänning, talsyntes, översättning eller avsiktsigenkänning skapar du alltid en konfiguration.

Det finns några sätt som du [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)kan initiera en:

* Med en prenumeration: skicka in en nyckel och den associerade regionen.
* Med en slutpunkt: passera i en taltjänstslutpunkt. En nyckel eller auktoriseringstoken är valfri.
* Med en värd: skicka in en värdadress. En nyckel eller auktoriseringstoken är valfri.
* Med en auktoriseringstoken: skicka in en auktoriseringstoken och den associerade regionen.

Låt oss ta en titt [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) på hur en skapas med hjälp av en nyckel och region. Se [regionens supportsida](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regionidentifierare.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Initiera en identifierare

När du har [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)skapat en är nästa steg [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)att initiera en . När du initierar en [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)måste du skicka `speechConfig`den till . Detta ger de autentiseringsuppgifter som taltjänsten kräver för att validera din begäran.

Så här ser du ut så här [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) om du känner igen tal med enhetens standardmikrofon:

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

Om du vill ange ljudinmatningsenheten måste du [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) skapa en `audioConfig` och ange [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)parametern när du initierar .

> [!TIP]
> [Läs om hur du hämtar enhets-ID:et för ljudinmatningsenheten](../../../how-to-select-audio-input-devices.md).

Referera `AudioConfig` till objektet enligt följande:

```javascript
const audioConfig = sdk.AudioConfig.fromDefaultMicrophoneInput();
const speechConfig = sdk.SpeechConfig.fromSubscription(speechConfig, audioConfig);
```

Om du vill tillhandahålla en ljudfil i stället för att använda `audioConfig`en mikrofon måste du fortfarande ange en . Detta kan dock bara göras när du riktar in dig [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest)på **Node.js** och när du skapar en , i stället för att anropa `fromDefaultMicrophoneInput`, anropar `fromWavFileOutput` och skickar du parametern. `filename`

```javascript
const audioConfig = sdk.AudioConfig.fromWavFileInput("YourAudioFile.wav");
const speechConfig = sdk.SpeechConfig.fromSubscription(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Identifiera tal

[Klassen Recognizer](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) för tal-SDK för C# visar några metoder som du kan använda för taligenkänning.

* Erkännande av ett slag (async) – Utför igenkänning i ett icke-blockerande (asynkront) läge. Detta kommer att känna igen ett enda uttryck. Slutet på ett enda uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunder av ljud bearbetas.
* Kontinuerlig igenkänning (async) - Asynkront initierar kontinuerlig igenkänning. Användaren registrerar sig till händelser och hanterar olika programtillstånd. Anropa om du vill stoppa asynkron kontinuerlig igenkänning anropar du [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync).

> [!NOTE]
> Läs mer om hur [du väljer ett taligenkänningsläge](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Erkännande av ett skott

Här är ett exempel på asynkron enkelbildsigenkänning med: [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync)

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Du måste skriva en kod för att hantera resultatet. I detta exempel [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest#reason)utvärderas:

* Skriver ut igenkänningsresultatet:`ResultReason.RecognizedSpeech`
* Om det inte finns någon igenkänningsmatchning informerar du användaren:`ResultReason.NoMatch`
* Om ett fel påträffas skriver du ut felmeddelandet:`ResultReason.Canceled`

```javascript
switch (result.reason) {
    case sdk.ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.Text}`);
        console.log("    Intent not recognized.");
        break;
    case sdk.ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case sdk.ResultReason.Canceled:
        const cancellation = sdk.CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.Reason}`);

        if (cancellation.Reason == sdk.CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.ErrorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
}
```

### <a name="continuous-recognition"></a>Kontinuerligt erkännande

Kontinuerlig igenkänning är lite mer involverad än single-shot erkännande. Det kräver att du `Recognizing` `Recognized`prenumererar `Canceled` på , och händelser för att få igenkänningsresultaten. Om du vill stoppa [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)igenkänningen måste du ringa . Här är ett exempel på hur kontinuerlig igenkänning utförs på en ljudinmatningsfil.

Låt oss börja med att definiera indata och initiera en: [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

Vi prenumererar på de händelser [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)som skickas från .

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizing): Signal för händelser som innehåller resultat för mellanliggande igenkänning.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognized): Signal för händelser som innehåller slutliga igenkänningsresultat (som anger ett lyckat igenkänningsförsök).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#sessionstopped): Signal för händelser som anger slutet på en igenkänningssession (operation).
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#canceled): Signal för händelser som innehåller annullerade igenkänningsresultat (som anger ett igenkänningsförsök som avbröts som ett resultat eller en direkt begäran om annullering eller, alternativt, ett transport- eller protokollfel).

```javascript
recognizer.Recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.Result.Text}`);
};

recognizer.recognized = (s, e) => {
    if (e.Result.Reason == sdk.ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.Result.Text}`);
    }
    else if (e.Result.Reason == sdk.ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.Reason}`);

    if (e.Reason == sdk.CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.ErrorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.ErrorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.SessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Med allt klart kan [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)vi ringa.

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Dikteringsläge

När du använder kontinuerlig igenkänning kan du aktivera dikteringsbearbetning med hjälp av motsvarande "aktivera dikteringsfunktion". Det här läget gör att talkonfigurationsförekomsten tolkar ordbeskrivningar av meningsstrukturer, till exempel interpunktion. Till exempel skulle uttrycket "Bor du i stan frågetecken" tolkas som texten "Bor du i stan?".

Om du vill aktivera dikteringsläge använder du [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--) metoden på din [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Ändra källspråk

En vanlig uppgift för taligenkänning är att ange indataspråk (eller källspråk). Låt oss ta en titt på hur du skulle ändra inmatningsspråket till italienska. I din kod, [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)hitta din , lägg sedan till den här raden direkt under den.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

Egenskapen [`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#speechrecognitionlanguage) förväntar sig en språkspråksformatsträng. Du kan ange valfritt värde i kolumnen **Språk i** listan över [språk/språk](../../../language-support.md)som stöds .

## <a name="improve-recognition-accuracy"></a>Förbättra igenkänningsnoggrannheten

Det finns några sätt att förbättra igenkänningsnoggrannheten med Tal-SDK. Låt oss ta en titt på fraslistor. Fraslistor används för att identifiera kända fraser i ljuddata, till exempel en persons namn eller en viss plats. Enstaka ord eller fullständiga fraser kan läggas till i en fraslista. Under igenkänningen används en post i en fraslista om en exakt matchning för hela frasen ingår i ljudet. Om det inte går att hitta en exakt matchning till frasen assisteras inte igenkänningen.

> [!IMPORTANT]
> Funktionen Fraslista är endast tillgänglig på engelska.

Om du vill använda en [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) fraslista skapar du först [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest#addphrase-string-)ett objekt och lägger sedan till specifika ord och fraser med .

Alla ändringar [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) som börjar gälla på nästa igenkänning eller efter en återanslutning till taltjänsten.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Om du behöver rensa din fraslista:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andra alternativ för att förbättra igenkänningsnoggrannheten

Fraslistor är bara ett alternativ för att förbättra igenkänningsnoggrannheten. Du kan också: 

* [Förbättra noggrannheten med anpassat tal](../../../how-to-custom-speech.md)
* [Förbättra noggrannheten med klientmodeller](../../../tutorial-tenant-model.md)