---
title: PostgreSQL-tillägg i Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)
description: Beskriver möjlighet att utöka funktionerna i din databas med tillägg i Azure Database för PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 4022c95bfda8cbdaed75876793bfbba4254a5c54
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410257"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---hyperscale-citus-preview"></a>PostgreSQL-tillägg i Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)

PostgreSQL ger möjlighet att utöka funktionerna i din databas med tillägg. Tillägg kan paketera flera relaterade SQL-objekt tillsammans i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. Tillägg kan fungera som gör de inbyggda funktionerna för efter att läsas in i databasen. Läs mer på PostgreSQL-tillägg, [paketering relaterade objekt i ett tillägg](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Hur du använder PostgreSQL-tillägg

PostgreSQL-tillägg måste installeras i databasen innan du kan använda dem. Installera ett visst tillägg genom att köra den [skapa tillägg](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) från psql-verktyget för att läsa in paketerade objekten i databasen.

Azure Database för PostgreSQL stöder för närvarande en delmängd av viktiga tillägg som anges ovan. Tillägg utöver de som visas stöds inte. Du kan inte skapa dina egna tillägg med Azure Database för PostgreSQL-tjänsten.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Tillägg som stöds av Azure Database för PostgreSQL

I tabellerna nedan listas de standard PostgreSQL-tillägg som för närvarande stöds av Azure Database för PostgreSQL. Den här informationen är också tillgängligt genom att köra `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Datatillägg för typer

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Innehåller datatypen är skiftlägeskänslig tecken. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Ger en datatyp för flerdimensionella kuber. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Innehåller en datatyp för att lagra uppsättningar med nyckel/värde-par. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Innehåller datatyper för internationella produkten numrering standarder. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Stora objektunderhåll. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Ger en datatyp för den hierarkiska träd-liknande strukturer. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Datatypen för att representera linjesegment eller med intervall. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Typ för topp-n JSONB. |

### <a name="functions-extensions"></a>Functions-tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funktioner för fält som ökar automatiskt. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Innebär att beräkna bra cirkel avstånd på jordens yta. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Har flera funktioner för att fastställa likheter och avståndet mellan strängar. |
> | [Infoga\_användarnamn](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funktioner för att spåra vem som har ändrat en tabell. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Heltal aggregator och uppräknare (föråldrad). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Tillhandahåller funktioner och operatorer för att manipulera null kostnadsfria matriser med heltal. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funktioner för att spåra tid för senaste ändring. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Tillhandahåller kryptografiska funktioner. |
> | [PG\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Hanterar partitionerade tabeller genom tid eller -ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Innehåller funktioner och operatorer för att fastställa likheten mellan alfanumerisk text baserat på trigram matchning. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funktioner för att implementera referensintegritet (föråldrad). |
> | sessionen\_analytics | Funktioner för att fråga hstore matriser. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Innehåller funktioner som manipulerar hela tabeller, inklusive korstabell. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Ändra vilka meddelanden som utlöses. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funktioner för att implementera tid resa. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genererar universell unik identifierare (UUID). |

### <a name="full-text-search-extensions"></a>Fulltextsökning tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Innehåller en mall för text search ordlista för heltal. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Text search ordlista mall för utökade synonymen bearbetning. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | En text search ordlista som tar bort uttal (diakritiska tecken) från lexemes. |

### <a name="index-types-extensions"></a>Index typer tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [Blom](https://www.postgresql.org/docs/current/bloom.html) | Blom åtkomstmetod - signaturen för filbaserad index. |
> | [b-trädet\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Innehåller exempel GIN operatorn klasser som implementerar B-trädet som beteendet för vissa datatyper. |
> | [b-trädet\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Innehåller GiST index operatorn klasser som implementerar B-trädet. |

### <a name="language-extensions"></a>Språktillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL kan läsas in procedurmässig språk. |

### <a name="hyperscale-extensions"></a>Hyperskala tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus distribuerad databas. |
> | shard\_rebalancer | Balansera om data i en servergrupp vid noden tillägg eller borttagning på ett säkert sätt. |

### <a name="miscellaneous-extensions"></a>Diverse tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Administrativa funktioner för PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funktioner för att verifiera integriteten för relationen. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Främmande data Omslutning för platt filåtkomst. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Kontrollerar du innehållet i databassidor på en låg nivå. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Ger möjlighet att undersöka vad som händer i delade buffert cachen i realtid. |
> | [PG\_cron](https://github.com/citusdata/pg_cron) | Jobbschemat för PostgreSQL. |
> | [PG\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Granska kartan ledigt utrymme (FSM). |
> | [PG\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Är ett sätt att läsa in relationsdata till bufferten-cachen. |
> | [PG\_stat\_uttryck](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Ger möjlighet att spåra statistik för körning av alla SQL-instruktioner som körs av en server. (Se nedan för en anteckning i det här tillägget). |
> | [PG\_synlighet](https://www.postgresql.org/docs/current/pgvisibility.html) | Granska synlighet karta (VM) och information på sidan-nivå. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Ger ett sätt för att visa på radnivå låsning information. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Ger ett sätt för att visa statistik i tuppeln på servernivå. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Främmande data omslutning används för att komma åt data som lagras i externa PostgreSQL-servrar. (Se nedan för en anteckning i det här tillägget).|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Information om SSL-certifikat. |
> | [TSM\_system\_rader](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE-metod som tar emot antal rader som en gräns. |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE-metod som tar emot tid i millisekunder som en gräns. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Ger dig möjlighet att skapa hypotetiskt index som inte kostar CPU eller disk. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | En modul som stöder anslutningar till andra PostgreSQL-databaser i en databas-session. (Se nedan för en anteckning i det här tillägget). |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath-fråga och XSLT. |


### <a name="postgis-extensions"></a>PostGIS tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topologi, postgis\_tiger\_geocoder, postgis\_sfcgal | Spatial- och geografiska objekt för PostgreSQL. |
> | adress\_standardizer, adress\_standardizer\_data\_oss | Används för att parsa en adress till innehåll. Används för att stödja geokodning adress normalisering steg. |
> | postgis\_sfcgal | PostGIS SFCGAL funktioner. |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder och omvänd geocoder. |
> | postgis\_topologi | PostGIS spatial topologier och funktioner. |


## <a name="pgstatstatements"></a>pg_stat_statements
Den [pg\_stat\_instruktioner tillägget](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) är förinstallerade på varje Azure Database for PostgreSQL-server att ge dig ett sätt att spåra statistik för körning av SQL-uttryck.
Inställningen `pg_stat_statements.track`, som styr vilka instruktioner räknas av tillägget, standardvärdet är `top`, vilket innebär att alla instruktioner direkt från klienter spåras. Två andra spårnings-nivåerna är `none` och `all`. Den här inställningen kan konfigureras som en parameter för server via den [Azure-portalen](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) eller [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Det finns en kompromiss mellan körning frågeinformationen pg_stat_statements ger och påverkan på serverprestanda som loggas varje SQL-uttryck. Om du inte aktivt använder tillägget pg_stat_statements, rekommenderar vi att du ställer in `pg_stat_statements.track` till `none`. Observera att vissa tredjeparts övervakningstjänster vara beroende av pg_stat_statements att leverera prestandainsikter för frågan, så kontrollera om så är fallet för dig eller inte.

## <a name="dblink-and-postgresfdw"></a>dblink och postgres_fdw
dblink och postgres_fdw kan du ansluta från en PostgreSQL-server till en annan eller till en annan databas på samma server. Mottagande servern måste tillåta anslutningar från den sändande servern via dess brandvägg. När du använder dessa tillägg för att ansluta till Azure Database for PostgreSQL-servrar, kan detta göras genom att ställa in ”Tillåt åtkomst till Azure-tjänster” till ON. Detta krävs också om du vill använda tilläggen för att gå tillbaka till samma server. Inställningen ”Tillåt åtkomst till Azure-tjänster” finns på sidan för Azure portal för Postgres-servern under anslutningssäkerhet. Aktivera ”Tillåt åtkomst till Azure-tjänster” på vitlistor alla Azure-IP-adresser.

För närvarande stöds utgående anslutningar från Azure Database för PostgreSQL inte, förutom anslutningar till andra Azure Database for PostgreSQL-servrar.
