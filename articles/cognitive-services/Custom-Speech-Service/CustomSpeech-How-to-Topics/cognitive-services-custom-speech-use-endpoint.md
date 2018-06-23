---
title: Använda en anpassad tal slutpunkt med anpassade tal tjänsten i Azure | Microsoft Docs
description: Lär dig hur du använder en anpassad till text till tal-slutpunkt med anpassade tal tjänsten i kognitiva tjänster.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: d28065d7962ee660cafd4b3321abdd6a8f94abcb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351813"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Använda en anpassad till text till tal-slutpunkt
Du kan skicka begäranden till en Azure anpassad tal Service till text till tal-slutpunkt på ett liknande sätt som du kan i standardslutpunkten kognitiva Services tal. De här slutpunkterna är funktionellt identiska standardslutpunkterna för tal-API. Därför är samma funktioner som är tillgängliga via klientbiblioteket eller REST API för tal-API: T också tillgängligt för anpassade slutpunkten.

Slutpunkter som du skapar med den här tjänsten kan bearbeta olika antal samtidiga begäranden. Volymen är beroende av den prisnivå som är associerad med din prenumeration. Ett fel uppstår om för många begäranden tas emot. Den kostnadsfria nivån har en månatlig begränsning av förfrågningar.

Tjänsten förutsätter att data skickas i realtid. Om den skickas snabbare anses begäran körs förrän varaktigheten ljud i realtid har passerat.

> [!NOTE]
> Vi stöder den [nya webbsocketar](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) ännu. Om du planerar att använda web sockets med anpassade tal-slutpunkt följer du anvisningarna här.
>
> Den nya [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) stöd kommer snart. Om du planerar att anropa anpassade tal slutpunkten via HTTP, följer du anvisningarna här.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Skicka begäranden med hjälp av klientbiblioteket tal

