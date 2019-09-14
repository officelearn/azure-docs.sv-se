---
title: 'Kom igång med API: t för tal igenkänning i Microsoft C# med hjälp av tjänst biblioteket | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Använd biblioteket för Taligenkänning i Bing igenkännings tjänsten om du vill konvertera det talade språket till text.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 71c3e471a8844eb6c6b70921e40c94338a084a8b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965848"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Snabbstart: Använda biblioteket för Taligenkänning i Bing igenkännings tjänsten i&#35; C för .net Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Tjänst biblioteket är för utvecklare som har sin egen moln tjänst och vill anropa tjänsten Speech service. Använd inte denna SDK om du vill anropa tjänsten för tal igenkänning från enhets appar. (Använd andra klient bibliotek eller REST-API: er för det.)

Om du vill C# använda tjänst biblioteket installerar du [NuGet-paketet Microsoft. Bing. Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). För API-referens för bibliotek, se [Microsoft Speech C# service-biblioteket](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

I följande avsnitt beskrivs hur du installerar, skapar och kör C# exempel programmet med hjälp av C# tjänst biblioteket.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Följande exempel har utvecklats för Windows 8 + och .NET 4.5 + Framework med hjälp av [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Hämta exempel programmet

Klona exemplet från exempel lagrings platsen för [ C# tal tjänst bibliotek](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) .

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på API för tal igenkänning och få en prenumerations nyckel för kostnads fri utvärdering

Speech API är en del av Cognitive Services (tidigare Project-Oxford). Du kan få kostnads fria utvärderings prenumerations nycklar från sidan [Cognitive Services prenumeration](https://azure.microsoft.com/try/cognitive-services/) . När du har valt Speech API väljer du **Hämta API-nyckel** för att hämta nyckeln. Den returnerar en primär och sekundär nyckel. Båda nycklarna är knutna till samma kvot, så du kan använda båda nycklarna.

> [!IMPORTANT]
> * Hämta en prenumerations nyckel. Innan du kan använda klient biblioteken för tal måste du ha en [prenumerations nyckel](https://azure.microsoft.com/try/cognitive-services/).
>
> * Använd din prenumerations nyckel. Med det tillhandahållna C# tjänst bibliotekets exempel program måste du ange din prenumerations nyckel som en av kommando rads parametrarna. Mer information finns i [Kör exempel programmet](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Steg 1: Installera exempel programmet

1. Starta Visual Studio 2015 och välj **fil** > **Öppna** > **projekt/lösning**.

2. Öppna Visual Studio 2015-lösningen (. SLN) med namnet SpeechClient. SLN genom att dubbelklicka på den. Lösningen öppnas i Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Steg 2: Bygg exempel programmet

Tryck på CTRL + SKIFT + B eller Välj **build** på menyn i menyfliksområdet. Välj sedan **build-lösning**.

## <a name="step-3-run-the-sample-application"></a>Steg 3: Köra exempelprogrammet

1. När bygget är klart trycker du på F5 eller väljer **Starta** på menyn i menyfliksområdet för att köra exemplet.

2. Öppna utdata-katalogen för exemplet, till exempel SpeechClientSample\bin\Debug. Tryck på Skift + högerklicka och välj **Öppna kommando fönstret här**.

3. Kör `SpeechClientSample.exe` med följande argument:

   * Arg[0]: Ange en WAV-fil med indata.
   * Arg[1]: Ange språket för ljudet.
   * Arg[2]: Ange tolknings lägen: *Förkortat* för `ShortPhrase` läget `LongDictation` och långt för läget.
   * Arg[3]: Ange prenumerations nyckeln för att komma åt tjänsten för tal igenkänning.

## <a name="samples-explained"></a>Exempel på förklaring

### <a name="recognition-modes"></a>Igenkännings lägen

* `ShortPhrase`offline En uttryck upp till 15 sekunder lång. När data skickas till servern får klienten flera delar av resultaten och ett slut resultat.
* `LongDictation`offline En uttryck upp till 10 minuter lång. När data skickas till servern får klienten flera partiella resultat och flera slut resultat baserat på var servern anger att meningen ska pausas.

### <a name="supported-audio-formats"></a>Ljud format som stöds

Speech API stöder ljud/WAV med följande codecenheter:

* PCM, enkel kanal
* Siren
* SirenSR

### <a name="preferences"></a>Inställningar

Om du vill skapa en SpeechClient måste du först skapa ett inställnings objekt. Preferences-objektet är en uppsättning parametrar som konfigurerar röst tjänstens beteende. Det består av följande fält:

* `SpeechLanguage`: Språkvarianten för ljudet som skickas till tal tjänsten.
* `ServiceUri`: Slut punkten som används för att anropa tal tjänsten.
* `AuthorizationProvider`: En IAuthorizationProvider-implementering som används för att hämta tokens för att få åtkomst till tal tjänsten. Även om exemplet tillhandahåller en Cognitive Services Authorization-provider rekommenderar vi starkt att du skapar din egen implementering för att hantera cachelagring av token.
* `EnableAudioBuffering`: Ett avancerat alternativ. Se [hantering av anslutningar](#connection-management).

### <a name="speech-input"></a>Tal ingång

SpeechInput-objektet består av två fält:

* **Ljud**: En data Ströms implementering som du väljer från vilken SDK: n hämtar ljud. Det kan vara vilken [ström](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) som helst som har stöd för läsning.
   > [!NOTE]
   > SDK: n identifierar strömmens slut när data strömmen returnerar **0** som läst.

* **RequestMetadata**: Metadata om tal förfrågan. Mer information finns i [referensen](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Tal förfrågan

När du har instansierat ett SpeechClient-och SpeechInput-objekt använder du RecognizeAsync för att göra en begäran till tal tjänsten.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

När begäran har slutförts slutförs uppgiften som returnerades av RecognizeAsync. Den sista RecognitionResult är slutet av igenkänningen. Aktiviteten kan Miss lyckas om tjänsten eller SDK: n Miss lyckas.

### <a name="speech-recognition-events"></a>Tal igenkännings händelser

#### <a name="partial-results-event"></a>Partiell resultat händelse

Den här händelsen anropas varje gång tal tjänsten förutsäger vad du kan säga, till och med innan du har slutfört tal ( `MicrophoneRecognitionClient`om du använder) eller Slutför sändningen av `DataRecognitionClient`data (om du använder). Du kan prenumerera på händelsen med hjälp av `SpeechClient.SubscribeToPartialResult()`. Du kan också använda prenumerations metoden `SpeechClient.SubscribeTo<RecognitionPartialResult>()`för allmänna händelser.

**Retur format** | Beskrivning |
------|------
**LexicalForm** | Det här formuläret är optimalt för användning av program som behöver RAW, obehandlade tal igenkännings resultat.
**DisplayText** | Den identifierade frasen med inversen text normalisering, versaler, interpunktion och svordomar tillämpas. Svordomar maskeras med asterisker efter det inledande-symbolen, till exempel "d * * *." Det här formuläret är optimalt för användning av program som visar tal igenkännings resultatet för en användare.
**Tillit** | Graden av exakthet som den identifierade frasen representerar för det associerade ljudet som definieras av tal igenkännings servern.
**MediaTime** | Den aktuella tiden i förhållande till starten av ljud strömmen (i 100 – parallella tidsenheter).
**MediaDuration** | Den aktuella frasens varaktighet/längd i förhållande till ljud segmentet (i 100 – parallella tidsenheter).

#### <a name="result-event"></a>Resultat händelse
När du har slutfört tal `ShortPhrase` (i läge) anropas den här händelsen. Du har fått de n bästa alternativen för resultatet. I `LongDictation` läget kan händelsen anropas flera gånger, baserat på var servern anger att meningen ska pausas. Du kan prenumerera på händelsen med hjälp av `SpeechClient.SubscribeToRecognitionResult()`. Du kan också använda prenumerations metoden `SpeechClient.SubscribeTo<RecognitionResult>()`för allmänna händelser.

**Retur format** | Beskrivning |
------|------|
**RecognitionStatus** | Status för hur igenkänningen genererades. Exempelvis har den skapats som ett resultat av lyckad igenkänning eller på grund av att anslutningen avbröts osv.
**Uttryck** | Uppsättningen av n-bäst identifierade fraser med igenkännings förtroendet.

Mer information om igenkännings resultat finns i [utdataformat](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Tal igenkännings svar

Exempel på tal svar:
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High)
```

## <a name="connection-management"></a>Anslutnings hantering

API: et använder en enda WebSocket-anslutning per begäran. För optimal användar upplevelse försöker SDK att återansluta till tal tjänsten och starta igenkänningen från den senaste RecognitionResult som den tog emot. Om ljudbegäran till exempel är två minuter lång, fick SDK: n en RecognitionEvent vid ett-minuters tecken, och ett nätverks fel uppstod efter fem sekunder, och en ny anslutning som börjar från en minut påbörjas av SDK: n.

>[!NOTE]
>SDK: n söker inte tillbaka till en-minuters markering eftersom data strömmen kanske inte stöder sökning. I stället behåller SDK en intern buffert som används för att buffra ljudet och rensar bufferten när den tar emot RecognitionResult-händelser.

## <a name="buffering-behavior"></a>Buffer-beteende

Som standard buffrar SDK ljud så att det kan återställas när ett nätverks avbrott inträffar. I ett scenario där det är bättre att ta bort ljudet förlorat när nätverket kopplas från och startar om anslutningen, är det bäst att inaktivera ljud buffring genom att ställa `EnableAudioBuffering` in i Preferences-objektet i. `false`

## <a name="related-topics"></a>Relaterade ämnen

[Microsoft Speech C# service Library-referens](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
