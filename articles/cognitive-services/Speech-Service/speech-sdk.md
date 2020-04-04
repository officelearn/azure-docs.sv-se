---
title: Om tal-SDK - Taltjänst
titleSuffix: Azure Cognitive Services
description: Strategiprogramutvecklingspaketet (SDK) visar många av taltjänstens funktioner, vilket gör det enklare att utveckla talaktiverade program.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: a2ff4a94c1b2941f645cd7032ef476d33dffdb00
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656529"
---
# <a name="about-the-speech-sdk"></a>Om Speech SDK

Strategiprogramutvecklingspaketet (SDK) exponerar många av taltjänstens funktioner för att ge dig möjlighet att utveckla talaktiverade program. Speech SDK finns på många programmeringsspråk och på alla plattformar.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Scenariofunktioner

Tal-SDK visar många funktioner från taltjänsten, men inte alla. Funktionerna i Tal-SDK associeras ofta med scenarier. Speech SDK är perfekt för både scenarier i realtid och i realtid, med hjälp av lokala enheter, filer, Azure-bloblagring och till och med indata- och utdataströmmar. När ett scenario inte kan uppnås med Tal-SDK letar du efter ett REST API-alternativ.

### <a name="speech-to-text"></a>Tal till text

[Tal-till-text](speech-to-text.md) (kallas även *taligenkänning)* transkriberar ljudströmmar till text som dina program, verktyg eller enheter kan använda eller visa. Använd tal-till-text med [Språkförståelse (LUIS)](../luis/index.yml) för att härleda användaravsikter från transkriberat tal och agera på röstkommandon. Använd [Talöversättning](speech-translation.md) för att översätta talinmatning till ett annat språk med ett enda samtal. Mer information finns i [Grunderna för tal till text](speech-to-text-basics.md).

### <a name="text-to-speech"></a>Text till tal

