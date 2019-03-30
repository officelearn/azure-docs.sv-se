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
ms.openlocfilehash: 30c6fc189ebcd497a214828f65213a55cefdf03f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632806"
---
Lagringen begränsas av diskutrymme eller av en hård gräns på den *maxantalet* index, dokument eller andra övergripande resurser, som inträffar först. I följande tabell beskrivs gränser. Övre gräns för det index, dokument och andra objekt, se [begränsningar av resursen](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resurs | Kostnadsfri | Basic<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Servicenivåavtal (SLA)<sup>3</sup>  |Nej |Ja |Ja |Ja |Ja |Ja |Ja |Ja |
| Lagringsutrymme per partition |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partitioner per tjänst |Gäller inte |1 |12 |12 |12 |3 |12 |12 |
| Partitionsstorlek |Gäller inte |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliker |Gäller inte |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> basic har en fast partition. På den här nivån kan används ytterligare search-enheter för att tilldela fler repliker för ökad frågearbetsbelastningar.

<sup>2</sup> S3 HD har en hård gräns på tre partitioner, vilket är lägre än partitionsgränsen för S3. Den nedre partitionsbegränsningen har införts eftersom antalet index för S3 HD är betydligt högre. Med tanke på att det finns tjänstbegränsningar för både datorresurser (lagring och behandling) och innehåll (index och dokument) nås innehållsgränsen först.

<sup>3</sup> servicenivåavtal erbjuds för fakturerbar tjänster på dedikerade resurser. Kostnadsfria tjänster och förhandsversion funktioner har inget serviceavtal. För fakturerbar tjänster gälla serviceavtalen när du etablerar tillräckligt med redundans för din tjänst. Två eller fler repliker krävs för frågan (läsa) serviceavtal. Tre eller fler repliker krävs för frågor och indexering (skrivskyddad) serviceavtal. Antalet partitioner är inte en SLA-beräkningen. 