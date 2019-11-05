---
title: Vad är Speech Services?
titleSuffix: Azure Cognitive Services
description: 'Tal tjänsterna är arbetskonton av tal-till-text, text till tal och tal översättning till en enda Azure-prenumeration. Det är enkelt att lägga till tal för program, verktyg och enheter med tal-SDK, tal enheter SDK eller REST API: er. Lägg till talfunktioner i en befintlig chatt-robot, konvertera text till tal i ett översättnings program eller Skriv över stora volymer av Call Center-data.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/05/2019
ms.author: erhopf
ms.openlocfilehash: 46e9182d9660729dbf65367f52483d96428760ff
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467404"
---
# <a name="what-are-the-speech-services"></a>Vad är Speech Services?

Tal tjänsterna är arbetskonton av tal-till-text, text till tal och tal översättning till en enda Azure-prenumeration. Det är enkelt att aktivera program, verktyg och enheter med [tal-SDK](speech-sdk-reference.md), [tal enheter SDK](https://aka.ms/sdsdk-quickstart)eller [REST API: er](rest-apis.md).

> [!IMPORTANT]
> Tal tjänster har ersatt API för Bing-taligenkänning, Translator Speech och Custom Speech. Se *instruktions guider > migrering* för instruktioner om migrering.

Dessa funktioner utgör Azures tal tjänster. Använd länkarna i den här tabellen för att lära dig mer om vanliga användnings fall för varje funktion eller Sök i API-referensen.

| Tjänst | Funktion | Beskrivning | SDK | REST |
|---------|---------|-------------|-----|------|
| [Tal till text](speech-to-text.md) | Tal till text | Tal till text skickar ljud strömmar till text i real tid som dina program, verktyg eller enheter kan använda eller Visa. Använd tal-till-text med [language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) för att härleda användar avsikter från inställt tal och agera på röst kommandon. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch-avskrift](batch-transcription.md) | Med batch-avskrifter kan du använda asynkrona tal-till-text-avskrifter av stora data volymer. Det här är en REST-baserad tjänst som använder samma slut punkt som anpassning och modell hantering. | Nej | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Konversations avskrift](conversation-transcription-service.md) | Aktiverar tal igenkänning i real tid, högtalar-ID och diarization. Det är perfekt för att skriva över person möten med möjlighet att skilja på högtalare. | Ja | Nej |
| | [Skapa Custom Speech modeller](#customize-your-speech-experience) | Om du använder tal-till-text för igenkänning och avskriftering i en unik miljö kan du skapa och träna anpassade ljud-, språk-och uttals modeller för att hantera omgivande brus eller branschspecifika vokabulär. | Nej | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Text till tal](text-to-speech.md) | Text till tal | Text till tal konverterar inmatad text till mänskligt syntetiskt tal med [SSML (Speech syntes Markup Language)](text-to-speech.md#speech-synthesis-markup-language-ssml). Välj bland standard röster och neurala röster (se [språk stöd](language-support.md)). | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Skapa anpassade röster](#customize-your-speech-experience) | Skapa anpassade röst teckensnitt som är unika för ditt varumärke eller din produkt. | Nej | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Talöversättning](speech-translation.md) | Talöversättning | Tal översättning möjliggör real tids översättning av tal till dina program, verktyg och enheter i real tid. Använd den här tjänsten för tal-till-tal-och tal-till-text-översättning. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nej |
| [Röst assistenter](voice-assistants.md) | Röst assistenter | Röst assistenter som använder Azure Speech Services ger utvecklare möjlighet att skapa naturliga, mänskliga konversations gränssnitt för sina program och upplevelser. Röst assistents tjänsten ger snabb, tillförlitlig interaktion mellan en enhet och en assistent implementering som använder bot Framework: s direkta rad tal kanal eller den integrerade anpassade kommandona (för hands version) för att slutföra uppgiften. | [Ja](voice-assistants.md) | Nej |

## <a name="news-and-updates"></a>Nyheter och uppdateringar

Lär dig vad som är nytt med Azure Speech Services.

* September 2019
  * Utgivna tal SDK-1.7.0. En fullständig lista över uppdateringar, förbättringar och kända problem finns i [viktig information](releasenotes.md).
* Augusti 2019
  * **Ny självstudie**: [röst aktivera din robot med talet SDK, C# ](tutorial-voice-enable-your-bot-speech-sdk.md)
  * Du har lagt till en ny tal format [`chat`](speech-synthesis-markup.md#adjust-speaking-styles)för `en-US-JessaNeural` rösten. 
* Juni 2019
  * Utgivna tal SDK-1.6.0. En fullständig lista över uppdateringar, förbättringar och kända problem finns i [viktig information](releasenotes.md).
* Maj 2019 – dokumentation är nu tillgänglig för [konversations avskrift](conversation-transcription-service.md), meddelande om [samtals Center](call-center-transcription.md)och [röst assistenter](voice-assistants.md).
* Maj 2019
  * Utgivna tal SDK-1.5.1. En fullständig lista över uppdateringar, förbättringar och kända problem finns i [viktig information](releasenotes.md).
  * Utgivna tal SDK-1.5.0. En fullständig lista över uppdateringar, förbättringar och kända problem finns i [viktig information](releasenotes.md).

## <a name="try-speech-services"></a>Testa tal tjänster

Vi erbjuder snabb starter i de flesta populära programmeringsspråk, som var utformade för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller de populäraste snabb starterna för varje funktion. Använd den vänstra navigeringen för att utforska fler språk och plattformar.

| Tal till text (SDK) | Text till tal (SDK) | Översättning (SDK) |
|----------------------|----------------------|-------------------|
| [Identifiera tal från en ljudfil](quickstarts/speech-to-text-from-file.md) | [Syntetisera tal i en ljudfil](quickstarts/text-to-speech-audio-file.md) | [Översätt tal till text](quickstarts/translate-speech-to-text.md) |
| [Identifiera tal med en mikrofon](quickstarts/speech-to-text-from-microphone.md) | [Syntetisera tal till en talare](quickstarts/text-to-speech.md) | [Översätt tal till flera mål språk](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Identifiera tal som lagras i Blob Storage](quickstarts/from-blob.md) | [Asynkron syntes för lång Forms ljud](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Översätt tal till tal](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Tal till text och text till tal har också REST-slutpunkter och tillhör ande snabb starter.

När du har haft möjlighet att använda tal tjänsterna kan du prova vår självstudie som lär dig hur du kan identifiera avsikter från tal med hjälp av talet SDK och LUIS.

* [Självstudie: identifiera avsikter från tal med talet SDK och LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
* [Självstudie: röst aktivera din robot med talet SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Självstudie: Bygg en kolv-app för att översätta text, analysera sentiment och syntetisera översatt text till tal, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Hämta exempelkod

Exempel kod finns på GitHub för var och en av Azure Speech Services. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning och arbeta med anpassade modeller. Använd dessa länkar om du vill visa SDK: er och REST-exempel:

* [Exempel på tal-till-text-text till tal och tal översättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch-avskrifts exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Text till tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Röst assistents exempel (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Anpassa din tal upplevelse

Azure Speech Services fungerar bra med inbyggda modeller, men du kanske vill anpassa och justera upplevelsen för din produkt eller miljö ytterligare. Anpassnings alternativ sträcker sig från akustisk modell justering till unika röst teckensnitt för ditt varumärke. När du har skapat en anpassad modell kan du använda den med valfri Azure Speech Services.

| Tjänst för taligenkänning | Plattform | Beskrivning |
|----------------|-------------|-------------|
| Tal till text | [Custom Speech](https://aka.ms/customspeech) | Anpassa tal igenkännings modeller till dina behov och tillgängliga data. Övervinn hinder för taligenkänning som samtalsstil, vokabulär och bakgrundsljud. |
| Text till tal | [Anpassad röst](https://aka.ms/customvoice) | Bygg en igenkännings Bart, en-av-en-röst för dina text till tal-appar med dina inläsnings data tillgängliga. Du kan finjustera röst utmatningarna ytterligare genom att justera en uppsättning röst parametrar. |

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Tal enheter SDK](speech-devices-sdk.md)
* [REST API: tal till text](rest-speech-to-text.md)
* [REST API: text till tal](rest-text-to-speech.md)
* [REST API: batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
