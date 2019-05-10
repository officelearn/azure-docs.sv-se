---
title: Skapa en realtidsinstrumentpanel med Azure Database for PostgreSQL – hyperskala (Citus) (förhandsversion) självstudien
description: Den här självstudien visar hur du skapar, fylla i och frågar distribuerade tabeller på Azure Database för PostgreSQL Hyperscale (Citus) (förhandsversion).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 9f3473d83678ffea888dad736a9620006b2961f7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406396"
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

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Skapa en Azure Database för PostgreSQL

Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Välj **databaser** från sidan **Nytt** och välj **Azure Database för PostgreSQL** från sidan **databaser**.
3. För alternativ för distribution, klickar du på den **skapa** knappen **hyperskala (Citus) servergrupp - FÖRHANDSVERSION.**
4. Fyll i formuläret om den nya servern och uppge följande information:
   - Resursgrupp: Klicka på den **Skapa nytt** länken under textrutan för det här fältet. Ange ett namn som **myresourcegroup**.
   - Servergruppnamn: **mydemoserver** (namn på en server, som mappar till DNS-namn och måste vara globalt unikt).
   - Administratörens användarnamn: **myadmin** (det används senare att ansluta till databasen).
   - Lösenord: måste vara minst åtta tecken långt och måste innehålla tecken från tre av följande kategorier-engelska versala bokstäver, engelska gemena bokstäver, siffror (0-9) och icke-alfanumeriska tecken (!, $, #, % osv.)
   - Plats: Använd den plats som är närmast dina användare att ge dem snabbast åtkomst till data.

   > [!IMPORTANT]
   > Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här självstudien. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

5. Klicka på **konfigurera servergrupp**. Lämna inställningarna i den avsnittet oförändrade och klicka på **spara**.
6. Klicka på **granska + skapa** och sedan **skapa** att etablera servern. Etableringen tar några minuter.
7. Sidan omdirigerar för att övervaka distributionen. Då live status ändras från **distributionen pågår** till **distributionen är klar**, klickar du på den **utdata** menyalternativ till vänster på sidan.
8. Sidan utdata innehåller ett coordinator värdnamn med en knapp bredvid den att kopiera värdet till Urklipp. Registrera den här informationen för senare användning.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Azure Database for PostgreSQL-tjänsten använder en brandvägg på servernivå. Som standard förhindrar brandväggen alla externa program och verktyg ansluter till servern eller databaser på servern. Vi måste lägga till en regel som öppnar brandväggen för ett specifikt IP-adressintervall.

1. Från den **utdata** avsnitt där du tidigare kopierade coordinator noden värdnamn, klickar du på tillbaka till den **översikt** menyalternativ.

2. Hitta gruppen skalning för din distribution i listan över resurser och klicka på den. (Namnet inleds med ”sg-”.)

3. Klicka på **brandväggen** under **Security** på den vänstra menyn.

4. Klicka på länken **+ Lägg till brandväggsregel för aktuella klientens IP-adress**. Klicka slutligen på den **spara** knappen.

5. Klicka på **Spara**.

   > [!NOTE]
   > Azure PostgreSQL-servern kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 5432 bli nekad av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Ansluta till databasen med psql i Cloud Shell

Nu använder vi [psql](https://www.postgresql.org/docs/current/app-psql.html)-kommandoradsverktyget för att ansluta till Azure Database for PostgreSQL-servern.
1. Starta Azure Cloud Shell via terminalikonen överst i navigeringsfönstret.

   ![Azure Database för PostgreSQL – Azure Cloud Shell-terminalikonen](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. Azure Cloud Shell öppnas i din webbläsare så att du kan skriva bash-kommandon.

   ![Azure Database för PostgreSQL – Azure Shell Bash-prompten](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. I Cloud Shell-prompten ansluter du till din Azure Database för PostgreSQL-server med psql-kommandona. Följande format används för att ansluta till en Azure Database för PostgreSQL-server med [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html)-verktyget:
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Till exempel följande kommando ansluter till standarddatabasen som heter **citus** på din PostgreSQL-server **mydemoserver.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange ditt lösenord för serveradministratören när du uppmanas till detta.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

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

Vi ska också skapa en tabell som ska innehålla vårt aggregeringar per minut och en tabell som underhåller positionen för vår senaste samlade uppdateringen. Kör följande i psql samt:

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
      concat('https://example.com/', md5(random()::text)),
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
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Frågan lägger till en rad ungefär varje kvartal sekund. Rader som är lagrade på olika arbetsnoder enligt anvisningarna från kolumnen distribution `site_id`.

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

## <a name="performing-rollups"></a>Utför uppdateringar

Frågan ovan fungerar bra i de tidiga stadierna, men dess prestanda kommer att sänkas med ditt data. Även med distribuerad bearbetning av det går snabbare att förväg compute dessa data än beräkna om flera gånger.

Vi kan se till att vår instrumentpanel håller sig kvar snabbt genom att regelbundet samlar in rådata till en sammanställd tabell. I det här fallet vi ska slås samman till vår sammansättningstabellen för en minut, men du kan också ha aggregeringar på 5 minuter, 15 minuter, en timme, och så vidare.

Eftersom vi ständigt körs den här samlad ska vi skapa en funktion för att utföra den. Kör följande kommandon i psql att skapa den `rollup_http_request` funktion.

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

Och med våra data i form av en preaggregeras vi frågar Upplyft för att få samma rapport som tidigare. Kör följande:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Upphör att gälla gamla data

Uppdateringar göra frågor snabbare, men vi behöver att ta bort gamla data för att undvika obundna lagringskostnader. Bara bestämma hur länge du vill behålla data för varje granularitet och använda standard frågor för att ta bort utgångna data. I följande exempel beslutat att hålla rådata under en dag och per minut aggregeringar i en månad:

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