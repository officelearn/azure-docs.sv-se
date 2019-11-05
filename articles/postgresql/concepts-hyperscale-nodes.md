---
title: Noder i Azure Database for PostgreSQL – storskalig (citus)
description: Lär dig mer om de två typerna av noder, koordinator och arbetare i en Server grupp i Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 5348c8bbe432cd024a8da93ca866ae4458f956e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468085"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Noder i Azure Database for PostgreSQL – storskalig (citus)

Den storskaliga (citus) värd typen tillåter Azure Database for PostgreSQL servrar (kallas noder) att koordineras med varandra i en "delad Nothing"-arkitektur. Noderna i en Server grupp har samlat mer data och använder fler processor kärnor än vad som är möjligt på en enskild server. Arkitekturen gör det också möjligt för databasen att skalas genom att lägga till fler noder i Server gruppen.

## <a name="coordinator-and-workers"></a>Koordinator och arbetare

Varje server grupp har en koordinator-nod och flera arbetare. Programmen skickar sina frågor till koordinator-noden, som vidarebefordrar dem till relevanta arbetare och ackumulerar sina resultat. Program kan inte ansluta direkt till arbets tagarna.

För varje fråga dirigerar koordinatorn antingen den till en enda arbetsnoden, eller parallelizes den över flera beroende på om de data som krävs finns på en enda nod eller flera. Koordinatorn bestämmer vad som ska göras vid konsultering av metadata tabeller. De här tabellerna spårar DNS-namn och hälsa för arbetsnoder och data fördelningen mellan noderna.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur noder lagrar [distribuerade data](concepts-hyperscale-distributed-data.md)
