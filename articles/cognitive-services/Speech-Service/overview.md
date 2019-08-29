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
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 0aa4286d8cb630f221613bebd13f7ea722224ac6
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068849"
---
# <a name="what-are-the-speech-services"></a>Vad är Speech Services?

Tal tjänsterna är arbetskonton av tal-till-text, text till tal och tal översättning till en enda Azure-prenumeration. Det är enkelt att aktivera program, verktyg och enheter med [tal-SDK](speech-sdk-reference.md), [tal enheter SDK](https://aka.ms/sdsdk-quickstart)eller [REST API: er](rest-apis.md).

> [!IMPORTANT]
> Tal tjänster har ersatt API för Bing-taligenkänning, Translator Speech och Custom Speech. Se *instruktions guider > migrering* för instruktioner om migrering.

Dessa funktioner utgör Azures tal tjänster. Använd länkarna i den här tabellen för att lära dig mer om vanliga användnings fall för varje funktion eller Sök i API-referensen.

| Tjänsten | Funktion | Beskrivning | SDK | REST |
|---------|---------|-------------|-----|------|
| [Tal till text](speech-to-text.md) | Tal till text | Tal till text skickar ljud strömmar till text i real tid som dina program, verktyg eller enheter kan använda eller Visa. Använd tal-till-text med [language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) för att härleda användar avsikter från inställt tal och agera på röst kommandon. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch-avskrift](batch-transcription.md) | Med batch-avskrifter kan du använda asynkrona tal-till-text-avskrifter av stora data volymer. Det här är en REST-baserad tjänst som använder samma slut punkt som anpassning och modell hantering. | Nej | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Konversations avskrift](conversation-transcription-service.md) | Aktiverar tal igenkänning i real tid, högtalar-ID och diarization. Det är perfekt för att skriva över person möten med möjlighet att skilja på högtalare. | Ja | Nej |
| | [Skapa Custom Speech modeller](#customize-your-speech-experience) | Om du använder tal-till-text för igenkänning och avskriftering i en unik miljö kan du skapa och träna anpassade ljud-, språk-och uttals modeller för att hantera omgivande brus eller branschspecifika vokabulär. | Nej | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Text till tal](text-to-speech.md) | Text till tal | Text till tal konverterar inmatad text till mänskligt syntetiskt tal med [SSML (Speech syntes Markup Language)](text-to-speech.md#speech-synthesis-markup-language-ssml). Välj bland standard röster och neurala röster (se [språk stöd](language-support.md)). | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Skapa anpassade röster](#customize-your-speech-experience) | Skapa anpassade röst teckensnitt som är unika för ditt varumärke eller din produkt. | Nej | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Talöversättning](speech-translation.md) | Talöversättning | Tal översättning möjliggör real tids översättning av tal till dina program, verktyg och enheter i real tid. Använd den här tjänsten för tal-till-tal-och tal-till-text-översättning. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nej |
| [Röst-första virtuella assistenter](voice-first-virtual-assistants.md) | Röst-första virtuella assistenter | Anpassade virtuella assistenter som använder Azure Speech Services ger utvecklare möjlighet att skapa naturliga, mänskliga konversations gränssnitt för sina program och upplevelser. Robot Framework: s direkta rad tal kanal förbättrar dessa funktioner genom att tillhandahålla en samordnad, dirigerad start punkt till en kompatibel robot som möjliggör röst i, röst ut-interaktion med låg latens och hög tillförlitlighet. | [Ja](voice-first-virtual-assistants.md) | Nej |

## <a name="news-and-updates"></a>Nyheter och uppdateringar

Lär dig vad som är nytt med Azure Speech Services.

* 2019 augusti
  * **Ny självstudie**: [Röstaktivera din robot med Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
  * Har lagt till en ny tal [`chat`](speech-synthesis-markup.md#adjust-speaking-styles)format, `en-US-JessaNeural` för rösten. 
* 2019 juni
  * Utgivna tal SDK-1.6.0. En fullständig lista över uppdateringar, förbättringar och kända problem finns i [viktig information](releasenotes.md).
* Maj 2019 – dokumentationen är nu tillgänglig för [konversations](conversation-transcription-service.md)avskrifter, avskriftering av [samtals Center](call-center-transcription.md)och [röst-och första virtuella assistenter](voice-first-virtual-assistants.md).
* Maj 2019
  * Utgivna tal SDK-1.5.1. En fullständig lista över uppdateringar, förbättringar och kända problem finns i [viktig information](releasenotes.md).
  * Utgivna tal SDK-1.5.0. En fullständig lista över uppdateringar, förbättringar och kända problem finns i [viktig information](releasenotes.md).

## <a name="try-speech-services"></a>Testa tal tjänster

Vi erbjuder snabb starter i de flesta populära programmeringsspråk, som var utformade för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller de populäraste snabb starterna för varje funktion. Använd den vänstra navigeringen för att utforska fler språk och plattformar.

| Tal till text (SDK) | Text till tal (SDK) | Översättning (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [Java Script (webbläsare)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Tal till text och text till tal har också REST-slutpunkter och tillhör ande snabb starter.

När du har haft möjlighet att använda tal tjänsterna kan du prova vår självstudie som lär dig hur du kan identifiera avsikter från tal med hjälp av talet SDK och LUIS.

* [Självstudier: Identifiera avsikter från tal med talet SDK och LUIS.C#](how-to-recognize-intents-from-speech-csharp.md)
* [Självstudier: Röst möjliggör din robot med talet SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Självstudier: Bygg en kolv-app för att översätta text, analysera sentiment och syntetisera översatt text till tal, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Hämta exempelkod

Exempel kod finns på GitHub för var och en av Azure Speech Services. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning och arbeta med anpassade modeller. Använd dessa länkar om du vill visa SDK: er och REST-exempel:

* [Exempel på tal-till-text-text till tal och tal översättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch-avskrifts exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Text till tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Röst-första exempel på virtuella assistenter (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Anpassa din tal upplevelse

Azure Speech Services fungerar bra med inbyggda modeller, men du kanske vill anpassa och justera upplevelsen för din produkt eller miljö ytterligare. Anpassnings alternativ sträcker sig från akustisk modell justering till unika röst teckensnitt för ditt varumärke. När du har skapat en anpassad modell kan du använda den med valfri Azure Speech Services.

| Tjänst för taligenkänning | Plattform | Beskrivning |
|----------------|-------------|-------------|
| Tal till text | [Anpassat tal](https://aka.ms/customspeech) | Anpassa tal igenkännings modeller till dina behov och tillgängliga data. Övervinn hinder för taligenkänning som samtalsstil, vokabulär och bakgrundsljud. |
| Text till tal | [Anpassad röst](https://aka.ms/customvoice) | Bygg en igenkännings Bart, en-av-en-röst för dina text till tal-appar med dina inläsnings data tillgängliga. Du kan finjustera röst utmatningarna ytterligare genom att justera en uppsättning röst parametrar. |

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Tal enheter SDK](speech-devices-sdk.md)
* [REST API: Tal till text](rest-speech-to-text.md)
* [REST API: Text till tal](rest-text-to-speech.md)
* [REST API: Batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
