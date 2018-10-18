---
title: Använda PostgreSQL-tillägg i Azure Database för PostgreSQL
description: Beskriver möjlighet att utöka funktionerna i din databas med tillägg i Azure Database för PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/30/2018
ms.openlocfilehash: f9216800f0e91d71f1da3f2bac16ecfcbf8b4850
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376558"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>PostgreSQL-tillägg i Azure Database för PostgreSQL
PostgreSQL ger möjlighet att utöka funktionerna i din databas med tillägg. Tillägg kan paketera flera relaterade SQL-objekt tillsammans i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. Tillägg kan fungera som gör de inbyggda funktionerna för efter att läsas in i databasen. Läs mer på PostgreSQL-tillägg, [paketering relaterade objekt i ett tillägg](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Hur du använder PostgreSQL-tillägg
PostgreSQL-tillägg måste installeras i databasen innan du kan använda dem. Installera ett visst tillägg genom att köra den [skapa tillägg](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) från psql-verktyget för att läsa in paketerade objekten i databasen.

Azure Database för PostgreSQL stöder för närvarande en delmängd av viktiga tillägg som anges ovan. Tillägg utöver de som visas stöds inte. Du kan inte skapa dina egna tillägg med Azure Database för PostgreSQL-tjänsten.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Tillägg som stöds av Azure Database för PostgreSQL
I tabellerna nedan listas de standard PostgreSQL-tillägg som för närvarande stöds av Azure Database för PostgreSQL. Den här informationen är också tillgängligt genom att köra `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Datatillägg för typer

> [!div class="mx-tableFixed"]
| **Tillägget** | **Beskrivning** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Ger en datatyp för automatisk-krypterade lösenord. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Innehåller datatypen är skiftlägeskänslig tecken. |
| [kub](https://www.postgresql.org/docs/9.6/static/cube.html) | Ger en datatyp för flerdimensionella kuber. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Innehåller en datatyp för att lagra uppsättningar med nyckel/värde-par. |
| [är](https://www.postgresql.org/docs/9.6/static/isn.html) | Innehåller datatyper för internationella produkten numrering standarder. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Ger en datatyp för den hierarkiska träd-liknande strukturer. |

### <a name="functions-extensions"></a>Functions-tillägg

> [!div class="mx-tableFixed"]
| **Tillägget** | **Beskrivning** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Innebär att beräkna bra cirkel avstånd på jordens yta. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Har flera funktioner för att fastställa likheter och avståndet mellan strängar. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Tillhandahåller funktioner och operatorer för att manipulera null kostnadsfria matriser med heltal. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Tillhandahåller kryptografiska funktioner. |
| [PG\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Hanterar partitionerade tabeller genom tid eller -ID. |
| [PG\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Innehåller funktioner och operatorer för att fastställa likheten mellan alfanumerisk text baserat på trigram matchning. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Innehåller funktioner som manipulerar hela tabeller, inklusive korstabell. |
| [UUID ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genererar universell unik identifierare (UUID). |

### <a name="full-text-search-extensions"></a>Fulltextsökning tillägg

> [!div class="mx-tableFixed"]
| **Tillägget** | **Beskrivning** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Innehåller en mall för text search ordlista för heltal. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | En text search ordlista som tar bort uttal (diakritiska tecken) från lexemes. |

### <a name="index-types-extensions"></a>Index typer tillägg

> [!div class="mx-tableFixed"]
| **Tillägget** | **Beskrivning** |
|---|---|
| [b-trädet\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Innehåller exempel GIN operatorn klasser som implementerar B-trädet som beteendet för vissa datatyper. |
| [b-trädet\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Innehåller GiST index operatorn klasser som implementerar B-trädet. |

### <a name="language-extensions"></a>Språktillägg

> [!div class="mx-tableFixed"]
| **Tillägget** | **Beskrivning** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL kan läsas in procedurmässig språk. |

### <a name="miscellaneous-extensions"></a>Diverse tillägg

> [!div class="mx-tableFixed"]
| **Tillägget** | **Beskrivning** |
|---|---|
| [PG\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Ger möjlighet att undersöka vad som händer i delade buffert cachen i realtid. |
| [PG\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Är ett sätt att läsa in relationsdata till bufferten-cachen. |
| [PG\_stat\_uttryck](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Ger möjlighet att spåra statistik för körning av alla SQL-instruktioner som körs av en server. (Se nedan för en anteckning i det här tillägget). |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Ger ett sätt för att visa på radnivå låsning information. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Ger ett sätt för att visa statistik i tuppeln på servernivå. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Främmande data omslutning används för att komma åt data som lagras i externa PostgreSQL-servrar. |
| [hypopg](https://hypopg.readthedocs.io/en/latest/) | Ger dig möjlighet att skapa hypotetiskt index som inte kostar CPU eller disk. |
| [plv8](https://plv8.github.io/) | Ett Javascript-språktillägg för PostgreSQL som kan användas för lagrade procedurer, utlösare, osv. |

### <a name="postgis-extensions"></a>PostGIS tillägg

> [!div class="mx-tableFixed"]
| **Tillägget** | **Beskrivning** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topologi, postgis\_tiger\_geocoder, postgis\_sfcgal | Spatial- och geografiska objekt för PostgreSQL. |
| adress\_standardizer, adress\_standardizer\_data\_oss | Används för att parsa en adress till innehåll. Används för att stödja geokodning adress normalisering steg. |
| [pgrouting](http://pgrouting.org/) | Utökar PostGIS / PostgreSQL geospatiala databasen att tillhandahålla geospatiala routning funktioner. |


### <a name="using-pgstatstatements"></a>Med hjälp av pg_stat_statements
Den [pg\_stat\_instruktioner tillägget](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) är förinstallerade på varje Azure Database for PostgreSQL-server att ge dig ett sätt att spåra statistik för körning av SQL-uttryck.
Inställningen `pg_stat_statements.track`, som styr vilka instruktioner räknas av tillägget, standardvärdet är `top`, vilket innebär att alla instruktioner direkt från klienter spåras. Två andra spårnings-nivåerna är `none` och `all`. Den här inställningen kan konfigureras som en parameter för server via den [Azure-portalen](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) eller [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Det finns en kompromiss mellan körning frågeinformationen pg_stat_statements ger och påverkan på serverprestanda som loggas varje SQL-uttryck. Om du inte aktivt använder tillägget pg_stat_statements, rekommenderar vi att du ställer in `pg_stat_statements.track` till `none`. Observera att vissa tredjeparts övervakningstjänster vara beroende av pg_stat_statements att leverera prestandainsikter för frågan, så kontrollera om så är fallet för dig eller inte.


## <a name="next-steps"></a>Nästa steg
Om du inte ser ett tillägg som du vill använda, berätta för oss. Rösta på befintliga begäranden eller skapa nya feedback och förfrågningar i vår [kundfeedbackforumet](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
