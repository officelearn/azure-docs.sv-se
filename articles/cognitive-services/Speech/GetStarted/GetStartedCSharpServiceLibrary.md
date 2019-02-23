---
title: Kom igång med Microsoft för Taligenkänning med hjälp av C#-tjänstbibliotek | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använda tjänstbiblioteket taligenkänning för Bing-taligenkänning för att konvertera talspråk till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0f445d1fff48ee7a04c0b1c1d64c808f87d824b7
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673179"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Snabbstart: Använd Bing-taligenkänning igenkänning av tjänsten-biblioteket i C&#35; för .NET Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Tjänstbiblioteket är för utvecklare som har sina egna molntjänst och vill anropa Speech Service från sin tjänst. Om du vill att anropa tjänsten för taligenkänning från enheten-bunden appar, Använd inte denna SDK. (Använd andra klientbibliotek eller REST API: er för som.)

Om du vill använda C#-tjänstbibliotek, installera den [NuGet-paketet Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Biblioteket API-referens, finns det [Microsoft Speech C#-tjänstbibliotek](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

I följande avsnitt beskrivs hur du installerar, bygga och köra C#-exempelprogram med hjälp av C# service biblioteket.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

I följande exempel har utvecklats specifikt för Windows 8 + och .NET 4.5 + Framework med hjälp av [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Hämta exempelprogrammet

Klona exemplet från den [tal C#-biblioteket tjänstexemplet](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) lagringsplats.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på det tal-API: T och få en kostnadsfri utvärderingsprenumeration-nyckel

API för taligenkänning är en del av Cognitive Services (tidigare projekt Oxford). Du kan få kostnadsfri utvärderingsprenumeration nycklar från den [Cognitive Services-prenumeration](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt API för taligenkänning, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar den primära och sekundära nyckeln. Båda nycklarna är knutna till samma kvot, så du kan använda någon av nycklarna.

> [!IMPORTANT]
> * Få en prenumerationsnyckel. Innan du kan använda klientbibliotek tal, måste du ha en [prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/).
>
> * Använd din prenumerationsnyckel. Med den angivna C# service library exempelprogrammet måste du ange din prenumerationsnyckel som en av kommandoradsparametrar. Mer information finns i [kör exempelprogrammet](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Steg 1: Installera exempelprogrammet

1. Starta Visual Studio 2015 och välj **filen** > **öppna** > **projekt/lösning**.

2. Dubbelklicka för att öppna filen Visual Studio 2015-lösning (.sln) med namnet SpeechClient.sln. Lösningen öppnas i Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Steg 2: Skapa exempelprogrammet

Tryck på Ctrl + Skift + B eller välj **skapa** på menyn i menyfliksområdet. Välj sedan **skapa lösning**.

## <a name="step-3-run-the-sample-application"></a>Steg 3: Köra exempelprogrammet

1. När bygget har slutförts, tryck på F5 eller välj **starta** på menyn menyfliksområdet för att köra exemplet.

2. Öppna katalogen för exemplet, till exempel SpeechClientSample\bin\Debug. Tryck på SKIFT + höger Klicka och välj **här öppna kommandofönstret**.

3. Kör `SpeechClientSample.exe` med följande argument:

   * Arg[0]: Ange en inkommande ljudfil WAV.
   * Arg[1]: Ljud språkinställningen.
   * Arg[2]: Ange erkännande lägen: *Kort* för den `ShortPhrase` läge och *lång* för den `LongDictation` läge.
   * Arg[3]: Ange prenumerationsnyckeln för att komma åt tjänsten för taligenkänning.

## <a name="samples-explained"></a>Exempel beskrivs

### <a name="recognition-modes"></a>Igenkänning av lägen

* `ShortPhrase` läge: Ett uttryck upp till 15 sekunder långa. När data skickas till servern, klienten tar emot flera delresultat och ett sista bästa resultat.
* `LongDictation` läge: Ett uttryck upp till 10 minuter lång. När data skickas till servern, tar klienten emot flera delresultat och flera slutresultat baserat på var servern indikerar paus i meningen.

### <a name="supported-audio-formats"></a>Ljudformat som stöds

API för taligenkänning stöder audio/WAV med hjälp av följande:

* PCM en kanal
* Siren
* SirenSR

### <a name="preferences"></a>Inställningar

Om du vill skapa en SpeechClient, måste du först skapa ett objekt i inställningar. Inställningar för objektet är en uppsättning parametrar som konfigurerar speech-tjänsten. Det består av följande fält:

* `SpeechLanguage`: Språkversionen av ljudet skickas till taltjänsten.
* `ServiceUri`: Den slutpunkt som används för att anropa speech-tjänsten.
* `AuthorizationProvider`: En IAuthorizationProvider-implementering som används för att hämta token för att komma åt speech-tjänsten. Även om det innehåller en Cognitive Services-auktoriseringsprovider, rekommenderar vi att du skapar en egen implementering för att hantera tokencachelagring.
* `EnableAudioBuffering`: Ett avancerat alternativ. Se [anslutningshanteringen](#connection-management).

### <a name="speech-input"></a>Talindata

Objektet SpeechInput består av två fält:

* **Ljud**: En stream-implementering av valfri som SDK: N hämtar ljud. Det kan vara något [stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) som har stöd för läsning.
   > [!NOTE]
   > SDK: N identifierar slutet på strömmen när strömmen returnerar **0** i read.

* **RequestMetadata**: Metadata om tal-begäran. Mer information finns i den [referens](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Tal-begäran

När du har instansierats en SpeechClient och SpeechInput objekt, kan du använda RecognizeAsync och gör en begäran för Speech Service.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

När begäran har slutförts uppgiften som returneras av RecognizeAsync har slutförts. Senaste RecognitionResult är slut erkännande. Uppgiften kan misslyckas om tjänsten eller SDK uppstår ett oväntat fel.

### <a name="speech-recognition-events"></a>Tal igenkänning av händelser

#### <a name="partial-results-event"></a>Ofullständiga resultat händelse

Den här händelsen anropas varje gång Speech Service förutsäger vad du kan säger, även innan du slutför talar (om du använder `MicrophoneRecognitionClient`) eller har skickat data (om du använder `DataRecognitionClient`). Du kan prenumerera på händelsen med hjälp av `SpeechClient.SubscribeToPartialResult()`. Eller så kan du använda metoden allmänna händelser prenumeration `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Returnera formatet** | Beskrivning |
------|------
**LexicalForm** | Det här formuläret är optimalt för användning av program som behöver råa, obearbetade tal resultat.
**DisplayText** | Frasen med inverterade text normalisering, versaler, interpunktion och svordomar maskning tillämpas. Svordomar maskeras med asterisker efter det första tecknet, till exempel ”d ***”. Det här formuläret är optimalt för användning av program som visar igenkänningsresultat tal för en användare.
**förtroende** | Konfidensnivån frasen representerar för det associerade ljudet som definieras av tal igenkänning av servern.
**MediaTime** | Den aktuella tiden i förhållande till början av ljudström (i 100 nanosekunder tidsenheter).
**MediaDuration** | Aktuella frasen varaktighet/längden i förhållande till ljud-segment (i 100 nanosekunder tidsenheter).

#### <a name="result-event"></a>Resultatet händelse
När du är klar talar (i `ShortPhrase` läge), den här händelsen kallas. Du får se n bästa val för resultatet. I `LongDictation` läge, händelsen kan anropas flera gånger, baserat på var servern indikerar paus i meningen. Du kan prenumerera på händelsen med hjälp av `SpeechClient.SubscribeToRecognitionResult()`. Eller så kan du använda metoden allmänna händelser prenumeration `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Returnera formatet** | Beskrivning |
------|------|
**RecognitionStatus** | Status på hur erkännande tillhandahålls. Exempelvis har skapades till följd av lyckade igenkänning av eller på grund av avbryter anslutning osv.
**Fraser** | Uppsättningen n bästa erkända fraser tryggt erkännande.

Mer information om resultat finns i [utdataformat](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Tal igenkänning av svar

Tal svar exempel:
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

## <a name="connection-management"></a>Anslutningshanteringen

API: et använder en enda WebSocket-anslutning per begäran. För optimala användarupplevelse, SDK: erna försöker återansluta till Speech Service och starta erkännande från den senaste RecognitionResult som togs emot. Om ljud begäran är två minuter långa, SDK: N tog emot en RecognitionEvent vid position för en minut och ett nätverksfel uppstod när du har fem sekunder, exempelvis startar en ny anslutning som startar från en minut mark SDK.

>[!NOTE]
>SDK: N försöka inte tillbaka en minut mark eftersom strömmen inte kanske stöd för sökning. SDK håller i stället en intern buffert att den använder för att buffra ljudet och tar bort bufferten som den tar emot RecognitionResult händelser.

## <a name="buffering-behavior"></a>Buffringsbeteendet

Som standard buffrar SDK ljud så att den kan återställa när det uppstår ett avbrott i nätverket. I ett scenario där det är bättre att ignorera ljudet försvinner kopplas från nätverket och starta om anslutningen är det bäst att inaktivera ljud buffring genom att ange `EnableAudioBuffering` i inställningar-objektet till `false`.

## <a name="related-topics"></a>Relaterade ämnen

[Biblioteksreferens för Microsoft Speech C#-tjänst](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
