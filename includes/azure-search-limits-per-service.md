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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272736"
---
Lagringen begränsas av diskutrymme eller av en hård gräns för det *maximala antalet* index, dokument eller andra resurser på hög nivå, beroende på vilket som inträffar först. Följande tabell dokumenterar lagringsgränser. Maximala gränser för index, dokument och andra objekt finns i [Gränser efter resurs](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resurs | Kostnadsfri | Grundläggande<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 (på andra) | L2 (på andra) |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Servicenivåavtal (SLA)<sup>3</sup>  |Inga |Ja |Ja |Ja |Ja |Ja |Ja |Ja |
| Lagringsutrymme per partition |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partitioner per tjänst |Ej tillämpligt |1 |12 |12 |12 |3 |12 |12 |
| Partitionsstorlek |Ej tillämpligt |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliker |Ej tillämpligt |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic har en fast partition. På den här nivån används ytterligare sökenheter för att allokera fler repliker för ökade frågearbetsbelastningar.

<sup>2</sup> S3 HD har en hård gräns på tre partitioner, vilket är lägre än partitionsgränsen för S3. Den nedre partitionsbegränsningen har införts eftersom antalet index för S3 HD är betydligt högre. Med tanke på att det finns tjänstbegränsningar för både datorresurser (lagring och behandling) och innehåll (index och dokument) nås innehållsgränsen först.

<sup>3</sup> Servicenivåavtal erbjuds för fakturerbara tjänster på särskilda resurser. Kostnadsfria tjänster och förhandsgranskningsfunktioner har inget serviceavtal. För fakturerbara tjänster börjar SLA:er gälla när du tillhandahåller tillräcklig redundans för din tjänst. Två eller flera repliker krävs för frågeslator (läs). Tre eller flera repliker krävs för fråge- och indexeringsslalaord (läs-skriv). Antalet partitioner är inte ett SLA-övervägande. 