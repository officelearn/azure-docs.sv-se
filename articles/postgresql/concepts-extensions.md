---
title: Använda PostgreSQL-tillägg i Azure Database for PostgreSQL-enskild server
description: Beskriver möjligheten att utöka funktionerna i databasen med hjälp av tillägg i Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: 93cc02fafcfa153c452f37c2bc69bb47e2629f1d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998069"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-tillägg i Azure Database for PostgreSQL-enskild server
PostgreSQL ger möjlighet att utöka funktionerna i databasen med hjälp av tillägg. Tillägg buntar samman flera relaterade SQL-objekt i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. Efter att ha lästs in i databasen, fungerar utöknings funktionen som inbyggda funktioner.

## <a name="how-to-use-postgresql-extensions"></a>Använda PostgreSQL-tillägg
PostgreSQL-tillägg måste vara installerade i databasen innan du kan använda dem. Om du vill installera ett visst tillägg kör du kommandot [skapa tillägg](https://www.postgresql.org/docs/current/static/sql-createextension.html) från psql-verktyget för att läsa in de paketerade objekten i databasen.

Azure Database for PostgreSQL stöder en delmängd av nyckel tillägg som anges nedan. Tillägg utöver de som anges i listan stöds inte. Du kan inte skapa ditt eget tillägg i Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Tillägg som stöds av Azure Database for PostgreSQL
I följande tabeller visas de standard PostgreSQL-tillägg som för närvarande stöds av Azure Database for PostgreSQL. Den här informationen är också tillgänglig genom `SELECT * FROM pg_available_extensions;`att köra.

### <a name="data-types-extensions"></a>Tillägg för data typer

> [!div class="mx-tableFixed"]
> | **Utöka** | **Beskrivning** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Tillhandahåller en datatyp för automatiskt krypterade lösen ord. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Innehåller en Skift läges okänslig tecken sträng typ. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Tillhandahåller en datatyp för flerdimensionella kuber. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Tillhandahåller en datatyp för lagring av uppsättningar med nyckel/värde-par. |
> | [inte är](https://www.postgresql.org/docs/9.6/static/isn.html) | Tillhandahåller data typer för internationella standarder för produkt nummer. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Tillhandahåller en datatyp för hierarkiska träd strukturer. |

### <a name="functions-extensions"></a>Funktions tillägg

> [!div class="mx-tableFixed"]
> | **Utöka** | **Beskrivning** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Ger ett sätt att beräkna fantastiska avstånd på jordens yta. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Innehåller flera funktioner som avgör likheter och avstånd mellan strängar. |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Tillhandahåller funktioner och operatorer för att ändra null-fria matriser med heltal. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Tillhandahåller kryptografiska funktioner. |
> | [PG\_part](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Hanterar partitionerade tabeller efter tid eller ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Tillhandahåller funktioner och operatorer för att fastställa likheten mellan alfanumerisk text baserat på trigram matchning. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Innehåller funktioner som ändrar hela tabeller, inklusive kors. |
> | [UUID – ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genererar universellt unika identifierare (UUID). (Se nedan för en anteckning om tillägget). |
> | [orafce](https://github.com/orafce/orafce) | Innehåller en delmängd av funktioner och paket som emuleras från kommersiella databaser. |

### <a name="full-text-search-extensions"></a>Full texts öknings tillägg

> [!div class="mx-tableFixed"]
> | **Utöka** | **Beskrivning** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Innehåller en mall för text Sök lexikon för heltal. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | En text Sök ord lista som tar bort accenttecken (dia kritiska tecken) från lexemes. |

### <a name="index-types-extensions"></a>Tillägg för index typer

> [!div class="mx-tableFixed"]
> | **Utöka** | **Beskrivning** |
> |---|---|
> | [b\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Tillhandahåller exempel på GIN operator klasser som implementerar B-Tree som beteende för vissa data typer. |
> | [b\_register](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Tillhandahåller klasser för registrerings index operatorer som implementerar B-träd. |

### <a name="language-extensions"></a>Språk tillägg

> [!div class="mx-tableFixed"]
> | **Utöka** | **Beskrivning** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL-inbelastnings förfarande språk. |
> | [plv8](https://plv8.github.io/) | Ett språk tillägg för Java Script för PostgreSQL som kan användas för lagrade procedurer, utlösare osv. |

### <a name="miscellaneous-extensions"></a>Diverse tillägg

> [!div class="mx-tableFixed"]
> | **Utöka** | **Beskrivning** |
> |---|---|
> | [PG\_-buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Ger ett sätt att undersöka vad som händer i det delade cacheminnet i real tid. |
> | [PG\_-förvarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Är ett sätt att läsa in relations data i bufferten. |
> | [rapporter\_om\_PG stat](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Ger ett sätt att spåra körnings statistik för alla SQL-uttryck som körs av en server. (Se nedan för en anteckning om tillägget). |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Ger möjlighet att Visa lås information på radnivå. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Ger ett sätt att visa statistik på tuple-nivå. |
> | [postgres\_FDW](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Sekundärt data omslag som används för att komma åt data som lagras i externa PostgreSQL-servrar. (Se nedan för en anteckning om tillägget).|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Ger ett sätt att skapa hypotetiska index som inte kostar CPU eller disk. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | En modul som stöder anslutningar till andra PostgreSQL-databaser inifrån en databas-session. (Se nedan för en anteckning om tillägget). |


### <a name="postgis-extensions"></a>PostGIS-tillägg

> [!div class="mx-tableFixed"]
> | **Utöka** | **Beskrivning** |
> |---|---|
> | [Postgis](https://www.postgis.net/), postgis\_-topologi,\_postgis\_Tiger-kod, postgis\_sfcgal | Spatialdata och geografiska objekt för PostgreSQL. |
> | adress\_standardiserare, adress\_standardiserade\_data\_US | Används för att parsa en adress till komponent element. Används för att stödja kod normaliserings steg för den här adressen. |
> | [pgrouting](https://pgrouting.org/) | Utökar den geospatiala PostGIS/PostgreSQL-databasen för att tillhandahålla Geospatial routning. |


### <a name="time-series-extensions"></a>Tids serie tillägg

> [!div class="mx-tableFixed"]
> | **Utöka** | **Beskrivning** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | En SQL-databas med tids serier som stöder automatiserad partitionering för snabbare inläsning och frågor. Innehåller tidsorienterade analys funktioner, optimeringar och skalar PostgreSQL för arbets belastningar för tids serier. TimescaleDB har utvecklats av och ett registrerat varumärke som tillhör [tids skala, Inc.](https://www.timescale.com/) (Se nedan för en anteckning om tillägget). |

## <a name="postgres-11-extensions"></a>Postgres 11-tillägg

Följande tillägg är tillgängliga i Azure Database for PostgreSQL servrar som har postgres version 11.

> [!div class="mx-tableFixed"]
> | **Utöka**| **Tilläggs version** | **Beskrivning** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Används för att parsa en adress till komponent element. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exempel på adress Standardiserare för data uppsättning|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | stöd för indexering av vanliga data typer i GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | stöd för indexering av vanliga data typer i registret|
> |[citext](https://www.postgresql.org/docs/11/static/citext.html)                       | 1.5             | datatyp för Skift läges känsliga sträng strängar|
> |[cube](https://www.postgresql.org/docs/11/static/cube.html)                         | 1.4             | datatyp för flerdimensionella kuber|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | ansluta till andra PostgreSQL-databaser inifrån en databas|
> |[dict_int](https://www.postgresql.org/docs/11/static/dict-int.html)                     | 1.0             | mall för text Sök lexikon för heltal|
> |[earthdistance](https://www.postgresql.org/docs/11/static/earthdistance.html)                | 1.1             | beräkna suveräna cirkel avstånd på jordens yta|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/static/fuzzystrmatch.html)                | 1.1             | Bestäm likheter och avstånd mellan strängar|
> |[hstore](https://www.postgresql.org/docs/11/static/hstore.html)                       | 1.5             | datatyp för lagring av uppsättningar av (nyckel, värde) par|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Hypotetiska index för PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/static/intarray.html)                     | 1.2             | funktioner, operatörer och index stöd för en 1d-matris med heltal|
> |[inte är](https://www.postgresql.org/docs/11/static/isn.html)                          | 1.2             | data typer för internationella standarder för produkt nummer|
> |[ltree](https://www.postgresql.org/docs/11/static/ltree.html)                        | 1.1             | datatyp för hierarkiska träd strukturer|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funktioner och operatorer som emulerar en delmängd funktioner och paket från kommersiella RDBMS|
> |[pgcrypto](https://www.postgresql.org/docs/11/static/pgcrypto.html)                     | 1.3             | kryptografiska funktioner|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting-tillägg|
> |[pgrowlocks](https://www.postgresql.org/docs/11/static/pgrowlocks.html)                   | 1.2             | Visa lås information på radnivå|
> |[pgstattuple](https://www.postgresql.org/docs/11/static/pgstattuple.html)                  | 1.5             | Visa statistik på tuple nivå|
> |[pg_buffercache](https://www.postgresql.org/docs/11/static/pgbuffercache.html)               | 1.3             | granska cacheminnet för den delade bufferten|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Tillägg för att hantera partitionerade tabeller efter tid eller ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | Förvärm Relations data|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/static/pgstatstatements.html)           | 1.6             | spåra körnings statistik för alla SQL-instruktioner som körs|
> |[pg_trgm](https://www.postgresql.org/docs/11/static/pgtrgm.html)                      | 1.4             | text likhets mätning och indexs ökning baserat på trigrams|
> |[plpgsql](https://www.postgresql.org/docs/11/static/plpgsql.html)                      | 1.0             | PL/pgSQL Procedure language|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/Java Script (V8) Trusted Procedure language|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometri, geografi och raster avstånds typer och funktioner|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL-funktioner|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger-kod och omvänd landskod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Spatiala typer och funktioner för PostGIS-topologi|
> |[postgres_fdw](https://www.postgresql.org/docs/11/static/postgres-fdw.html)                 | 1.0             | sekundär data omslutning för fjärranslutna PostgreSQL-servrar|
> |[tablefunc](https://www.postgresql.org/docs/11/static/tablefunc.html)                    | 1.0             | funktioner som ändrar hela tabeller, inklusive kors|
> |[unaccent](https://www.postgresql.org/docs/11/static/unaccent.html)                     | 1.1             | texts öknings ord lista som tar bort accenttecken|
> |[UUID – ossp](https://www.postgresql.org/docs/11/static/uuid-ossp.html)                    | 1.1             | generera globalt unika identifierare (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
Pg_stat_statements-tillägget är förinstallerat på alla Azure Database for PostgreSQL Server för att ge dig möjlighet att spåra körnings statistik för SQL-uttryck.
Inställningen `pg_stat_statements.track`, som styr vilka instruktioner som räknas av tillägget, är standardvärdet, `top`vilket innebär att alla instruktioner som utfärdas direkt av klienter spåras. De två andra spårnings nivåerna `none` är `all`och. Den här inställningen kan konfigureras som en server parameter via [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) eller [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Det finns en kompromiss mellan information om pg_stat_statements tillhandahåller och påverkan på Server prestanda när varje SQL-sats loggas. Om du inte aktivt använder pg_stat_statements-tillägget rekommenderar vi att du ställer in `pg_stat_statements.track` på. `none` Observera att vissa övervaknings tjänster från tredje part kan förlita sig på pg_stat_statements för att leverera frågor om prestanda insikter, så kontrol lera om detta är fallet för dig eller inte.

## <a name="dblink-and-postgres_fdw"></a>dbLink och postgres_fdw
med dbLink och postgres_fdw kan du ansluta från en PostgreSQL-server till en annan, eller till en annan databas på samma server. Den mottagande servern måste tillåta anslutningar från den sändande servern via brand väggen. När du använder dessa tillägg för att ansluta mellan Azure Database for PostgreSQL-servrar kan du göra detta genom att ange "Tillåt åtkomst till Azure-tjänster" till på. Detta behövs även om du vill använda tilläggen för att återgå till samma server. Inställningen "Tillåt åtkomst till Azure-tjänster" finns på Azure Portal sidan för postgres-servern under anslutnings säkerhet. Att aktivera "Tillåt åtkomst till Azure-tjänster" på placerar alla Azure IP-adresser i listan över tillåtna.

För närvarande stöds inte utgående anslutningar från Azure Database for PostgreSQL, förutom för anslutningar till andra Azure Database for PostgreSQL-servrar.

## <a name="uuid"></a>uuid
Om du planerar att använda `uuid_generate_v4()` från UUID-ossp-tillägget kan du jämföra med med `gen_random_uuid()` pgcrypto-tillägget för prestanda förmåner.


## <a name="timescaledb"></a>TimescaleDB
TimescaleDB är en databas för tids serier som är paketerad som ett tillägg för PostgreSQL. TimescaleDB tillhandahåller tidsorienterade analys funktioner, optimeringar och skalar postgres för arbets belastningar för tids serier.

[Lär dig mer om TimescaleDB](https://docs.timescale.com/latest), ett registrerat varumärke som tillhör [tids skala, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Installerar TimescaleDB
Om du vill installera TimescaleDB måste du ta med den i serverns delade preload-bibliotek. En ändring av postgres `shared_preload_libraries` -parametern kräver att en omstart av **servern** börjar gälla. Du kan ändra parametrar med hjälp av [Azure Portal](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> TimescaleDB kan aktive ras på Azure Database for PostgreSQL version 9,6 och 10

Använda [Azure Portal](https://portal.azure.com/):

1. Välj din Azure Database for PostgreSQL-server.

2. Välj **Server parametrar**på sid panelen.

3. Sök efter `shared_preload_libraries` parametern.

4. Välj **TimescaleDB**.

5. Klicka på **Spara** för att bevara ändringarna. Du får ett meddelande när ändringen har sparats. 

6. Efter aviseringen **startar du om** servern för att tillämpa ändringarna. Information om hur du startar om en server finns i [starta om en Azure Database for postgresql-server](howto-restart-server-portal.md).


Nu kan du aktivera TimescaleDB i postgres-databasen. Anslut till databasen och utfärda följande kommando:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Om du ser ett fel bekräftar du att du har [startat om servern](howto-restart-server-portal.md) när du har sparat shared_preload_libraries. 

Nu kan du skapa en TimescaleDB-tabell [från början](https://docs.timescale.com/getting-started/creating-hypertables) eller migrera [befintliga Time Series-data i postgresql](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Nästa steg
Om du inte ser ett tillägg som du vill använda, kan du berätta för oss. Rösta på befintliga förfrågningar eller skapa nya feedback-förfrågningar i vårt [feedback-forum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
