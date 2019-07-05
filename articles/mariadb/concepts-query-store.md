---
title: Query Store i Azure Database for MariaDB
description: Den här artikeln beskriver funktionen Query Store i Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 883f780059e38c53dedda309dd059cc714539f80
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462097"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Övervaka Azure-databas för MariaDB prestanda med Query Store

**Gäller för:**  Azure Database for MariaDB 10.2

> [!NOTE]
> Query Store är i förhandsversion.

Funktionen för Query Store i Azure Database for Mariadb tillhandahåller ett sätt att spåra prestanda för frågor över tid. Query Store förenklar prestandafelsökning genom att hjälpa dig snabbt hitta körs längst och mest resurskrävande frågor. Query Store avbildas automatiskt en historik över frågor och körningsstatistik och den lagrar dem för granskning. När du delar den upp data efter tidsfönster så att du kan se databasanvändningsmönster. Data för alla användare, databaser och frågor lagras i den **mysql** schemadatabas i Azure Database for MariaDB-instans.

## <a name="common-scenarios-for-using-query-store"></a>Vanliga scenarier för att använda Query Store

Frågearkivet kan användas på flera olika scenarier, inklusive följande:

- Identifiera försämrad frågor
- Avgör hur många gånger har en fråga utförts under en viss tidsperiod
- Jämföra Genomsnittlig körningstid för en fråga över tidsfönster att visa stora deltan

## <a name="enabling-query-store"></a>Aktivera Query Store

Query Store är en valbar funktion så att den inte är aktiv som standard på en server. Query store är aktiverat eller inaktiverat globalt för alla databaser på en viss server och kan inte stängas eller inaktivera per databas.

### <a name="enable-query-store-using-the-azure-portal"></a>Aktivera Query Store med Azure portal

1. Logga in på Azure Portal och välj din Azure Database for MariaDB-server.
1. Välj **serverparametrar** i den **inställningar** på menyn.
1. Sök efter query_store_capture_mode-parametern.
1. Ange värdet för alla och **spara**.

Så här aktiverar du vänta statistik i din fråga Store:

1. Sök efter query_store_wait_sampling_capture_mode-parametern.
1. Ange värdet för alla och **spara**.

Det kan ta upp till 20 minuter för den första batchen för att bevara i mysql-databas.

## <a name="information-in-query-store"></a>Informationen i Query Store

Query Store har två lager:

- En körningsstatistik lagra för att spara frågan körning statistik information för.
- En vänta statistik butik för att spara för vänta statistikinformation.

För att minimera användning av diskutrymme, sammanställs körningsstatistik för körning i arkivet runtime statistik över ett fast, konfigurerbara tidsfönster. Informationen i dessa lager är synligt genom att fråga store frågevyer.

Följande fråga returnerar information om frågor i Query Store:

```sql
SELECT * FROM mysql.query_store;
```

Eller den här frågan för wait-statistik:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Frågor för att hitta vänta

Vänta händelsetyper kombinera olika vänta händelser i buckets efter likhet. Query Store ger händelsetyp vänta och specifika vänta händelsenamn frågan i fråga. Att kunna korrelera informationen vänta med frågan runtime statistik innebär att du kan få en djupare förståelse för vad bidrar till fråga prestandaegenskaper.

Här följer några exempel på hur du kan få fler insikter om din arbetsbelastning med vänta statistik i Query Store:

| **Fjärrvisning** | **Åtgärd** |
|---|---|
|Hög Lås väntar | Kontrollera fråga texter för frågor som påverkas och identifiera mål-entiteter. Kontrollera Query Store för andra frågor som ändrar samma entitet, som ofta körs och/eller har hög varaktighet. Du bör ändra programlogiken för att förbättra samtidighet när du har identifierat de här frågorna, eller använda en mindre begränsande isoleringsnivå. |
|Hög buffert-i/o-väntar | Hitta frågor med ett stort antal fysiska läsningar i Query Store. Överväg att introduktion till ett index i den underliggande entiteten, göra strävar efter i stället för genomsökningar om de matchar frågor med hög i/o-väntar. Detta skulle minimera i/o-overhead från frågorna. Kontrollera den **Prestandarekommendationer** för servern i portalen för att se om det finns indexrekommendationer för den här servern som skulle optimera frågorna. |
|Extra minne väntar | Hitta det översta minnet som förbrukar frågorna i Query Store. De här frågorna förmodligen mer fördröja förloppet för frågor som påverkas. Kontrollera den **Prestandarekommendationer** för servern i portalen för att se om det finns indexrekommendationer som skulle optimera förfrågningarna.|

