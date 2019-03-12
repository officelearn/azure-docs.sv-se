---
title: ta med fil
description: ta med fil
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: a466ea29fa31c4c628724f3d5138a1612ef7a0f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553912"
---
Du kan skapa flera tjänster inom en prenumeration. Var och en kan etableras på en specifik nivå. Du är begränsas bara av tjänster som tillåts på varje nivå. Du kan till exempel skapa upp till 12 på Basic-nivån och en annan 12 tjänsterna på S1-nivå i samma prenumeration. Läs mer om nivåer [väljer en SKU eller nivå för Azure Search](../articles/search/search-sku-tier.md).

Maximal tjänstbegränsningar kan höjas på begäran. Kontakta Azure-supporten om du behöver fler tjänster inom samma prenumeration.

| Resurs            | Kostnadsfria<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Maximal tjänster    |1     | 12    | 12  | 6  | 6  | 6     |
| Maximal skala i search-enheter (SU)<sup>2</sup> |Gäller inte |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup>kostnadsfri baseras på delade, inte dedikerade resurser. Skala upp stöds inte på delade resurser.

<sup>2</sup>sökenheter fakturerar enheter som allokerats som antingen en *repliken* eller en *partition*. Båda resurserna behöver du för lagring, indexering och frågeåtgärder. Läs mer om SU beräkningar i [skala resursnivåer för fråge- och arbetsbelastningar](../articles/search/search-capacity-planning.md). 