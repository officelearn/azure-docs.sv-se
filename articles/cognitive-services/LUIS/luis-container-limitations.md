---
title: Begränsningar för behållare – LUIS
titleSuffix: Azure Cognitive Services
description: De LUIS behållar språk som stöds.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507862"
---
# <a name="language-understanding-luis-container-limitations"></a>Language Understanding (LUIS) begränsningar för behållare

LUIS-behållare har några viktiga begränsningar. I den här artikeln beskrivs de här begränsningarna från beroenden som inte stöds för en delmängd språk som stöds.

## <a name="supported-dependencies-for-latest-container"></a>Beroenden som stöds för `latest` container

Den senaste LUIS-behållaren, som lanserades på [//build/2019](https://news.microsoft.com/build2019/), stöder:

* [Nya fördefinierade domäner](luis-reference-prebuilt-domains.md): de här företags fokuserade domänerna omfattar entiteter, exempel yttranden och mönster. Utöka dessa domäner för eget bruk.

## <a name="unsupported-dependencies-for-latest-container"></a>Beroenden som inte stöds för `latest` container

Om du vill [Exportera för container](luis-container-howto.md#export-packaged-app-from-luis)måste du ta bort beroenden som inte stöds från Luis-appen. När du försöker exportera för container rapporterar LUIS-portalen dessa funktioner som inte stöds och som du måste ta bort.

Du kan använda ett LUIS-program om det **inte innehåller** något av följande beroenden:

App-konfigurationer som inte stöds|Information|
|--|--|
|Container kulturer stöds inte| Nederländska (`nl-NL`)<br>Japanska (`ja-JP`)<br>Tyska stöds endast med 1.0.2- [tokenizer](luis-language-support.md#custom-tokenizer-versions).|
|Entiteter som inte stöds för alla kulturer|Fördefinierad enhets [fras](luis-reference-prebuilt-keyphrase.md) för alla kulturer|
|Entiteter som inte stöds för engelsk (`en-US`) kultur|[GeographyV2](luis-reference-prebuilt-geographyV2.md) fördefinierade entiteter|
|Tal Prima|Externa beroenden stöds inte i behållaren.|
|Sentimentanalys|Externa beroenden stöds inte i behållaren.|
|Stavnings kontroll i Bing|Externa beroenden stöds inte i behållaren.|

## <a name="languages-supported"></a>Språk som stöds

LUIS-behållare har stöd för en delmängd av de [språk som stöds](luis-language-support.md#languages-supported) av Luis-korrekt. LUIS-behållare kan förstå yttranden på följande språk:

| Språk | Nationell inställning | Fördefinierad domän | Fördefinierad entitet | Rekommendationer för fras lista | **[text analys](../text-analytics/language-support.md)<br>(Sentiment och<br>Reserverade|
|--|--|:--:|:--:|:--:|:--:|
| Amerikansk engelska | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[kinesiska](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Franska (Frankrike) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Franska (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Tyska |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italienska |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreanska |`ko-KR` | ✔️ | ❌ | ❌ | Endast *nyckel fras* |
| Portugisiska (Brasilien) |`pt-BR` | ✔️ | ✔️ | ✔️ | alla under kulturer |
| Spanska (Spanien) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Spanska (Mexiko)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turkiska | `tr-TR` |✔️| ❌ | ❌ | Endast *sentiment* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]