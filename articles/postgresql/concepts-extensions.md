---
title: "Använder PostgreSQL-tillägg i Azure-databas för PostgreSQL | Microsoft Docs"
description: "Beskriver möjligheten att utöka funktionerna i databasen med tillägg i Azure-databas för PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: a80b27dc8f1a15bf2e62c9992be8bfa02cacb2f6
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>PostgreSQL-tillägg i Azure-databas för PostgreSQL
PostgreSQL ger dig möjlighet att utöka funktionerna i databasen med hjälp av tillägg. Tillägg låter för paketering flera relaterade SQL-objekt tillsammans i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. Tillägg kan fungera som har inbyggda funktioner efter som läses in i databasen. Mer information om PostgreSQL-tillägg finns [paketering relaterade objekt i ett tillägg](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Hur du använder PostgreSQL-tillägg
PostgreSQL tillägg måste installeras i databasen innan du kan använda dem. Om du vill installera ett visst tillägg, körs det [skapa tillägg](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) från psql verktyg för att läsa in paketerade objekt i databasen.

Azure-databas för PostgreSQL stöder för närvarande en delmängd av nyckeln tillägg som anges nedan. Tillägg utöver de som visas stöds inte. Du kan skapa dina egna tillägg med Azure-databas för PostgreSQL-tjänsten.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Tillägg som stöds av Azure-databas för PostgreSQL
I tabellerna nedan listas de standard PostgreSQL-tillägg som för närvarande stöds av Azure-databas för PostgreSQL. Den här informationen är också tillgänglig genom att fråga `pg\_available\_extensions`.

### <a name="data-types-extensions"></a>Datatyperna tillägg

> [!div class="mx-tableFixed"]
| **Tillägg** | **Beskrivning** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Innehåller en datatyp för automatisk krypterade lösenord. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Ger en skiftlägeskänslig tecken strängtyp. |
| [kub](https://www.postgresql.org/docs/9.6/static/cube.html) | Innehåller en datatyp för flerdimensionella kuber. |
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
| [PG\_stat\_instruktioner](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Ger möjlighet att spåra körningen statistik för alla SQL-instruktioner som körs av en server. |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Ger möjlighet att visa radnivå låsning information. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Ger möjlighet att visa statistik på zonnivå tuppel. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Extern data wrapper används för att komma åt data som lagras i externa PostgreSQL-servrar. |

### <a name="postgis-extensions"></a>PostGIS tillägg

> [!div class="mx-tableFixed"]
| **Tillägg** | **Beskrivning** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topologi, postgis\_tiger\_geocoder, postgis\_sfcgal | Spatial- och geografiska objekt för PostgreSQL. |
| adress\_standardizer, adress\_standardizer\_data\_oss | Används för att parsa en adress till innehåll. Används för att stödja geokodning adress normalisering steg. |
| [grouting](http://pgrouting.org/) | Utökar PostGIS / PostgreSQL geospatiala database tillhandahåller geospatiala routning funktioner. |

## <a name="next-steps"></a>Nästa steg
Om du inte ser ett tillägg som du vill använda, berätta för oss. Rösta på befintliga begäranden eller skapa nya feedback och begäranden i vår [kunden Feedbackforum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
