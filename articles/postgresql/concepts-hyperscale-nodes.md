---
title: Noder i Azure Database for PostgreSQL – storskalig (citus) (för hands version)
description: Lär dig mer om de två typerna av noder, koordinator och arbetare i en Server grupp i Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 097fcdb3a7e53bb63db9dc2d352d754062df7be6
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947559"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Noder i Azure Database for PostgreSQL – storskalig (citus) (för hands version)

Värd typen för den storskaliga (citus) (för hands version) gör att Azure Database for PostgreSQL servrar (kallas noder) kan koordineras med varandra i en "delad Nothing"-arkitektur. Noderna i en Server grupp har samlat mer data och använder fler processor kärnor än vad som är möjligt på en enskild server. Arkitekturen gör det också möjligt för databasen att skalas genom att lägga till fler noder i Server gruppen.

## <a name="coordinator-and-workers"></a>Koordinator och arbetare

Varje server grupp har en koordinator-nod och flera arbetare. Programmen skickar sina frågor till koordinator-noden, som vidarebefordrar dem till relevanta arbetare och ackumulerar sina resultat. Program kan inte ansluta direkt till arbets tagarna.

För varje fråga dirigerar koordinatorn antingen den till en enda arbetsnoden, eller parallelizes den över flera beroende på om de data som krävs finns på en enda nod eller flera. Koordinatorn bestämmer vad som ska göras vid konsultering av metadata tabeller. De här tabellerna spårar DNS-namn och hälsa för arbetsnoder och data fördelningen mellan noderna.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur noder lagrar [distribuerade data](concepts-hyperscale-distributed-data.md)
