---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 92fb6bb470c6ab9c1a8f1f0542c26a8fef07e6b3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376648"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att känna igen mänskligt tal (kallas ofta tal till text). I den här snabb starten får du lära dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativt tal-till-text-konvertering.

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod, se [exempel på python-snabb starts exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter:

* Du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova med att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-and-import-the-speech-sdk"></a>Installera och importera tal-SDK

Innan du kan göra något måste du installera talet SDK.

```Python
pip install azure-cognitiveservices-speech
```

Om du använder macOS och kör installations problem kan du behöva köra kommandot först.

```Python
python3 -m pip install --upgrade pip
```

När du har installerat talet SDK importerar du det till python-projektet med den här instruktionen.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering.

> [!NOTE]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

Det finns några sätt som du kan initiera en [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) :

* Med en prenumeration: skicka i en nyckel och tillhör ande region.
* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

Låt oss ta en titt på hur en [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) har skapats med hjälp av en nyckel och region. Se sidan [region support](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regions-ID.

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>Initiera en identifierare

När du har skapat en [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) är nästa steg att initiera en [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) . När du initierar en [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) skickar du det `speech_config` . Detta ger de autentiseringsuppgifter som tal tjänsten behöver för att verifiera din begäran.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

## <a name="recognize-from-microphone-or-file"></a>Identifiera från mikrofon eller fil

Om du vill ange enheten för ljud inspelning måste du skapa en [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) och skicka den som en parameter när du initierar [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) .

Om du vill känna igen tal med din enhets mikrofon skapar du bara en `SpeechRecognizer` utan att skicka ett `AudioConfig`

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

> [!TIP]
> Om du vill referera till en enhet efter ID skapar du en `AudioConfig` med `AudioConfig(device_name="<device id>")` 
>  [Lär dig hur du hämtar enhets-ID: t för enheten för ljud inspelning](../../../how-to-select-audio-input-devices.md).

Om du vill känna igen tal från en ljudfil i stället för att använda en mikrofon skapar [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) du en och använder- `filename` parametern.

```Python
audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>Identifiera tal

[Igenkännings klassen](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) för tal-SDK för python visar några metoder som du kan använda för tal igenkänning.

* En avbildad igenkänning (Sync) – utför igenkänning i ett block läge (synkront). Returnerar efter att en enskild uttryck har identifierats. Slutet på en enskild uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunders ljud bearbetas. Uppgiften returnerar igenkännings texten som resultat.
* Identifiering av enstaka bild (async) – utför igenkänning i ett icke-blockerande (asynkront) läge. Detta kommer att identifiera en enda uttryck. Slutet på en enskild uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunders ljud bearbetas.
* Kontinuerlig igenkänning (synkronisering) – startar synkront löpande igenkänning. Klienten måste ansluta till `EventSignal` för att få igenkännings resultat. Anropa [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--)om du vill stoppa igenkänning.
* Kontinuerlig igenkänning (async) – initierar kontinuerlig igenkännings åtgärd asynkront. Användaren måste ansluta till EventSignal för att få igenkännings resultat. Anropa [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--)för att stoppa asynkron löpande igenkänning.

> [!NOTE]
> Läs mer om hur du [väljer ett tal igenkännings läge](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Igenkänning av enstaka bild

Här är ett exempel på en synkron igenkänning med [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once) :

```Python
result = speech_recognizer.recognize_once()
```

Här är ett exempel på en asynkron igenkänning av enstaka bild med [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture) :

```Python
result = speech_recognizer.recognize_once_async()
```

Oavsett om du har använt metoden synkront eller asynkront måste du skriva kod för att iterera genom resultatet. I det här exemplet utvärderas [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python) :

* Skriver ut resultatet för igenkänning: `speechsdk.ResultReason.RecognizedSpeech`
* Om det inte finns någon igenkännings matchning, informera användaren: `speechsdk.ResultReason.NoMatch `
* Om ett fel påträffas skriver du ut fel meddelandet: `speechsdk.ResultReason.Canceled`

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

### <a name="continuous-recognition"></a>Kontinuerlig igenkänning

Kontinuerlig igenkänning är lite mer engagerande än igenkänning av enstaka steg. Det kräver att du ansluter till `EventSignal` för att få igenkännings resultaten, och om du vill stoppa igenkänningen måste du anropa [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) eller [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--). Här är ett exempel på hur kontinuerlig igenkänning utförs på en inspelnings fil.

Vi börjar med att definiera indatamängden och initiera en [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) :

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Nu ska vi skapa en variabel för att hantera läget för tal igenkänning. För att starta ska vi ställa in detta på `False` , eftersom vi i början av igenkänningen kan anta att det inte är klart.

```Python
done = False
```

Nu ska vi skapa en motringning för att stoppa kontinuerlig igenkänning när en tas `evt` emot. Det finns några saker att tänka på.

* När en tas `evt` emot `evt` skrivs meddelandet ut.
* När en tas `evt` emot anropas [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) för att stoppa igenkänningen.
* Igenkännings statusen har ändrats till `True` .

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Det här kod exemplet visar hur du ansluter motringningar till händelser som skickas från [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--) .

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): Signal för händelser som innehåller mellanliggande igenkännings resultat.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): Signal för händelser som innehåller slutgiltiga igenkännings resultat (indikerar ett lyckat igenkännings försök).
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): Signal för händelser som indikerar början på en avläsnings session (åtgärd).
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): Signal för händelser som indikerar att en avläsnings session avslutas (åtgärd).
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): Signal för händelser som innehåller avbrutna igenkännings resultat (vilket indikerar ett igenkännings försök som avbrutits som ett resultat eller en direkt uppsägnings förfrågan eller, alternativt, ett transport-eller protokoll haveri).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Med allt konfigurerat kan vi anropa [start_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Diktamensläge

När du använder kontinuerlig igenkänning kan du aktivera dikterings bearbetning genom att använda motsvarande "Aktivera diktering"-funktion. Det här läget kommer att göra att tal konfigurations instansen tolkar ord beskrivningar av menings strukturer som interpunktion. Till exempel skulle uttryck "är du bor i stadens frågetecken" tolkas som texten "är du bor i staden?".

Om du vill aktivera dikteringsläget använder du [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) metoden på din [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) .

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Ändra käll språk

En vanlig uppgift för tal igenkänning anger språk för indata (eller källa). Låt oss ta en titt på hur du ändrar indatamängds språk till tyska. Leta upp din SpeechConfig i din kod och Lägg sedan till den här raden direkt under den.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) är en parameter som tar en sträng som ett argument. Du kan ange ett värde i listan över [språk](../../../language-support.md)som stöds.

## <a name="improve-recognition-accuracy"></a>Förbättra igenkännings precisionen

Det finns några sätt att förbättra igenkännings precisionen med talet SDK. Låt oss ta en titt på fras listor. Fras listor används för att identifiera kända fraser i ljuddata, t. ex. en persons namn eller en viss plats. Enstaka ord eller fullständiga fraser kan läggas till i en fras lista. Under igenkänning används en post i en fras lista om en exakt matchning för hela frasen ingår i ljudet. Om det inte går att hitta en exakt matchning till frasen går det inte att hitta igenkänning.

> [!IMPORTANT]
> Funktionen fras lista är bara tillgänglig på engelska.

Om du vill använda en fras lista måste du först skapa ett [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) objekt och sedan lägga till vissa ord och fraser med [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-) .

Eventuella ändringar [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) börjar gälla nästa igenkänning eller efter en åter anslutning till tal-tjänsten.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Om du behöver rensa fras listan: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andra alternativ för att förbättra igenkännings precisionen

Fras listor är bara ett alternativ för att förbättra igenkännings precisionen. Du kan även: 

* [Förbättra noggrannheten med anpassat tal](../../../how-to-custom-speech.md)
* [Förbättra noggrannheten med klientmodeller](../../../tutorial-tenant-model.md)
