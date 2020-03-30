---
title: Query Store - Azure Database för MariaDB
description: Lär dig mer om Query Store-funktionen i Azure Database för MariaDB som hjälper dig att spåra prestanda över tid.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a502638744009fc34a7f0a27f8034b89d2c8fa26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527817"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Övervaka Azure Database för MariaDB-prestanda med Query Store

**Gäller:** Azure-databas för MariaDB 10.2

Funktionen Query Store i Azure Database för Mariadb är ett sätt att spåra frågeprestanda över tid. Query Store förenklar felsökningen av prestanda genom att hjälpa dig att snabbt hitta de mest tidskrävande och mest resursintensiva frågorna. Query Store samlar automatiskt in en historik över frågor och körningsstatistik, och den behåller dem för din granskning. Den separerar data efter tidsfönster så att du kan se databasanvändningsmönster. Data för alla användare, databaser och frågor lagras i **mysql-schemadatabasen** i Azure Database for MariaDB-instansen.

## <a name="common-scenarios-for-using-query-store"></a>Vanliga scenarier för att använda Query Store

Frågearkivet kan användas i ett antal scenarier, bland annat följande:

- Identifiera regresserade frågor
- Bestämma hur många gånger en fråga har körts i ett visst tidsfönster
- Jämföra den genomsnittliga körningstiden för en fråga över tidsfönster för att se stora deltan

## <a name="enabling-query-store"></a>Aktivera Frågebutik

Query Store är en opt-in-funktion, så den är inte aktiv som standard på en server. Frågearkivet är aktiverat eller inaktiverat globalt för alla databaser på en viss server och kan inte aktiveras eller inaktiveras per databas.

### <a name="enable-query-store-using-the-azure-portal"></a>Aktivera Query Store med Azure-portalen

1. Logga in på Azure-portalen och välj din Azure-databas för MariaDB-server.
1. Välj **Serverparametrar** i avsnittet **Inställningar** på menyn.
1. Sök efter parametern query_store_capture_mode.
1. Ange värdet till ALLA och **Spara**.

Så här aktiverar du väntestatistik i Frågearkivet:

1. Sök efter parametern query_store_wait_sampling_capture_mode.
1. Ange värdet till ALLA och **Spara**.

Tillåt upp till 20 minuter för den första omgången data att finnas kvar i mysql-databasen.

## <a name="information-in-query-store"></a>Information i Frågearbeta

Query Store har två butiker:

- Körningsstatistikarkivet för att bevara statistikinformationen för frågekörning.
- Väntestatistiken lagrar för ihållande väntestatistikinformation.

För att minimera utrymmesanvändningen sammanställs körningsstatistiken i körningsstatistikarkivet över ett fast, konfigurerbart tidsfönster. Informationen i dessa butiker är synlig genom att fråga efter frågearkivvyerna.

Följande fråga returnerar information om frågor i Query Store:

```sql
SELECT * FROM mysql.query_store;
```

Eller den här frågan för väntestatistik:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Hitta vänta frågor

> [!NOTE]
> Vänta statistik bör inte aktiveras under rusningstid arbetsbelastning eller aktiveras på obestämd tid för känsliga arbetsbelastningar. <br>För arbetsbelastningar som körs med hög CPU-användning eller på servrar som konfigurerats med lägre virtuella kärnor bör du vara försiktig när du aktiverar väntestatistik. Det bör inte vara aktiverat på obestämd tid. 

Vänta händelsetyper kombinerar olika vänta händelser i buckets efter likhet. Query Store innehåller typen väntad händelse, specifika vänta händelsenamn och frågan i fråga. Att kunna korrelera den här vänta-informationen med frågekörningsstatistiken innebär att du kan få en djupare förståelse för vad som bidrar till frågeprestandaegenskaper.

Här är några exempel på hur du kan få mer insikter om din arbetsbelastning med hjälp av väntestatistiken i Query Store:

