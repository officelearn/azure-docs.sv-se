---
title: Query Store - Azure-databas för PostgreSQL - Single Server
description: I den här artikeln beskrivs funktionen Query Store i Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: ccc503e6718ee8f516920cfbea3ad86e7ed81d84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768273"
---
# <a name="monitor-performance-with-the-query-store"></a>Övervaka prestanda med Frågearkivet

**Gäller:** Azure-databas för PostgreSQL - Single Server version 9.6, 10, 11

Funktionen Query Store i Azure Database for PostgreSQL är ett sätt att spåra frågeprestanda över tid. Query Store förenklar felsökningen av prestanda genom att hjälpa dig att snabbt hitta de mest tidskrävande och mest resursintensiva frågorna. Query Store samlar automatiskt in en historik över frågor och körningsstatistik, och den behåller dem för din granskning. Den separerar data efter tidsfönster så att du kan se databasanvändningsmönster. Data för alla användare, databaser och frågor lagras i en databas med namnet **azure_sys** i Azure-databasen för PostgreSQL-instans.

> [!IMPORTANT]
> Ändra inte **azure_sys** eller dess scheman. Om du gör det förhindrar du att Frågearkivet och relaterade prestandafunktioner fungerar korrekt.

## <a name="enabling-query-store"></a>Aktivera Frågebutik
Query Store är en opt-in-funktion, så den är inte aktiv som standard på en server. Arkivet är aktiverat eller inaktiverat globalt för alla databaser på en viss server och kan inte aktiveras eller inaktiveras per databas.

### <a name="enable-query-store-using-the-azure-portal"></a>Aktivera Query Store med Azure-portalen
1. Logga in på Azure-portalen och välj din Azure-databas för PostgreSQL-server.
2. Välj **Serverparametrar** i avsnittet **Inställningar** på menyn.
3. Sök efter `pg_qs.query_capture_mode` parametern.
4. Ange värdet `TOP` till och **Spara**.

Så här aktiverar du väntestatistik i Frågearkivet: 
1. Sök efter `pgms_wait_sampling.query_capture_mode` parametern.
1. Ange värdet `ALL` till och **Spara**.


Alternativt kan du ställa in dessa parametrar med hjälp av Azure CLI.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Vänta 20 minuter för den första databatchen ska hinna sparas i databasen azure_sys.

## <a name="information-in-query-store"></a>Information i Frågearbeta
Query Store har två butiker:
- Ett körningsstatistikarkiv för att bevara statistikinformationen för frågekörning.
- En väntestatistikbutik för ihållande väntestatistikinformation.

Vanliga scenarier för att använda Query Store är:
- Bestämma hur många gånger en fråga har körts i ett visst tidsfönster
- Jämföra den genomsnittliga körningstiden för en fråga över tidsfönster för att se stora deltan
- Identifiera de senaste X-timmarnas tidskrävande frågor
- Identifiera de vanligaste N-frågorna som väntar på resurser
- Förstå vänta naturen för en viss fråga

För att minimera utrymmesanvändningen sammanställs körningsstatistiken i körningsstatistikarkivet över ett fast, konfigurerbart tidsfönster. Informationen i dessa butiker är synlig genom att fråga efter frågearkivvyerna.

## <a name="access-query-store-information"></a>Information om Access Query Store

Query Store-data lagras i azure_sys-databasen på Postgres-servern. 

Följande fråga returnerar information om frågor i Query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Eller den här frågan för väntestatistik:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

Du kan också avge Query Store-data till [Azure Monitor Logs](../azure-monitor/log-query/log-query-overview.md) för analys och avisering, eventhubbar för direktuppspelning och Azure Storage för arkivering. Loggkategorierna som ska konfigureras är **QueryStoreRuntimeStatistics** och **QueryStoreWaitStatistics**. Mer information om installationen finns i artikeln [för diagnostikinställningar för Azure Monitor.](../azure-monitor/platform/diagnostic-settings.md)


## <a name="finding-wait-queries"></a>Hitta vänta frågor
Vänta händelsetyper kombinerar olika vänta händelser i buckets efter likhet. Query Store innehåller typen väntad händelse, specifika vänta händelsenamn och frågan i fråga. Att kunna korrelera den här vänta-informationen med frågekörningsstatistiken innebär att du kan få en djupare förståelse för vad som bidrar till frågeprestandaegenskaper.

