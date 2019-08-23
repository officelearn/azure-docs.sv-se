---
title: Query Store i Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs funktionen Query Store i Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 5ddbff62421d97b1105a997bd084e1fe5b44cf12
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907422"
---
# <a name="monitor-performance-with-the-query-store"></a>Övervaka prestanda med Query Store

**Gäller för:** Azure Database for PostgreSQL-enskild server 9,6 och 10

Funktionen Query Store i Azure Database for PostgreSQL ger dig ett sätt att spåra frågeresultaten över tid. Query Store fören klar prestanda fel sökningen genom att hjälpa dig att snabbt hitta de allra som körs och de flesta resurs intensiva frågor. Query Store samlar automatiskt in en historik över frågor och körnings statistik och behåller dem för din granskning. Den separerar data efter tids period, så att du kan se databas användnings mönster. Data för alla användare, databaser och frågor lagras i en databas med namnet **azure_sys** i Azure Database for PostgreSQL-instansen.

> [!IMPORTANT]
> Ändra inte **azure_sys** -databasen eller dess scheman. Om du gör det förhindras Frågearkivet och relaterade prestanda funktioner från att fungera korrekt.

## <a name="enabling-query-store"></a>Aktivera Query Store
Frågearkivet är en valbar funktion, så den är inte aktiv som standard på en server. Store är aktiverat eller inaktiverat globalt för alla databaser på en viss server och kan inte aktive ras eller stängas av per databas.

### <a name="enable-query-store-using-the-azure-portal"></a>Aktivera Query Store med hjälp av Azure Portal
1. Logga in på Azure Portal och välj Azure Database for PostgreSQL-servern.
2. Välj **Server parametrar** i avsnittet **Inställningar** på menyn.
3. Sök efter `pg_qs.query_capture_mode` parametern.
4. Ange värdet till `TOP` och **Spara**.

Så här aktiverar du väntande statistik i Frågearkivet: 
1. Sök efter `pgms_wait_sampling.query_capture_mode` parametern.
1. Ange värdet till `ALL` och **Spara**.


Du kan också ange dessa parametrar med hjälp av Azure CLI.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Låt upp till 20 minuter innan den första data mängden sparas i azure_sys-databasen.

## <a name="information-in-query-store"></a>Information i Frågearkivet
Frågearkivet har två butiker:
- En körnings statistik för att spara information om statistik för körning av fråga.
- En väntande stat för att spara information om väntande statistik.

Vanliga scenarier för att använda Query Store är:
- Bestämma hur många gånger en fråga kördes under ett angivet tidsintervall
- Jämför genomsnittlig körnings tid för en fråga över tid i Windows för att se stora delta
- Identifiera de längsta körnings frågorna de senaste X timmarna
- Identifiera de N främsta frågorna som väntar på resurser
- Förstå vänte natur för en viss fråga

För att minimera utrymmes användningen sammanställs körnings statistiken för körning i körnings statistik lagret över en fast, konfigurerbar tids period. Informationen i dessa butiker är synlig genom att fråga vyn över frågearkivet.

Följande fråga returnerar information om frågor i Frågearkivet:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Eller den här frågan för wait-statistik:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Hitta väntande frågor
Väntande händelse typer kombinerar olika vänte händelser till buckets efter likhet. Frågearkivet innehåller vänte händelse typ, ett särskilt namn på wait-händelsen och frågan i fråga. Om du vill korrelera denna wait-information med frågans körnings statistik innebär det att du får en djupare förståelse för vad som bidrar till att fråga prestanda egenskaperna.

Här följer några exempel på hur du kan få mer insikter om din arbets belastning med väntande statistik i Frågearkivet:

