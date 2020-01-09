---
title: Tal-till-text-tal-tjänst
titleSuffix: Azure Cognitive Services
description: Funktionen tal-till-text aktiverar real tids avskrift av ljud strömmar till text. Dina program, verktyg eller enheter kan använda, Visa och vidta åtgärder för den här text ingången. Den här tjänsten fungerar sömlöst med text till tal-(tal syntes) och funktioner för tal översättning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: erhopf
ms.openlocfilehash: 8518f92a4f4df1686d4b338783a93d969e04d219
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379969"
---
# <a name="what-is-speech-to-text"></a>Vad är tal-till-text?

Tal-till-text från tjänsten Speech, som även kallas tal igenkänning, aktiverar real tids avskrift av ljud strömmar till text. Dina program, verktyg eller enheter kan använda, Visa och vidta åtgärder på den här texten som kommando inmatade. Den här tjänsten drivs av samma igenkännings teknik som Microsoft använder för Cortana och Office-produkter. Det fungerar sömlöst med <a href="./speech-translation.md" target="_blank">översättnings <span class="docon docon-navigate-external x-hidden-focus"></span> </a> -och <a href="./text-to-speech.md" target="_blank">text till tal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> -tjänstens erbjudanden. En fullständig lista över tillgängliga tal-till-text-språk finns i [språk som stöds](language-support.md#speech-to-text).

Tjänsten tal-till-text används som standard för att använda den universella språk modellen. Den här modellen tränade med Microsoft-ägda data och distribueras i molnet. Det är optimalt för konversations-och dikterings scenarier. När du använder tal-till-text för igenkänning och avskrift i en unik miljö kan du skapa och träna anpassade modeller för akustisk, språk och uttal. Anpassning är användbart för att adressera omgivande brus eller branschspecifika vokabulär.

> [!NOTE]
> Taligenkänning i Bing togs ur bruk den 15 oktober 2019. Om dina program, verktyg eller produkter använder Taligenkänning i Bing-API: er eller Custom Speech har vi skapat guider som hjälper dig att migrera till tal tjänsten.
> - [Migrera från Taligenkänning i Bing till tal tjänsten](how-to-migrate-from-bing-speech.md)
> - [Migrera från Custom Speech till tal tjänsten](how-to-migrate-from-custom-speech-service.md)

## <a name="get-started-with-speech-to-text"></a>Kom igång med tal till text

Tjänsten tal-till-text är tillgänglig via tal- [SDK: n](speech-sdk.md). Det finns flera vanliga scenarier som snabb starter på olika språk och plattformar:

 - [Snabb start: identifiera tal med mikrofon ingång](quickstarts/speech-to-text-from-microphone.md)
 - [Snabb start: identifiera tal från en fil](quickstarts/speech-to-text-from-file.md)
 - [Snabb start: identifiera tal som lagras i Blob Storage](quickstarts/from-blob.md)

Om du föredrar att använda funktionen för att använda tal-till-text-REST går du till [REST-API: er](rest-speech-to-text.md).

## <a name="tutorials-and-sample-code"></a>Självstudier och exempel kod

När du har haft möjlighet att använda tal tjänsten kan du prova vår självstudie som lär dig hur du identifierar avsikter från tal med hjälp av talet SDK och LUIS.

- [Självstudie: identifiera avsikter från tal med talet SDK och LUIS med hjälp avC#](how-to-recognize-intents-from-speech-csharp.md)

Exempel koden för talet SDK finns på GitHub. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning och arbeta med anpassade modeller.

- [Tal till text-exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-avskrifts exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Anpassning

Förutom standard modellen för tal tjänster kan du skapa anpassade modeller. Anpassningen hjälper till att undvika hinder för tal igenkänning, till exempel talad stil, vokabulär och bakgrunds brus, se [Custom Speech](how-to-custom-speech.md). Anpassnings alternativ varierar beroende på språk och nationella inställningar, se [språk som stöds](supported-languages.md) för att kontrol lera stödet.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Nästa steg

- [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
- [Hämta tal-SDK](speech-sdk.md)