## <a name="configuration-options"></a>Konfigurationsalternativ

När Query Store är aktiverat sparas data i windows för 15 minuters aggregering, upp till 500 olika frågor per fönster.

Följande alternativ är tillgängliga för att konfigurera Query Store-parametrar.

| **Parametern** | **Beskrivning** | **Standard** | **Adressintervall** |
|---|---|---|---|
| query_store_capture_mode | Aktivera query store-funktionen på/av baserat på värdet. Obs! Om performance_schema är av, kommer aktivera query_store_capture_mode aktivera performance_schema och en delmängd av prestanda schemat instrument som krävs för den här funktionen. | ALLA | INGEN, ALLA |
| query_store_capture_interval | Query store avbilda intervallet i minuter. Kan du ange intervallet som frågan-måtten sammanställs | 15 | 5 - 60 |
| query_store_capture_utility_queries | Att stänga av eller på att samla in alla verktyg-frågor som körs i systemet. | NO | JA, NEJ |
| query_store_retention_period_in_days | Tidsfönster i dagar att kvarhålla data i query store. | 7 | 1 - 30 |

Följande alternativ gäller specifikt för att vänta statistik.

| **Parametern** | **Beskrivning** | **Standard** | **Adressintervall** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Tillåter att aktivera på / av wait-statistik. | INGEN | INGEN, ALLA |
| query_store_wait_sampling_frequency | Alters frekvensen för wait-exemplet på några sekunder. 5 och 300 sekunder. | 30 | 5-300 |

> [!NOTE]
> För närvarande **query_store_capture_mode** ersätter den här konfigurationen, vilket innebär att båda **query_store_capture_mode** och **query_store_wait_sampling_capture_mode** måste aktiveras för alla för vänta statistics ska fungera. Om **query_store_capture_mode** är avstängd, och sedan vänta statistik är avstängd samt eftersom vänta statistik använder performance_schema aktiverat och den fråga som avbildas av frågearkivet.

Använd den [Azure-portalen](howto-server-parameters.md) att hämta eller ange ett annat värde för en parameter.

## <a name="views-and-functions"></a>Vyer och funktioner