| **Permanenta** | **Åtgärd** |
|---|---|
|Hög lås väntar | Kontrol lera fråge texterna för de berörda frågorna och identifiera målentiteten. Leta i Frågearkivet efter andra frågor som ändrar samma entitet, som körs ofta och/eller har hög varaktighet. När du har identifierat dessa frågor bör du överväga att ändra program logiken till att förbättra samtidigheten eller använda en mindre begränsande isolerings nivå.|
| Hög buffert i/o väntar | Hitta frågorna med ett stort antal fysiska läsningar i Frågearkivet. Om de matchar frågorna med höga i/o-väntan, bör du införa ett index för den underliggande entiteten för att göra det i stället för genomsökningar. Detta skulle minimera IO-omkostnaderna för frågorna. Kontrol lera **prestanda rekommendationerna** för servern i portalen för att se om det finns några index rekommendationer för den här servern som skulle optimera frågorna.|
| Hög minnes väntan | Hitta de mest krävande minnes frågorna i Frågearkivet. Dessa frågor fördröjer förmodligen ytterligare förloppet för de frågor som påverkas. Kontrol lera **prestanda rekommendationerna** för servern i portalen för att se om det finns några index rekommendationer som skulle optimera dessa frågor.|

## <a name="configuration-options"></a>Konfigurationsalternativ
När Query Store har Aktiver ATS sparas data i 15-minuters agg regerings fönster, upp till 500 distinkta frågor per fönster. 

Följande alternativ är tillgängliga för att konfigurera parametrar för Frågearkivet.

| **Parametern** | **Beskrivning** | **Standard** | **Område**|
|---|---|---|---|
| pg_qs.query_capture_mode | Anger vilka instruktioner som spåras. | inga | ingen, Top, alla |
| pg_qs.max_query_text_length | Anger den maximala fråge längden som kan sparas. Längre frågor kommer att trunkeras. | 6000 | 100 – 10 000 |
| pg_qs.retention_period_in_days | Anger kvarhållningsperioden. | 7 | 1 - 30 |
| pg_qs.track_utility | Anger om verktygs kommandon spåras | på | på, av |

Följande alternativ gäller specifikt för väntande statistik.

| **Parametern** | **Beskrivning** | **Standard** | **Område**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Anger vilka instruktioner som spåras för väntande statistik. | inga | ingen, alla|
| Pgms_wait_sampling.history_period | Ange frekvensen, i millisekunder, vid sampling av väntande händelser. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs. query_capture_mode** ersätter **pgms_wait_sampling. query_capture_mode**. Om pg_qs. query_capture_mode är ingen, har inställningen pgms_wait_sampling. query_capture_mode ingen påverkan.


Använd [Azure Portal](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md) för att hämta eller ange ett annat värde för en parameter.

## <a name="views-and-functions"></a>Vyer och funktioner
Visa och hantera Frågearkivet med följande vyer och funktioner. Alla i den offentliga PostgreSQL-rollen kan använda dessa vyer för att se data i Frågearkivet. Dessa vyer är bara tillgängliga i **azure_sys** -databasen.

Frågorna normaliseras genom att titta på deras struktur efter att du tagit bort litteraler och konstanter. Om två frågor är identiska förutom literala värden, har de samma hash.

### <a name="query_storeqs_view"></a>query_store.qs_view
Den här vyn returnerar alla data i Frågearkivet. Det finns en rad för varje distinkt databas-ID, användar-ID och fråge-ID. 

