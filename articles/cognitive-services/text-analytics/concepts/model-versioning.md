---
title: Ange modell versionen i Textanalys v3
titleSuffix: Azure Cognitive Services
description: Lär dig hur du anger den API för textanalys modell som används för dina data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/21/2020
ms.author: aahi
ms.openlocfilehash: 9431ff862dd987a1a806087053014e7c880bf801
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2020
ms.locfileid: "84143308"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Modell version i API för textanalys

Med version 3 av API för textanalys kan du välja den modell version som används på dina data. Använd den valfria `model-version` parametern för att välja modell versionen i API-begärandena. Exempel: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Om den här parametern inte anges kommer API: et att standardvärdet vara den senaste stabila versionen. 

## <a name="available-versions"></a>Tillgängliga versioner

Använd tabellen nedan för att se vilka modell versioner som stöds av varje slut punkt.


| Slutpunkt                        | Versioner som stöds                       | Senaste version |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


Du hittar information om uppdateringarna för de här modellerna i [Vad är nytt](../whats-new.md).

## <a name="next-steps"></a>Nästa steg

* [Översikt över Textanalys](../overview.md)
* [Sentiment-analys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Enhets igenkänning](../how-tos/text-analytics-how-to-entity-linking.md)