---
title: Begränsningar för behållare - LUIS
titleSuffix: Azure Cognitive Services
description: De LUIS-behållarspråk som stöds.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7fe773b35c5aba31b2fea66bd2be7b2745eac3ee
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879249"
---
# <a name="language-understanding-luis-container-limitations"></a>Begränsningar i LUIS-behållare (Language Understanding)

LUIS-behållarna har några anmärkningsvärda begränsningar. Den här artikeln innehåller information om dessa begränsningar från beroenden som inte stöds till en delmängd av språk som stöds.

## <a name="supported-dependencies-for-latest-container"></a>Beroenden som `latest` stöds för behållare

Den senaste LUIS-behållaren, som släpptes på [//build/ 2019](https://news.microsoft.com/build2019/), stöder:

* [Nya fördefinierade domäner](luis-reference-prebuilt-domains.md): dessa företagsfokuserade domäner inkluderar entiteter, exempelyttranden och mönster. Utöka dessa domäner för eget bruk.

## <a name="unsupported-dependencies-for-latest-container"></a>Beroenden som inte `latest` stöds för behållare

Om du vill [exportera efter behållare](luis-container-howto.md#export-packaged-app-from-luis)måste du ta bort beroenden som inte stöds från LUIS-appen. När du försöker exportera för behållaren rapporterar LUIS-portalen dessa funktioner som inte stöds som du behöver ta bort.

Du kan använda ett LUIS-program om det **inte innehåller** något av följande beroenden:

Appkonfigurationer som inte stöds|Information|
|--|--|
|Behållarekulturer som inte stöds| Nederländska`nl-NL`( )<br>Japanska`ja-JP`( )<br>Tyska stöds endast med [tokenizern 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Enheter som inte stöds för alla kulturer|[KeyPhrase](luis-reference-prebuilt-keyphrase.md) fördefinierade enhet för alla kulturer|
|Enheter som inte stöds`en-US`för engelsk ( ) kultur|[GeographyV2](luis-reference-prebuilt-geographyV2.md) fördefinierade enheter|
|Tal priming|Externa beroenden stöds inte i behållaren.|
|Sentimentanalys|Externa beroenden stöds inte i behållaren.|
|Stavningskontroll av Bing|Externa beroenden stöds inte i behållaren.|

## <a name="languages-supported"></a>Språk som stöds

LUIS-behållare stöder en delmängd av de [språk som stöds](luis-language-support.md#languages-supported) av korrekt LUIS. LUIS-behållarna kan förstå yttranden på följande språk:

| Språk | Nationell inställning | Fördefinierad domän | Fördefinierad enhet | Rekommendationer för fraslista | **[Textanalys](../text-analytics/language-support.md)<br>(Sentiment och<br>Nyckelord)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikansk engelska | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Kinesiska](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Franska (Frankrike) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Franska (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Tyska |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italienska |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreansk |`ko-KR` | ✔️ | ❌ | ❌ | *Endast nyckelfras* |
| Portugisiska (Brasilien) |`pt-BR` | ✔️ | ✔️ | ✔️ | inte alla underkulturer |
| Spanska (Spanien) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Spanska (Mexiko)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turkiska | `tr-TR` |✔️| ❌ | ❌ | *Endast känsla* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]