Här är några exempel på hur du kan få mer insikter om din arbetsbelastning med hjälp av väntestatistiken i Query Store:

| **Observation** | **Åtgärd** |
|---|---|
|High Lock väntar | Kontrollera frågetexterna för de berörda frågorna och identifiera målentiteterna. Leta i Query Store efter andra frågor som ändrar samma entitet, som körs ofta och/eller har hög varaktighet. När du har identifierat dessa frågor bör du överväga att ändra programlogiken för att förbättra samtidigheten eller använda en mindre restriktiv isoleringsnivå.|
| Hög buffert-I/o väntar | Hitta frågor med ett stort antal fysiska läsningar i Query Store. Om de matchar frågorna med höga IO-väntetider kan du överväga att införa ett index på den underliggande entiteten för att göra sökningar i stället för genomsökningar. Detta skulle minimera IO omkostnader för frågorna. Kontrollera **prestandarekommendationerna** för servern i portalen för att se om det finns indexrekommendationer för den här servern som skulle optimera frågorna.|
| Högt minne väntar | Hitta det översta minne som förbrukar frågor i Query Store. Dessa frågor försenar förmodligen ytterligare förlopp för de berörda frågorna. Kontrollera **prestandarekommendationerna** för servern i portalen för att se om det finns indexrekommendationer som skulle optimera dessa frågor.|

## <a name="configuration-options"></a>Konfigurationsalternativ
När Query Store är aktiverat sparar den data i 15-minuters aggregeringsfönster, upp till 500 olika frågor per fönster. 

Följande alternativ är tillgängliga för att konfigurera Frågelagringsparametrar.

| **Parametern** | **Beskrivning** | **Default** | **Intervall**|
|---|---|---|---|
| pg_qs.query_capture_mode | Anger vilka satser som spåras. | ingen | ingen, topp, alla |
| pg_qs.max_query_text_length | Anger den maximala frågelängden som kan sparas. Längre frågor kommer att trunkeras. | 6000 | 100 - 10K |
| pg_qs.retention_period_in_days | Anger kvarhållningsperioden. | 7 | 1 - 30 |
| pg_qs.track_utility | Anger om verktygskommandon spåras | på | på, av |

Följande alternativ gäller specifikt för att vänta statistik.

| **Parametern** | **Beskrivning** | **Default** | **Intervall**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Anger vilka utdrag som spåras för väntestatistik. | ingen | ingen, alla|
| Pgms_wait_sampling.history_period | Ställ in frekvensen i millisekunder, där väntehändelser samplas. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** ersätter **pgms_wait_sampling.query_capture_mode**. Om pg_qs.query_capture_mode är NONE har inställningen pgms_wait_sampling.query_capture_mode ingen effekt.


Använd [Azure-portalen](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md) för att hämta eller ange ett annat värde för en parameter.

## <a name="views-and-functions"></a>Vyer och funktioner
Visa och hantera Query Store med hjälp av följande vyer och funktioner. Alla i den offentliga rollen PostgreSQL kan använda dessa vyer för att se data i Query Store. Dessa vyer är endast tillgängliga i **azure_sys** databas.

Frågor normaliseras genom att titta på deras struktur efter att ha tagit bort litteraler och konstanter. Om två frågor är identiska med undantag för litterala värden har de samma hash.

### <a name="query_storeqs_view"></a>query_store.qs_view
Den här vyn returnerar alla data i Query Store. Det finns en rad för varje distinkt databas-ID, användar-ID och fråge-ID. 

