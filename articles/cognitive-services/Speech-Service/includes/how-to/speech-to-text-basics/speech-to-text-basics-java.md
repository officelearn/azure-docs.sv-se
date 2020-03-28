---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 7d29f781950784cdb8951b3a6e45d5b5d0f9819f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79539817"
---
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har en Azure-konto- och taltjänstprenumeration. Om du inte har ett konto och en prenumeration [provar du taltjänsten utan kostnad](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera Tal-SDK. Beroende på din plattform använder du följande instruktioner:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Körtid<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Om du vill anropa taltjänsten med tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable). Den här klassen innehåller information om din prenumeration, till exempel din nyckel och associerade region, slutpunkt, värd eller auktoriseringstoken.

> [!NOTE]
> Oavsett om du utför taligenkänning, talsyntes, översättning eller avsiktsigenkänning skapar du alltid en konfiguration.

Det finns några sätt som du [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)kan initiera en:

* Med en prenumeration: skicka in en nyckel och den associerade regionen.
* Med en slutpunkt: passera i en taltjänstslutpunkt. En nyckel eller auktoriseringstoken är valfri.
* Med en värd: skicka in en värdadress. En nyckel eller auktoriseringstoken är valfri.
* Med en auktoriseringstoken: skicka in en auktoriseringstoken och den associerade regionen.

Låt oss ta en titt [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) på hur en skapas med hjälp av en nyckel och region.

```java
SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Initiera en identifierare

När du har [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)skapat en är nästa steg [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)att initiera en . När du initierar en [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)måste du skicka `config`den till . Detta ger de autentiseringsuppgifter som taltjänsten kräver för att validera din begäran.

Så här ser du ut så här [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) om du känner igen tal med enhetens standardmikrofon:

```java
SpeechRecognizer recognizer = new SpeechRecognizer(config);
```

Om du vill ange ljudinmatningsenheten måste du [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) skapa en `audioConfig` och ange [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)parametern när du initierar .

> [!TIP]
> [Läs om hur du hämtar enhets-ID:et för ljudinmatningsenheten](../../../how-to-select-audio-input-devices.md).

Lägg först till `import` följande satser.

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;
```

Därefter kan du referera till `AudioConfig` objektet på följande sätt:

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Om du vill tillhandahålla en ljudfil i stället för att använda `audioConfig`en mikrofon måste du fortfarande ange en . Men när du [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable)skapar en `fromDefaultMicrophoneInput`, i stället `fromWavFileOutput` för `filename` att ringa , kommer du att anropa och skicka parametern.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

## <a name="recognize-speech"></a>Identifiera tal

[Klassen Recognizer](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) för Tal SDK för Java visar några metoder som du kan använda för taligenkänning.

* Erkännande av ett slag (async) – Utför igenkänning i ett icke-blockerande (asynkront) läge. Detta kommer att känna igen ett enda uttryck. Slutet på ett enda uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunder av ljud bearbetas.
* Kontinuerlig igenkänning (async) - Asynkront initierar kontinuerlig igenkänning. Om du vill tillhandahålla en ljudfil i stället för att använda en mikrofon måste du fortfarande ange en. Om du vill stoppa asynkron kontinuerlig igenkänning anropar [duContinuousRecognitionAsync](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync).

> [!NOTE]
> Läs mer om hur [du väljer ett taligenkänningsläge](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Erkännande av ett skott

Här är ett exempel på asynkron enkelbildsigenkänning med: [`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable)

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

Du måste skriva en kod för att hantera resultatet. I detta exempel [`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable)utvärderas:

* Skriver ut igenkänningsresultatet:`ResultReason.RecognizedSpeech`
* Om det inte finns någon igenkänningsmatchning informerar du användaren:`ResultReason.NoMatch`
* Om ett fel påträffas skriver du ut felmeddelandet:`ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

### <a name="continuous-recognition"></a>Kontinuerligt erkännande

Kontinuerlig igenkänning är lite mer involverad än single-shot erkännande. Det kräver att du `recognizing` `recognized`prenumererar `canceled` på , och händelser för att få igenkänningsresultaten. Om du vill stoppa [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)igenkänningen måste du ringa . Här är ett exempel på hur kontinuerlig igenkänning utförs på en ljudinmatningsfil.

Låt oss börja med att definiera indata och initiera en: [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Låt oss sedan skapa en variabel för att hantera taligenkänningstillståndet. Till att börja med `Semaphore` deklarerar vi en på klassens omfattning.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Vi prenumererar på de händelser [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)som skickas från .

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable): Signal för händelser som innehåller resultat för mellanliggande igenkänning.
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable): Signal för händelser som innehåller slutliga igenkänningsresultat (som anger ett lyckat igenkänningsförsök).
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable): Signal för händelser som anger slutet på en igenkänningssession (operation).
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable): Signal för händelser som innehåller annullerade igenkänningsresultat (som anger ett igenkänningsförsök som avbröts som ett resultat eller en direkt begäran om annullering eller, alternativt, ett transport- eller protokollfel).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Med allt klart kan [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)vi ringa.

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Dikteringsläge

När du använder kontinuerlig igenkänning kan du aktivera dikteringsbearbetning med hjälp av motsvarande "aktivera dikteringsfunktion". Det här läget gör att talkonfigurationsförekomsten tolkar ordbeskrivningar av meningsstrukturer, till exempel interpunktion. Till exempel skulle uttrycket "Bor du i stan frågetecken" tolkas som texten "Bor du i stan?".

Om du vill aktivera dikteringsläge använder du [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable) metoden på din [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Ändra källspråk

En vanlig uppgift för taligenkänning är att ange indataspråk (eller källspråk). Låt oss ta en titt på hur du skulle ändra inmatningsspråket till franska. I din kod, [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)hitta din , lägg sedan till den här raden direkt under den.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable)är en parameter som tar en sträng som ett argument. Du kan ange valfritt värde i listan över [språk/språk](../../../language-support.md)som stöds .

## <a name="improve-recognition-accuracy"></a>Förbättra igenkänningsnoggrannheten

Det finns några sätt att förbättra igenkänningsnoggrannheten med Tal-SDK. Låt oss ta en titt på fraslistor. Fraslistor används för att identifiera kända fraser i ljuddata, till exempel en persons namn eller en viss plats. Enstaka ord eller fullständiga fraser kan läggas till i en fraslista. Under igenkänningen används en post i en fraslista om en exakt matchning för hela frasen ingår i ljudet. Om det inte går att hitta en exakt matchning till frasen assisteras inte igenkänningen.

> [!IMPORTANT]
> Funktionen Fraslista är endast tillgänglig på engelska.

Om du vill använda en [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) fraslista skapar du först [`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_)ett objekt och lägger sedan till specifika ord och fraser med .

Alla ändringar [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) som börjar gälla på nästa igenkänning eller efter en återanslutning till taltjänsten.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Om du behöver rensa din fraslista: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andra alternativ för att förbättra igenkänningsnoggrannheten

Fraslistor är bara ett alternativ för att förbättra igenkänningsnoggrannheten. Du kan också: 

* [Förbättra noggrannheten med anpassat tal](../../../how-to-custom-speech.md)
* [Förbättra noggrannheten med klientmodeller](../../../tutorial-tenant-model.md)