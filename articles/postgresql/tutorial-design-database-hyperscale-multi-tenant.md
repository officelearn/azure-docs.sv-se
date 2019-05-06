---
title: Utforma en databas för flera innehavare med Azure Database for PostgreSQL – hyperskala (Citus) (förhandsversion) självstudien
description: Den här självstudien visar hur du skapar, fylla i och frågar distribuerade tabeller på Azure Database för PostgreSQL Hyperscale (Citus) (förhandsversion).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: b135baf73e21cd524b6e8fad35452362f36cf0c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080809"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Självstudie: utforma en databas för flera innehavare med hjälp av Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)

I den här självstudien använder du Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion) för att lära dig hur du:

> [!div class="checklist"]
> * Skapa en servergrupp i hyperskala (Citus)
> * Använd psql-verktyget för att skapa ett schema
> * Shard tabeller mellan noder
> * Mata in exempeldata
> * Fråga klientdata
> * Dela data mellan klienter
> * Anpassa den schemat per-klienten

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
   - Servergruppnamn: Ange ett unikt namn för den nya servergruppen som också används för en server underdomän.
   - Administratörens användarnamn: Ange ett unikt användarnamn kommer den att användas senare att ansluta till databasen.
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

   ![Azure Database för PostgreSQL – Azure Cloud Shell-terminalikonen](./media/tutorial-design-database-hyperscale-multi-tenant/psql-cloud-shell.png)

2. Azure Cloud Shell öppnas i din webbläsare så att du kan skriva bash-kommandon.

   ![Azure Database för PostgreSQL – Azure Shell Bash-prompten](./media/tutorial-design-database-hyperscale-multi-tenant/psql-bash.png)

3. I Cloud Shell-prompten ansluter du till din Azure Database för PostgreSQL-server med psql-kommandona. Följande format används för att ansluta till en Azure Database för PostgreSQL-server med [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html)-verktyget:
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Till exempel följande kommando ansluter till standarddatabasen som heter **citus** på din PostgreSQL-server **mydemoserver.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange ditt lösenord för serveradministratören när du uppmanas till detta.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>Använd psql-verktyget för att skapa ett schema

Efter att ha anslutit till Azure Database för PostgreSQL – kan hyperskala (Citus) (förhandsversion) med psql, du utföra några grundläggande uppgifter. Den här självstudien visar hur du skapar en webbapp som tillåter annonsörer att spåra sina kampanjer.

Flera företag kan använda appen, så vi skapar en tabell för att lagra företag och en annan för sina kampanjer. Kör följande kommandon i psql-konsolen:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Varje kampanj betalar att köra annonser. Lägg till en tabell för annonser, genom att köra följande kod i psql efter koden ovan:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Slutligen kan spårar vi statistik om klick och intrycket för varje annons:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Du kan se de nyligen skapade tabellerna i listan över tabeller nu i psql genom att köra:

```postgres
\dt
```

Program för flera innehavare kan genomdriva unikhet bara per klient, vilket är anledningen till alla primära nycklar och sekundärnycklar inkluderar företags-ID.

## <a name="shard-tables-across-nodes"></a>Shard tabeller mellan noder

En storskalig distribution lagrar tabellrader på olika noder baserat på värdet för en kolumn som anges av användaren. Den här ”distributionskolumn” som hämtas som klient äger vilka rader.

Vi ställer distributionskolumn vara företagets\_-id, klient-ID. Kör dessa funktioner i psql:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Mata in exempeldata

Ladda ned exempel på datauppsättningar i kommandoraden normala utanpå psql nu:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Tillbaka i psql massinläsa data. Glöm inte att köra psql i samma katalog som du laddade ned datafilerna.

```sql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Dessa data kommer nu att sprida över arbetsnoder.

## <a name="query-tenant-data"></a>Fråga klientdata

När programmet begär data för en enda klient, kan databasen köra frågan i en enda arbetsnod. Enda klient frågefilter genom en enda klient-ID. Till exempel följande fråga filter `company_id = 5` för annonser och intrycket. Försök att köra den i psql att se resultaten.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Dela data mellan klienter

Fram till nu alla tabeller har distribuerats av `company_id`, men vissa data naturligt ”tillhör inte” klientorganisation särskilt och kan delas. Alla företag i ad-plattformen exempel kanske exempelvis vill få geografisk information för sin publik baserat på IP-adresser.

Skapa en tabell för att lagra delade geografisk information. Kör det i psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Därefter gör `geo_ips` ”referenstabellen” att lagra en kopia av tabellen på varje arbetsnod.

```sql
SELECT create_reference_table('geo_ips');
```

Läs in exempeldata. Kom ihåg att köra detta i psql från inuti katalogen där du hämtade datauppsättningen.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Ansluta till tabellen klick med geo\_IP-adresser är effektivt på alla noder.
Här är en koppling för att hitta platser för alla som klickat på ad
290. Försök köra frågan i psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Anpassa den schemat per-klienten

Varje klient kan behöva lagra särskild information som krävs inte för andra. Alla klienter dela dock en vanlig infrastruktur med en identisk databasschemat. Kan de extra data var?

Ett tips är att använda en kunskapsuppsättning Kolumntyp som Postgresql's JSONB.  Vår schemat innehåller ett JSONB fält `clicks` kallas `user_data`.
Ett företag (exempelvis företaget fem) kan använda kolumnen för att spåra om användaren är på en mobil enhet.

Här är en fråga för att hitta som klickar på mer: mobila eller traditionella besökare.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Vi kan optimera den här frågan för ett enda företag genom att skapa en [partiella index](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Generellt kan vi skapa en [GIN index](https://www.postgresql.org/docs/current/static/gin-intro.html) på varje nyckel och värde i kolumnen.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en servergrupp. Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort servergruppen. Tryck på den *ta bort* knappen i den *översikt* sidan för din servergrupp. När du uppmanas att göra på en popup-sida bekräfta namnet på servergruppen och klickar på sista *ta bort* knappen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att etablera en servergrupp i hyperskala (Citus). Du är ansluten till den med psql, skapas ett schema och distribuerade data. Du har lärt dig att fråga data både inom och mellan klienter och för att anpassa det per klient.

Därefter lär dig mer om begreppet hyperskala.
> [!div class="nextstepaction"]
> [Hyperskala nodtyper](https://aka.ms/hyperscale-concepts)
