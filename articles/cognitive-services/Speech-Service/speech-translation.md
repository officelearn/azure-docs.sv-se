---
title: Tal översättning med Speech service
titleSuffix: Azure Cognitive Services
description: Med tal tjänsten kan du lägga till en fullständig översättning av tal från slut punkt till slut punkt i real tid till dina program, verktyg och enheter. Samma API kan användas för både tal till tal- och tal till text-översättning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: cef6ff109607455881970aeb95eaf0859f6f4f05
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055540"
---
# <a name="what-is-speech-translation"></a>Vad är talöversättning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Tal översättning från Speech service möjliggör real tids-, tal-till-tal-och tal-till-text-översättning i real tid av ljud strömmar. Med tal-SDK har dina program, verktyg och enheter åtkomst till käll avskrifter och översättnings utdata för tillhandahållen ljud. Interimistiska avskrifter och översättnings resultat returneras som tal identifieras och slutliga resultat kan konverteras till syntetiskt tal.

Microsofts översättnings motor drivs av två olika metoder: statistisk maskin översättning (SMT) och neurala Machine Translation (NMT). SMT använder avancerad statistisk analys för att uppskatta de bästa möjliga översättningarna med några få ord. Med NMT används neurala-nätverk för att tillhandahålla mer exakta, naturligt förekommande översättningar genom att använda den fullständiga kontexten för meningar för att översätta ord.

Idag använder Microsoft NMT för översättning till de flesta populära språken. Alla [språk som är tillgängliga för översättning av tal till tal](language-support.md#speech-translation) drivs av NMT. Översättning av tal till text kan använda SMT-eller NMT beroende på språk paret. När mål språket stöds av NMT, är den fullständiga översättningen NMT. När mål språket inte stöds av NMT, är översättningen en hybrid av NMT och SMT, med engelska som en "Pivot"-lösning mellan de två språken.

## <a name="core-features"></a>Kärn funktioner

* Översättning av tal till text med igenkännings resultat.
* Översättning av tal till tal.
* Stöd för översättning till flera mål språk.
* Resultat av löpande igenkänning och översättning.

## <a name="get-started"></a>Kom igång 

Se [snabb start](get-started-speech-translation.md) för att komma igång med tal översättning. Röst översättnings tjänsten är tillgänglig via [talet SDK](speech-sdk.md) och tal- [CLI](spx-overview.md).

## <a name="sample-code"></a>Exempelkod

Exempel koden för talet SDK finns på GitHub. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning/översättning och arbeta med anpassade modeller.

* [Exempel på tal-till-text-och översättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migreringsguider

Om dina program, verktyg eller produkter använder [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)har vi skapat guider som hjälper dig att migrera till tal tjänsten.

* [Migrera från Translator Speech API till tal tjänsten](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referens dokument

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: tal till text](rest-speech-to-text.md)
* [REST API: text till tal](rest-text-to-speech.md)
* [REST API: batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

* Slutför [snabb](get-started-speech-translation.md) starten för tal omvandling
* [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
