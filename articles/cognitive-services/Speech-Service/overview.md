---
title: Vad är Azure Speech Services?
titleSuffix: Azure Cognitive Services
description: 'Azure Speech Services är för möjliggör tal till text, tal och talöversättning i en enda Azure-prenumeration. Det är enkelt att lägga till tal, program, verktyg och enheter med SDK för tal, tal Devices SDK eller REST API: er. Lägga till talfunktioner i en befintlig chattrobot, omvandla text till tal i en översättning program eller transkribera stora datavolymer anrop center.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: db32124cb4262745bcb2217224124ec444ccc97e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236843"
---
# <a name="what-are-the-speech-services"></a>Vad är Speech Services?

Azure Speech Services är med tal till text, tal och talöversättning – möjliggör till en enda Azure-prenumeration. Det är enkelt att tal kan dina program, verktyg och enheter med den [tal SDK](speech-sdk-reference.md), [tal Devices SDK](https://aka.ms/sdsdk-quickstart), eller [REST API: er](rest-apis.md).

> [!IMPORTANT]
> Taltjänster har ersatt API för Bing-tal och Talöversättning anpassat tal. Se *anvisningar hjälper > migrering* för migreringsanvisningar.

Dessa funktioner utgör Azure Speech Services. Använd länkarna i den här tabellen om du vill lära dig mer om vanliga användningsområden för varje funktion eller bläddra API-referens.

| Tjänst | Funktion | Beskrivning | SDK | REST |
|---------|---------|-------------|-----|------|
| [Speech-to-Text](speech-to-text.md) | Tal-till-text | Tal till text transkriberar ljudströmmar till text i realtid som dina program, verktyg eller enheter kan använda eller visa. Använd tal till text med [Språkförståelse (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) att härleda användaren avsikter från transkriberade tal och vidta åtgärder för röstkommandon. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch avskrift](batch-transcription.md) | Batch avskrift gör det möjligt för asynkron tal till text transkription av stora mängder data. Det här är en REST-baserad tjänst som använder samma slutpunkt som anpassning och modellhantering. | Nej | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Konversationen avskrift](conversation-transcription-service.md) | Möjliggör i realtid taligenkänning, talaridentifiering och diarization. Det är perfekt för att skriva av personligen möten med möjlighet att skilja talare. | Ja | Nej |
| | [Skapa anpassade talmodeller](#customize-your-speech-experience) | Om du använder tal till text för taligenkänning och taltranskription i en miljö kan du skapa och skapa anpassade akustiska, språk och uttal av modeller till adress omgivande ljuden eller branschspecifika ordförråd. | Nej | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Text till tal](text-to-speech.md) | Text-till-tal | Text till tal konverterar indata-text till människoliknande syntetiskt tal. Välj mellan standard röster och neural röster (se [språkstöd](language-support.md)). | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Skapa anpassade röster](#customize-your-speech-experience) | Skapa anpassade rösttyper unika för ditt varumärke eller din produkt. | Nej | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Talöversättning](speech-translation.md) | Talöversättning | Talöversättning gör i realtid, flera språk översättning av tal till dina program, verktyg och enheter. Använd den här tjänsten för tal-till-tal- och tal till text. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nej |
| [Röst första virtuella assistenter](voice-first-virtual-assistants.md) | Röst första virtuella assistenter | Anpassade virtuella assistenter med hjälp av Azure Speech Services gör att utvecklare kan skapa naturlig, människoliknande konversationsanpassade gränssnitt för sina program och upplevelser. I Bot Framework Direct Line tal kanal förbättrar funktionerna genom att tillhandahålla en samordnad, dirigerad startpunkten till en kompatibel robot som gör det möjligt för röst i röst ut interaktion med låg fördröjning och hög tillförlitlighet. | [Ja](voice-first-virtual-assistants.md) | Nej |

## <a name="news-and-updates"></a>Nyheter och uppdateringar

Läs mer om nyheterna med Azure Speech Services.

* Maj 2019 - dokumentation finns nu tillgänglig för [konversationen avskrift](conversation-transcription-service.md), [anropa Center avskrift](call-center-transcription.md), och [röst första virtuella assistenter](voice-first-virtual-assistants.md).
* Maj 2019 - släppte tal SDK 1.5.0. En fullständig lista över uppdateringar, förbättringar och kända problem finns i [viktig](releasenotes.md).
* April 2019 - är tal SDK 1.4.0 med stöd för text till tal (Beta) för C++, C#, och Java i Windows och Linux. Dessutom SDK stöder nu MP3- och Opus/Ogg ljudformat för C++ och C# i Linux. En fullständig lista över uppdateringar, förbättringar och kända problem finns i [viktig](releasenotes.md).
* Mars 2019 – är en ny slutpunkt för tal som returnerar en fullständig lista över röster som är tillgängliga i en viss region nu tillgänglig. Nya regioner stöds nu för text till tal. Mer information finns i [text till tal-API-referens (REST)](rest-text-to-speech.md).

## <a name="try-speech-services"></a>Försök Speech Services

Vi erbjuder snabbstarterna i mest populära programmeringsspråk, alla har utformats för att se dig köra kod i mindre än 10 minuter. Den här tabellen innehåller de mest populära Snabbstart för varje funktion. Utforska ytterligare språk och plattformar med hjälp av det vänstra navigeringsfältet.

| Tal till text (SDK) | Text till tal (SDK) | Translation (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (Browser)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Tal till text och text till tal har även REST-slutpunkter och associerade snabbstarter.

När du har haft möjlighet att använda Speech Services, prova vår självstudie som Lär dig hur du identifierar intentioner från tal med hjälp av tal SDK och LUIS.

* [Självstudie: Identifiera avsikter från tal med tal SDK och LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
* [Självstudie: Skapa en Flask-app för att översätta text, analysera sentiment och syntetisera översatt text till tal, REST](https://docs.microsoft.com/en-us/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Hämta exempelkod

Exempelkoden finns på GitHub för varje Azure Speech Services. De här exemplen omfattar vanliga scenarier som läsa ljud från en fil eller dataström, kontinuerliga och inleveransen erkännande och arbeta med anpassade modeller. Använd dessa länkar om du vill visa SDK och REST-exempel:

* [Tal till text och text till tal speech translation-exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch avskrift exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Text till tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>Anpassa upplevelsen tal

Azure Speech Services fungerar bra med inbyggda modeller, men du kanske vill att ytterligare anpassa och justera upplevelsen för din produkt eller miljö. Anpassning av alternativ för allt från akustisk modell justering till unika rösttyper för ditt varumärke. När du har skapat en anpassad modell kan använda du den med någon av Azure Speech Services.

| Tjänst för taligenkänning | Modell | Beskrivning |
|----------------|-------|-------------|
| Tal till text | [Akustisk modell](how-to-customize-acoustic-models.md) | Skapa en anpassad akustisk modell för program, verktyg, eller enheter som är särskilt används miljöer som i en bil eller på fabriksgolvet, var och en med specifika inspelning villkor. Exempel är accenttecken tal, specifika bakgrundsljud eller med en specifik mikrofon för inspelning. |
| | [Språkmodell](how-to-customize-language-model.md) | Skapa en anpassad språkmodell för att förbättra avskrift för fält-specifika ordförråd och grammatik, till exempel medicinsk terminologi eller IT-jargong. |
| | [Uttalsmodell](how-to-customize-pronunciation.md) | Du kan definiera fonetiska, formulär och visning av ett ord eller en term med en anpassad uttal-modell. Det är användbart för att hantera anpassade villkor, till exempel produktnamn eller förkortningar. Allt du behöver för att komma igång är en uttal-fil – en enkel txt-fil. |
| Text till tal | [Rösttyp](how-to-customize-voice-font.md) | Anpassade rösttyper kan du skapa en identifierbara, en av en typ ton för ditt varumärke. Det tar bara en liten mängd data för att komma igång. Ju mer data som du ger, mer naturliga och människoliknande din rösttyp kommer ljud. |

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Tal enheter SDK](speech-devices-sdk.md)
* [REST-API: Speech-to-text](rest-speech-to-text.md)
* [REST-API: Text till tal](rest-text-to-speech.md)
* [REST-API: Batch transkription och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skaffa en prenumerationsnyckel för Speech Services utan kostnad](get-started.md)
