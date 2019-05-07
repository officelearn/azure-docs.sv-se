---
title: Noder i Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)
description: De två typerna av noder i en servergrupp.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077282"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Noder i Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)

Hyperskala (Citus) (förhandsversion) som är värd för typen kan Azure Database for PostgreSQL-servrar (som kallas noder) för att koordinera med varandra i en arkitektur med ”delat ingenting”. Noderna i en servergrupp sammantaget lagra mer data och använda mer CPU-kärnor än vad som skulle vara möjligt på en enskild server. Arkitekturen kan också databasen för att skala genom att lägga till fler noder i servergruppen.

## <a name="coordinator-and-workers"></a>Coordinator och arbetare

Varje servergrupp har en koordinatornod och flera arbetare. Program skickar sina frågor till koordinatornoden, som vidarebefordrar till relevanta arbetare och ackumulerar resultaten. Det går inte att ansluta direkt till arbetare program.

För varje fråga koordinatorn antingen den vidare till en enda arbetsnod eller parallelizes över flera beroende på om nödvändiga data ligger på en nod eller flera. Koordinatorn avgör vad som ska göras med hjälp av metadatatabeller. Dessa tabeller spåra DNS-namn och hälsotillståndet för arbetsnoder och fördelning av data mellan noder.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur noder lagra [distribuerad data](concepts-hyperscale-distributed-data.md)