[Text-till-tal](text-to-speech.md) (även känd som *talsyntes*) omvandlar text till människoliknande syntetiserat tal. Indatatexten är antingen stränglitteraler eller med hjälp av [SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md) Mer information om standardröster eller neurala röster finns i [Text-till-talspråk och röststöd](language-support.md#text-to-speech).

### <a name="voice-assistants"></a>Röstassistenter

Röstassistenter som använder Speech SDK gör det möjligt för utvecklare att skapa naturliga, människoliknande konversationsgränssnitt för sina program och upplevelser. Röstassistenttjänsten ger snabb och tillförlitlig interaktion mellan en enhet och en assistent. Implementeringen använder Bot Frameworks direct line speech-kanal eller den integrerade tjänsten Custom Commands (Preview) för slutförande av aktiviteter. Dessutom kan röstassistenter skapas med hjälp av [Custom Voice Portal](https://aka.ms/customvoice) för att skapa en unik röstupplevelse.

#### <a name="keyword-spotting"></a>Sökordsspottning

Begreppet [sökordsspottning](speech-devices-sdk-create-kws.md) stöds i Tal-SDK. Keyword spotting är handlingen att identifiera ett sökord i tal, följt av en åtgärd när du hör sökordet. Till exempel skulle "Hey Cortana" aktivera Cortana-assistenten.

### <a name="meeting-scenarios"></a>Mötesscenarier

Speech SDK är perfekt för att transkribera mötesscenarier, oavsett om det kommer från en enda enhet eller konversation med flera enheter.

#### <a name="conversation-transcription"></a>Samtalstranskription

[Konversationsavskription](conversation-transcription.md) möjliggör taligenkänning i realtid (och asynkron), högtalaridentifiering och meningsattribution till varje högtalare (kallas även *diarisering*). Det är perfekt för att transkribera personliga möten med förmågan att skilja högtalare.

#### <a name="multi-device-conversation"></a>Konversation med flera enheter

Med [Konversation med flera enheter](multi-device-conversation.md)ansluter du flera enheter eller klienter i en konversation för att skicka talbaserade eller textbaserade meddelanden, med enkelt stöd för transkription och översättning.

### <a name="custom--agent-scenarios"></a>Anpassade / agentscenarier

Tal-SDK kan användas för att transkribera callcenter-scenarier, där telefonidata genereras.

#### <a name="call-center-transcription"></a>Avskrifter av kundtjänst

[Call Center Transkription](call-center-transcription.md) är vanligt scenario för tal-till-text för att transkribera stora volymer telefonidata som kan komma från olika system, till exempel Interaktiv röstsvar (IVR). De senaste taligenkänningsmodellerna från taltjänsten utmärker sig på att transkribera dessa telefonidata, även i de fall då data är svåra för en människa att förstå.

### <a name="codec-compressed-audio-input"></a>Codec komprimerad ljudingång

Flera av tal-SDK-programmeringsspråken stöder codec-komprimerade ljudingångsströmmar. Mer information finns i <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">använda komprimerade <span class="docon docon-navigate-external x-hidden-focus"> </span>ljudinmatningsformat </a>.

## <a name="rest-api"></a>REST-API

Tal-SDK täcker många funktioner i taltjänsten, men för vissa scenarier kanske du vill använda REST API.While the Speech SDK covers many feature capabilitis of the Speech Service, for some scenarios you might want to use the REST API. Som ett exempel exponeras slutpunktshantering endast via REST API.As a example, endpoint management is only exposed via the REST API.

> [!TIP]
> När du förlitar dig på REST API, använd Swagger Editor för att automatiskt generera klientbibliotek. Om du till exempel vill generera ett batchtranskriptionsklientbibliotek.
> 1. Kopiera exempel-URL:en nedan:
>     ```http
>     https://westus.cris.ai/docs/v2.0/swagger
>     ```
> 1. Navigera till <a href="https://editor.swagger.io/" target="_blank">Swagger <span class="docon docon-navigate-external x-hidden-focus"></span> Editor</a>
> 1. Välj URL **för filimport** > **Import URL** och klistra in URL:en
> 1. Välj **Generera klient** och välj önskat programmeringsspråk

### <a name="batch-transcription"></a>Batch-transkription

[Batch transkription](batch-transcription.md) möjliggör asynkron tal-till-text transkription av stora mängder data. Batch transkription är endast möjligt från REST API.

## <a name="customization"></a>Anpassning

Taltjänsten ger bra funktioner med standardmodeller över tal-till-text, text-till-tal och talöversättning. Ibland kanske du vill öka baslinjens prestanda för att fungera ännu bättre med ditt unika användningsfall. Taltjänsten har en mängd olika anpassningsverktyg utan kod som gör det enkelt och gör att du kan skapa en konkurrensfördel med anpassade modeller baserat på dina egna data. Dessa modeller är endast tillgängliga för dig och din organisation.

### <a name="custom-speech-to-text"></a>Anpassad tal-till-text

När du använder tal-till-text för igenkänning och transkription i en unik miljö kan du skapa och träna anpassade akustiska modeller, språk- och uttalsmodeller för att ta itu med omgivningsbrus eller branschspecifika ordförråd. Skapandet och hanteringen av no-code Custom Speech-modeller är tillgängligt via [Custom Speech Portal](https://aka.ms/customspeech). När custom speech-modellen har publicerats kan den förbrukas av Tal-SDK.

### <a name="custom-text-to-speech"></a>Anpassad text till tal

Anpassad text-till-tal, även känd som Custom Voice är en uppsättning online-verktyg som gör att du kan skapa en igenkännlig, one-of-a-kind röst för ditt varumärke. Skapandet och hanteringen av no-code Custom Voice-modeller är tillgängligt via [Custom Voice Portal](https://aka.ms/customvoice). När Custom Voice-modellen har publicerats kan den förbrukas av Speech SDK.

## <a name="get-the-speech-sdk"></a>Hämta tal-SDK

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[Macos](#tab/macos)

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
* [Se hur du känner igen tal i C #](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
