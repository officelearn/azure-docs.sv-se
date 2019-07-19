---
title: Tal till text med Azure Speech Services
titleSuffix: Azure Cognitive Services
description: Tal-till-text från Azure Speech Services, även kallat tal-till-text, aktiverar real tids avskrift av ljud strömmar till text som dina program, verktyg eller enheter kan använda, Visa och vidta åtgärder för som kommando indata. Den här tjänsten drivs av samma igenkännings teknik som Microsoft använder för Cortana och Office-produkter och fungerar sömlöst med översättning och text till tal.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cb9362c4d58deb5472c8d5adab39cdd1cc4e2600
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333565"
---
# <a name="what-is-speech-to-text"></a>Vad är tal-till-text?

Tal-till-text från Azure Speech Services, även kallat tal-till-text, aktiverar real tids avskrift av ljud strömmar till text som dina program, verktyg eller enheter kan använda, Visa och vidta åtgärder för som kommando indata. Den här tjänsten drivs av samma igenkännings teknik som Microsoft använder för Cortana och Office-produkter och fungerar sömlöst med översättning och text till tal.  En fullständig lista över tillgängliga tal-till-text-språk finns i [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Som standard använder tal-till-text-tjänsten den universella språk modellen. Den här modellen tränade med Microsoft-ägda data och distribueras i molnet. Det är optimalt för konversations-och dikterings scenarier. Om du använder tal-till-text för igenkänning och avskriftering i en unik miljö kan du skapa och träna anpassade ljud-, språk-och uttals modeller för att hantera omgivande brus eller branschspecifika vokabulär.

Du kan enkelt spela in ljud från en mikrofon, läsa från en ström eller komma åt ljudfiler från lagring med API: er för tal-SDK och REST-API: er. Tal-SDK: n stöder WAV/PCM 16-bit, 16 kHz/8 kHz, ett ljud med en kanal för tal igenkänning. Ytterligare ljud format stöds med hjälp av [slut punkten tal-till-text](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) eller batch-avskrifts [tjänsten](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Kärn funktioner

Här är de funktioner som är tillgängliga via API: er för tal-SDK och REST:

| Användningsfall | SDK | REST |
|----------|-----|------|
| Korta yttranden (< 15 sekunder). Har endast stöd för slutlig avskrifts resultat. | Ja | Ja |
| Kontinuerlig avskrift av långt yttranden och strömmande ljud (> 15 sekunder). Har stöd för mellanliggande och slutliga avskrifts resultat. | Ja | Nej |
| Härleda avsikter från igenkännings resultat med [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Ja | Nej\* |
| Batch-avskrift av ljudfiler asynkront. | Nej | Ja\** |
| Skapa och hantera tal modeller. | Nej | Ja\** |
| Skapa och hantera anpassade modell distributioner. | Nej | Ja\** |
| Skapa precisions test för att mäta precisionen för bas linje modellen jämfört med anpassade modeller. | Nej | Ja\** |
| Hantera prenumerationer. | Nej | Ja\** |

\* *LUIS avsikter och entiteter kan erhållas med hjälp av en separat LUIS-prenumeration. Med den här prenumerationen kan SDK anropa LUIS för dig och tillhandahålla entiteter och avsikts resultat. Med REST-API kan du anropa LUIS själv för att härleda avsikter och entiteter med LUIS-prenumeration.*

\** *Dessa tjänster är tillgängliga med cris.ai-slutpunkten. Se [referens för Swagger](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Kom igång med tal till text

Vi erbjuder snabb starter i de flesta populära programmeringsspråk, som var utformade för att du ska kunna köra kod på mindre än 10 minuter. [Den här tabellen](https://aka.ms/csspeech#5-minute-quickstarts) innehåller en fullständig lista över tal SDK-snabb starter som organiseras av platfrom och språk.  API-referensen kan också hittas [här](https://aka.ms/csspeech#reference).

Om du föredrar att använda funktionen för att använda tal-till-text-REST går du till [REST-API: er](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Självstudier och exempel kod

När du har haft möjlighet att använda tal tjänsterna kan du prova vår självstudie som lär dig hur du kan identifiera avsikter från tal med hjälp av talet SDK och LUIS.

* [Självstudier: Identifiera avsikter från tal med talet SDK och LUIS.C#](how-to-recognize-intents-from-speech-csharp.md)

Exempel koden för talet SDK finns på GitHub. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning och arbeta med anpassade modeller.

* [Tal till text-exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch-avskrifts exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Anpassning

Förutom den universella modellen som används av tal tjänsterna kan du skapa anpassade ljud-, språk-och uttals modeller som är specifika för din upplevelse. Här är en lista över anpassnings alternativ:

| Modell | Beskrivning |
|-------|-------------|
| [Akustisk modell](how-to-customize-acoustic-models.md) | Att skapa en anpassad akustisk modell är användbart om ditt program, verktyg eller enheter används i en viss miljö, t. ex. i en bil eller fabrik med specifika registrerings villkor. Exempel innefattar tal med brytning, vissa bakgrundsljud eller användning av en särskild mikrofon för inspelning. |
| [Språkmodell](how-to-customize-language-model.md) | Skapa en anpassad språk modell för att förbättra avskriften av branschspecifika vokabulär och grammatik, till exempel medicinsk terminologi eller IT-jargong. |
| [Uttalsmodell](how-to-customize-pronunciation.md) | Med en anpassad uttal-modell kan du definiera fonetisk form och visa ett ord eller en term. Det är användbart för att hantera anpassade villkor, till exempel produktnamn eller förkortningar. Allt du behöver för att komma igång är en uttal-fil – en enkel txt-fil. |

> [!NOTE]
> Anpassnings alternativen varierar efter språk/språk (se [språk som stöds](supported-languages.md)).

## <a name="migration-guides"></a>Stöd linjer för migrering

> [!WARNING]
> Taligenkänning i Bing tas ur bruk den 15 oktober 2019.

Om dina program, verktyg eller produkter använder Taligenkänning i Bing-API: er eller Custom Speech har vi skapat guider som hjälper dig att migrera till tal tjänster.

* [Migrera från Taligenkänning i Bing till tal tjänsterna](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrera från Custom Speech till tal tjänsterna](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](https://aka.ms/csspeech)
* [Tal enheter SDK](speech-devices-sdk.md)
* [REST API: Tal till text](rest-speech-to-text.md)
* [REST API: Text till tal](rest-text-to-speech.md)
* [REST API: Batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
