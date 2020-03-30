---
title: Distribuerade data – Hyperskala (Citus) – Azure-databas för PostgreSQL
description: Lär dig mer om distribuerade tabeller, referenstabeller, lokala tabeller och shards i Azure Database för PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243658"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Distribuerade data i Azure Database för PostgreSQL – Hyperskala (Citus)

I den här artikeln beskrivs de tre tabelltyperna i Azure Database for PostgreSQL – Hyperscale (Citus).
Den visar hur distribuerade tabeller lagras som shards och hur shards placeras på noder.

## <a name="table-types"></a>Tabelltyper

Det finns tre typer av tabeller i en citusservergrupp (Hyperscale), som alla används för olika ändamål.

### <a name="type-1-distributed-tables"></a>Typ 1: Distribuerade tabeller

Den första typen, och vanligaste, är distribuerade tabeller. De verkar vara normala tabeller till SQL-uttryck, men de är vågrätt partitionerade mellan arbetsnoder. Vad detta innebär är att raderna i tabellen lagras på olika noder, i fragmenttabeller som kallas shards.

Hyperskala (Citus) kör inte bara SQL utan DDL-satser i ett kluster.
Ändra schemat för en distribuerad tabell kaskad för att uppdatera alla tabellens shards över arbetare.

#### <a name="distribution-column"></a>Kolumnen Distribution

Hyperskala (Citus) använder algoritmisk sharding för att tilldela rader till shards. Tilldelningen görs deterministiskt baserat på värdet i en tabellkolumn som kallas distributionskolumnen. Klusteradministratören måste ange den här kolumnen när en tabell distribueras.
Att göra rätt val är viktigt för prestanda och funktionalitet.

### <a name="type-2-reference-tables"></a>Typ 2: Referenstabeller

En referenstabell är en typ av distribuerad tabell vars hela innehåll är koncentrerat till en enda shard. Fragmentet replikeras på varje arbetare. Frågor om alla arbetare kan komma åt referensinformationen lokalt, utan nätverkets omkostnader för att begära rader från en annan nod. Referenstabeller har ingen distributionskolumn eftersom det inte finns något behov av att skilja mellan separata shards per rad.

Referenstabeller är vanligtvis små och används för att lagra data som är relevanta för frågor som körs på en arbetsnod. Ett exempel är uppräknade värden som orderstatus eller produktkategorier.

### <a name="type-3-local-tables"></a>Typ 3: Lokala tabeller

När du använder Hyperscale (Citus) är den koordinatornod som du ansluter till en vanlig PostgreSQL-databas. Du kan skapa vanliga tabeller på koordinatorn och välja att inte fragmentera dem.

En bra kandidat för lokala tabeller skulle vara små administrativa tabeller som inte deltar i anslutningsfrågor. Ett exempel är en användartabell för program inloggning och autentisering.

## <a name="shards"></a>Skärvor

I föregående avsnitt beskrivs hur distribuerade tabeller lagras som shards på arbetsnoder. I det här avsnittet beskrivs mer tekniska detaljer.

Metadatatabellen `pg_dist_shard` på koordinatorn innehåller en rad för varje fragment i varje distribuerad tabell i systemet. Raden matchar ett fragment-ID med ett intervall av heltal i ett hash-utrymme (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Om koordinatornsnoden vill avgöra vilken `github_events`fragment som innehåller en rad i hashar den värdet för distributionskolumnen på raden. Sedan kontrollerar noden\'vilket fragmentintervall som innehåller hash-värdet. Intervallen definieras så att bilden av hash-funktionen är deras osammanhängande förening.

### <a name="shard-placements"></a>Shard placeringar

Anta att fragment 102027 är associerad med raden i fråga. Raden läss eller skrivs `github_events_102027` i en tabell som kallas i en av arbetarna. Vilken arbetare? Det bestäms helt av metadatatabellerna. Mappningen av shard till arbetaren kallas fragmentplaceringen.

Koordinatorn nod skriver frågor till fragment som hänvisar `github_events_102027` till specifika tabeller som och kör dessa fragment på lämpliga arbetare. Här är ett exempel på en fråga som körs bakom kulisserna för att hitta noden som innehåller fragment-ID 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>Nästa steg
- Läs om hur du [väljer en distributionskolumn](concepts-hyperscale-choose-distribution-column.md) för distribuerade tabeller.
