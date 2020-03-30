---
title: Tillägg – Hyperskala (Citus) – Azure-databas för PostgreSQL
description: Beskriver möjligheten att utöka databasens funktioner med hjälp av tillägg i Azure Database for PostgreSQL - Hyperscale (Citus)
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485411"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL-tillägg i Azure Database för PostgreSQL – Hyperskala (Citus)

PostgreSQL ger möjlighet att utöka funktionaliteten i databasen med hjälp av tillägg. Tillägg möjliggör buntning av flera relaterade SQL-objekt tillsammans i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. När du har lästs in i databasen kan tillägg fungera som inbyggda funktioner. Mer information om PostgreSQL-tillägg finns [i Paketrelaterade objekt i ett tillägg](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Använd PostgreSQL-tillägg

PostgreSQL-tillägg måste installeras i databasen innan du kan använda dem. Om du vill installera ett visst tillägg kör du kommandot [SKAPA TILLÄGG](https://www.postgresql.org/docs/current/static/sql-createextension.html) från psql-verktyget för att läsa in de paketerade objekten i databasen.

Azure Database for PostgreSQL - Hyperscale (Citus) stöder för närvarande en delmängd av nyckeltillägg som anges här. Andra tillägg än de som anges stöds inte. Du kan inte skapa ett eget tillägg med Azure Database för PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Tillägg som stöds av Azure Database för PostgreSQL

I följande tabeller listas standardtillägg för PostgreSQL som för närvarande stöds av Azure Database for PostgreSQL. Den här informationen är `SELECT * FROM pg_available_extensions;`också tillgänglig genom att köra .

### <a name="data-types-extensions"></a>Tillägg för datatyper

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** |
> |---|---|
> | [citext (citext)](https://www.postgresql.org/docs/current/static/citext.html) | Tillhandahåller en skiftlägesokänslig teckensträngtyp. |
> | [Kub](https://www.postgresql.org/docs/current/static/cube.html) | Tillhandahåller en datatyp för flerdimensionella kuber. |
> | [Hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Innehåller en datatyp för lagring av uppsättningar med nyckelvärdespar. |
> | [Hll](https://github.com/citusdata/postgresql-hll) | Tillhandahåller en HyperLogLog-datastruktur. |
> | [Isn](https://www.postgresql.org/docs/current/static/isn.html) | Tillhandahåller datatyper för internationella produktnumreringsstandarder. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Underhåll av stora objekt. |
> | [ltree (ltree)](https://www.postgresql.org/docs/current/static/ltree.html) | Tillhandahåller en datatyp för hierarkiska trädliknande strukturer. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Datatyp för representerar linjesegment eller flyttalsintervall. |
> | [topn (topn)](https://github.com/citusdata/postgresql-topn/) | Typ för top-n JSONB. |

### <a name="full-text-search-extensions"></a>Fullständiga tillägg för sökning

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Tillhandahåller en textsökordlista för heltal. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Mall för textsökningsordlista för utökad synonymbearbetning. |
> | [Unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | En textsökordlista som tar bort accenter (diakritiska tecken) från lexemes. |

### <a name="functions-extensions"></a>Funktioner tillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** |
> |---|---|
> | [autoinc (autoinc)](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funktioner för fält för automatisk inkrytning. |
> | [jordansen](https://www.postgresql.org/docs/current/static/earthdistance.html) | Ger ett sätt att beräkna storcirkelavstånd på jordens yta. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Innehåller flera funktioner för att bestämma likheter och avstånd mellan strängar. |
> | [infoga\_användarnamn](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funktioner för att spåra vem som har ändrat en tabell. |
> | [intagg (intagg)](https://www.postgresql.org/docs/current/intagg.html) | Heltalsaggregator och uppräkning (föråldrad). |
> | [intarray (intarray)](https://www.postgresql.org/docs/current/static/intarray.html) | Tillhandahåller funktioner och operatorer för att manipulera null-fria matriser av heltal. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funktioner för att spåra senaste ändringstid. |
> | [pgcrypto (pgcrypto)](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Tillhandahåller kryptografiska funktioner. |
> | [pg\_partman (pg partman)](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Hanterar partitionerade tabeller efter tid eller ID. |
> | [pg\_trgm (pg trgm)](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Tillhandahåller funktioner och operatorer för att bestämma likheten mellan alfanumerisk text baserat på trigrammatchning. |
> | [refint (refint)](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funktioner för att implementera referensintegritet (föråldrad). |
> | sessionsanalys\_ | Funktioner för att fråga hstore-matriser. |
> | [tablefunc (bordskrot)](https://www.postgresql.org/docs/current/static/tablefunc.html) | Innehåller funktioner som manipulerar hela tabeller, inklusive korstab. |
> | [tcn (tcn)](https://www.postgresql.org/docs/current/tcn.html) | Utlöst ändringsmeddelanden. |
> | [tidresa](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funktioner för att genomföra tidsresor. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Genererar universellt unika identifierare (UUIDs). |

### <a name="hyperscale-extensions"></a>Hyperskalatillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** |
> |---|---|
> | [citus (citus)](https://github.com/citusdata/citus) | Citus distribuerade databas. |
> | shard\_rebalancer | Balansera om data i en servergrupp på ett säkert sätt vid tillägg eller borttagning av nod. |

### <a name="index-types-extensions"></a>Tillägg för indextyper

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** |
> |---|---|
> | [Blom](https://www.postgresql.org/docs/current/bloom.html) | Bloom-åtkomstmetod - signaturfilbaserat index. |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Innehåller exempel-GIN-operatorklasser som implementerar B-trädliknande beteende för vissa datatyper. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Tillhandahåller GiST-indexoperatorklasser som implementerar B-träd. |

### <a name="language-extensions"></a>Språktillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** |
> |---|---|
> | [plpgsql (2010)](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL laddningsbart procedurspråk. |

### <a name="miscellaneous-extensions"></a>Diverse tillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** |
> |---|---|
> | [adminpack (adminpack)](https://www.postgresql.org/docs/current/adminpack.html) | Administrativa funktioner för PostgreSQL. |
> | [amcheck (amcheck)](https://www.postgresql.org/docs/current/amcheck.html) | Funktioner för att verifiera relationsintegritet. |
> | [fil\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Inplastare för främmande data för platt filåtkomst. |
> | [pageinspect (sidaspect)](https://www.postgresql.org/docs/current/pageinspect.html) | Kontrollera innehållet på databassidorna på en låg nivå. |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Ger ett sätt att undersöka vad som händer i den delade buffertcachen i realtid. |
> | [pg\_cron (pg cron)](https://github.com/citusdata/pg_cron) | Jobbschema för PostgreSQL. |
> | [pg\_freespacemap (pg freespacemap)](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Undersök den lediga utrymmeskartan (FSM). |
> | [pg\_förvar](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Här kan du läsa in relationsdata i buffertcachen. |
> | [pg\_\_stat uttalanden](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Ger ett sätt att spåra körningsstatistik för alla SQL-uttryck som körs av en server. I avsnittet "pg_stat_statements" finns information om det här tillägget. |
> | [pg\_synlighet](https://www.postgresql.org/docs/current/pgvisibility.html) | Undersök synlighetskartan (VM) och synlighetsinformationen på sidnivå. |
> | [pgrowlocks (pgrowlocks)](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Ger ett sätt att visa låsningsinformation på radnivå. |
> | [pgstattuple (pgstattuple)](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Ger ett sätt att visa statistik på tuppelnivå. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Inplastare för främmande data som används för att komma åt data som lagras i externa PostgreSQL-servrar. Se avsnittet "dblink och postgres_fdw" för information om det här tillägget.|
> | [sslinfo (sslinfo)](https://www.postgresql.org/docs/current/sslinfo.html) | Information om SSL-certifikat. |
> | [tsm-systemrader\_\_](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE-metoden, som accepterar antalet rader som en gräns. |
> | [tsm-systemtid\_\_](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE-metoden, som accepterar tid i millisekunder som en gräns. |
> | [hypopg (på efterkäpp)](https://hypopg.readthedocs.io/en/latest/) | Ger ett sätt att skapa hypotetiska index som inte kostar CPU eller disk. |
> | [dblink (dblink)](https://www.postgresql.org/docs/current/dblink.html) | En modul som stöder anslutningar till andra PostgreSQL-databaser inifrån en databassession. Se avsnittet "dblink och postgres_fdw" för information om det här tillägget. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath-frågor och XSLT. |


### <a name="postgis-extensions"></a>PostGIS-tillägg

> [!div class="mx-tableFixed"]
> | **Anknytning** | **Beskrivning** |
> |---|---|
> | [PostGIS](https://www.postgis.net/),\_postgis topologi,\_\_postgis tiger geocoder, postgis\_sfcgal | Rumsliga och geografiska objekt för PostgreSQL. |
> | adress\_standardiserare,\_adress\_\_standardiserare data oss | Används för att tolka en adress i ingående element. Används för att stödja geokodning adress normalisering steg. |
> | postgis\_sfcgal (olikartade) | PostGIS SFCGAL fungerar. |
> | postgis\_\_tiger geokodare | PostGIS tiger geocoder och omvänd geokodare. |
> | postgis\_topologi | Rumsliga typer och funktioner i PostGIS-topologin. |


## <a name="pg_stat_statements"></a>pg_stat_statements
Tillägget [\_för\_pg stat-satser](https://www.postgresql.org/docs/current/pgstatstatements.html) är förinläst på alla Azure Database för PostgreSQL-server för att ge dig ett sätt att spåra körningsstatistik för SQL-uttryck.

Inställningen `pg_stat_statements.track` styr vilka satser som räknas av tillägget. Det standardvärden `top`till , vilket innebär att alla utdrag som utfärdas direkt av klienter spåras. De två andra `none` spårningsnivåerna är och `all`. Den här inställningen kan konfigureras som en serverparameter via [Azure-portalen](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) eller [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Det finns en kompromiss mellan information om körning av frågor pg_stat_statements ger och effekten på serverns prestanda när den loggar varje SQL-uttryck. Om du inte aktivt använder tillägget pg_stat_statements rekommenderar vi `pg_stat_statements.track` att `none`du ställer in på . Vissa övervakningstjänster från tredje part kan förlita sig på pg_stat_statements för att leverera information om frågeprestanda, så bekräfta om så är fallet för dig eller inte.

## <a name="dblink-and-postgres_fdw"></a>dblink och postgres_fdw
Du kan använda dblink och postgres_fdw för att ansluta från en PostgreSQL-server till en annan eller till en annan databas på samma server. Den mottagande servern måste tillåta anslutningar från den sändande servern via brandväggen. Om du vill använda dessa tillägg för att ansluta mellan Azure Database för PostgreSQL-servrar anger du **Tillåt åtkomst till Azure-tjänster** till ON. Du måste också aktivera den här inställningen om du vill använda tilläggen för att loopa tillbaka till samma server. Inställningen **Tillåt åtkomst till Azure-tjänster** finns på Azure-portalsidan för Postgres-servern under **Anslutningssäkerhet**. Turning **Tillåt åtkomst till Azure-tjänster** PÅ vitlistar alla Azure IPs.

För närvarande stöds inte utgående anslutningar från Azure Database för PostgreSQL, förutom anslutningar till andra Azure-databas för PostgreSQL-servrar.