| **Observation** | **Åtgärd** |
|---|---|
|High Lock väntar | Kontrollera frågetexterna för de berörda frågorna och identifiera målentiteterna. Leta i Query Store efter andra frågor som ändrar samma entitet, som körs ofta och/eller har hög varaktighet. När du har identifierat dessa frågor bör du överväga att ändra programlogiken för att förbättra samtidigheten eller använda en mindre restriktiv isoleringsnivå. |
|Hög buffert-I/o väntar | Hitta frågor med ett stort antal fysiska läsningar i Query Store. Om de matchar frågorna med höga IO-väntetider kan du överväga att införa ett index på den underliggande entiteten för att söka i stället för genomsökningar. Detta skulle minimera IO omkostnader för frågorna. Kontrollera **prestandarekommendationerna** för servern i portalen för att se om det finns indexrekommendationer för den här servern som skulle optimera frågorna. |
|Högt minne väntar | Hitta det översta minne som förbrukar frågor i Query Store. Dessa frågor försenar förmodligen ytterligare förlopp för de berörda frågorna. Kontrollera **prestandarekommendationerna** för servern i portalen för att se om det finns indexrekommendationer som skulle optimera dessa frågor.|

## <a name="configuration-options"></a>Konfigurationsalternativ

När Query Store är aktiverat sparar den data i 15-minuters aggregeringsfönster, upp till 500 olika frågor per fönster.

Följande alternativ är tillgängliga för att konfigurera Frågelagringsparametrar.

| **Parametern** | **Beskrivning** | **Default** | **Intervall** |
|---|---|---|---|
| query_store_capture_mode | Aktivera/av funktionen för frågelagring baserat på värdet. Om performance_schema är AVSTÄNGD aktiveras query_store_capture_mode performance_schema och en delmängd av prestandaschemainstrument som krävs för den här funktionen. | ALL | INGEN, ALLA |
| query_store_capture_interval | Insamlingsintervallet för frågearkivet på några minuter. Gör det möjligt att ange det intervall där frågemåtten aggregeras | 15 | 5 - 60 |
| query_store_capture_utility_queries | Aktivera eller av för att samla in alla verktygsfrågor som körs i systemet. | NO | JA, NEJ. |
| query_store_retention_period_in_days | Tidsfönster i dagar för att behålla data i frågearkivet. | 7 | 1 - 30 |

Följande alternativ gäller specifikt för att vänta statistik.

| **Parametern** | **Beskrivning** | **Default** | **Intervall** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Tillåter att slå på / av vänta statistik. | Ingen | INGEN, ALLA |
| query_store_wait_sampling_frequency | Ändrar frekvensen för vänta-provtagning på några sekunder. 5 till 300 sekunder. | 30 | 5-300 |

> [!NOTE]
> För närvarande **ersätter query_store_capture_mode** den här konfigurationen, vilket innebär att både **query_store_capture_mode** och **query_store_wait_sampling_capture_mode** måste aktiveras till ALLA för att vänta statistik ska fungera. Om **query_store_capture_mode** är inaktiverat inaktiveras även väntestatistiken eftersom väntestatistiken använder performance_schema aktiverat och query_text som fångas upp av frågearkivet.

Använd [Azure-portalen](howto-server-parameters.md) för att hämta eller ange ett annat värde för en parameter.

## <a name="views-and-functions"></a>Vyer och funktioner

