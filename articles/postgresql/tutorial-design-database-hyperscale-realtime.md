---
title: 'Självstudiekurs: Utforma en instrumentpanel i realtid – Hyperskala (Citus) - Azure-databas för PostgreSQL'
description: Den här självstudien visar hur du skapar, fyller i och frågar distribuerade tabeller på Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: f4eeb646de8b68c2c8d30586d0c75cece5317e40
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76716328"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Självstudiekurs: Utforma en instrumentpanel för analys i realtid med hjälp av Azure Database for PostgreSQL – Hyperscale (Citus)

I den här självstudien använder du Azure Database for PostgreSQL - Hyperscale (Citus) för att lära dig hur du:

> [!div class="checklist"]
> * Skapa en Hyperscale-servergrupp (Citus)
> * Använda psql-verktyget för att skapa ett schema
> * Fragmenttabeller över noder
> * Generera exempeldata
> * Utföra sammanslagningar
> * Fråga råa och aggregerade data
> * Förfalla data

## <a name="prerequisites"></a>Krav

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Använda psql-verktyget för att skapa ett schema

När du är ansluten till Azure Database for PostgreSQL - Hyperscale (Citus) med psql kan du utföra några grundläggande uppgifter. Den här självstudien hjälper dig att använda trafikdata från webbanalys och sedan rulla upp data för att tillhandahålla instrumentpaneler i realtid baserat på dessa data.

Låt oss skapa en tabell som kommer att förbruka alla våra råa webbtrafikdata. Kör följande kommandon i psql-terminalen:

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

Vi kommer också att skapa en tabell som kommer att hålla våra per-minuten aggregat, och en tabell som behåller positionen för vår senaste sammanslagning. Kör även följande kommandon i psql:

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

Du kan se de nyskapade tabellerna i listan över tabeller nu med det här psql-kommandot:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Fragmenttabeller över noder

En hyperskala distribution lagrar tabellrader på olika noder baserat på värdet för en användarbeteckning kolumn. Den här "distributionskolumnen" markerar hur data fragmenteras mellan noder.

Låt oss ange att distributionskolumnen ska vara plats-ID,\_fragmentnyckeln. I psql kör du dessa funktioner:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Generera exempeldata

Nu bör vår servergrupp vara redo att inta vissa data. Vi kan köra följande lokalt `psql` från vår anslutning för att kontinuerligt infoga data.

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

Frågan infogar ungefär åtta rader varje sekund. Raderna lagras på olika arbetsnoder enligt anvisningarna `site_id`i distributionskolumnen .

   > [!NOTE]
   > Låt datagenereringsfrågan köras och öppna en andra PSQL-anslutning för de återstående kommandona i den här självstudien.
   >

## <a name="query"></a>Söka i data

Hyperskala hosting alternativet tillåter flera noder att bearbeta frågor parallellt för hastighet. Databasen beräknar till exempel aggregat som SUMMA och ANTAL på arbetsnoder och kombinerar resultaten till ett slutgiltigt svar.

Här är en fråga för att räkna webbförfrågningar per minut tillsammans med några statistik.
Prova att köra den i psql och observera resultaten.

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

## <a name="rolling-up-data"></a>Rulla upp data

Den föregående frågan fungerar bra i ett tidigt skede, men dess prestanda försämras när dina data skalas. Även med distribuerad bearbetning är det snabbare att förberäkna data än att beräkna om dem upprepade gånger.

Vi kan se till att vår instrumentpanel håller sig snabb genom att regelbundet rulla upp rådata i en aggregerad tabell. Du kan experimentera med aggregeringsvaraktigheten. Vi använde en aggregeringstabell per minut, men du kan dela upp data i 5, 15 eller 60 minuter istället.

För att köra denna roll-up lättare, vi kommer att sätta den i en plpgsql funktion. Kör dessa kommandon i psql `rollup_http_request` för att skapa funktionen.

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

Med vår funktion på plats, kör den för att rulla upp data:

```sql
SELECT rollup_http_request();
```

Och med våra data i en föraggregerad form kan vi fråga den samlade tabellen för att få samma rapport som tidigare. Kör följande fråga:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Utgående gamla data

Sammanslagningarna gör frågor snabbare, men vi måste fortfarande förfalla gamla data för att undvika obegränsade lagringskostnader. Bestäm hur länge du vill behålla data för varje granularitet och använd standardfrågor för att ta bort utgångna data. I följande exempel bestämde vi oss för att behålla rådata för en dag och aggregeringar per minut i en månad:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

I produktionen kan du radbrytas dessa frågor i en funktion och anropa den varje minut i ett cron-jobb.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en servergrupp. Om du inte förväntar dig att behöva dessa resurser i framtiden tar du bort servergruppen. Tryck på knappen *Ta bort* på sidan *Översikt* för servergruppen. När du uppmanas att göra det på en popup-sida *Delete* bekräftar du namnet på servergruppen och klickar på den slutliga delete-knappen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att etablera en citus-servergrupp (Hyperscale). Du har anslutit till den med psql, skapat ett schema och distribuerade data. Du har lärt dig att fråga data i råform, regelbundet aggregera dessa data, fråga de aggregerade tabellerna och förfalla gamla data.

Läs sedan om begreppen hyperskala.
> [!div class="nextstepaction"]
> [Nodtyper för hyperskala](https://aka.ms/hyperscale-concepts)
