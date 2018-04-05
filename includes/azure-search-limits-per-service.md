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
ms.openlocfilehash: b4062aab5a453505ef4586f422a124d4bbf715cb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
Lagring är begränsad av diskutrymme eller av en hård gräns på den *maximalt antal* för index, dokument eller andra övergripande resurser, beroende på vilket som kommer först. I följande tabell beskrivs Lagringsgränser. Övre gräns för index, dokument och andra objekt finns [gränser som resursen](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resurs | Kostnadsfri | Basic&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Servicenivåavtal (SLA) <sup>3</sup>  |Nej |Ja |Ja |Ja |Ja |Ja |
| Lagringsutrymme per partition |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partitioner per tjänst |Gäller inte |1 |12 |12 |12 |3 |
| Partitionsstorlek |Gäller inte |2 GB |25 GB |100 GB |200 GB |200 GB |
| Repliker |Gäller inte |3 |12 |12 |12 |12 |

<sup>1</sup> basic har en fast partition. I det här skiktet används ytterligare SUs för att tilldela flera repliker för ökad frågan arbetsbelastningar.

<sup>2</sup> S3 HD har en fast begränsning på 3 partitioner, vilket är lägre än partitionsgränsen för S3. Den nedre partitionsbegränsningen har införts eftersom antalet index för S3 HD är betydligt högre. Med tanke på att det finns tjänstbegränsningar för både datorresurser (lagring och behandling) och innehåll (index och dokument) nås innehållsgränsen först.

<sup>3</sup> servicenivåavtal (SLA) erbjuds för fakturerbar tjänster på dedicerade resurser. Kostnadsfria tjänster och förhandsgranska funktioner har inga SLA. För fakturerbar tjänster börjar SLA gälla när du etablerar tillräcklig redundans för din tjänst. Två eller flera repliker krävs för SERVICENIVÅAVTAL för frågan (läsa). Tre eller flera repliker krävs för fråga och indexering SLA (skrivskyddad). Antalet partitioner är inte ett SLA-faktor. 