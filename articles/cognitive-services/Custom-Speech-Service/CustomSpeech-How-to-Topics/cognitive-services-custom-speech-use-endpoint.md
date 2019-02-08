---
title: Använda en anpassad slutpunkt med Custom Speech Service på Azure | Microsoft Docs
description: Lär dig hur du använder en anpassad tal till text-slutpunkt med Custom Speech Service i Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: nitinme
ms.openlocfilehash: 95d91b5bfea81b44aee2f6ae3be5ca2c8ad223ce
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55855955"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Använd en anpassad slutpunkt för tal till text

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Du kan skicka begäranden till en Azure Custom Speech Service tal till text-slutpunkt på ett liknande sätt som du kan i standardslutpunkten Cognitive Services tal. De här slutpunkterna är funktionellt identiska med standardslutpunkterna för API för taligenkänning. Därför finns även på samma sätt som är tillgänglig via klientbiblioteket eller REST-API för API för taligenkänning för din anpassade slutpunkt.

Slutpunkter som du skapar med hjälp av den här tjänsten kan bearbeta olika antal samtidiga begäranden. Volymen beror på prisnivå som är associerade med prenumerationen. Ett fel inträffar om för många begäranden tas emot. Den kostnadsfria nivån har en månatliga gräns för begäranden.

Tjänsten förutsätter att data överförs i realtid. Om det har skickats snabbare, anses begäran köras tills dess ljud varaktighet i realtid har passerat.

> [!NOTE]
> Vi stöder den [nya webbsockets](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) ännu. Om du planerar att använda webbsockets med din slutpunkt för anpassat tal, följer du instruktionerna här.
>
> Den nya [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) stöd kommer snart. Om du planerar att anropa din slutpunkt för anpassat tal via HTTP, följer du instruktionerna här.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Skicka begäranden med hjälp av talklientbiblioteket

