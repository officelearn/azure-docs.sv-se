---
title: Använd PostgreSQL tillägg i Azure-databas för PostgreSQL
description: Beskriver möjligheten att utöka funktionerna i databasen med tillägg i Azure-databas för PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/30/2018
ms.openlocfilehash: c1e541450103c0c30854c027c5dc39f3b94cdafd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640582"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>PostgreSQL-tillägg i Azure-databas för PostgreSQL
PostgreSQL ger dig möjlighet att utöka funktionerna i databasen med hjälp av tillägg. Tillägg låter för paketering flera relaterade SQL-objekt tillsammans i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. Tillägg kan fungera som har inbyggda funktioner efter som läses in i databasen. Mer information om PostgreSQL-tillägg finns [paketering relaterade objekt i ett tillägg](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Hur du använder PostgreSQL-tillägg
PostgreSQL tillägg måste installeras i databasen innan du kan använda dem. Om du vill installera ett visst tillägg, körs det [skapa tillägg](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) från psql verktyg för att läsa in paketerade objekt i databasen.

Azure-databas för PostgreSQL stöder för närvarande en delmängd av nyckeln tillägg som anges nedan. Tillägg utöver de som visas stöds inte. Du kan skapa dina egna tillägg med Azure-databas för PostgreSQL-tjänsten.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Tillägg som stöds av Azure-databas för PostgreSQL
I tabellerna nedan listas de standard PostgreSQL-tillägg som för närvarande stöds av Azure-databas för PostgreSQL. Den här informationen är också tillgänglig genom att köra `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Datatyperna tillägg

> [!div class="mx-tableFixed"]
| **Tillägg** | **Beskrivning** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Innehåller en datatyp för automatisk krypterade lösenord. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Ger en skiftlägeskänslig tecken strängtyp. |
| [Kub](https://www.postgresql.org/docs/9.6/static/cube.html) | Innehåller en datatyp för flerdimensionella kuber. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Innehåller en datatyp för att lagra uppsättningar med nyckel/värde-par. |
| [körs](https://www.postgresql.org/docs/9.6/static/isn.html) | Innehåller datatyper för internationella produkten numrering standarder. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Innehåller en datatyp för hierarkisk trädstruktur-liknande strukturer. |

### <a name="functions-extensions"></a>Funktioner tillägg

> [!div class="mx-tableFixed"]
| **Tillägg** | **Beskrivning** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Innebär att beräkna bra cirkel avstånd på jordens yta. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Innehåller flera funktioner för att fastställa likheter och avståndet mellan strängar. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Ger funktioner och operatorer för att ändra null utan matriser med heltal. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Tillhandahåller kryptografiska funktioner. |
| [PG\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Hanterar partitionerade tabeller genom tid eller -ID. |
| [PG\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Ger funktioner och operatorer för att fastställa liknande alfanumeriskt baserat på trigram matchning. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Tillhandahåller funktioner som hanterar hela tabeller, inklusive korstabell. |
| [UUID-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genererar universellt unika identifierare (UUID: er). |

### <a name="full-text-search-extensions"></a>Fulltextsökning tillägg

> [!div class="mx-tableFixed"]
| **Tillägg** | **Beskrivning** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Innehåller en mall för text search ordlista för heltal. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | En text search ordlista som tar bort accent (Diakritisk tecken) från lexemes. |

### <a name="index-types-extensions"></a>Index typer tillägg

> [!div class="mx-tableFixed"]
| **Tillägg** | **Beskrivning** |
|---|---|
| [BTree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Innehåller exempel GIN operatorn klasser som implementerar B-trädet som beteendet för vissa datatyper. |
| [BTree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Innehåller GiST index operatorn klasser som implementerar B-trädet. |

### <a name="language-extensions"></a>-Tillägg

> [!div class="mx-tableFixed"]
| **Tillägg** | **Beskrivning** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL inläsningsbar procedurmässig språk. |

### <a name="miscellaneous-extensions"></a>Diverse tillägg

> [!div class="mx-tableFixed"]
| **Tillägg** | **Beskrivning** |
|---|---|
| [PG\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Ger möjlighet att undersöka vad som händer i delade buffert cachen i realtid. |
| [PG\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Ger ett sätt att läsa in relationsdata i bufferten cachen. |
| [PG\_stat\_instruktioner](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Ger möjlighet att spåra körningen statistik för alla SQL-instruktioner som körs av en server. (Se nedan för en anteckning på det här tillägget). |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Ger möjlighet att visa radnivå låsning information. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Ger möjlighet att visa statistik på zonnivå tuppel. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Extern data wrapper används för att komma åt data som lagras i externa PostgreSQL-servrar. |
| [hypopg](https://hypopg.readthedocs.io/en/latest/) | Ger dig möjlighet att skapa hypotetiska index som inte kostar processor- eller. |

### <a name="postgis-extensions"></a>PostGIS tillägg

> [!div class="mx-tableFixed"]
| **Tillägg** | **Beskrivning** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topologi, postgis\_tiger\_geocoder, postgis\_sfcgal | Spatial- och geografiska objekt för PostgreSQL. |
| adress\_standardizer, adress\_standardizer\_data\_oss | Används för att parsa en adress till innehåll. Används för att stödja geokodning adress normalisering steg. |
| [pgrouting](http://pgrouting.org/) | Utökar PostGIS / PostgreSQL geospatiala database tillhandahåller geospatiala routning funktioner. |


### <a name="using-pgstatstatements"></a>Med hjälp av pg_stat_statements
Den [pg\_stat\_instruktioner tillägget](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) är förinstallerat på varje Azure-databas för PostgreSQL server att ge dig ett sätt att spåra statistik för körning av SQL-instruktioner.
Inställningen `pg_stat_statements.track`, som styr vilka instruktioner räknas varje filnamnstillägg som standard `top`, vilket innebär att alla instruktioner direkt från klienter spåras. De två andra spårning nivåerna `none` och `all`. Den här inställningen kan konfigureras som en ServerParameter via den [Azure-portalen](https://docs.microsoft.com/en-us/azure/postgresql/howto-configure-server-parameters-using-portal) eller [Azure CLI](https://docs.microsoft.com/en-us/azure/postgresql/howto-configure-server-parameters-using-cli).

Det finns en kompromiss mellan körning frågeinformation pg_stat_statements ger och påverkan på serverprestanda som loggar varje SQL-uttryck. Om du inte aktivt använder tillägget pg_stat_statements, rekommenderar vi att du ställer in `pg_stat_statements.track` till `none`. Observera att vissa tredjeparts övervaka tjänster kan förlita sig på pg_stat_statements att leverera insikter i frågeprestanda, så bekräfta om detta är fallet för du eller inte.


## <a name="next-steps"></a>Nästa steg
Om du inte ser ett tillägg som du vill använda, berätta för oss. Rösta på befintliga begäranden eller skapa nya feedback och begäranden i vår [kunden Feedbackforum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
