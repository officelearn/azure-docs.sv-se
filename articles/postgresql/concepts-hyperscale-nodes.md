---
title: Noder – storskalig (citus) – Azure Database for PostgreSQL
description: Lär dig mer om de två typerna av noder, koordinator och arbetare i en Server grupp i Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382805"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Noder i Azure Database for PostgreSQL – storskalig (citus)

Den storskaliga (citus) värd typen tillåter Azure Database for PostgreSQL servrar (kallas noder) att koordineras med varandra i en "delad Nothing"-arkitektur. Noderna i en Server grupp har samlat mer data och använder fler processor kärnor än vad som är möjligt på en enskild server. Arkitekturen gör det också möjligt för databasen att skalas genom att lägga till fler noder i Server gruppen.

## <a name="coordinator-and-workers"></a>Koordinator och arbetare

Varje server grupp har en koordinator-nod och flera arbetare. Programmen skickar sina frågor till koordinator-noden, som vidarebefordrar dem till relevanta arbetare och ackumulerar sina resultat. Program kan inte ansluta direkt till arbets tagarna.

Med storskalig (citus) kan databas administratören *distribuera* tabeller och lagra olika rader på olika arbetsnoder. Distribuerade tabeller är nyckeln till storskalig prestanda. Det går inte att distribuera tabeller helt och hållet på koordinator-noden och kan inte dra nytta av parallellitet mellan datorer.

För varje fråga i distribuerade tabeller dirigerar koordinatorn antingen den till en enda arbetsnoden, eller parallelizes den över flera beroende på om de data som krävs finns på en enda nod eller flera. Koordinatorn bestämmer vad som ska göras vid konsultering av metadata tabeller. De här tabellerna spårar DNS-namn och hälsa för arbetsnoder och data fördelningen mellan noderna.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [distribuerade tabeller](concepts-hyperscale-distributed-data.md)