Visa och hantera Query Store med hjälp av följande vyer och funktioner. Alla i rollen [välj privilegium](howto-create-users.md#create-additional-admin-users) kan använda dessa vyer för att se data i Query Store. Dessa vyer är endast tillgängliga i **mysql-databasen.**

Frågor normaliseras genom att titta på deras struktur efter att ha tagit bort litteraler och konstanter. Om två frågor är identiska med undantag för litterala värden har de samma hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Den här vyn returnerar alla data i Query Store. Det finns en rad för varje distinkt databas-ID, användar-ID och fråge-ID.

| **Namn** | **Datatyp** | **IS_NULLABLE** | **Beskrivning** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Namnet på schemat |
| `query_id`| bigint(20) | NO| Unikt ID som genereras för den specifika frågan, om samma fråga körs i olika schema, genereras ett nytt ID |
| `timestamp_id` | timestamp| NO| Tidsstämpel där frågan körs. Detta baseras på query_store_interval konfigurationen|
| `query_digest_text`| långtext| NO| Den normaliserade frågetexten efter att ha tagit bort alla litteraler|
| `query_sample_text` | långtext| NO| Första utseendet på den faktiska frågan med litteraler|
| `query_digest_truncated` | bit| JA| Om frågetexten har trunkerats. Värdet blir Ja om frågan är längre än 1 KB|
| `execution_count` | bigint(20)| NO| Antalet gånger frågan har körts för det här tidsstämpel-ID/under den konfigurerade intervallperioden|
| `warning_count` | bigint(20)| NO| Antal varningar som den här frågan genererade under den interna|
| `error_count` | bigint(20)| NO| Antal fel som den här frågan har genererat under intervallet|
| `sum_timer_wait` | double| JA| Total körningstid för den här frågan under intervallet|
| `avg_timer_wait` | double| JA| Genomsnittlig körningstid för den här frågan under intervallet|
| `min_timer_wait` | double| JA| Minsta körningstid för den här frågan|
| `max_timer_wait` | double| JA| Maximal körningstid|
| `sum_lock_time` | bigint(20)| NO| Total tid för alla lås för den här frågekörningen under det här tidsfönstret|
| `sum_rows_affected` | bigint(20)| NO| Antal rader som påverkas|
| `sum_rows_sent` | bigint(20)| NO| Antal rader som skickats till klienten|
| `sum_rows_examined` | bigint(20)| NO| Antal undersökta rader|
| `sum_select_full_join` | bigint(20)| NO| Antal fullständiga kopplingar|
| `sum_select_scan` | bigint(20)| NO| Antal urvalssökningar |
| `sum_sort_rows` | bigint(20)| NO| Antal rader sorterade|
| `sum_no_index_used` | bigint(20)| NO| Antal gånger då frågan inte använde några index|
| `sum_no_good_index_used` | bigint(20)| NO| Antal gånger då frågekörningsmotorn inte använde några bra index|
| `sum_created_tmp_tables` | bigint(20)| NO| Totalt antal temporära tabeller har skapats|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Totalt antal temp-tabeller som skapats i disk (genererar I/O)|
| `first_seen` | timestamp| NO| Den första förekomsten (UTC) för frågan under aggregeringsfönstret|
| `last_seen` | timestamp| NO| Den sista förekomsten (UTC) för frågan under det här aggregeringsfönstret|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

I den här vyn returneras väntehändelser i Query Store. Det finns en rad för varje distinkt databas-ID, användar-ID, fråge-ID och händelse.

| **Namn**| **Datatyp** | **IS_NULLABLE** | **Beskrivning** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Början av intervallet (15-minuters ökning)|
| `interval_end` | timestamp | NO| Slutet av intervallet (15-minuters ökning)|
| `query_id` | bigint(20) | NO| Genererade unikt ID för den normaliserade frågan (från frågearkivet)|
| `query_digest_id` | varchar(32) | NO| Den normaliserade frågetexten efter att ha tagit bort alla litteraler (från frågearkivet) |
| `query_digest_text` | långtext | NO| Första utseendet på den faktiska frågan med litteraler (från frågearkivet) |
| `event_type` | varchar(32) | NO| Kategori för vänta-händelsen |
| `event_name` | varchar(128) | NO| Namnet på vänta-händelsen |
| `count_star` | bigint(20) | NO| Antal väntande händelser som samplats under intervallet för frågan |
| `sum_timer_wait_ms` | double | NO| Total väntetid (i millisekunder) för den här frågan under intervallet |

### <a name="functions"></a>Funktioner

| **Namn**| **Beskrivning** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Rensar alla frågelagringsdata före den angivna tidsstämpeln |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Rensar alla vänta-händelsedata före den angivna tidsstämpeln |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Rensar rekommendationer vars förfallodatum är före den angivna tidsstämpeln |

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

- Om en MariaDB-server `default_transaction_read_only` har parametern på kan Query Store inte samla in data.
- Query Store-funktionen kan avbrytas om den stöter\>på långa Unicode-frågor ( = 6000 byte).
- Lagringstiden för väntestatistik är 24 timmar.
- I väntestatistik används exempel ti fånga en bråkdel av händelserna. Frekvensen kan ändras `query_store_wait_sampling_frequency`med hjälp av parametern .

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Frågeprestandainsikter](concepts-query-performance-insight.md)
