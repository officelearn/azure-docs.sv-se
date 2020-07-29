---
title: Kategorier som stöds för identifiering av namngiven entitet
titleSuffix: Azure Cognitive Services
description: Lär dig mer om entiteter som stöds i API för textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: f66ed9a7d26a0c2d7def7d17820379b8e3460460
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373085"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Entitets kategorier som stöds i API för textanalys v3

Använd den här artikeln för att hitta enhets kategorier som kan returneras av den [namngivna enhets igenkänningen](how-tos/text-analytics-how-to-entity-linking.md) (ner). En för hands version av NER v 3.1 är också tillgänglig, vilket innebär att du kan identifiera personlig `PII` information () och hälso tillstånd ( `PHI` ). Du kan också klicka på fliken **hälsa** för att se en lista över kategorier som stöds i textanalys för hälso tillstånd.

## <a name="entity-categories"></a>Enhets kategorier

#### <a name="general"></a>[Allmänt](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="personal"></a>[Personligt](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Hälsa](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Nästa steg

* [Använda namngiven enhets igenkänning i Textanalys](how-tos/text-analytics-how-to-entity-linking.md)
