---
title: Distribuerade data i Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)
description: Tabeller och shards som distribueras i servergruppen.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077342"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Distribuerade data i Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)

Den här artikeln beskrivs de tre tabelltyper i hyperskala (Citus).
Den visar hur distribuerade tabeller lagras som shards och det sätt som shards placeras på noder.

## <a name="table-types"></a>Tabelltyper

Det finns tre typer av tabeller i en servergrupp i hyperskala, var och en används för olika syften.

### <a name="type-1-distributed-tables"></a>Typ 1: distribuerade tabeller

Det är den första typen och de flesta vanliga *distribuerade* tabeller. De visas som vanliga tabeller till SQL-uttryck, men är vågrätt *partitionerade* över arbetsnoder. Det innebär att raderna i tabellen lagras på olika noder i fragment tabeller kallas *shards*.

Hyperskala körs inte bara SQL men DDL-instruktionerna i ett kluster, så om du ändrar schemat för en distribuerad tabell påverkar andra poster för att uppdatera alla tabellens fragment i arbetare.

#### <a name="distribution-column"></a>Distributionskolumn

Hyperskala använder algoritmiska horisontell partitionering för att tilldela rader till shards. Tilldelningen har gjorts deterministiskt baserat på värdet för en tabellkolumn som kallas den *distributionskolumn.* Klusteradministratören måste ange den här kolumnen när du distribuerar en tabell.
Det är viktigt för prestanda och funktioner vilket gör det rätta valet.

### <a name="type-2-reference-tables"></a>Typ 2: register för

En tabell är en typ av distribuerad tabell vars hela innehållet är koncentrerade till en enda partition. Fragmentet replikeras på varje worker så frågor på alla worker kan komma åt Referensinformation lokalt, utan nätverksresurser på förfrågande rader från en annan nod. Referenstabeller har inga distributionskolumn eftersom det finns inget behov att skilja separata fragment per rad.

Referenstabeller är normalt små och används för att lagra data som är relevanta för frågor som körs på varje worker-nod. Till exempel räkna upp värdena som order status eller produktkategorier.

### <a name="type-3-local-tables"></a>Typ 3: lokala tabeller

När du använder hyperskala är koordinatornoden som du ansluter till en vanlig PostgreSQL-databas. Du kan skapa vanliga tabeller på koordinatorn och väljer att inte fragment dem.

En bra kandidat för lokala tabeller är liten administrativa tabeller som inte ingår i join-frågor. Exempelvis kan en användare tabell för programmet inloggning och autentisering.

## <a name="shards"></a>Fragment

Föregående avsnitt beskrivs hur distribuerade tabeller lagras som shards på arbetsnoderna. Det här avsnittet hämtar mer i de tekniska detaljerna.

Den `pg_dist_shard` metadatatabell på koordinatorn innehåller en rad för varje fragment i varje distribuerad tabell i systemet. Raden matchar ett fragment-ID med ett intervall av heltal med hash blanksteg (shardminvalue, shardmaxvalue):

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

Om koordinatornoden vill fastställa vilken fragment innehåller en rad med `github_events`, den hashar värdet för den distribution-kolumnen i raden och kontrollerar vilket fragment\'s adressintervallet innehåller hashvärdet. (Intervall har definierats så att avbildningen av hash-funktionen är deras åtskilda union.)

### <a name="shard-placements"></a>Shard placeringar

Anta att fragmentet 102027 är associerad med den aktuella raden. Raden ska läsas eller skrivas i en tabell med namnet `github_events_102027` i någon av anställda. Vilken worker? Det bestäms helt och hållet av metadatatabeller och mappningen av fragment till worker kallas fragmentet *placering*.

Koordinatornoden returpaket frågor i fragment som refererar till de specifika tabellerna som `github_events_102027`, och kör dessa fragment på lämplig arbetare. Här är ett exempel på en fråga som körs i bakgrunden och letar upp noden håller fragment-ID: T 102027.

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
- Lär dig hur du [väljer en distributionskolumn](concepts-hyperscale-choose-distribution-column.md) för distribuerade tabeller