|**Namn**   |**Typ** | **Reference**  | **Beskrivning**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID från tabellen runtime_stats_entries|
|user_id    |OID    |pg_authid.oid  |OID för den användare som körde instruktionen|
|db_id  |OID    |pg_database.oid    |OID för databasen där instruktionen kördes|
|query_id   |bigint  || Intern hash-kod, beräknad från instruktionens parse-träd|
|query_sql_text |Varchar (10000)  || Text för en representativ instruktion. Olika frågor med samma struktur grupperas tillsammans. den här texten är texten för den första av frågorna i klustret.|
|plan_id    |bigint |   |ID för planen som motsvarar den här frågan, inte tillgängligt ännu|
|start_time |timestamp  ||  Frågor sammanställs av tidsbuckets – tids perioden för en Bucket är 15 minuter som standard. Detta är start tiden som motsvarar tidsbucket för den här posten.|
|end_time   |timestamp  ||  Slut tid som motsvarar tidsbucket för den här posten.|
|anrop  |bigint  || Antal gånger som frågan kördes|
|total_time |dubbel precision   ||  Total körnings tid i millisekunder för fråga|
|min_time   |dubbel precision   ||  Minsta körnings tid för fråga, i millisekunder|
|max_time   |dubbel precision   ||  Maximal tid för frågekörning, i millisekunder|
|mean_time  |dubbel precision   ||  Genomsnittlig tid för körning av fråga, i millisekunder|
|stddev_time|   dubbel precision    ||  Standard avvikelse för frågans körnings tid, i millisekunder |
|rader   |bigint ||  Totalt antal rader som hämtats eller påverkats av instruktionen|
|shared_blks_hit|   bigint  ||  Totalt antal träffar för delade block-cache med instruktionen|
|shared_blks_read|  bigint  ||  Totalt antal delade block som lästs av instruktionen|
|shared_blks_dirtied|   bigint   || Totalt antal delade block som dirtied av instruktionen |
|shared_blks_written|   bigint  ||  Totalt antal delade block som skrivits av instruktionen|
|local_blks_hit|    bigint ||   Totalt antal lokala block cacheträffar per instruktionen|
|local_blks_read|   bigint   || Totalt antal lokala block som lästs av instruktionen|
|local_blks_dirtied|    bigint  ||  Totalt antal lokala block dirtied med instruktionen|
|local_blks_written|    bigint  ||  Totalt antal lokala block som skrivits av instruktionen|
|temp_blks_read |bigint  || Totalt antal temporära block som lästs av instruktionen|
|temp_blks_written| bigint   || Totalt antal temporära block som skrivits av instruktionen|
|blk_read_time  |dubbel precision    || Total tid som instruktionen använder för att läsa block, i millisekunder (om track_io_timing har Aktiver ATS, annars noll)|
|blk_write_time |dubbel precision    || Total tid som instruktionen ägnat åt att skriva block, i millisekunder (om track_io_timing är aktiverat, annars noll)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Den här vyn returnerar text data i Frågearkivet. Det finns en rad för varje distinkt query_text.

|**Namn**|  **Typ**|   **Beskrivning**|
|---|---|---|
|query_text_id  |bigint     |ID för query_texts-tabellen|
|query_sql_text |Varchar (10000)     |Text för en representativ instruktion. Olika frågor med samma struktur grupperas tillsammans. den här texten är texten för den första av frågorna i klustret.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Den här vyn returnerar information om väntande händelser i Frågearkivet. Det finns en rad för varje distinkt databas-ID, användar-ID, fråge-ID och händelse.

|**Namn**|  **Typ**|   **Reference**| **Beskrivning**|
|---|---|---|---|
|user_id    |OID    |pg_authid.oid  |OID för den användare som körde instruktionen|
|db_id  |OID    |pg_database.oid    |OID för databasen där instruktionen kördes|
|query_id   |bigint     ||Intern hash-kod, beräknad från instruktionens parse-träd|
|event_type |text       ||Den typ av händelse som server delen väntar på|
|händelse  |text       ||Vänte händelse namnet om Server delen väntar på att stoppas|
|anrop  |Integer        ||Antal insamlade händelser|


### <a name="functions"></a>Funktioner
Query_store. qs_reset () returnerar void

`qs_reset` ignorerar all statistik som har samlats in hittills i Query Store. Den här funktionen kan bara utföras av Server administratörs rollen.

Query_store.staging_data_reset() returns void

`staging_data_reset` ignorerar all statistik som samlas in i minnet av Frågearkivet (det vill säga data i minnet som inte har tömts till databasen). Den här funktionen kan bara utföras av Server administratörs rollen.

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem
- Om PostgreSQL-servern har parametern default_transaction_read_only på kan Frågearkivet inte samla in data.
- Query Store-funktionen kan avbrytas om den påträffar långa Unicode-frågor (> = 6000 byte).
- [Läs repliker](concepts-read-replicas.md) replikerar Query Store-data från huvud servern. Det innebär att en Läs repliks Frågearkivet inte tillhandahåller statistik om frågor som körs på Läs repliken.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [scenarier där Query Store kan vara särskilt användbart](concepts-query-store-scenarios.md).
- Lär dig mer om [metod tips för att använda Query Store](concepts-query-store-best-practices.md).
