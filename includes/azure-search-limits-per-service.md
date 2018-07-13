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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756287"
---
Lagringen begränsas av diskutrymme eller av en hård gräns på den *maxantalet* index, dokument eller andra övergripande resurser, som inträffar först. I följande tabell beskrivs gränser. Övre gräns för det index, dokument och andra objekt, se [begränsningar av resursen](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resurs | Kostnadsfri | Grundläggande&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Servicenivåavtal (SLA) <sup>3</sup>  |Nej |Ja |Ja |Ja |Ja |Ja |
| Lagringsutrymme per partition |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partitioner per tjänst |Gäller inte |1 |12 |12 |12 |3 |
| Partitionsstorlek |Gäller inte |2 GB |25 GB |100 GB |200 GB |200 GB |
| Repliker |Gäller inte |3 |12 |12 |12 |12 |

<sup>1</sup> basic har en fast partition. På den här nivån kan används ytterligare su: er för att tilldela fler repliker för ökad frågearbetsbelastningar.

<sup>2</sup> S3 HD har en fast begränsning på 3 partitioner, vilket är lägre än partitionsgränsen för S3. Den nedre partitionsbegränsningen har införts eftersom antalet index för S3 HD är betydligt högre. Med tanke på att det finns tjänstbegränsningar för både datorresurser (lagring och behandling) och innehåll (index och dokument) nås innehållsgränsen först.

<sup>3</sup> servicenivåavtal (SLA) erbjuds för fakturerbar tjänster på dedikerade resurser. Kostnadsfria tjänster och förhandsversion funktioner har inget serviceavtal. För fakturerbar tjänster gälla serviceavtalen när du etablerar tillräckligt med redundans för din tjänst. Två eller fler repliker krävs för frågan (läsa) serviceavtal (SLA). Tre eller fler repliker krävs för frågor och indexering SLA (skrivskyddad). Antalet partitioner är inte en SLA-beräkningen. 