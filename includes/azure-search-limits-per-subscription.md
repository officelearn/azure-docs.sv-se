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
ms.openlocfilehash: 8a4f794c8ef24a90498954629c131904621c5b43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "30844100"
---
Du kan skapa flera tjänster inom en prenumeration, var och en tillhandahållas på en specifik nivå begränsas bara av antal tjänster på varje nivå. Du kan skapa upp till 12 services på den grundläggande nivån och en annan 12 tjänster på S1 nivån inom samma prenumeration. Läs mer om nivåer [Välj en SKU- eller nivå för Azure Search](../articles/search/search-sku-tier.md).

Högsta tillåtna gränser kan aktiveras på begäran. Kontakta Azure-supporten om du behöver fler tjänster inom samma prenumeration.

| Resurs            | Ledigt&nbsp;<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Maximal tjänster    |1     | 12    | 12  | 6  | 6  | 6     |
| Maximal skala i SU&nbsp;<sup>2</sup> |Gäller inte |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> lediga baseras på delade, inte dedicerade resurser. Skala upp stöds inte på delade resurser.

<sup>2</sup> search-enheter (SU) fakturerar enheter som allokerats som antingen en *replik* eller en *partition*. Du behöver båda resurserna för lagring, indexering och frågor. Läs mer om SU beräkningar i [skala resursen nivåer för fråge- och arbetsbelastningar](../articles/search/search-capacity-planning.md). 