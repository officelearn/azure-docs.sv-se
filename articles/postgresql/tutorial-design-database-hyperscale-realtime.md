---
title: Skapa en realtidsinstrumentpanel med Azure Database for PostgreSQL – hyperskala (Citus) (förhandsversion) självstudien
description: Den här självstudien visar hur du skapar, fylla i och frågar distribuerade tabeller på Azure Database för PostgreSQL Hyperscale (Citus) (förhandsversion).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: a5e4b2073a29785ee851b2733c12d6331afe59d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65757556"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Självstudier: Utforma en instrumentpanel för analys i realtid med hjälp av Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)

I den här självstudien använder du Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion) för att lära dig hur du:

> [!div class="checklist"]
> * Skapa en servergrupp i hyperskala (Citus)
> * Använd psql-verktyget för att skapa ett schema
> * Shard tabeller mellan noder
> * Generera exempeldata
> * Utföra uppdateringar
> * Fråga efter rådata och sammanställda data
> * Ta bort data

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Använd psql-verktyget för att skapa ett schema

Efter att ha anslutit till Azure Database för PostgreSQL – kan hyperskala (Citus) (förhandsversion) med psql, du utföra några grundläggande uppgifter. Den här självstudien vägleder dig genom att mata in trafikdata från webbanalyser, sedan samlar in dessa data och ger instrumentpaneler i realtid baserat på dessa data.

Nu ska vi skapa en tabell som kommer att använda alla våra raw webbdata trafik. Kör följande kommandon i psql terminal:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Vi ska också skapa en tabell som ska innehålla vårt aggregeringar per minut och en tabell som underhåller positionen för vår senaste samlade uppdateringen. Kör följande kommandon i psql samt:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Du kan se de nyligen skapade tabellerna i listan över tabeller nu med det här psql-kommandot:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Shard tabeller mellan noder

En storskalig distribution lagrar tabellrader på olika noder baserat på värdet för en kolumn som anges av användaren. Den här ”distributionskolumn” markerar hur data är fragmenterade (sharded) mellan noder.

Vi ställer distributionskolumn vara plats\_-id, shardnyckeln. Kör dessa funktioner i psql:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Generera exempeldata

Vår servergrupp bör nu vara redo att mata in vissa data. Du kan köra följande lokalt från våra `psql` anslutning till kontinuerligt infoga data.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Frågan infogar cirka åtta rader per sekund. Rader som är lagrade på olika arbetsnoder enligt anvisningarna från kolumnen distribution `site_id`.

   > [!NOTE]
   > Lämna data generation frågan som körs och öppna en andra psql-anslutning för de resterande kommandona i den här självstudien.
   >

## <a name="query"></a>Fråga

Hyperskala som är värd för alternativet gör att flera noder att bearbeta frågor parallellt för hastighet. Till exempel databasen beräknar aggregeringar som SUM och COUNT på arbetsnoder och kombinerar resultaten till en slutlig svar.

Här är en fråga till antal begäranden per minut tillsammans med några få statistik.
Försök att köra i psql och se resultaten.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Samlar in data

Den föregående frågan fungerar bra i de tidiga stadierna, men dess prestanda försämras med ditt data. Även med distribuerad bearbetning går det snabbare för att beräkna förväg data än om du vill beräkna om det upprepade gånger.

Vi kan se till att vår instrumentpanel håller sig kvar snabbt genom att regelbundet samlar in rådata till en sammanställd tabell. Du kan experimentera med aggregering varaktighet. Vi har använt en per minut sammansättningstabellen, men du kan dela upp data i 5, 15 eller 60 minuter i stället.

Om du vill köra den här samlad enklare vi att placera den i en plpgsql-funktion. Kör följande kommandon i psql att skapa den `rollup_http_request` funktion.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Kör den samlar in data med vår funktion på plats:

```sql
SELECT rollup_http_request();
```

Och med våra data i form av en preaggregeras vi frågar Upplyft för att få samma rapport som tidigare. Kör följande fråga:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Upphör att gälla gamla data

Uppdateringar göra frågor snabbare, men vi behöver att ta bort gamla data för att undvika obundna lagringskostnader. Bestäm hur länge du vill behålla data för varje granularitet och använda standard frågor för att ta bort utgångna data. I följande exempel beslutat att hålla rådata under en dag och per minut aggregeringar i en månad:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

I produktion kan du omsluta dessa frågor i en funktion och anropa varje minut i ett cron-jobb.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en servergrupp. Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort servergruppen. Tryck på den *ta bort* knappen i den *översikt* sidan för din servergrupp. När du uppmanas att göra på en popup-sida bekräfta namnet på servergruppen och klickar på sista *ta bort* knappen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att etablera en servergrupp i hyperskala (Citus). Du är ansluten till den med psql, skapas ett schema och distribuerade data. Du har lärt dig att fråga data både i obearbetat format regelbundet samla datan, fråga aggregerade tabeller och ta bort gamla data.

Därefter lär dig mer om begreppet hyperskala.
> [!div class="nextstepaction"]
> [Hyperskala nodtyper](https://aka.ms/hyperscale-concepts)