---
title: Tillägg - Azure-databas för PostgreSQL - Single Server
description: Lär dig mer om tillgängliga Postgres-tillägg i Azure Database för PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201279"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-tillägg i Azure Database för PostgreSQL - Single Server
PostgreSQL ger möjlighet att utöka funktionaliteten i din databas med hjälp av tillägg. Tillägg buntar ihop flera relaterade SQL-objekt i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. Efter att ha lästs in i databasen fungerar tillägg som inbyggda funktioner.

## <a name="how-to-use-postgresql-extensions"></a>Så här använder du PostgreSQL-tillägg
PostgreSQL-tillägg måste installeras i databasen innan du kan använda dem. Om du vill installera ett visst tillägg kör du kommandot [SKAPA TILLÄGG](https://www.postgresql.org/docs/current/sql-createextension.html) från psql-verktyget för att läsa in de paketerade objekten i databasen.

Azure Database for PostgreSQL stöder en delmängd av viktiga tillägg som anges nedan. Den här informationen är `SELECT * FROM pg_available_extensions;`också tillgänglig genom att köra . Tillägg utöver de som anges stöds inte. Du kan inte skapa ett eget tillägg i Azure Database för PostgreSQL.

## <a name="postgres-11-extensions"></a>Postgres 11 förlängningar

Följande tillägg är tillgängliga i Azure Database för PostgreSQL-servrar som har Postgres version 11. 

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Används för att tolka en adress i ingående element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exempel på adressstandardisator-datauppsättning|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | stöd för indexering av vanliga datatyper i GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | stöd för indexering av vanliga datatyper i GiST|
> |[citext (citext)](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | datatyp för skiftlägesokänsliga teckensträngar|
> |[Kub](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | datatyp för flerdimensionella kuber|
> |[dblink (dblink)](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | textsökordlista för heltal|
> |[jordansen](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | beräkna storcirkelavstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | bestämma likheter och avstånd mellan strängar|
> |[Hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | datatyp för lagring av uppsättningar (nyckel, värde) par|
> |[hypopg (på efterkäpp)](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Hypotetiska index för PostgreSQL|
> |[intarray (intarray)](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funktioner, operatorer och indexstöd för 1D-matriser med heltal|
> |[Isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | datatyper för internationella produktnumreringsstandarder|
> |[ltree (ltree)](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | datatyp för hierarkiska trädliknande strukturer|
> |[Orafce](https://github.com/orafce/orafce)                       | 3.7             | Funktioner och operatörer som emulerar en delmängd av funktioner och paket från kommersiella RDBMS|
> |[pgaudit (pgaudit)](https://www.pgaudit.org/)                     | 1.3.1             | tillhandahåller granskningsfunktioner|
> |[pgcrypto (pgcrypto)](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrouting (pgrouting )](https://pgrouting.org/)                    | 2.6.2           | pgRouting Förlängning|
> |[pgrowlocks (pgrowlocks)](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | visa låsningsinformation på radnivå|
> |[pgstattuple (pgstattuple)](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | visa statistik på tuppelnivå|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | undersöka den delade buffertcachen|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Tillägg för att hantera partitionerade tabeller efter tid eller ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | relationsdata före varm|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | spåra körningsstatistik för alla SQL-uttryck som körs|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | textlikhetsmätning och indexsökning baserat på trigram|
> |[plpgsql (2010)](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL förfarandespråk|
> |[plv8 (på andra)](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) tillförlitligt förfarandespråk|
> |[Postgis](https://www.postgis.net/)                      | 2.5.1           | Rumsliga typer och funktioner och funktioner och funktioner för PostGIS-geometri, geografi och raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger geokodare och omvänd geokodare|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Rumsliga typer och funktioner i PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | inplastare för fjärrdatapostagreSQL-servrar|
> |[tablefunc (bordskrot)](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funktioner som manipulerar hela tabeller, inklusive crosstab|
> |[tidsskaladb](https://docs.timescale.com/latest)                    | 1.3.2             | Aktiverar skalbara skär och komplexa frågor för tidsseriedata|
> |[Unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | textsökningsordlista som tar bort accenter|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | generera universellt unika identifierare (UUIDs)|

## <a name="postgres-10-extensions"></a>Postgres 10 förlängningar 

Följande tillägg är tillgängliga i Azure Database för PostgreSQL-servrar som har Postgres version 10.

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Används för att tolka en adress i ingående element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exempel på adressstandardisator-datauppsättning|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | stöd för indexering av vanliga datatyper i GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | stöd för indexering av vanliga datatyper i GiST|
> |[chkpass (olika)](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | datatyp för automatiskt krypterade lösenord|
> |[citext (citext)](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | datatyp för skiftlägesokänsliga teckensträngar|
> |[Kub](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | datatyp för flerdimensionella kuber|
> |[dblink (dblink)](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | textsökordlista för heltal|
> |[jordansen](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | beräkna storcirkelavstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | bestämma likheter och avstånd mellan strängar|
> |[Hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | datatyp för lagring av uppsättningar (nyckel, värde) par|
> |[hypopg (på efterkäpp)](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetiska index för PostgreSQL|
> |[intarray (intarray)](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | funktioner, operatorer och indexstöd för 1D-matriser med heltal|
> |[Isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | datatyper för internationella produktnumreringsstandarder|
> |[ltree (ltree)](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | datatyp för hierarkiska trädliknande strukturer|
> |[Orafce](https://github.com/orafce/orafce)                       | 3.7             | Funktioner och operatörer som emulerar en delmängd av funktioner och paket från kommersiella RDBMS|
> |[pgaudit (pgaudit)](https://www.pgaudit.org/)                     | 1.2             | tillhandahåller granskningsfunktioner|
> |[pgcrypto (pgcrypto)](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrouting (pgrouting )](https://pgrouting.org/)                    | 2.5.2           | pgRouting Förlängning|
> |[pgrowlocks (pgrowlocks)](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | visa låsningsinformation på radnivå|
> |[pgstattuple (pgstattuple)](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | visa statistik på tuppelnivå|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | undersöka den delade buffertcachen|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Tillägg för att hantera partitionerade tabeller efter tid eller ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | relationsdata före varm|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | spåra körningsstatistik för alla SQL-uttryck som körs|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | textlikhetsmätning och indexsökning baserat på trigram|
> |[plpgsql (2010)](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL förfarandespråk|
> |[plv8 (på andra)](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) tillförlitligt förfarandespråk|
> |[Postgis](https://www.postgis.net/)                      | 2.4.3           | Rumsliga typer och funktioner och funktioner och funktioner för PostGIS-geometri, geografi och raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS tiger geokodare och omvänd geokodare|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | Rumsliga typer och funktioner i PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | inplastare för fjärrdatapostagreSQL-servrar|
> |[tablefunc (bordskrot)](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funktioner som manipulerar hela tabeller, inklusive crosstab|
> |[tidsskaladb](https://docs.timescale.com/latest)                    | 1.1.1             | Aktiverar skalbara skär och komplexa frågor för tidsseriedata|
> |[Unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | textsökningsordlista som tar bort accenter|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | generera universellt unika identifierare (UUIDs)|

## <a name="postgres-96-extensions"></a>Postgres 9,6 förlängningar 

Följande tillägg är tillgängliga i Azure Database för PostgreSQL-servrar som har Postgres version 9.6.

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Används för att tolka en adress i ingående element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Exempel på adressstandardisator-datauppsättning|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | stöd för indexering av vanliga datatyper i GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | stöd för indexering av vanliga datatyper i GiST|
> |[chkpass (olika)](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | datatyp för automatiskt krypterade lösenord|
> |[citext (citext)](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | datatyp för skiftlägesokänsliga teckensträngar|
> |[Kub](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | datatyp för flerdimensionella kuber|
> |[dblink (dblink)](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | textsökordlista för heltal|
> |[jordansen](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | beräkna storcirkelavstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | bestämma likheter och avstånd mellan strängar|
> |[Hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | datatyp för lagring av uppsättningar (nyckel, värde) par|
> |[hypopg (på efterkäpp)](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetiska index för PostgreSQL|
> |[intarray (intarray)](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | funktioner, operatorer och indexstöd för 1D-matriser med heltal|
> |[Isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | datatyper för internationella produktnumreringsstandarder|
> |[ltree (ltree)](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | datatyp för hierarkiska trädliknande strukturer|
> |[Orafce](https://github.com/orafce/orafce)                       | 3.7             | Funktioner och operatörer som emulerar en delmängd av funktioner och paket från kommersiella RDBMS|
> |[pgaudit (pgaudit)](https://www.pgaudit.org/)                     | 1.1.2             | tillhandahåller granskningsfunktioner|
> |[pgcrypto (pgcrypto)](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrouting (pgrouting )](https://pgrouting.org/)                    | 2.3.2           | pgRouting Förlängning|
> |[pgrowlocks (pgrowlocks)](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | visa låsningsinformation på radnivå|
> |[pgstattuple (pgstattuple)](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | visa statistik på tuppelnivå|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | undersöka den delade buffertcachen|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Tillägg för att hantera partitionerade tabeller efter tid eller ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | relationsdata före varm|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | spåra körningsstatistik för alla SQL-uttryck som körs|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | textlikhetsmätning och indexsökning baserat på trigram|
> |[plpgsql (2010)](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL förfarandespråk|
> |[plv8 (på andra)](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) tillförlitligt förfarandespråk|
> |[Postgis](https://www.postgis.net/)                      | 2.3.2           | Rumsliga typer och funktioner och funktioner och funktioner för PostGIS-geometri, geografi och raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS tiger geokodare och omvänd geokodare|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Rumsliga typer och funktioner i PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | inplastare för fjärrdatapostagreSQL-servrar|
> |[tablefunc (bordskrot)](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funktioner som manipulerar hela tabeller, inklusive crosstab|
> |[tidsskaladb](https://docs.timescale.com/latest)                    | 1.1.1             | Aktiverar skalbara skär och komplexa frågor för tidsseriedata|
> |[Unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | textsökningsordlista som tar bort accenter|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | generera universellt unika identifierare (UUIDs)|

## <a name="postgres-95-extensions"></a>Postgres 9,5 förlängningar 

Följande tillägg är tillgängliga i Azure Database för PostgreSQL-servrar som har Postgres version 9.5.

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Används för att tolka en adress i ingående element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Exempel på adressstandardisator-datauppsättning|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | stöd för indexering av vanliga datatyper i GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | stöd för indexering av vanliga datatyper i GiST|
> |[chkpass (olika)](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | datatyp för automatiskt krypterade lösenord|
> |[citext (citext)](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | datatyp för skiftlägesokänsliga teckensträngar|
> |[Kub](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | datatyp för flerdimensionella kuber|
> |[dblink (dblink)](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | textsökordlista för heltal|
> |[jordansen](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | beräkna storcirkelavstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | bestämma likheter och avstånd mellan strängar|
> |[Hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | datatyp för lagring av uppsättningar (nyckel, värde) par|
> |[hypopg (på efterkäpp)](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetiska index för PostgreSQL|
> |[intarray (intarray)](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | funktioner, operatorer och indexstöd för 1D-matriser med heltal|
> |[Isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | datatyper för internationella produktnumreringsstandarder|
> |[ltree (ltree)](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | datatyp för hierarkiska trädliknande strukturer|
> |[Orafce](https://github.com/orafce/orafce)                       | 3.7             | Funktioner och operatörer som emulerar en delmängd av funktioner och paket från kommersiella RDBMS|
> |[pgaudit (pgaudit)](https://www.pgaudit.org/)                     | 1.0.7             | tillhandahåller granskningsfunktioner|
> |[pgcrypto (pgcrypto)](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | kryptografiska funktioner|
> |[pgrouting (pgrouting )](https://pgrouting.org/)                    | 2.3.0           | pgRouting Förlängning|
> |[pgrowlocks (pgrowlocks)](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | visa låsningsinformation på radnivå|
> |[pgstattuple (pgstattuple)](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | visa statistik på tuppelnivå|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | undersöka den delade buffertcachen|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Tillägg för att hantera partitionerade tabeller efter tid eller ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | relationsdata före varm|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | spåra körningsstatistik för alla SQL-uttryck som körs|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | textlikhetsmätning och indexsökning baserat på trigram|
> |[plpgsql (2010)](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL förfarandespråk|
> |[Postgis](https://www.postgis.net/)                      | 2.3.0           | Rumsliga typer och funktioner och funktioner och funktioner för PostGIS-geometri, geografi och raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS tiger geokodare och omvänd geokodare|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Rumsliga typer och funktioner i PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | inplastare för fjärrdatapostagreSQL-servrar|
> |[tablefunc (bordskrot)](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funktioner som manipulerar hela tabeller, inklusive crosstab|
> |[Unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | textsökningsordlista som tar bort accenter|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | generera universellt unika identifierare (UUIDs)|


## <a name="pg_stat_statements"></a>pg_stat_statements
Det [pg_stat_statements tillägget](https://www.postgresql.org/docs/current/pgstatstatements.html) är förinläst på alla Azure Database för PostgreSQL-server för att ge dig ett sätt att spåra körningsstatistik för SQL-uttryck.
Inställningen `pg_stat_statements.track`, som styr vilka satser som räknas `top`av tillägget, som standard , vilket innebär att alla utdrag som utfärdas direkt av klienter spåras. De två andra `none` spårningsnivåerna är och `all`. Den här inställningen kan konfigureras som en serverparameter via [Azure-portalen](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) eller [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Det finns en kompromiss mellan information om körning av frågor pg_stat_statements ger och inverkan på serverns prestanda när den loggar varje SQL-uttryck. Om du inte aktivt använder tillägget pg_stat_statements rekommenderar vi `pg_stat_statements.track` att `none`du ställer in på . Observera att vissa övervakningstjänster från tredje part kan förlita sig på pg_stat_statements för att leverera information om frågeprestanda, så bekräfta om så är fallet för dig eller inte.

## <a name="dblink-and-postgres_fdw"></a>dblink och postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) och [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) kan du ansluta från en PostgreSQL-server till en annan, eller till en annan databas på samma server. Den mottagande servern måste tillåta anslutningar från den sändande servern via brandväggen. När du använder dessa tillägg för att ansluta mellan Azure Database för PostgreSQL-servrar kan detta göras genom att ange "Tillåt åtkomst till Azure-tjänster" till ON. Detta behövs också om du vill använda tilläggen för att loopa tillbaka till samma server. Inställningen "Tillåt åtkomst till Azure-tjänster" finns på Azure-portalsidan för Postgres-servern under Anslutningssäkerhet. Om du aktiverar "Tillåt åtkomst till Azure-tjänster" på placeras alla Azure-IPs i listan över tillåtna.

För närvarande stöds inte utgående anslutningar från Azure Database för PostgreSQL, förutom anslutningar till andra Azure-databas för PostgreSQL-servrar.

## <a name="uuid"></a>uuid
Om du planerar `uuid_generate_v4()` att använda från [uuid-ossp](https://www.postgresql.org/docs/current/uuid-ossp.html) `gen_random_uuid()` förlängning , överväga att jämföra med från [pgcrypto förlängning](https://www.postgresql.org/docs/current/pgcrypto.html) för prestanda fördelar.

## <a name="pgaudit"></a>pgAudit (på nytt)
[Tillägget pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) ger sessions- och objektgranskningsloggning. Mer information om hur du använder det här tillägget i Azure Database för PostgreSQL finns i [artikeln för granskningsbegrepp](concepts-audit.md). 

## <a name="pg_prewarm"></a>pg_prewarm
Det pg_prewarm tillägget läser in relationsdata i cacheminnet. Förvärmning av cacheminnen innebär att dina frågor har bättre svarstider på sin första körning efter en omstart. I Postgres 10 och nedan sker förvärmning manuellt med hjälp av [förvärmningsfunktionen](https://www.postgresql.org/docs/10/pgprewarm.html).

I Postgres 11 och högre kan du konfigurera förvärmning så att det sker [automatiskt.](https://www.postgresql.org/docs/current/pgprewarm.html) Du måste inkludera pg_prewarm i `shared_preload_libraries` parameterns lista och starta om servern för att kunna tillämpa ändringen. Parametrar kan ställas in från [Azure-portalen,](howto-configure-server-parameters-using-portal.md) [CLI,](howto-configure-server-parameters-using-cli.md)REST API eller ARM-mallen. 

## <a name="timescaledb"></a>TidsskalaDB
TimescaleDB är en tidsseriedatabas som paketerats som ett tillägg för PostgreSQL. TimescaleDB tillhandahåller tidsorienterade analytiska funktioner, optimeringar och skalor Postgres för arbetsbelastningar i tidsserier.

[Läs mer om TimescaleDB](https://docs.timescale.com/latest), ett registrerat varumärke som tillhör [Timescale, Inc.](https://www.timescale.com/). Azure Database för PostgreSQL tillhandahåller den öppna källkodsversionen av Tidsskala. Mer information om vilka tidsskalefunktioner som är tillgängliga i den här versionen finns [i produktjämn jämförelsen tidsskala](https://www.timescale.com/products/).

### <a name="installing-timescaledb"></a>Installera timescaleDB
Om du vill installera TimescaleDB måste du inkludera den i serverns delade förinläsningsbibliotek. En ändring av Postgres `shared_preload_libraries` parameter kräver att en omstart av **servern** börjar gälla. Du kan ändra parametrar med hjälp av [Azure-portalen](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).

Använda [Azure-portalen:](https://portal.azure.com/)

1. Välj din Azure Database for PostgreSQL-server.

2. Välj **Serverparametrar**i sidofältet .

3. Sök efter `shared_preload_libraries` parametern.

4. Välj **TimescaleDB**.

5. Välj **Spara** om du vill bevara ändringarna. Du får en avisering när ändringen har sparats. 

6. Efter meddelandet **startar du om** servern för att tillämpa ändringarna. Mer information om hur du startar om en server finns i [Starta om en Azure-databas för PostgreSQL-server](howto-restart-server-portal.md).


Du kan nu aktivera TimescaleDB i din Postgres-databas. Anslut till databasen och utfärda följande kommando:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Om ett fel visas bekräftar du att du har startat om servern efter att du har [sparat](howto-restart-server-portal.md) shared_preload_libraries. 

Du kan nu skapa en tidskalebarDB hypertable [från grunden](https://docs.timescale.com/getting-started/creating-hypertables) eller migrera [befintliga tidsseriedata i PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Återställa en tidsskaledatabas
Om du vill återställa en tidsskaledatabas med pg_dump och pg_restore måste du köra `timescaledb_pre_restore()` `timescaledb_post restore()`två hjälpprocedurer i måldatabasen: och .

Förbered först måldatabasen:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Nu kan du köra pg_dump i den ursprungliga databasen och sedan göra pg_restore. Efter återställningen måste du köra följande kommando i den återställda databasen:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Nästa steg
Kontakta oss om du inte ser ett tillägg som du vill använda. Rösta på befintliga förfrågningar eller skapa nya feedbackförfrågningar i vårt [feedbackforum.](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)