Visa och hantera Query Store med hjälp av följande vyer och funktioner. Alla i den [väljer behörighet allmänna rollen](howto-create-users.md#create-additional-admin-users) kan använda dessa vyer för att se data i Query Store. Dessa vyer är bara tillgängliga i den **mysql** databas.

Frågor normaliserade genom att titta på deras struktur när du tar bort tidslitteraler och konstanter. Om två frågor är identiska förutom literalvärden, har de samma hash.

### <a name="mysqlquerystore"></a>mysql.query_store

Den här vyn returnerar alla data i Query Store. Det finns en rad för varje distinkt databas-ID, användar-ID och fråga-ID.

| **Name** | **Datatyp** | **IS_NULLABLE** | **Beskrivning** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Namnet på schemat |
| `query_id`| bigint(20) | NO| Unikt ID som genereras för frågan, om samma fråga som körs i olika schema, ett nytt ID genereras |
| `timestamp_id` | timestamp| NO| Tidsstämpel som frågan körs. Detta baseras på konfigurationen av query_store_interval|
| `query_digest_text`| longtext| NO| När du tar bort alla litteraler normaliserade frågetexten|
| `query_sample_text` | longtext| NO| Första utseendet på den faktiska frågan med litteraler|
| `query_digest_truncated` | bit| JA| Om frågetexten har trunkerats. Värdet kommer att vara Ja om frågan är längre än 1 KB|
| `execution_count` | bigint(20)| NO| Antal gånger frågan har utförts för den här tidsstämpel-ID / under det konfigurerade intervallet|
| `warning_count` | bigint(20)| NO| Antal varningar som den här frågan som genererats under den interna|
| `error_count` | bigint(20)| NO| Antal fel som den här frågan som genererats under period|
| `sum_timer_wait` | double| JA| Total körningstid för den här frågan under intervallet|
| `avg_timer_wait` | double| JA| Genomsnittlig körningstid för den här frågan under intervallet|
| `min_timer_wait` | double| JA| Minsta körningstid för den här frågan|
| `max_timer_wait` | double| JA| Maximal körningstid|
| `sum_lock_time` | bigint(20)| NO| Sammanlagd tid som har använt för alla Lås för den här frågekörning under den här tidsperioden|
| `sum_rows_affected` | bigint(20)| NO| Antalet berörda rader|
| `sum_rows_sent` | bigint(20)| NO| Antalet rader som skickas till klienten|
| `sum_rows_examined` | bigint(20)| NO| Antalet rader som undersöks|
| `sum_select_full_join` | bigint(20)| NO| Antalet full JOIN|
| `sum_select_scan` | bigint(20)| NO| Antal väljer genomsökningar |
| `sum_sort_rows` | bigint(20)| NO| Antalet rader sorterade|
| `sum_no_index_used` | bigint(20)| NO| Antal gånger när frågan inte använde index|
| `sum_no_good_index_used` | bigint(20)| NO| Antal gånger när motorn för körning av frågan inte använde bra index|
| `sum_created_tmp_tables` | bigint(20)| NO| Totalt antal temporära tabeller som har skapats|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Totalt antal temporära tabeller som skapats på disken (genererar i/o)|
| `first_seen` | timestamp| NO| Den första förekomsten (UTC) av frågan under fönstret aggregering|
| `last_seen` | timestamp| NO| Den sista förekomsten (UTC) av frågan under den här aggregeringen perioden|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

Den här vyn returnerar vänta händelsedata i Query Store. Det finns en rad för varje distinkt databas-ID, användar-ID, fråge-ID och händelse.

| **Name**| **Datatyp** | **IS_NULLABLE** | **Beskrivning** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Början av intervallet (15 minuters räkna upp)|
| `interval_end` | timestamp | NO| Slutet av intervallet (15 minuters räkna upp)|
| `query_id` | bigint(20) | NO| Genererade unika ID: T för den normaliserade frågan (från frågearkivet)|
| `query_digest_id` | varchar(32) | NO| Normaliserade frågetexten när du tar bort alla litteraler (från frågearkivet) |
| `query_digest_text` | longtext | NO| Första utseendet på den faktiska frågan med litteraler (från frågearkivet) |
| `event_type` | varchar(32) | NO| Vänta händelsekategori |
| `event_name` | varchar(128) | NO| Vänta händelsens namn |
| `count_star` | bigint(20) | NO| Flera väntetiden händelser samplas under intervallet för frågan |
| `sum_timer_wait_ms` | double | NO| Total väntetid (i millisekunder) för den här frågan under intervallet |

### <a name="functions"></a>Functions

| **Name**| **Beskrivning** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Tar bort alla query store-data innan den givna tidsstämpeln |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Alla återställningspunkter vänta händelsedata innan den givna tidsstämpeln |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Tar bort rekommendationer vars förfallotid är innan den givna tidsstämpeln |

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

- Om en MariaDB-server har parametern `default_transaction_read_only` , Query Store kan inte samla in data.
- Funktionen för Query Store kan avbrytas om det uppstår långa Unicode-frågor (\>= 6000 byte).
- Kvarhållningsperioden för vänta statistik är 24 timmar.
- Vänta statistik använder exemplet ti avbildning som en del av händelserna. Hur ofta kan ändras med hjälp av parametern `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Prestandainsikter för fråga](concepts-query-performance-insight.md)
