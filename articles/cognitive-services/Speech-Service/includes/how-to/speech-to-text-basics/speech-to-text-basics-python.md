---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: c8354a67d26210035355f4947cb5f17cee934508
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79372825"
---
## <a name="prerequisites"></a>Krav

Denna artikel förutsätter:

* Du har en Azure-konto- och taltjänstprenumeration. Om du inte har och konto och prenumeration – [Prova taltjänsten gratis](../../../get-started.md).

## <a name="install-and-import-the-speech-sdk"></a>Installera och importera Tal-SDK

Innan du kan göra något måste du installera Tal-SDK.

```Python
pip install azure-cognitiveservices-speech
```

Om du använder macOS och stöter på installationsproblem kan du behöva köra det här kommandot först.

```Python
python3 -m pip install --upgrade pip
```

När Speech SDK har installerats importerar du det till python-projektet med det här uttrycket.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Om du vill anropa taltjänsten med tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python). Den här klassen innehåller information om din prenumeration, till exempel din nyckel och associerade region, slutpunkt, värd eller auktoriseringstoken.

> [!NOTE]
> Oavsett om du utför taligenkänning, talsyntes, översättning eller avsiktsigenkänning skapar du alltid en konfiguration.

Det finns några sätt som du [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)kan initiera en:

* Med en prenumeration: skicka in en nyckel och den associerade regionen.
* Med en slutpunkt: passera i en taltjänstslutpunkt. En nyckel eller auktoriseringstoken är valfri.
* Med en värd: skicka in en värdadress. En nyckel eller auktoriseringstoken är valfri.
* Med en auktoriseringstoken: skicka in en auktoriseringstoken och den associerade regionen.

Låt oss ta en titt [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) på hur en skapas med hjälp av en nyckel och region.

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>Initiera en identifierare

När du har [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)skapat en är nästa steg [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)att initiera en . När du initierar en [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)måste du skicka `speech_config`den till . Detta ger de autentiseringsuppgifter som taltjänsten kräver för att validera din begäran.

Så här ser du ut så här [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) om du känner igen tal med enhetens standardmikrofon:

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

Om du vill ange ljudinmatningsenheten måste du [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) skapa en `audio_config` och ange [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)parametern när du initierar .

> [!TIP]
> [Läs om hur du hämtar enhets-ID:et för ljudinmatningsenheten](../../../how-to-select-audio-input-devices.md).

```Python
audio_config = AudioConfig(device_name="<device id>");
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Om du vill tillhandahålla en ljudfil i stället för att använda `audio_config`en mikrofon måste du fortfarande ange en . Men när du [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python)skapar en , `device_name`i stället för `filename` att tillhandahålla , ska du använda parametern.

```Python
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>Identifiera tal

[Klassen Recognizer](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) för Tal SDK för Python visar några metoder som du kan använda för taligenkänning.

* En bildigenkänning (synkronisering) – Utför igenkänning i ett blockerande (synkront) läge. Returnerar efter att ett enda uttryck har identifierats. Slutet på ett enda uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunder av ljud bearbetas. Aktiviteten returnerar igenkänningstexten som resultat.
* Erkännande av ett slag (async) – Utför igenkänning i ett icke-blockerande (asynkront) läge. Detta kommer att känna igen ett enda uttryck. Slutet på ett enda uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunder av ljud bearbetas.
* Kontinuerlig igenkänning (synkronisering) - Initierar synkront kontinuerlig igenkänning. Klienten måste `EventSignal` ansluta till för att få igenkänningsresultat. Om du vill stoppa igenkänningen ringer [du stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--).
* Kontinuerlig igenkänning (async) - Asynkront initierar kontinuerlig igenkänning. Användaren måste ansluta till EventSignal för att få igenkänningsresultat. Om du vill stoppa asynkront kontinuerlig igenkänning anropar du [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--).

