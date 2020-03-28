---
title: 'Självstudiekurs: Utforma en databas med flera innehavare – Hyperskala (Citus) - Azure-databas för PostgreSQL'
description: Den här självstudien visar hur du skapar, fyller i och frågar distribuerade tabeller på Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 17ac29de243f4abfff1cfc83fc6424799978bf0e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74978159"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Självstudiekurs: utforma en databas med flera innehavare med hjälp av Azure Database for PostgreSQL – Hyperscale (Citus)

I den här självstudien använder du Azure Database for PostgreSQL - Hyperscale (Citus) för att lära dig hur du:

> [!div class="checklist"]
> * Skapa en Hyperscale-servergrupp (Citus)
> * Använda psql-verktyget för att skapa ett schema
> * Fragmenttabeller över noder
> * Mata in exempeldata
> * Fråga klientdata
> * Dela data mellan klienter
> * Anpassa schemat per klient

## <a name="prerequisites"></a>Krav

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Använda psql-verktyget för att skapa ett schema

När du är ansluten till Azure Database for PostgreSQL - Hyperscale (Citus) med psql kan du utföra några grundläggande uppgifter. Den här självstudien går igenom att skapa en webbapp som gör det möjligt för annonsörer att spåra sina kampanjer.

Flera företag kan använda appen, så låt oss skapa en tabell för att hålla företag och ett annat för deras kampanjer. Kör dessa kommandon i PSQL-konsolen:

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

Varje kampanj betalar för att köra annonser. Lägg till en tabell för annonser också, genom att köra följande kod i psql efter koden ovan:

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

Slutligen spårar vi statistik om klick och visningar för varje annons:

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

Du kan se de nyskapade tabellerna i listan över tabeller som nu är i psql genom att köra:

```postgres
\dt
```

Program med flera innehavare kan endast framtvinga unikhet per klient, vilket är anledningen till att alla primära och externa nycklar innehåller företags-ID.

## <a name="shard-tables-across-nodes"></a>Fragmenttabeller över noder

En hyperskala distribution lagrar tabellrader på olika noder baserat på värdet för en användarbeteckning kolumn. Den här "distributionskolumnen" markerar vilken klient som äger vilka rader.

Nu ska vi ange att\_distributionskolumnen ska vara företags-ID, klientidentifieraren. I psql kör du dessa funktioner:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Mata in exempeldata

Utanför psql nu, i den normala kommandoraden, ladda ner exempeldatauppsättningar:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Tillbaka inuti psql, bulk ladda data. Var noga med att köra PSQL i samma katalog där du hämtade datafilerna.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Dessa data kommer nu att spridas över arbetsnoder.

## <a name="query-tenant-data"></a>Fråga klientdata

När programmet begär data för en enskild klient kan databasen köra frågan på en enda arbetarnod. En klientfrågor filtrerar efter ett enda klient-ID. Följande frågefilter `company_id = 5` för annonser och visningar. Prova att köra den i PSQL för att se resultatet.

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

Hittills har alla tabeller `company_id`distribuerats av , men vissa data inte naturligt "tillhör" någon klient i synnerhet, och kan delas. Alla företag i exempelvis annonsplattformen kanske vill hämta geografisk information för sin målgrupp baserat på IP-adresser.

Skapa en tabell för att lagra delad geografisk information. Kör följande kommandon i psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Gör `geo_ips` sedan en "referenstabell" för att lagra en kopia av tabellen på varje arbetsnod.

```sql
SELECT create_reference_table('geo_ips');
```

Ladda den med exempeldata. Kom ihåg att köra det här kommandot i PSQL inifrån katalogen där du hämtade datauppsättningen.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Att ansluta till klicktabellen med geo\_ips är effektivt på alla noder.
Här är en gå med för att hitta platser för alla som klickade på annons
290. Prova att köra frågan i psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Anpassa schemat per klient

Varje klient kan behöva lagra särskild information som inte behövs av andra. Alla klienter delar dock en gemensam infrastruktur med ett identiskt databasschema. Vart kan de extra uppgifterna ta vägen?

Ett knep är att använda en öppen kolumn typ som PostgreSQL's JSONB.  Vårt schema har ett JSONB-fält i `clicks` det anropade `user_data`.
Ett företag (säg företag fem) kan använda kolumnen för att spåra om användaren är på en mobil enhet.

Här är en fråga för vem som klickar mer: mobil eller traditionella besökare.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Vi kan optimera den här frågan för ett enda företag genom att skapa ett [partiellt index](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Mer allmänt kan vi skapa en [GIN index](https://www.postgresql.org/docs/current/static/gin-intro.html) på varje nyckel och värde i kolumnen.

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

I föregående steg skapade du Azure-resurser i en servergrupp. Om du inte förväntar dig att behöva dessa resurser i framtiden tar du bort servergruppen. Tryck på knappen *Ta bort* på sidan *Översikt* för servergruppen. När du uppmanas att göra det på en popup-sida *Delete* bekräftar du namnet på servergruppen och klickar på den slutliga delete-knappen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att etablera en citus-servergrupp (Hyperscale). Du har anslutit till den med psql, skapat ett schema och distribuerade data. Du har lärt dig att fråga data både inom och mellan klienter och anpassa schemat per klient.

Läs sedan om begreppen hyperskala.
> [!div class="nextstepaction"]
> [Nodtyper för hyperskala](https://aka.ms/hyperscale-concepts)