Starta recognition klienten för att skicka begäranden till din anpassade slutpunkt med hjälp av klientbiblioteket tal. Använder klienten tal SDK från [NuGet](http://nuget.org/). Sök efter *taligenkänning*, och välj speech recognition paket från Microsoft för din plattform. Vissa exempelkod finns på [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows). Klienten tal SDK tillhandahåller en klassen **SpeechRecognitionServiceFactory**, som innehåller följande metoder:

  *   ```CreateDataClient(...)```: En klient som recognition data.
  *   ```CreateDataClientWithIntent(...)```: En data recognition klient med avsikt.
  *   ```CreateMicrophoneClient(...)```: En mikrofon recognition klient.
  *   ```CreateMicrophoneClientWithIntent(...)```: En mikrofon recognition klient med avsikt.

Mer detaljerad dokumentation finns på [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/home). Anpassad tal Tjänsteslutpunkter stöder samma SDK.

Data recognition klienten är lämpligt för taligenkänning från data, till exempel en fil eller någon annan ljud. Mikrofon recognition klienten är lämpligt för taligenkänning från mikrofonens. Användning av avsikt i antingen klienten kan returnera strukturerade avsiktshantering resultat från den [språk förstå Intelligent Service](https://www.luis.ai/) (THOMAS) om du har skapat ett THOMAS program för ditt scenario.

Alla fyra typer av klienter kan initieras på två sätt. Den första metoden använder standard kognitiva Services tal API. Det andra sättet kan du ange en URL som motsvarar din anpassade slutpunkt som skapats med anpassade tal-tjänsten.

Du kan till exempel skapa en **DataRecognitionClient** som skickar begäranden till en anpassad slutpunkt med hjälp av följande metod:

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

Den **your_subscriptionId** och **endpointURL** referera till nyckeln för prenumeration och sockets Webbadress, på den **distributionsinformation** sidan.

Den **AuthenticationUri** används för att ta emot en token från Autentiseringstjänsten. Den här URI måste anges separat, som visas i följande exempelkod.

Den här exempelkod visar hur du använder klient-SDK:

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
> När du använder **skapa** metoder i SDK, du måste ange prenumerations-ID för två gånger på grund av överbelastning av den **skapa** metoder.
>

Tjänsten anpassad tal använder två olika URL: er för kortform och långa. Både visas på den **distributioner** sidan. Använd rätt slutpunkts-URL för specifika formuläret du vill använda.

Mer information om anropar olika recognition klienterna med din egen slutpunkt, finns det [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) klass. Dokumentationen på den här sidan refererar till ljud modellen anpassning, men det gäller för alla slutpunkter har skapats med hjälp av anpassade tal-tjänsten.

## <a name="send-requests-by-using-the-speech-protocol"></a>Skicka begäranden via protokollet tal

Slutpunkter som visas för den [tal protokollet](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) är slutpunkter för öppen källa Web Socket tal-protokollet.

Endast officiella klientimplementeringen är för närvarande för [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Om du vill börja med exemplet som anges, gör följande ändringar i koden och bygga exemplet igen:

1. I _src\sdk\speech.browser\SpeechConnectionFactory.ts_, ersätter värden namnet ”wss://speech.platform.bing.com” med värdnamn som visas på informationssidan för din distribution. Infoga inte den fullständiga URI men bara *wss* protokollet schema och värdnamnet. Exempel:

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Ange den _recognitionMode_ parameter i _samples\browser\Samples.html_ enligt dina behov:
    * _RecognitionMode.Interactive_ stöder förfrågningar upp till 15 sekunder.
    * _RecognitionMode.Conversation_ och _RecognitionMode.Dictation_ (båda är likvärdiga i anpassade tal Service) support begär upp till 10 minuter.

3. Bygga exemplet med hjälp av ”gulp build” innan du använder den.

> [!NOTE]
> Kontrollera att du använder rätt URI: N för det här protokollet. Det nödvändiga schemat är *wss* (inte *http* som klienten protocol). 

Mer information finns i [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) dokumentation.

## <a name="send-requests-by-using-http"></a>Skicka begäranden via HTTP

En begäran skickades till din anpassade slutpunkt med hjälp av en HTTP post liknar skickar en begäran med HTTP kognitiva Services Bing tal-API: et. Ändra URL: en för att återspegla adressen för din egen distribution.

Det finns vissa begränsningar på förfrågningar som skickas via HTTP för både kognitiva Services tal slutpunkten och de anpassade slutpunkter har skapats med den här tjänsten. HTTP-begäran kan inte returnera ofullständiga resultat under igenkänningsprocessen. Dessutom begränsas varaktigheten för begäranden till 10 sekunder för ljud och 14 sekunder övergripande.

Följ samma process som du använder kognitiva Services tal-API: t för att skapa en post-begäran.

1. Hämta en åtkomst-token med ditt prenumerations-ID. Denna token krävs för att komma åt recognition slutpunkten. Det kan återanvändas i 10 minuter.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      Den **subscriptionId** ska vara inställd på prenumerations-ID som du använder för den här distributionen. Svaret är vanlig token som du behöver för nästa begäran.

2. Bokför ljud till slutpunkten genom att använda POST igen.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    Den **token** är den åtkomst-token som du fick med det föregående anropet. Den **https_endpoint** är full-adressen för din anpassade till text till tal-slutpunkt som visas på den **distributionsinformation** sidan.

Mer information om HTTP post parametrar och svarsformatet finns i [Microsoft kognitiva Services Bing tal HTTP API](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Skicka begäranden genom att använda Service-bibliotek
Biblioteket tjänsten gör det möjligt för din tjänst så att användning av Microsoft Speech skrivfel molnet för att konvertera talas till text i realtid, så att klientappen kan skicka ljud och ta emot partiella resultat tillbaka samtidigt och asynkront. Information om SDK-tjänsten kan hittas [här](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> När du använder tjänsten biblioteket du behöver ändra URI för auktoriseringsprovider i implementeringen av **IAuthorizationProvider** till https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>Nästa steg
* Noggrannhet kan förbättras med din [anpassade ljud modellen](cognitive-services-custom-speech-create-acoustic-model.md).
* Noggrannhet kan förbättras med en [anpassade språk modellen](cognitive-services-custom-speech-create-language-model.md).
