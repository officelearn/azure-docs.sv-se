---
title: Vad är Speech Service?
titleSuffix: Azure Cognitive Services
description: Taltjänsten är enandet av tal-till-text- och text-till-tal- och talöversättning till en enda Azure-prenumeration. Lägg till tal i dina program, verktyg och enheter med Tal-SDK-, Talenheter SDK- eller REST-API:erna.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 464e21caf3105b12f0fbf44dc2aa3674a252d3c6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401025"
---
# <a name="what-is-the-speech-service"></a>Vad är Speech Service?

Taltjänsten är enandet av tal-till-text, text-till-tal och talöversättning till en enda Azure-prenumeration. Det är enkelt att ta till ett tal med dina program, verktyg och enheter med [Tal-SDK-,](speech-sdk-reference.md) [Talenheter SDK-](https://aka.ms/sdsdk-quickstart)eller [REST-API:er](rest-apis.md).

> [!IMPORTANT]
> Taltjänsten har ersatt Bing Speech API och Translator Speech. Se _Instruktioner > migreringsinstruktioner._

Dessa funktioner utgör taltjänsten. Använd länkarna i den här tabellen om du vill veta mer om vanliga användningsfall för varje funktion eller bläddra i API-referensen.

| Tjänst | Funktion | Beskrivning | SDK | REST |
|---------|---------|-------------|-----|------|
| [Tal-till-text](speech-to-text.md) | Tal-till-text i realtid | Tal-till-text transkriberar eller översätter ljudströmmar eller lokala filer till text i realtid som dina program, verktyg eller enheter kan använda eller visa. Använd tal-till-text med [Språkförståelse (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) för att härleda användaravsikter från transkriberat tal och agera på röstkommandon. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch tal-till-text](batch-transcription.md) | Batch Tal-till-text möjliggör asynkron tal-till-text transkription av stora volymer talljuddata som lagras i Azure Blob Storage. Förutom att konvertera talljud till text, batch tal-till-text möjliggör också diarisering och sentiment-analys. | Inga | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Konversation med flera enheter](multi-device-conversation.md) | Anslut flera enheter eller klienter i en konversation för att skicka tal- eller textbaserade meddelanden, med enkelt stöd för transkription och översättning| Ja | Inga |
| | [Konversation Transkription](conversation-transcription-service.md) | Möjliggör taligenkänning i realtid, högtalaridentifiering och diarisering. Det är perfekt för att transkribera personliga möten med förmågan att skilja högtalare. | Ja | Inga |
| | [Skapa anpassade talmodeller](#customize-your-speech-experience) | Om du använder tal-till-text för igenkänning och transkription i en unik miljö kan du skapa och träna anpassade akustiska, språkliga och uttalsmodeller för att ta itu med omgivningsbuller eller branschspecifika ordförråd. | Inga | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Text-till-tal](text-to-speech.md) | Text till tal | Text-till-tal konverterar indatatext till människoliknande syntetiserat tal med hjälp av [SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md) Välj mellan standardröster och neurala röster (se [Språkstöd).](language-support.md) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Skapa anpassade röster](#customize-your-speech-experience) | Skapa anpassade röstteckensnitt som är unika för ditt varumärke eller din produkt. | Inga | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Talöversättning](speech-translation.md) | Talöversättning | Talöversättning möjliggör översättning i realtid av tal till dina program, verktyg och enheter i realtid. Använd den här tjänsten för tal-till-tal- och tal-till-text-översättning. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Inga |
| [Röstassistenter](voice-assistants.md) | Röstassistenter | Röstassistenter som använder taltjänsten ger utvecklare möjlighet att skapa naturliga, människoliknande konversationsgränssnitt för sina program och upplevelser. Röstassistenttjänsten ger snabb och tillförlitlig interaktion mellan en enhet och en assistentimplementering som använder Bot Frameworks direct line speech-kanal eller den integrerade tjänsten Custom Commands (Preview) för slutförande av uppgifter. | [Ja](voice-assistants.md) | Inga |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Prova taltjänsten

Vi erbjuder snabbstarter på de flesta populära programmeringsspråk, var och en utformad för att få dig att köra kod på mindre än 10 minuter. Den här tabellen innehåller de mest populära snabbstarterna för varje funktion. Använd vänsternavigering för att utforska ytterligare språk och plattformar.

| Tal till text (SDK) | Text-till-tal (SDK) | Översättning (SDK) |
|----------------------|----------------------|-------------------|
| [Identifiera tal från en ljudfil](quickstarts/speech-to-text-from-file.md) | [Syntetisera tal till en ljudfil](quickstarts/text-to-speech-audio-file.md) | [Översätta tal till text](quickstarts/translate-speech-to-text.md) |
| [Identifiera tal med en mikrofon](quickstarts/speech-to-text-from-microphone.md) | [Syntetisera tal till en högtalare](quickstarts/text-to-speech.md) | [Översätta tal till flera målspråk](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Identifiera tal som lagras i bloblagring](quickstarts/from-blob.md) | [Asynkron syntes för dikteringsljud](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Översätta tal-till-tal](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Tal-till-text och text-till-tal har också REST-slutpunkter och tillhörande snabbstarter.

När du har haft en chans att använda taltjänsten kan du prova vår handledning som lär dig hur du känner igen avsikter från tal med tal-SDK och LUIS.

- [Självstudiekurs: Känna igen avsikter från tal med tal SDK och LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Självstudiekurs: Röst aktivera din bot med Tal SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Självstudiekurs: Skapa en flaskapp för att översätta text, analysera sentiment och syntetisera översatt text till tal, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Hämta exempelkod

Exempelkod är tillgänglig på GitHub för taltjänsten. Dessa exempel täcker vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel-shot erkännande, och arbeta med anpassade modeller. Använd dessa länkar för att visa SDK- och REST-exempel:

- [Exempel på tal-till-text, text-till-tal och talöversättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Prov för batchutskrift (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Text-till-tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Exempel på röstassistenter (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Anpassa din talupplevelse

Taltjänsten fungerar bra med inbyggda modeller, men du kanske vill anpassa och justera upplevelsen för din produkt eller miljö ytterligare. Anpassningsalternativen sträcker sig från akustisk modelljustering till unika röstteckensnitt för ditt varumärke.

| Tjänst för taligenkänning | Plattform | Beskrivning |
| -------------- | -------- | ----------- |
| Tal till text | [Anpassat tal](https://aka.ms/customspeech) | Anpassa taligenkänningsmodeller efter dina behov och tillgängliga data. Övervinna hinder för taligenkänning som talstil, ordförråd och bakgrundsljud. |
| Text till tal | [Anpassad röst](https://aka.ms/customvoice) | Skapa en identifierbar, unik röst för dina text-till-tal-appar med dina tillgängliga taldata. Du kan finjustera röstutgångarna ytterligare genom att justera en uppsättning röstparametrar. |

## <a name="reference-docs"></a>Referensdokument

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Tal-till-text](rest-speech-to-text.md)
- [REST API: Text-till-tal](rest-text-to-speech.md)
- [REST API: Batch transkription och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skaffa en prenumerationsnyckel för taltjänsten gratis](get-started.md)
