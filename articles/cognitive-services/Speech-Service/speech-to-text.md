---
title: Tal till text med Azure Speech Services
titleSuffix: Azure Cognitive Services
description: Tal till text från Azure Speech Services, även kallat tal till text, aktiverar i realtid transkription av ljudströmmar till text som dina program, verktyg eller enheter kan använda, visa och vidta åtgärder för som kommandoindata. Den här tjänsten drivs av samma taligenkänningsteknik som Microsoft använder för Cortana och Office-produkter, och fungerar sömlöst med översättning och text till tal.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9e6bc1264e668ba5c6593ce36e721f54e685c391
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59008500"
---
# <a name="what-is-speech-to-text"></a>Vad är tal till text?

Tal till text från Azure Speech Services, även kallat tal till text, aktiverar i realtid transkription av ljudströmmar till text som dina program, verktyg eller enheter kan använda, visa och vidta åtgärder för som kommandoindata. Den här tjänsten drivs av samma taligenkänningsteknik som Microsoft använder för Cortana och Office-produkter, och fungerar sömlöst med översättning och text till tal.  En fullständig lista över tillgängliga språk för tal till text, se [språk som stöds](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/language-support#speech-to-text).

Som standard använder tjänsten tal till text Universal språkmodellen. Den här modellen har lärt sig med hjälp av data som ägs av Microsoft och har distribuerats i molnet. Det är optimala för konversationsanpassad och diktering scenarier. Om du använder tal till text för taligenkänning och taltranskription i en miljö kan du skapa och skapa anpassade akustiska, språk och uttal av modeller till adress omgivande ljuden eller branschspecifika ordförråd. 

Du kan enkelt spela in ljud från en mikrofon, läsa från en ström eller få åtkomst till ljud filer från storage med tal SDK och REST API: er. Tal SDK stöder WAV/PCM 16-bitars, 16 kHz, kanal-ljud för taligenkänning. Ytterligare ljudformat stöds med hjälp av den [tal till text REST-slutpunkt](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) eller [batch-tjänsten för taltranskription](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Kärnfunktioner

Här är funktionerna som är tillgängliga via tal SDK och REST API: er:

| Användningsfall | SDK | REST |
|----------|-----|------|
| Transkribera kort yttranden (< 15 sekunder). Stöder endast slutlig avskrift resultatet. | Ja | Ja |
| Kontinuerlig avskrift långa yttranden och strömmande ljud (> 15 sekunder). Har stöd för tillfälliga och slutlig avskrift resultat. | Ja | Nej |
| Härledd avsikter från igenkänningsresultat med [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Ja | Nej\* |
| Batch-avskrift av ljudfiler asynkront. | Nej | Ja\** |
| Skapa och hantera talmodeller. | Nej | Ja\** |
| Skapa och hantera distributioner av anpassade modeller. | Nej | Ja\** |
| Skapa precisionstester för att mäta riktighet baslinje modellen jämfört med anpassade modeller. | Nej | Ja\** |
| Hantera prenumerationer. | Nej | Ja\** |

\* *LUIS avsikter och entiteter kan erhållas med hjälp av en separat LUIS-prenumeration. Med den här prenumerationen SDK anropa LUIS för dig och ge entiteten och avsikt resultat. Med REST-API kan du anropa LUIS själv för att härleda avsikter och entiteter med LUIS-prenumeration.*

\** *De här tjänsterna är tillgängliga med hjälp av cris.ai-slutpunkt. Se [Swagger referens](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Kom igång med tal till text

Vi erbjuder snabbstarterna i mest populära programmeringsspråk, alla har utformats för att se dig köra kod i mindre än 10 minuter. Den här tabellen innehåller en fullständig lista över tal SDK snabbstarter ordnade efter språk.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Bläddra](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Bläddra](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Bläddra](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux | [Bläddra](https://aka.ms/csspeech/javaref) |
| [JavaScript, Browser](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Browser, Windows, Linux, macOS | [Bläddra](https://aka.ms/AA434tv) |
| [JavaScript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Bläddra](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Bläddra](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Bläddra](https://aka.ms/AA434tr)  |

Om du föredrar att använda REST-tjänst tal till text, se [REST API: er](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Självstudier och exempelkod

När du har haft möjlighet att använda Speech Services, prova vår självstudie som Lär dig hur du identifierar intentioner från tal med hjälp av tal SDK och LUIS.

* [Självstudier: Identifiera avsikter från tal med tal SDK och LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

Exempelkoden för tal SDK finns på GitHub. De här exemplen omfattar vanliga scenarier som läsa ljud från en fil eller dataström, kontinuerliga och inleveransen erkännande och arbeta med anpassade modeller.

* [Tal till text-exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch avskrift exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Anpassning

Förutom den universella modellen som används av Speech Services, kan du skapa anpassade akustiska, språk och uttal av modeller specifika till din upplevelse. Här är en lista med anpassningsalternativ:

| Modell | Beskrivning |
|-------|-------------|
| [Akustisk modell](how-to-customize-acoustic-models.md) | Skapa en anpassad akustisk modell är användbar om ditt program, verktyg eller enheter används i en viss miljö, som i en bil eller en fabrik med specifika inspelning villkor. Exempel innefattar tal med brytning, vissa bakgrundsljud eller användning av en särskild mikrofon för inspelning. |
| [Språkmodell](how-to-customize-language-model.md) | Skapa en anpassad språkmodell för att förbättra transkription av branschspecifika ordförråd och grammatik, till exempel medicinsk terminologi eller IT-jargong. |
| [Uttalsmodell](how-to-customize-pronunciation.md) | Du kan definiera fonetiska, formulär och visning av ett ord eller en term med en anpassad uttal-modell. Det är användbart för att hantera anpassade villkor, till exempel produktnamn eller förkortningar. Allt du behöver för att komma igång är en uttal-fil – en enkel txt-fil. |

> [!NOTE]
> Anpassningsalternativ varierar beroende på språk och nationella (se [språk som stöds](supported-languages.md)).

## <a name="migration-guides"></a>Migreringsguider

> [!WARNING]
> Bing-taligenkänning tas ur drift den 15 oktober 2019.

Om ditt program, verktyg eller produkter använder API: er för Bing-tal eller anpassat tal, har vi skapat guider som hjälper dig att migrera till Speech Services.

* [Migrera från Bing-tal till Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrera från anpassad tal till Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Tal enheter SDK](speech-devices-sdk.md)
* [REST-API: Speech-to-text](rest-speech-to-text.md)
* [REST-API: Text till tal](rest-text-to-speech.md)
* [REST-API: Batch transkription och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

* [Skaffa en prenumerationsnyckel för Speech Services utan kostnad](get-started.md)
* [Hämta tal SDK](speech-sdk.md)