|**Namn**   |**Typ** | **Referenser**  | **Beskrivning**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID från tabellen runtime_stats_entries|
|user_id    |Oid    |pg_authid.oid  |OID för användare som utförde utdraget|
|db_id  |Oid    |pg_database.oid    |OID för databas där uttalandet utfördes|
|query_id   |bigint  || Intern hash-kod som beräknas från uttryckets parsa träd|
|query_sql_text |Varchar(10000)  || Texten till ett representativt uttalande. Olika frågor med samma struktur grupperas tillsammans. Den här texten är texten för den första av frågorna i klustret.|
|plan_id    |bigint |   |ID för planen som motsvarar den här frågan, inte tillgänglig ännu|
|start_time |timestamp  ||  Frågor aggregeras efter tidssegment - tidsintervallet för en bucket är 15 minuter som standard. Det här är starttiden som motsvarar tidsenheten för den här posten.|
|end_time   |timestamp  ||  Sluttid som motsvarar tidsenheten för den här posten.|
|Samtal  |bigint  || Antal gånger som frågan har körts|
|total_time |dubbel precision   ||  Total körningstid för frågor i millisekunder|
|min_time   |dubbel precision   ||  Minsta tid för körning av frågor i millisekunder|
|max_time   |dubbel precision   ||  Maximal körningstid för frågor i millisekunder|
|mean_time  |dubbel precision   ||  Genomsnittlig körningstid för frågor, i millisekunder|
|stddev_time|   dubbel precision    ||  Standardavvikelse för frågekörningstiden, i millisekunder |
|Rader   |bigint ||  Totalt antal rader som hämtats eller påverkats av utdraget|
|shared_blks_hit|   bigint  ||  Totalt antal träffar av delad blockcache av uttrycket|
|shared_blks_read|  bigint  ||  Totalt antal delade block som läsats av uttalandet|
|shared_blks_dirtied|   bigint   || Totalt antal delade block som smutsats av uttalandet |
|shared_blks_written|   bigint  ||  Totalt antal delade block skrivna av uttalandet|
|local_blks_hit|    bigint ||   Totalt antal lokala blockcachen träffar av satsen|
|local_blks_read|   bigint   || Totalt antal lokala block som läsats av uttalandet|
|local_blks_dirtied|    bigint  ||  Totalt antal lokala block som smutsats av uttalandet|
|local_blks_written|    bigint  ||  Totalt antal lokala block som skrivits av uttalandet|
|temp_blks_read |bigint  || Totalt antal temp-block som läsats av uttalandet|
|temp_blks_written| bigint   || Totalt antal temp block skrivna av uttalandet|
|blk_read_time  |dubbel precision    || Total tid som satsen har spenderat läsblock i millisekunder (om track_io_timing är aktiverat, annars noll)|
|blk_write_time |dubbel precision    || Total tid som uttalandet har spenderats på att skriva block i millisekunder (om track_io_timing är aktiverat, annars noll)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
I den här vyn returneras frågetextdata i Query Store. Det finns en rad för varje distinkt query_text.

|**Namn**|  **Typ**|   **Beskrivning**|
|---|---|---|
|query_text_id  |bigint     |ID för tabellen query_texts|
|query_sql_text |Varchar(10000)     |Texten till ett representativt uttalande. Olika frågor med samma struktur grupperas tillsammans. Den här texten är texten för den första av frågorna i klustret.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
I den här vyn returneras väntehändelser i Query Store. Det finns en rad för varje distinkt databas-ID, användar-ID, fråge-ID och händelse.

|**Namn**|  **Typ**|   **Referenser**| **Beskrivning**|
|---|---|---|---|
|user_id    |Oid    |pg_authid.oid  |OID för användare som utförde utdraget|
|db_id  |Oid    |pg_database.oid    |OID för databas där uttalandet utfördes|
|query_id   |bigint     ||Intern hash-kod som beräknas från uttryckets parsa träd|
|event_type |text       ||Den typ av händelse som backend väntar på|
|händelse  |text       ||Namnet på vänta-händelse om backend väntar|
|Samtal  |Integer        ||Antal av samma händelse som fångas|


### <a name="functions"></a>Funktioner
Query_store.qs_reset() returnerar ogiltigt

`qs_reset` ignorerar all statistik som hittills samlats in av Query Store. Den här funktionen kan bara köras av serveradministratörsrollen.

Query_store.staging_data_reset() returnerar ogiltigt

`staging_data_reset` ignorerar all statistik som samlas in i minnet av Query Store (det vill än data i minnet som ännu inte har spolats till databasen). Den här funktionen kan bara köras av serveradministratörsrollen.

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem
- Om en PostgreSQL-server har parametern default_transaction_read_only på, kan Query Store inte samla in data.
- Query Store-funktionen kan avbrytas om den stöter på långa Unicode-frågor (>= 6000 byte).
- [Läs repliker](concepts-read-replicas.md) replikera Query Store-data från huvudservern. Det innebär att en läsrepliks Query Store inte tillhandahåller statistik om frågor som körs på läsrepliken.


## <a name="next-steps"></a>Nästa steg
- Läs mer om [scenarier där Query Store kan vara särskilt användbart](concepts-query-store-scenarios.md).
- Läs mer om [metodtips för att använda Query Store](concepts-query-store-best-practices.md).
