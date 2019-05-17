---
title: ta med fil
description: ta med fil
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212051"
---
Du kan skapa flera tjänster inom en prenumeration. Var och en kan etableras på en specifik nivå. Du är begränsas bara av tjänster som tillåts på varje nivå. Du kan till exempel skapa upp till 12 på Basic-nivån och en annan 12 tjänsterna på S1-nivå i samma prenumeration. Läs mer om nivåer [väljer en SKU eller nivå för Azure Search](../articles/search/search-sku-tier.md).

Maximal tjänstbegränsningar kan höjas på begäran. Kontakta Azure-supporten om du behöver fler tjänster inom samma prenumeration.

| Resource            | Kostnadsfria<sup>1</sup> | Grundläggande | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximal tjänster    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximal skala i search-enheter (SU)<sup>2</sup> |Gäller inte |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> kostnadsfri baseras på delade, inte dedikerade resurser. Skala upp stöds inte på delade resurser.

<sup>2</sup> sökenheter fakturerar enheter som allokerats som antingen en *repliken* eller en *partition*. Båda resurserna behöver du för lagring, indexering och frågeåtgärder. Läs mer om SU beräkningar i [skala resursnivåer för fråge- och arbetsbelastningar](../articles/search/search-capacity-planning.md). 