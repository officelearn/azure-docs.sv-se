---
title: Använd PostgreSQL-tillägg
description: Använd PostgreSQL-tillägg
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 58386786266c48c6e721094f9f2837709bb684e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631774"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Använd PostgreSQL-tillägg i Azure Arc-aktiverade PostgreSQL för storskalig Server grupp

PostgreSQL är på bästa sätt när du använder den med tillägg. I själva verket är ett viktigt-element i våra egna funktioner i den Microsoft-baserade `citus` tillägget som installeras som standard, vilket gör att postgres kan Shard data transparent över flera noder.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>Lista över tillägg
Förutom tilläggen i [`contrib`](https://www.postgresql.org/docs/12/contrib.html) är listan över tillägg som finns i behållarna för din Azure Arc-aktiverad postgresql-server grupp:
- `citus`, v: 9,4
- `pg_cron`, v: 1,2
- `plpgsql`, v: 1,0
- `postgis`, v: 3.0.2
- `plv8`, v: 2.3.14

Den här listan utvecklar övertid och uppdateringar publiceras i det här dokumentet. Det går ännu inte att lägga till tillägg utöver de som anges ovan.

Den här guiden tar i ett scenario att använda två av dessa tillägg:
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>Hantera tillägg

### <a name="enable-extensions"></a>Aktivera tillägg
Det här steget behövs inte för de tillägg som ingår i `contrib` .
Det allmänna formatet för kommandot för att aktivera tillägg är:

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>Aktivera ett tillägg när du skapar en Server grupp:
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>Aktivera ett tillägg på en instans som redan finns:
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>Hämta listan över aktiverade tillägg:
Kör något av följande kommando.

##### <a name="with-azdata"></a>Med azdata
```console
azdata arc postgres server show -n <server group name>
```
Bläddra i utdata och Lägg märke till engine\extensions-avsnitten i specifikationerna för din server grupp. Exempel:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
##### <a name="with-kubectl"></a>Med kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Bläddra i utdata och Lägg märke till engine\extensions-avsnitten i specifikationerna för din server grupp. Exempel:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


### <a name="create-extensions"></a>Skapa tillägg:
Anslut till din server grupp med det klient verktyg du väljer och kör standard frågan för PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>Hämta listan över tillägg som skapats i Server gruppen:
Anslut till din server grupp med det klient verktyg du väljer och kör standard frågan för PostgreSQL:
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>Ta bort ett tillägg från Server gruppen:
Anslut till din server grupp med det klient verktyg du väljer och kör standard frågan för PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>Använd PostGIS och Pg_cron tillägg

### <a name="the-postgis-extension"></a>PostGIS-tillägget

Vi kan antingen aktivera PostGIS-tillägget på en befintlig Server grupp eller skapa en ny med tillägget redan aktiverat:

**Aktivera ett tillägg när du skapar en Server grupp:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**Aktivera ett tillägg på en instans som redan finns:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

Du kan kontrol lera vilka tillägg som är installerade genom att använda nedanstående standard PostgreSQL-kommando när du har anslutit till instansen med ditt favorit verktyg för PostgreSQL-klient som Azure Data Studio:
```console
select * from pg_extension;
```

För ett PostGIS-exempel kan du börja med att hämta [exempel data](http://duspviz.mit.edu/tutorials/intro-postgis/) från MIT-avdelningen i tätorts studier & planera. Du kan behöva köra `apt-get install unzip` för att installera zippa när du använder den virtuella datorn för testning.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Nu ska vi ansluta till vår databas och skapa PostGIS-tillägget:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Om du vill använda ett tillägg i `postgis` paketet (till exempel,, `postgis_raster` `postgis_topology` .. `postgis_sfcgal` `fuzzystrmatch` .) måste du först skapa postgis-tillägget och sedan skapa det andra tillägget. Till exempel: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

Och skapa schemat:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Nu kan vi kombinera PostGIS med funktionen skala ut genom att göra den coffee_shops tabellen distribuerad:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Vi läser in några data:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

Och fyll i `geom` fältet med korrekt kodad latitud och longitud i `geometry` data typen postgis:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Nu kan vi visa en lista över de kaffe butiker som är närmast MIT (77 Massachusetts Ara vid 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>Pg_cron-tillägget

Vi aktiverar `pg_cron` vår postgresql-server grupp, förutom postgis:

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

Observera att detta startar om noderna och installerar ytterligare tillägg, vilket kan ta 2-3 minuter.

Nu kan vi ansluta igen och skapa `pg_cron` tillägget:

```sql
CREATE EXTENSION pg_cron;
```

I test syfte kan du göra en tabell `the_best_coffee_shop` som tar ett slumpmässigt namn från vår tidigare `coffee_shops` tabell och anger tabellens innehåll:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

Vi kan använda `cron.schedule` plus några SQL-uttryck för att få ett slumpmässigt tabell namn (Observera att en temporär tabell används för att lagra ett resultat för en distribuerad fråga) och lagra den i `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

Och nu får vi ett annat namn på en minut:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Se [PG_CRON viktigt](https://github.com/citusdata/pg_cron) om du vill ha fullständig information om syntaxen.

>[!NOTE]
>Det går inte att släppa `citus` tillägget. `citus`Tillägget krävs för att tillhandahålla skalnings upplevelsen.

## <a name="next-steps"></a>Nästa steg:
- Läs dokumentationen om [plv8](https://plv8.github.io/)
- Läs dokumentationen om [postgis](https://postgis.net/)
- Läs dokumentationen om [`pg_cron`](https://github.com/citusdata/pg_cron)
