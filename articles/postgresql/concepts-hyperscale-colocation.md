---
title: Tabellsamlokalisering - Hyperskala (Citus) - Azure-databas för PostgreSQL
description: Så här lagrar du relaterad information tillsammans för snabbare frågor
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967345"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Tabellsamlokalisering i Azure-databasen för PostgreSQL – Hyperskala (Citus)

Samlokalisering innebär att lagra relaterad information tillsammans på samma noder. Frågor kan gå snabbt när alla nödvändiga data är tillgängliga utan nätverkstrafik. Samlokalisera relaterade data på olika noder gör att frågor kan köras effektivt parallellt på varje nod.

## <a name="data-colocation-for-hash-distributed-tables"></a>Datasamlokalisering för hash-distribuerade tabeller

I Azure Database for PostgreSQL – Hyperscale (Citus) lagras en rad i en fragment om hash-värdet i distributionskolumnen faller inom shards hash-intervall. Shards med samma hash-intervall placeras alltid på samma nod. Rader med kolumnvärden för samma fördelning finns alltid på samma nod i tabeller.

![Skärvor](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Ett praktiskt exempel på samlokalisering

Tänk på följande tabeller som kan ingå i en Webbanalys för flera innehavare:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Nu vill vi svara på frågor som kan utfärdas av en kundinriktad instrumentpanel. En exempelfråga är "Returnera antalet besök under den senaste veckan för alla sidor som börjar med '/blog' i klient sex."

Om våra data fanns i distributionsalternativet för en server kan vi enkelt uttrycka vår fråga med hjälp av de omfattande relationsåtgärder som sql erbjuder:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Så länge [arbetsminnet](https://en.wikipedia.org/wiki/Working_set) för den här frågan får plats i minnet är en tabell med en server en lämplig lösning. Låt oss överväga möjligheterna att skala datamodellen med distributionsalternativet Hyperscale (Citus).

### <a name="distribute-tables-by-id"></a>Distribuera tabeller efter ID

Frågor med en server börjar sakta ned när antalet klienter och data som lagras för varje klient växer. Arbetsset slutar passa i minnet och CPU blir en flaskhals.

I det här fallet kan vi fragmentera data över många noder med hjälp av Hyperscale (Citus). Det första och viktigaste valet vi måste göra när vi bestämmer oss för att shard är fördelningen kolumnen. Låt oss börja med ett naivt `event_id` val att använda `page_id` för `page` händelsetabellen och för tabellen:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

När data sprids över olika arbetare kan vi inte utföra en koppling som vi skulle på en enda PostgreSQL-nod. I stället måste vi utfärda två frågor:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Därefter måste resultaten från de två stegen kombineras av programmet.

Köra frågorna måste konsultera data i shards utspridda över noder.

![Ineffektiva frågor](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

I det här fallet skapar datadistributionen betydande nackdelar:

-   Overhead från att fråga varje shard och köra flera frågor.
-   Overhead för Q1 returnerar många rader till klienten.
-   Q2 blir stort.
-   Behovet av att skriva frågor i flera steg kräver ändringar i programmet.

Data sprids, så frågorna kan parallelliseras. Det är bara fördelaktigt om mängden arbete som frågan gör är betydligt större än omkostnaderna för att fråga många shards.

### <a name="distribute-tables-by-tenant"></a>Distribuera tabeller efter klient

I Hyperscale (Citus) garanteras rader med samma distributionskolumnvärde att finnas på samma nod. Från och med kan vi `tenant_id` skapa våra tabeller med som distributionskolumn.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Nu hyperskala (Citus) kan svara på den ursprungliga en-server fråga utan ändringar (Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

På grund av filter och koppling på tenant_id vet Hyperscale (Citus) att hela frågan kan besvaras med hjälp av uppsättningen colocated shards som innehåller data för just den klienten. En enda PostgreSQL-nod kan svara på frågan i ett enda steg.

![Bättre fråga](media/concepts-hyperscale-colocation/colocation-better-query.png)

I vissa fall måste frågor och tabellscheman ändras för att inkludera klient-ID i unika villkor och kopplingsvillkor. Denna förändring är vanligtvis enkel.

## <a name="next-steps"></a>Nästa steg

- Se hur klientdata samlokaliseras i [självstudien för flera innehavare](tutorial-design-database-hyperscale-multi-tenant.md).
