---
title: ta med fil
description: ta med fil
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 179f525b7b6a7e51889b14b66df6c537ca56bd75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272736"
---
Lagringen begränsas av disk utrymme eller av en hård gräns på det *maximala antalet* index, dokument eller andra resurser på hög nivå, beroende på vilket som kommer först. I följande tabell dokumenteras lagrings gränser. För maximala gränser för index, dokument och andra objekt, se [gränser per resurs](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resurs | Kostnadsfri | Basic<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Service nivå avtal (SLA)<sup>3</sup>  |Inga |Ja |Ja |Ja |Ja |Ja |Ja |Ja |
| Lagringsutrymme per partition |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partitioner per tjänst |E.t. |1 |12 |12 |12 |3 |12 |12 |
| Partitionsstorlek |E.t. |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliker |E.t. |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic har en fast partition. På den här nivån används ytterligare Sök enheter för att allokera fler repliker för ökad arbets belastning för frågor.

<sup>2</sup> S3 HD har en hård gräns på tre partitioner, vilket är mindre än partitionens gräns för S3. Den nedre partitionsbegränsningen har införts eftersom antalet index för S3 HD är betydligt högre. Med tanke på att det finns tjänstbegränsningar för både datorresurser (lagring och behandling) och innehåll (index och dokument) nås innehållsgränsen först.

<sup>3</sup> service nivå avtal erbjuds för fakturerbara tjänster på dedikerade resurser. Kostnads fria tjänster och för hands versions funktioner har inget service avtal. För fakturerbara tjänster börjar service avtal att gälla när du tillhandahåller tillräckligt med redundans för din tjänst. Två eller fler repliker krävs för frågan (Läs) service avtal. Tre eller flera repliker krävs för fråge-och indexerings-service avtal (Läs-och Skriv behörighet). Antalet partitioner är inte ett SLA-avtal. 