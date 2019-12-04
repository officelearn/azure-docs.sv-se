---
title: Tal-till-text-tal-tjänst
titleSuffix: Azure Cognitive Services
description: Funktionen tal-till-text aktiverar real tids avskrift av ljud strömmar till text som dina program, verktyg eller enheter kan använda, Visa och vidta åtgärder för som indata. Den här tjänsten fungerar sömlöst med text till tal-(tal syntes) och funktioner för tal översättning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: erhopf
ms.openlocfilehash: f04ad388922ad7f73bf4409f9a846291cbb08da3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774016"
---
# <a name="what-is-speech-to-text"></a>Vad är tal-till-text?

Tal-till-text från Azure Speech Services, även kallat tal-till-text, aktiverar real tids avskrift av ljud strömmar till text som dina program, verktyg eller enheter kan använda, Visa och vidta åtgärder för som kommando indata. Den här tjänsten drivs av samma igenkännings teknik som Microsoft använder för Cortana och Office-produkter och fungerar sömlöst med översättning och text till tal. En fullständig lista över tillgängliga tal-till-text-språk finns i [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Som standard använder tal-till-text-tjänsten den universella språk modellen. Den här modellen tränade med Microsoft-ägda data och distribueras i molnet. Det är optimalt för konversations-och dikterings scenarier. Om du använder tal-till-text för igenkänning och avskriftering i en unik miljö kan du skapa och träna anpassade ljud-, språk-och uttals modeller för att hantera omgivande brus eller branschspecifika vokabulär.

Du kan enkelt spela in ljud från en mikrofon, läsa från en ström eller komma åt ljudfiler från lagring med API: er för tal-SDK och REST-API: er. Speech-SDK stöder ljud med en kanal i formatet WAV/PCM 16-bitars, 16 kHz/8 kHz för taligenkänning. Ytterligare ljud format stöds med hjälp av [slut punkten tal-till-text](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) eller [batch-avskrifts tjänsten](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Kärn funktioner

Här är de funktioner som är tillgängliga via API: er för tal-SDK och REST:

| Användningsfall | SDK | REST |
|--------- | --- | ---- |
| Korta yttranden (< 15 sekunder). Har endast stöd för ett slutligt avskrifts resultat. | Ja | Ja\* |
| Kontinuerlig avskrift av långt yttranden och strömmande ljud (> 15 sekunder). Har stöd för mellanliggande och slutliga avskrifts resultat. | Ja | Nej |
| Härleda avsikter från igenkännings resultat med [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Ja | Inga\*\* |
| Batch-avskrift av ljudfiler asynkront. | Nej  | Ja\*\*\* |
| Skapa och hantera tal modeller. | Nej | Ja\*\*\* |
| Skapa och hantera anpassade modell distributioner. | Nej  | Ja\*\*\* |
| Skapa precisions test för att mäta precisionen för bas linje modellen jämfört med anpassade modeller. | Nej  | Ja\*\*\* |
| Hantera prenumerationer. | Nej  | Ja\*\*\* |

\*_med hjälp av rest-funktionerna kan du överföra upp till 60 sekunders ljud och få ett slutligt avskrifts resultat._

\*\*_Luis-intentor och entiteter kan härledas med hjälp av en separat Luis-prenumeration. Med den här prenumerationen anropar SDK LUIS för dig och ger entiteter och avsikts resultat. Med REST API anropar du LUIS själv för att härleda intentor och entiteter med din LUIS-prenumeration._

\*\*\*_tjänsterna är tillgängliga med hjälp av Cris.AI-slutpunkten. Se [referens för Swagger](https://westus.cris.ai/swagger/ui/index)._

## <a name="get-started-with-speech-to-text"></a>Kom igång med tal till text

Vi erbjuder snabb starter i de flesta populära programmeringsspråk, som var utformade för att du ska kunna köra kod på mindre än 10 minuter. [Den här tabellen](https://aka.ms/csspeech#5-minute-quickstarts) innehåller en fullständig lista över tal SDK-snabb starter ordnade efter plattform och språk. API-referensen kan också hittas [här](https://aka.ms/csspeech#reference).

Om du föredrar att använda funktionen för att använda tal-till-text-REST går du till [REST-API: er](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Självstudier och exempel kod

När du har haft möjlighet att använda tal tjänsterna kan du prova vår självstudie som lär dig hur du kan identifiera avsikter från tal med hjälp av talet SDK och LUIS.

- [Självstudie: identifiera avsikter från tal med talet SDK och LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

Exempel koden för talet SDK finns på GitHub. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning och arbeta med anpassade modeller.

- [Tal till text-exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-avskrifts exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Anpassning

Förutom standard bas linje modellen som används av tal tjänsterna kan du anpassa modeller efter dina behov med tillgängliga data, för att undvika hinder för tal igenkänning, till exempel tal format, vokabulär och bakgrunds ljud, se [Custom Speech](how-to-custom-speech.md)

> [!NOTE]
> Anpassnings alternativen varierar efter språk/språk (se [språk som stöds](supported-languages.md)).

## <a name="migration-guides"></a>Migreringsguider

> [!WARNING]
> Taligenkänning i Bing togs ur bruk den 15 oktober 2019.

Om dina program, verktyg eller produkter använder Taligenkänning i Bing-API: er eller Custom Speech har vi skapat guider som hjälper dig att migrera till tal tjänster.

- [Migrera från Taligenkänning i Bing till tal tjänsterna](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
- [Migrera från Custom Speech till tal tjänsterna](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Referensdokument

- [Speech SDK](https://aka.ms/csspeech)
- [Tal enheter SDK](speech-devices-sdk.md)
- [REST API: tal till text](rest-speech-to-text.md)
- [REST API: text till tal](rest-text-to-speech.md)
- [REST API: batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

- [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
- [Hämta tal-SDK](speech-sdk.md)
