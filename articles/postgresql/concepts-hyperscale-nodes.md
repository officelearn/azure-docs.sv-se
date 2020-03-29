---
title: Noder – Hyperskala (Citus) – Azure-databas för PostgreSQL
description: Lär dig mer om de två typerna av noder, koordinator och arbetare, i en servergrupp i Azure Database för PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974010"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Noder i Azure Database för PostgreSQL – Hyperskala (Citus)

Den hyperskala (Citus) värdtypen tillåter Azure Database för PostgreSQL-servrar (kallas noder) att samordna med varandra i en "delad ingenting" arkitektur. Noderna i en servergrupp innehåller tillsammans mer data och använder fler CPU-kärnor än vad som skulle vara möjligt på en enda server. Arkitekturen gör det också möjligt för databasen att skala genom att lägga till fler noder i servergruppen.

## <a name="coordinator-and-workers"></a>Samordnare och arbetstagare

Varje servergrupp har en koordinatornod och flera arbetare. Ansökningar skickar sina frågor till koordinatorn noden, som vidarebefordrar den till berörda arbetstagare och ackumulerar deras resultat. Program kan inte ansluta direkt till arbetare.

För varje fråga dirigerar koordinatorn den antingen till en enda arbetsnod eller parallelliserar den över flera beroende på om de data som krävs finns på en enda nod eller flera. Samordnaren bestämmer vad som ska göra genom att konsultera metadatatabeller. Dessa tabeller spårar DNS-namn och hälsotillstånd för arbetsnoder och distribution av data mellan noder.

## <a name="next-steps"></a>Nästa steg
- Läs om hur noder lagrar [distribuerade data](concepts-hyperscale-distributed-data.md)