> [!NOTE]
> Läs mer om hur [du väljer ett taligenkänningsläge](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Erkännande av ett skott

Här är ett exempel på synkron enkelbildigenkänning med: [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once)

```Python
result = speech_recognizer.recognize_once()
```

Här är ett exempel på synkron enkelbildigenkänning med: [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture)

```Python
result = speech_recognizer.recognize_once_async()
```

Oavsett om du har använt den synkrona eller asynkrona metoden måste du skriva en kod för att iterera genom resultatet. I detta exempel [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python)utvärderas:

* Skriver ut igenkänningsresultatet:`speechsdk.ResultReason.RecognizedSpeech`
* Om det inte finns någon igenkänningsmatchning informerar du användaren:`speechsdk.ResultReason.NoMatch `
* Om ett fel påträffas skriver du ut felmeddelandet:`speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>Kontinuerligt erkännande

Kontinuerlig igenkänning är lite mer involverad än single-shot erkännande. Det kräver att du `EventSignal` ansluter till för att få igenkänningsresultaten, och för att stoppa igenkänningen måste du ringa [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) eller [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--). Här är ett exempel på hur kontinuerlig igenkänning utförs på en ljudinmatningsfil.

Låt oss börja med att definiera indata och initiera en: [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Låt oss sedan skapa en variabel för att hantera taligenkänningstillståndet. Till att börja med kommer `False`vi att ställa in detta till, eftersom i början av erkännandet kan vi lugnt anta att det inte är klar.

```Python
done = False
```

Nu ska vi skapa en motringning för att `evt` stoppa kontinuerlig igenkänning när en tas emot. Det finns några saker att tänka på.

* När `evt` ett tas `evt` emot skrivs meddelandet ut.
* När `evt` en tas emot anropas [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) för att stoppa igenkänningen.
* Igenkänningstillståndet ändras till `True`.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Det här kodexemplet visar hur du [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--)ansluter motringningar till händelser som skickas från .

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): Signal för händelser som innehåller resultat för mellanliggande igenkänning.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): Signal för händelser som innehåller slutliga igenkänningsresultat (som anger ett lyckat igenkänningsförsök).
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): Signal för händelser som indikerar början av en igenkänningssession (operation).
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): Signal för händelser som anger slutet på en igenkänningssession (operation).
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): Signal för händelser som innehåller annullerade igenkänningsresultat (som anger ett igenkänningsförsök som avbröts som ett resultat eller en direkt begäran om annullering eller, alternativt, ett transport- eller protokollfel).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Med allt som inrättats, kan vi ringa [start_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Dikteringsläge

När du använder kontinuerlig igenkänning kan du aktivera dikteringsbearbetning med hjälp av motsvarande "aktivera dikteringsfunktion". Det här läget gör att talkonfigurationsförekomsten tolkar ordbeskrivningar av meningsstrukturer, till exempel interpunktion. Till exempel skulle uttrycket "Bor du i stan frågetecken" tolkas som texten "Bor du i stan?".

Om du vill aktivera dikteringsläge använder du [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) metoden på din [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python).

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Ändra källspråk

En vanlig uppgift för taligenkänning är att ange indataspråk (eller källspråk). Låt oss ta en titt på hur du skulle ändra inmatningsspråket till tyska. Leta reda på din SpeechConfig i koden och lägg sedan till den här raden direkt under den.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language)är en parameter som tar en sträng som ett argument. Du kan ange valfritt värde i listan över [språk/språk](../../../language-support.md)som stöds .

## <a name="improve-recognition-accuracy"></a>Förbättra igenkänningsnoggrannheten

Det finns några sätt att förbättra igenkänningsnoggrannheten med Tal-SDK. Låt oss ta en titt på fraslistor. Fraslistor används för att identifiera kända fraser i ljuddata, till exempel en persons namn eller en viss plats. Enstaka ord eller fullständiga fraser kan läggas till i en fraslista. Under igenkänningen används en post i en fraslista om en exakt matchning för hela frasen ingår i ljudet. Om det inte går att hitta en exakt matchning till frasen assisteras inte igenkänningen.

> [!IMPORTANT]
> Funktionen Fraslista är endast tillgänglig på engelska.

Om du vill använda en [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) fraslista skapar du först [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-)ett objekt och lägger sedan till specifika ord och fraser med .

Alla ändringar [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) som börjar gälla på nästa igenkänning eller efter en återanslutning till taltjänsten.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Om du behöver rensa din fraslista: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andra alternativ för att förbättra igenkänningsnoggrannheten

Fraslistor är bara ett alternativ för att förbättra igenkänningsnoggrannheten. Du kan också: 

* [Förbättra noggrannheten med anpassat tal](../../../how-to-custom-speech.md)
* [Förbättra noggrannheten med klientmodeller](../../../tutorial-tenant-model.md)