Starta klienten för att skicka begäranden till din anpassade slutpunkt med hjälp av talklientbiblioteket. Använd klienten tal SDK från [NuGet](http://nuget.org/). Sök efter *taligenkänning*, och välj det tal igenkänning av paketet från Microsoft för din plattform. Exempelkod finns på [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows). Klient-SDK för taligenkänning erbjuder en klass för factory **SpeechRecognitionServiceFactory**, där du får följande metoder:

  *   ```CreateDataClient(...)```: En klient för igenkänning av data.
  *   ```CreateDataClientWithIntent(...)```: En data för klient med avsikt.
  *   ```CreateMicrophoneClient(...)```: En mikrofon igenkänning av klient.
  *   ```CreateMicrophoneClientWithIntent(...)```: En mikrofon igenkänning av klient med avsikt.

Detaljerad dokumentation finns i den [taligenkänning för Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home). Custom Speech Service-slutpunkterna stöder samma SDK.

Det är lämpligt för taligenkänning från data, till exempel en fil eller annan ljudkälla data igenkänning av klienten. Mikrofon igenkänning av klienten är lämpligt för taligenkänning från mikrofonen. Användning av avsikt i antingen klienten kan returnera strukturerade avsikt resultat från den [Language Understanding Intelligent Service](https://www.luis.ai/) (LUIS), om du har skapat en LUIS-program för ditt scenario.

Alla fyra typer av klienter kan skapas på två sätt. Den första metoden använder standard Cognitive Services API för taligenkänning. Det andra sättet kan du ange en URL som motsvarar din anpassade slutpunkt som skapats med Custom Speech Service.

Du kan till exempel skapa en **DataRecognitionClient** som skickar begäranden till en anpassad slutpunkt med hjälp av följande metod:

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

Den **your_subscriptionId** och **endpointURL** avser prenumerationsnyckeln och URL: en för web sockets, på den **distributionsinformation** sidan.

Den **AuthenticationUri** används för att ta emot en token från Autentiseringstjänsten. URI: N måste anges separat, enligt följande exempelkod.

Den här exempelkoden visar hur du använder klient-SDK:

```csharp
var dataClient = SpeechRecognitionServiceFactory.CreateDataClient(
  SpeechRecognitionMode.LongDictation,
  "en-us",
  "your_subscriptionId",
  "your_subscriptionId",
  "endpointURL");
// set the authorization Uri
dataClient.AuthenticationUri = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
```

> [!NOTE]
> När du använder **skapa** metoder i SDK, måste du ange prenumerations-ID två gånger på grund av överbelastning av de **skapa** metoder.
>

Custom Speech Service använder två olika URL: er för igenkänning av korta former och långa. Båda visas på den **distributioner** sidan. Använd rätt slutpunkts-URL för specifika formuläret du vill använda.

Mer information om hur du anropar olika igenkänning av klienterna med din anpassade slutpunkt finns i den [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) klass. Dokumentation om den här sidan hänvisar till akustisk modellanpassning, men den gäller för alla slutpunkter som skapats med hjälp av Custom Speech Service.

## <a name="send-requests-by-using-the-speech-protocol"></a>Skicka begäranden med hjälp av tal-protokollet

Slutpunkter som visas för den [tal protokollet](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) är slutpunkter för öppen källkod Web Socket tal-protokollet.

Endast officiella klientimplementeringen är för närvarande för [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Om du vill börja med exemplet som anges där, gör följande ändringar i koden och skapa exemplet igen:

1. I _src\sdk\speech.browser\SpeechConnectionFactory.ts_, ersätta värden namnet ”wss://speech.platform.bing.com” med värdnamn som visas på sidan för din distribution. Infoga inte den fullständiga URI men bara *wss* protokollet schema och värdnamnet. Exempel:

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Ange den _recognitionMode_ parameter i _samples\browser\Samples.html_ baserat på dina krav:
    * _RecognitionMode.Interactive_ stöder begär upp till 15 sekunder.
    * _RecognitionMode.Conversation_ och _RecognitionMode.Dictation_ (båda är motsvarande Custom Speech Service) supportförfrågningar upp till 10 minuter.

3. Skapa exemplet med hjälp av ”gulp build” innan du använder den.

> [!NOTE]
> Se till att du använder rätt URI: N för det här protokollet. Följande nödvändiga schema *wss* (inte *http* som klienten protocol). 

Mer information finns i den [taligenkänning för Bing](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) dokumentation.

## <a name="send-requests-by-using-http"></a>Skicka förfrågningar via HTTP

Skicka en begäran till din anpassade slutpunkt med hjälp av en HTTP post liknar skickar en begäran från HTTP till Cognitive Services Bing-Taligenkänning. Ändra URL: en för att återspegla adressen för din anpassade distribution.

Det finns vissa begränsningar för förfrågningar som skickas via HTTP för både Cognitive Services tal slutpunkten och de anpassade slutpunkter som skapats med den här tjänsten. HTTP-begäran kan inte returnera ofullständiga resultat under igenkänning av processen. Varaktigheten för begäranden är dessutom begränsad till 10 sekunder tills ljudinnehållet och 14 sekunder övergripande.

Följ samma process som du använder för Cognitive Services tal-API för att skapa en post-begäran.

1. Hämta en åtkomsttoken med hjälp av ditt prenumerations-ID. Den här token krävs för att få åtkomst till slutpunkt för taligenkänning. Det kan återanvändas i 10 minuter.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      Den **subscriptionId** ska vara inställd på prenumerations-ID som du använder för den här distributionen. Svaret är en vanlig token som du behöver för nästa begäran.

2. Publicera ljud till slutpunkten med hjälp av POST igen.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    Den **token** är den åtkomsttoken som du fick med det föregående anropet. Den **https_endpoint** är den fullständiga adressen för din anpassade tal till text-slutpunkt som visas på den **distributionsinformation** sidan.

Mer information om HTTP post-parametrar och svarsformat finns i den [Microsoft Cognitive Services Bing Speech HTTP API](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Skicka begäranden med hjälp av tjänsten-biblioteket
Tjänstbiblioteket möjliggör tjänsten att använda Microsoft Speech avskrift molnet för att konvertera talspråk till text i realtid, så att din klientapp kan skicka ljud och ta emot delvisa igenkänningsresultat tillbaka samtidigt och asynkront. Information för SDK finns [här](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> När du använder Tjänstbiblioteket du behöva ändra URI för auktoriseringsprovider i implementeringen av **IAuthorizationProvider** till https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>Nästa steg
* Förbättra noggrannheten med din [anpassad akustisk modell](cognitive-services-custom-speech-create-acoustic-model.md).
* Förbättra noggrannheten med en [anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md).
