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
ms.date: 04/03/2019
ms.author: erhopf
ms.openlocfilehash: 61f22568aa6e6cf04963b40ad7c47163e87b9800
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460498"
---
# <a name="what-are-the-speech-services"></a>Vad är Speech Services?

Azure Speech Services är med tal till text, tal och talöversättning – möjliggör till en enda Azure-prenumeration. Det är enkelt att tal kan dina program, verktyg och enheter med den [tal SDK](speech-sdk-reference.md), [tal Devices SDK](speech-devices-sdk-qsg.md), eller [REST API: er](rest-apis.md).

> [!IMPORTANT]
> Taltjänster har ersatt API för Bing-tal och Talöversättning anpassat tal. Se *anvisningar hjälper > migrering* för migreringsanvisningar.

Dessa funktioner utgör Azure Speech Services. Använd länkarna i den här tabellen om du vill lära dig mer om vanliga användningsområden för varje funktion eller bläddra API-referens.

| Tjänst | Funktion | Beskrivning | SDK | REST |
|---------|---------|-------------|-----|------|
| [Speech-to-Text](speech-to-text.md) | Tal till text | Tal till text transkriberar ljudströmmar till text i realtid som dina program, verktyg eller enheter kan använda eller visa. Använd tal till text med [Språkförståelse (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) att härleda användaren avsikter från transkriberade tal och vidta åtgärder för röstkommandon. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch avskrift](batch-transcription.md) | Batch avskrift gör det möjligt för asynkron tal till text transkription av stora mängder data. Det här är en REST-baserad tjänst som använder samma slutpunkt som anpassning och modellhantering. | Nej | [Ja](https://westus.cris.ai/swagger/ui/index) |
| | [Anpassning](#customize-your-speech-experience) | Om du använder tal till text för taligenkänning och taltranskription i en miljö kan du skapa och skapa anpassade akustiska, språk och uttal av modeller till adress omgivande ljuden eller branschspecifika ordförråd. | Nej | [Ja](https://westus.cris.ai/swagger/ui/index) |
| [Text till tal](text-to-speech.md) | Text till tal | Text till tal konverterar indata-text till människoliknande syntetiskt tal. Välj mellan standard röster och neural röster (se [språkstöd](language-support.md)). | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Anpassning](#customize-your-speech-experience) | Skapa anpassade rösttyper unika för ditt varumärke eller din produkt. | Nej | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Talöversättning](speech-translation.md) | Talöversättning | Talöversättning gör i realtid, flera språk översättning av tal till dina program, verktyg och enheter. Använd den här tjänsten för tal-till-tal- och tal till text. | [Ja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nej |

## <a name="news-and-updates"></a>Nyheter och uppdateringar

Läs mer om nyheterna med Azure Speech Services.

* April 2019 - är tal SDK 1.4.0 med stöd för text till tal (Beta) för C++, C#, och Java i Windows och Linux. Dessutom SDK stöder nu MP3- och Opus/Ogg ljudformat för C++ och C# i Linux. En fullständig lista över uppdateringar, förbättringar och kända problem finns i [viktig](releasenotes.md). 
* Mars 2019 – är en ny slutpunkt för tal som returnerar en fullständig lista över röster som är tillgängliga i en viss region nu tillgänglig. Nya regioner stöds nu för text till tal. Mer information finns i [text till tal-API-referens (REST)](rest-text-to-speech.md).
* Februari 2019 - är tal SDK 1.3.0 med stöd för [Unity (beta)](quickstart-csharp-unity.md). Lagt till stöd för den `AudioInput` klass, där du kan välja strömningskälla som ljud. En fullständig lista över förbättringar och kända problem finns i [viktig](releasenotes.md).
* December 2018 - är tal SDK 1.2.0 med stöd för [Python](quickstart-python.md) och [Node.js](quickstart-js-node.md), samt Ubuntu 18.04 LTS. Mer information finns i [viktig](releasenotes.md).

## <a name="try-speech-services"></a>Försök Speech Services

Vi erbjuder snabbstarterna i mest populära programmeringsspråk, alla har utformats för att se dig köra kod i mindre än 10 minuter. Den här tabellen innehåller de mest populära Snabbstart för varje funktion. Utforska ytterligare språk och plattformar med hjälp av det vänstra navigeringsfältet.

| Tal till text (SDK) | Translation (SDK) | Text till tal (REST) | Text till tal (SDK) |
|-------------------|-------------------|-----------------------|-----------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) | [Python (Windows, Linux, macOS)](quickstart-python-text-to-speech.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) |
| [JavaScript (Browser)](quickstart-js-browser.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) | [C#, .NET Core (Windows, Linux, macOS)](quickstart-dotnet-text-to-speech.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) | [Node.js (Windows, Linux, macOS)](quickstart-nodejs-text-to-speech.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) | |

När du har haft möjlighet att använda Speech Services, prova vår självstudie som Lär dig hur du identifierar intentioner från tal med hjälp av tal SDK och LUIS.

* [Självstudie: Identifiera avsikter från tal med tal SDK och LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

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
