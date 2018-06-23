---
title: Kom igång med Microsoft Speech Recognition API med hjälp av C#-tjänstbibliotek | Microsoft Docs
description: Använd Microsoft speech recognition service library talas konvertera till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/17/2017
ms.author: zhouwang
ms.openlocfilehash: 0320f41658a7ac4d6bf9e88ed998c853b665d485
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352278"
---
# <a name="get-started-with-the-speech-recognition-service-library-in-c35-for-net-windows"></a>Kom igång med tal recognition service biblioteket i C&#35; för .NET Windows

Service-biblioteket är för utvecklare som har sina egna Molntjänsten och vill att anropa tjänsten tal från tjänsten. Använd inte detta SDK om du vill anropa tjänsten speech recognition från enheter-bunden appar. (Använda andra klientbibliotek eller REST API: er för den.)

Om du vill använda C#-tjänstbibliotek, installera den [NuGet-paketet Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Biblioteket API-referens, finns det [Microsoft tal C#-tjänstbibliotek](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

I följande avsnitt beskrivs hur du installerar, skapa och kör exempelprogrammet C# med hjälp av C#-tjänsten biblioteket.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

I följande exempel har utvecklats specifikt för Windows 8 + och .NET 4.5 + Framework med hjälp av [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Hämta exempelprogrammet

Klona prov från den [tal C# service library exempel](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) databasen.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på Speech Recognition API och få en kostnadsfri utvärderingsprenumeration nyckel

Dikterings-API: N är en del av kognitiva tjänster (tidigare projekt Oxford). Du kan hämta en kostnadsfri utvärderingsprenumeration nycklar från den [kognitiva abonnemang](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt tal-API, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar en primär och en sekundär nyckel. Båda nycklarna är knutna till samma kvoten, så du kan använda antingen nyckel.

> [!IMPORTANT]
> * Hämta en nyckel för prenumerationen. Innan du kan använda klientbibliotek tal, måste du ha en [prenumeration nyckeln](https://azure.microsoft.com/try/cognitive-services/).
>
> * Använd din prenumeration nyckel. Med angivna C# service library exempelprogrammet måste du ange din prenumeration nyckel som en av kommandoradsparametrar. Mer information finns i [kör exempelprogrammet](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Steg 1: Installera exempelprogrammet

1. Starta Visual Studio 2015 och välj **filen** > **öppna** > **projekt/lösning**.

2. Dubbelklicka för att öppna filen för Visual Studio 2015-lösning (.sln) som heter SpeechClient.sln. Lösningen öppnas i Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Steg 2: Skapa exempelprogrammet

Tryck på Ctrl + Skift + B eller välj **skapa** på menyn i menyfliksområdet. Välj sedan **skapa lösning**.

## <a name="step-3-run-the-sample-application"></a>Steg 3: Kör exempelprogrammet

1. När versionen är klar, tryck på F5 eller välj **starta** på menyn menyfliksområdet om du vill köra exemplet.

2. Öppna den angivna katalogen för, till exempel SpeechClientSample\bin\Debug. Tryck på SKIFT + höger musknapp och välj **här öppna kommandofönstret**.

3. Kör `SpeechClientSample.exe` med följande argument:

   * %D{arg/ [0]: Ange en inkommande WAV-ljudfil.
   * %D{arg/ [1]: Språkinställningen ljud.
   * %D{arg/ [2]: Ange recognition lägen: *kort* för den `ShortPhrase` läge och *lång* för den `LongDictation` läge.
   * %D{arg/ [3]: Ange nyckeln prenumeration för att komma åt tjänsten recognition tal.

## <a name="samples-explained"></a>Exempel förklaras

### <a name="recognition-modes"></a>Recognition lägen

* `ShortPhrase` läge: en utterance upp till 15 sekunder långt. När data skickas till servern, klienten tar emot flera ofullständiga resultat och slutlig bästa resultat.
* `LongDictation` läge: en utterance upp till 10 minuter lång tid. När data skickas till servern, klienten tar emot flera ofullständiga resultat och flera slutliga resultat baserat på där servern anger meningen pausar.

### <a name="supported-audio-formats"></a>Ljud format som stöds

Dikterings-API: et stöder ljud/WAV med hjälp av följande:

* PCM kanal
* Siren
* SirenSR

### <a name="preferences"></a>Inställningar

Du måste först skapa ett objekt för inställningar för att skapa en SpeechClient. Inställningar för objektet är en uppsättning parametrar som konfigurerar tjänsten tal. Det består av följande fält:

* `SpeechLanguage`: Språk för ljud som skickas till tjänsten tal.
* `ServiceUri`: Den slutpunkt som används för att anropa tjänsten tal.
* `AuthorizationProvider`: En IAuthorizationProvider-implementering som används för att hämta token för att komma åt tjänsten tal. Även om exemplet ger en kognitiva Services auktoriseringsprovider så rekommenderar vi starkt att du skapar egna implementering för att hantera tokencachelagring.
* `EnableAudioBuffering`: Ett avancerat alternativ. Se [anslutningshanteringen](#connection-management).

### <a name="speech-input"></a>Talindata

Objektet SpeechInput består av två fält:

* **Ljud**: en stream-implementering av önskat som SDK hämtar ljud. Det kan vara något [dataströmmen](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) som har stöd för läsning.
   > [!NOTE]
   > SDK identifierar slutet på dataströmmen när dataströmmen returnerar **0** i skrivskyddat.

* **RequestMetadata**: Metadata om begäran tal. Mer information finns i [referens](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Dikterings-begäran

När du har instansiera en SpeechClient och SpeechInput objekt, kan du använda RecognizeAsync och gör en begäran till tal-tjänsten.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

När begäran slutförs uppgiften som returneras av RecognizeAsync har slutförts. Senaste RecognitionResult är i slutet av redovisning. Uppgiften kan misslyckas om tjänsten eller SDK uppstår ett oväntat fel.

### <a name="speech-recognition-events"></a>Tal recognition händelser

#### <a name="partial-results-event"></a>Ofullständiga resultat händelse

Den här händelsen anropas varje gång tal Service förutsäger vad du kan säger, innan du har talat klart (om du använder `MicrophoneRecognitionClient`) eller har skickat data (om du använder `DataRecognitionClient`). Du kan prenumerera på händelsen med hjälp av `SpeechClient.SubscribeToPartialResult()`. Eller så kan du använda metoden allmänna händelser prenumeration `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Returnera format** | Beskrivning |
------|------
**LexicalForm** | Det här formuläret är optimalt för användning av program som behöver rådata, obearbetat tal resultat.
**Visningstext** | Frasen med omvänd text normalisering, versaler, skiljetecken och svordomar maskering tillämpas. Svordomar maskeras med asterisker efter det första tecknet, till exempel ”d ***”. Det här formuläret är optimalt för användning av program som visar igenkänningsresultat tal för en användare.
**Förtroende** | Konfidensnivån frasen representerar för associerade audio som definieras av speech recognition server.
**MediaTime** | Den aktuella tiden i förhållande till början av ljudström (i 100 nanosekunder tidsenheter).
**MediaDuration** | Den aktuella frasen varaktighet /-längden i förhållande till ljud-segment (i 100 nanosekunder tidsenheter).

#### <a name="result-event"></a>Resultatet händelse
När du har talat klart (i `ShortPhrase` läge), kallas för den här händelsen. Du har angett med n bästa val för resultatet. I `LongDictation` läge händelsen kan anropas flera gånger, baserat på där servern anger meningen pausar. Du kan prenumerera på händelsen med hjälp av `SpeechClient.SubscribeToRecognitionResult()`. Eller så kan du använda metoden allmänna händelser prenumeration `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Returnera format** | Beskrivning |
------|------|
**RecognitionStatus** | Status på hur redovisning tillhandahålls. Till exempel tillverkats den på grund av lyckade recognition eller på grund av du avbryter anslutningen, osv.
**Fraser** | Uppsättningen n bäst tolkade fraser tryggt igenkänning.

Mer information om resultat finns [utdataformat](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Tal recognition svar

Exempel för tal-svar:
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

## <a name="connection-management"></a>Hantering av anslutningar

API: N använder en WebSocket-anslutning per begäran. För bästa möjliga användarupplevelsen försöker SDK återansluta till tal-tjänsten och starta inte igenkänning från den senaste RecognitionResult som togs emot. Om ljud-begäran är två minuter lång SDK tog emot en RecognitionEvent på en minut märket och ett nätverksfel uppstod efter fem sekunder, exempelvis startar en ny anslutning som startar från en minut märket SDK.

>[!NOTE]
>SDK: N söka inte tillbaka till en minut märket eftersom dataströmmen inte stöder sökning. I stället håller SDK en intern buffert att den använder för att buffra ljuduppspelningen och rensar bufferten som den tar emot RecognitionResult händelser.

## <a name="buffering-behavior"></a>Buffringsbeteendet

Som standard buffrar SDK ljud så att den kan återställa när ett nätverk avbrott inträffar. I ett scenario där det är bättre att ignorera ljuduppspelningen förlorade under Koppla ifrån nätverket och starta om anslutningen är det bäst att inaktivera ljud buffring genom att ange `EnableAudioBuffering` i inställningar-objektet till `false`.

## <a name="related-topics"></a>Relaterade ämnen

[Biblioteksreferens för Microsoft tal C#-tjänsten](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
