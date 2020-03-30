---
title: ta med fil
description: ta med fil
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272632"
---
Du kan skapa flera tjänster inom en prenumeration. Var och en kan etableras på en viss nivå. Du begränsas endast av antalet tjänster som tillåts på varje nivå. Du kan till exempel skapa upp till 12 tjänster på Basic-nivån och ytterligare 12 tjänster på S1-nivån inom samma prenumeration. Mer information om nivåer finns i [Välj en SKU eller nivå för Azure Cognitive Search](../articles/search/search-sku-tier.md).

Högsta servicegränser kan höjas på begäran. Om du behöver fler tjänster inom samma prenumeration kontaktar du Azure Support.

| Resurs            | Gratis<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 (på andra) | L2 (på andra) |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximala tjänster    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximal skala i sökenheter (SU)<sup>2</sup> |Ej tillämpligt |3 SU (SU) |36 SU (AVSU) |36 SU (AVSU) |36 SU (AVSU) |36 SU (AVSU) |36 SU (AVSU) |36 SU (AVSU) |

<sup>1</sup> Gratis bygger på delade, inte dedikerade, resurser. Uppskalning stöds inte på delade resurser.

<sup>2</sup> Sökenheter är faktureringsenheter, som tilldelas som antingen en *replik* eller en *partition*. Du behöver både resurser för lagring, indexering och frågeåtgärder. Mer information om SU-beräkningar finns i [Skala resursnivåer för fråge- och indexarbetsbelastningar](../articles/search/search-capacity-planning.md). 