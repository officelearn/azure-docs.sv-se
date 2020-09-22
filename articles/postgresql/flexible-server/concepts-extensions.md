---
title: Tillägg – Azure Database for PostgreSQL-flexibel Server
description: Lär dig mer om tillgängliga postgres-tillägg i Azure Database for PostgreSQL-flexibel Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1126c218f8e80b7d89183746890a3fae1357d29d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941321"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL-tillägg i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

PostgreSQL ger möjlighet att utöka funktionerna i databasen med hjälp av tillägg. Tillägg buntar samman flera relaterade SQL-objekt i ett enda paket som kan läsas in eller tas bort från databasen med ett kommando. När tilläggen har lästs in i databasen fungerar de som inbyggda funktioner.

## <a name="how-to-use-postgresql-extensions"></a>Använda PostgreSQL-tillägg
PostgreSQL-tillägg måste vara installerade i databasen innan du kan använda dem. Om du vill installera ett visst tillägg kör du kommandot [create Extension](https://www.postgresql.org/docs/current/sql-createextension.html) . Det här kommandot läser in paketerade objekt i databasen.

Azure Database for PostgreSQL stöder en delmängd av nyckel tillägg som anges nedan. Den här informationen är också tillgänglig genom att köra `SHOW azure.extensions;` . Tillägg som inte finns med i det här dokumentet stöds inte på Azure Database for PostgreSQL-flexibel Server. Du kan inte skapa eller läsa in ditt eget tillägg i Azure Database for PostgreSQL.


## <a name="postgres-12-extensions"></a>Postgres 12-tillägg

Följande tillägg är tillgängliga i Azure Database for PostgreSQL-flexibla servrar som har postgres version 12. 

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1,2             | funktioner för att verifiera Relations integritet|
> |[hinna](https://www.postgresql.org/docs/12/bloom.html)                    | 1,0             | anslutnings metod för blomma – signatur-filbaserat index|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | stöd för indexering av vanliga data typer i GIN|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | stöd för indexering av vanliga data typer i registret|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | datatyp för Skift läges känsliga sträng strängar|
> |[kuben](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | datatyp för flerdimensionella kuber|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1,2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1,0             | mall för text Sök lexikon för heltal|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1,0             | mall för text Sök lexikon för utökad synonym bearbetning|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1,1             | beräkna suveräna cirkel avstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1,1             | Bestäm likheter och avstånd mellan strängar|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | datatyp för lagring av uppsättningar av (nyckel, värde) par|
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1,1             | heltals Aggregator och uppräknare. Ersatt|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1,2             | funktioner, operatörer och index stöd för en 1d-matris med heltal|
> |[inte är](https://www.postgresql.org/docs/12/isn.html)                          | 1,2             | data typer för internationella standarder för produkt nummer|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1,1             | datatyp för hierarkiska träd strukturer|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1,7             | granska innehållet i databas sidor på en låg nivå|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | granska cacheminnet för den delade bufferten|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1,2             | kontrol lera kartan över ledigt utrymme (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1,2             | Förvärm Relations data|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1,7             | spåra körnings statistik för alla SQL-instruktioner som körs|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | text likhets mätning och indexs ökning baserat på trigrams|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1,2             | granska Synlighets kartan (VM) och Synlighets informationen på sid nivå|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | tillhandahåller gransknings funktioner|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1,2             | Visa lås information på radnivå|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | Visa statistik på tuple nivå|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1,0             | PL/pgSQL Procedure language|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS geometri, geografi och raster avstånds typer och funktioner|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1,0             | sekundär data omslutning för fjärranslutna PostgreSQL-servrar|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1,2             | information om SSL-certifikat|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1,0             |  TABLESAMPLE-metod som accepterar antalet rader som en gräns|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1,0             |  TABLESAMPLE-metod som accepterar tiden i millisekunder som en gräns|
> |[avaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1,1             | texts öknings ord lista som tar bort accenttecken|
> |[UUID – ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1,1             | generera globalt unika identifierare (UUID)|

## <a name="postgres-11-extensions"></a>Postgres 11-tillägg

Följande tillägg är tillgängliga i Azure Database for PostgreSQL-flexibla servrar som har postgres version 11. 

> [!div class="mx-tableFixed"]
> | **Anknytning**| **Tilläggsversion** | **Beskrivning** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1,1             | funktioner för att verifiera Relations integritet|
> |[hinna](https://www.postgresql.org/docs/11/bloom.html)                    | 1,0             | anslutnings metod för blomma – signatur-filbaserat index|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | stöd för indexering av vanliga data typer i GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | stöd för indexering av vanliga data typer i registret|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | datatyp för Skift läges känsliga sträng strängar|
> |[kuben](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | datatyp för flerdimensionella kuber|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1,2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1,0             | mall för text Sök lexikon för heltal|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1,0             | mall för text Sök lexikon för utökad synonym bearbetning|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1,1             | beräkna suveräna cirkel avstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1,1             | Bestäm likheter och avstånd mellan strängar|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | datatyp för lagring av uppsättningar av (nyckel, värde) par|
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1,1             | heltals Aggregator och uppräknare. Ersatt|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1,2             | funktioner, operatörer och index stöd för en 1d-matris med heltal|
> |[inte är](https://www.postgresql.org/docs/11/isn.html)                          | 1,2             | data typer för internationella standarder för produkt nummer|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1,1             | datatyp för hierarkiska träd strukturer|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1,7             | granska innehållet i databas sidor på en låg nivå|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | granska cacheminnet för den delade bufferten|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1,2             | kontrol lera kartan över ledigt utrymme (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1,2             | Förvärm Relations data|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | spåra körnings statistik för alla SQL-instruktioner som körs|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | text likhets mätning och indexs ökning baserat på trigrams|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1,2             | granska Synlighets kartan (VM) och Synlighets informationen på sid nivå|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | tillhandahåller gransknings funktioner|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1,2             | Visa lås information på radnivå|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Visa statistik på tuple nivå|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1,0             | PL/pgSQL Procedure language|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometri, geografi och raster avstånds typer och funktioner|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1,0             | sekundär data omslutning för fjärranslutna PostgreSQL-servrar|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1,2             | information om SSL-certifikat|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1,0             | funktioner som ändrar hela tabeller, inklusive kors|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1,0             |  TABLESAMPLE-metod som accepterar antalet rader som en gräns|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1,0             |  TABLESAMPLE-metod som accepterar tiden i millisekunder som en gräns|
> |[avaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1,1             | texts öknings ord lista som tar bort accenttecken|
> |[UUID – ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1,1             | generera globalt unika identifierare (UUID)|


## <a name="pg_prewarm"></a>pg_prewarm

Pg_prewarm-tillägget läser in relations data i cacheminnet. Att förvärma cacheminnet innebär att dina frågor har bättre svars tider vid första körningen efter en omstart. De automatiska för inaktiverade funktionerna är för närvarande inte tillgängliga i Azure Database for PostgreSQL-flexibel Server.


## <a name="next-steps"></a>Nästa steg

Om du inte ser ett tillägg som du vill använda, kan du berätta för oss. Rösta på befintliga förfrågningar eller skapa nya feedback-förfrågningar i vårt [feedback-forum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).