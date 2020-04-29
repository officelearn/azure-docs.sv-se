---
title: Om talet för tal-SDK – tal
titleSuffix: Azure Cognitive Services
description: Talfunktionerna Software Development Kit (SDK) visar många av funktionerna i tal tjänsten, vilket gör det enklare att utveckla tal aktiverade program.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f663c9af8c352d0170c633fe76b3fbc5268aad1e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399959"
---
# <a name="about-the-speech-sdk"></a>Om Speech SDK

Talfunktionerna Software Development Kit (SDK) visar många av funktionerna i tal tjänsten, så att du kan utveckla talbaserade program. Talet SDK är tillgängligt i många programmeringsspråk och på alla plattformar.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Scenario funktioner

Tal-SDK: n visar många funktioner från tal tjänsten, men inte alla. Funktionerna i tal-SDK: n är ofta kopplade till scenarier. Tal-SDK: n är idealisk för både real tids scenarier och icke-real tids scenarier, med hjälp av lokala enheter, filer, Azure Blob Storage och till och med indata och utgående data strömmar. När det inte går att nå ett scenario med talet SDK kan du leta efter ett REST API alternativ.

### <a name="speech-to-text"></a>Tal till text

[Tal till text](speech-to-text.md) (kallas även *tal igenkänning*) drar av ljud strömmar till text som dina program, verktyg eller enheter kan använda eller Visa. Använd tal-till-text med [language Understanding (Luis)](../luis/index.yml) för att härleda användar avsikter från inställt tal och agera på röst kommandon. Använd [tal översättning](speech-translation.md) för att översätta tal ingångar till ett annat språk med ett enda anrop. Mer information finns i [grunderna för tal till text](speech-to-text-basics.md).

### <a name="text-to-speech"></a>Text till tal

[Text till tal](text-to-speech.md) (kallas även *tal syntes*) konverterar text till humant syntetiskt tal. Inmatad text är antingen sträng litteraler eller med [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md). Mer information om standard-och neurala-röster finns i [text-till-tal-språk och röst stöd](language-support.md#text-to-speech).

### <a name="voice-assistants"></a>Röstassistenter

Röst assistenter som använder tal-SDK gör det möjligt för utvecklare att skapa naturliga, mänskliga konversations gränssnitt för sina program och upplevelser. Röst assistents tjänsten ger snabb och tillförlitlig interaktion mellan en enhet och en assistent. I implementeringen används bot-ramverkets direkta rad tal kanal eller den integrerade anpassade kommandona (för hands version) för att slutföra uppgiften. Dessutom kan röst assistenter skapas med hjälp av den [anpassade röst portalen](https://aka.ms/customvoice) för att skapa en unik röst upplevelse.

#### <a name="keyword-spotting"></a>Nyckelord upptäcka

Konceptet för [nyckelordet upptäcka](speech-devices-sdk-create-kws.md) stöds i tal-SDK: n. Nyckelordet upptäcka är en handling som identifierar ett nyckelord i tal, följt av en åtgärd för att höra nyckelordet. Till exempel "Hej Cortana" aktiverar Cortana-assistenten.

### <a name="meeting-scenarios"></a>Mötes scenarier

Tal-SDK är perfekt för att skriva över Mötes scenarier, oavsett om det är en enskild enhet eller en konversation med flera enheter.

#### <a name="conversation-transcription"></a>Samtalstranskription

[Konversations avskrift](conversation-transcription.md) möjliggör real tids (och asynkron) tal igenkänning, högtalar identifiering och menings tilldelning till varje talare (kallas även *diarization*). Det är perfekt för att skriva över person möten med möjlighet att skilja på högtalare.

#### <a name="multi-device-conversation"></a>Konversation med flera enheter

Med en [konversation med flera enheter](multi-device-conversation.md)ansluter du flera enheter eller klienter i en konversation för att skicka talbaserade eller textbaserade meddelanden, med enkel support för avskrifter och översättning.

### <a name="custom--agent-scenarios"></a>Anpassade/agent-scenarier

Tal-SDK: n kan användas för att skriva över samtals Center scenarier där telefoni data genereras.

#### <a name="call-center-transcription"></a>Avskrifter av kundtjänst

[Avskrift av samtals Center](call-center-transcription.md) är ett vanligt scenario för tal till text för att skriva över stora volymer av telefoni data som kan komma från olika system, till exempel interaktivt röst svar (IVR). De senaste tal igenkännings modellerna från tal tjänsten Excel vid inmatning av dessa telefoni data, även i fall då det är svårt för en mänsklig att förstå.

### <a name="codec-compressed-audio-input"></a>Codec-komprimerad ljud inspelning

Flera av programmeringsspråken för tal-SDK stöder codec-komprimerade ljud inspelnings strömmar. Mer information finns i <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">använda komprimerade ljud inspelnings <span class="docon docon-navigate-external x-hidden-focus"> </span>format </a>.

## <a name="rest-api"></a>REST-API

Även om tal-SDK: n täcker många funktioner i tal tjänsten kan du använda REST API för vissa scenarier.

### <a name="batch-transcription"></a>Batch-transkription

Med [batch-avskrifter](batch-transcription.md) kan du använda asynkrona tal-till-text-avskrifter av stora data volymer. Batch-avskrift är bara möjlig från REST API. Förutom att konvertera tal ljud till text, tillåter batch-tal-till-text också diarization och sentiment-analys.

## <a name="customization"></a>Anpassning

Tal tjänsten ger fantastiska funktioner med sina standard modeller i tal-till-text, text till tal och tal översättning. Ibland kanske du vill öka bas linje prestandan så att den fungerar ännu bättre med ditt unika användnings fall. Tal tjänsten har en rad olika anpassnings verktyg som gör det enkelt och ger dig möjlighet att skapa en konkurrens för del med anpassade modeller som baseras på dina egna data. Dessa modeller är bara tillgängliga för dig och din organisation.

### <a name="custom-speech-to-text"></a>Custom Speech till text

När du använder tal-till-text för igenkänning och avskriftering i en unik miljö kan du skapa och träna anpassade ljud-, språk-och uttals modeller för att hantera omgivande brus eller branschspecifika vokabulär. Det går inte att skapa och hantera Custom Speechs modeller utan kod på [Custom Speech portalen](https://aka.ms/customspeech). När Custom Speechs modellen har publicerats kan den användas av talet SDK.

### <a name="custom-text-to-speech"></a>Anpassad text till tal

Anpassad text till tal, även kallat anpassad röst är en uppsättning online-verktyg som du kan använda för att skapa en identifierbar och en-av-en-röst för ditt varumärke. Det går inte att skapa och hantera anpassade röst modeller som är tillgängliga via den [anpassade röst portalen](https://aka.ms/customvoice). När den anpassade röst modellen har publicerats kan den förbrukas av talet SDK.

## <a name="get-the-speech-sdk"></a>Hämta tal-SDK

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Webbläsare](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se känna igen tal i C #](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
