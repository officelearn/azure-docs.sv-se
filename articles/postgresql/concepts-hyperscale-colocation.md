---
title: Server begrepp i Azure Database for PostgreSQL
description: Den här artikeln innehåller riktlinjer för att konfigurera och hantera Azure Database for PostgreSQL-servrar och överväganden.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077477"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Samordning av tabellen i Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)

Samordning innebär att lagra relaterad information tillsammans på samma noderna. Frågor kan gå snabbt när alla nödvändiga data är tillgängliga utan någon nätverkstrafik. Samordna relaterade data på olika noder kan frågor för att effektivt köra parallellt på varje nod.

## <a name="data-colocation-for-hash-distributed-tables"></a>Samplacering av data för hash-distribuerad tabeller

I hyperskala lagras en rad i en shard om hashen för värdet i kolumnen distribution ligger inom det fragmentet hash intervall. Shards med samma hash-intervall placeras alltid på samma nod. Rader med jämn fördelning kolumnvärdena är alltid på samma nod i tabeller.

![Fragment](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>En praktiska exempel på samordning

Överväg följande tabeller som kan vara en del av en webbanalys med flera innehavare SaaS:

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

Nu vill vi att besvara frågor som kan utfärdas av en kundinriktad instrumentpanel, till exempel: ”Returnerar antalet besök under den senaste veckan för alla sidor som börjar med” / blogg ' i klient sex ”.

Om det var våra data i ett alternativ för distribution av enskild server, kan vi enkelt express frågan med den omfattande uppsättningen med relationella åtgärder som erbjuds av SQL:

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

Så länge som den [arbetsminnet](https://en.wikipedia.org/wiki/Working_set) för den här frågan ryms i minnet en enskild server-tabell är en lämplig lösning. Nu ska vi titta dock möjligheter att skala datamodell med alternativ för storskalig distribution.

### <a name="distributing-tables-by-id"></a>Distribuera tabeller efter ID

Enskild server frågor starta långsamt när antalet klienter och de data som lagras för varje klient växer. Sidmängd stoppas hamnar i minnet och CPU blir en flaskhals.

I det här fallet kan vi Fragmentera data över flera noder med hyperskala. Den första och viktigaste val som vi behöver göra när horisontell partitionering är kolumnen distribution. Låt oss börja med en naïve välja att använda `event_id` för tabellen händelse och `page_id` för den `page` tabell:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

När data är utspridda över olika arbetare, kan inte vi utföra en koppling som vi skulle göra på en enda PostgreSQL-nod. Vi måste i stället skicka två frågor:

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

Därefter kan måste resultaten från de två stegen kombineras av programmet.

Köra frågor som måste läsa data i fragment spridda över noder.

![ineffektiva frågor](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

I det här fallet skapar Datadistributionen betydande nackdelar:

-   Overhead från att skicka frågor varje shard, köra flera frågor
-   Arbetet med att Q1 många rader som returneras till klienten
-   Q2 bli stora
-   Behovet av att skriva frågor i flera steg kräver ändringar i programmet

Eftersom data sprids, kan frågorna parallelliseras. Det är dock endast fördelaktigt om mängden arbete som frågan är betydligt större än att behöva skicka frågor till många fragment.

### <a name="distributing-tables-by-tenant"></a>Distribuera tabeller av klient

I hyperskala garanterat rader med samma distribution kolumnvärdet är på samma nod. Börja om, kan vi skapa vårt tabeller med `tenant_id` som kolumnen distribution.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Nu kan hyperskala besvara den ursprungliga frågan för enskild server utan modifiering (Q1):

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

På grund av filter och sammanslagningen av tenant_id vet hyperskala att hela frågan besvaras med uppsättning samordnade shards som innehåller data för den specifika klienten. En enda PostgreSQL-nod kan besvara frågan i ett enda steg.

![bättre fråga](media/concepts-hyperscale-colocation/colocation-better-query.png)

I vissa fall kan ändras frågor och tabellscheman för att inkludera klient-ID i unika restriktioner och ansluta till villkor. Detta är vanligtvis en enkel ändring.

## <a name="next-steps"></a>Nästa steg

- Se hur klientdata samordnat i den [självstudien för flera innehavare](tutorial-design-database-hyperscale-multi-tenant.md)
