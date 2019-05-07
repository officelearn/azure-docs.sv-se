---
title: Query Store i Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskriver funktionen Query Store i Azure Database för PostgreSQL – enskild Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: b622de3e21d26676bb11d81a6facf8fea18cabc1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067190"
---
# <a name="monitor-performance-with-the-query-store"></a>Övervaka prestanda med Query Store

**Gäller för:** Azure Database för PostgreSQL – enskild Server 9.6 och 10

Query Store-funktionen i Azure Database for PostgreSQL ger ett sätt att spåra prestanda för frågor över tid. Query Store förenklar prestandafelsökning genom att hjälpa dig snabbt hitta körs längst och mest resurskrävande frågor. Query Store avbildas automatiskt en historik över frågor och körningsstatistik och den lagrar dem för granskning. När du delar den upp data efter tidsfönster så att du kan se databasanvändningsmönster. Data för alla användare, databaser och frågor lagras i en databas med namnet **azure_sys** i Azure Database for PostgreSQL-instans.

> [!IMPORTANT]
> Ändra inte den **azure_sys** databasen eller dess scheman. Gör detta kommer inte Query Store- och prestandadata relaterade funktioner inte fungerar korrekt.

## <a name="enabling-query-store"></a>Aktivera Query Store
Query Store är en valbar funktion så att den inte är aktiv som standard på en server. Arkivet är aktiverat eller inaktiverat globalt för alla databaser på en viss server och kan inte stängas eller inaktivera per databas.

### <a name="enable-query-store-using-the-azure-portal"></a>Aktivera Query Store med Azure portal
1. Logga in på Azure Portal och välj din Azure Database for PostgreSQL-server.
2. Välj **serverparametrar** i den **inställningar** på menyn.
3. Sök efter den `pg_qs.query_capture_mode` parametern.
4. Ange värdet till `TOP` och **spara**.

Så här aktiverar du vänta statistik i din fråga Store: 
1. Sök efter den `pgms_wait_sampling.query_capture_mode` parametern.
1. Ange värdet till `ALL` och **spara**.


Du kan också ange dessa parametrar med Azure CLI.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Kan ta upp till 20 minuter för den första batchen av data ska sparas i databasen azure_sys.

## <a name="information-in-query-store"></a>Informationen i Query Store
Query Store har två lager:
- En runtime stats butik för att spara frågan körning statistik information för.
- En vänta stats butik för att spara för vänta statistikinformation.

Vanliga scenarier för att använda Query Store är:
- Avgör hur många gånger har en fråga utförts under en viss tidsperiod
- Jämföra Genomsnittlig körningstid för en fråga över tidsfönster att visa stora deltan
- Identifiera längsta frågor som körs i förflutna X timmar
- Identifiera de x främsta Felgrupperna frågor som väntar på resurser
- Förstå vänta karaktär för en viss fråga

För att minimera användning av diskutrymme, sammanställs körningsstatistik för körning i arkivet runtime statistik över ett fast, konfigurerbara tidsfönster. Informationen i dessa lager är synligt genom att fråga store frågevyer.

Följande fråga returnerar information om frågor i Query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Eller den här frågan för väntestatistik:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Frågor för att hitta vänta
Vänta händelsetyper kombinera olika vänta händelser i buckets efter likhet. Query Store ger händelsetyp vänta och specifika vänta händelsenamn frågan i fråga. Att kunna korrelera informationen vänta med frågan runtime statistik innebär att du kan få en djupare förståelse för vad bidrar till fråga prestandaegenskaper.

Här följer några exempel på hur du kan få fler insikter om din arbetsbelastning med vänta statistik i Query Store:

| **Fjärrvisning** | **Åtgärd** |
|---|---|
|Hög Lås väntar | Kontrollera fråga texter för frågor som påverkas och identifiera mål-entiteter. Kontrollera Query Store för andra frågor som ändrar samma entitet, som ofta körs och/eller har hög varaktighet. Du bör ändra programlogiken för att förbättra samtidighet när du har identifierat de här frågorna, eller använda en mindre begränsande isoleringsnivå.|
| Hög buffert-i/o-väntar | Hitta frågor med ett stort antal fysiska läsningar i Query Store. Överväg att introduktion till ett index i den underliggande entiteten för att kunna göra strävar efter i stället för genomsökningar om de matchar frågor med hög i/o-väntar. Detta skulle minimera i/o-overhead från frågorna. Kontrollera den **Prestandarekommendationer** för servern i portalen för att se om det finns indexrekommendationer för den här servern som skulle optimera frågorna.|
| Extra minne väntar | Hitta det översta minnet som förbrukar frågorna i Query Store. De här frågorna förmodligen mer fördröja förloppet för frågor som påverkas. Kontrollera den **Prestandarekommendationer** för servern i portalen för att se om det finns indexrekommendationer som skulle optimera förfrågningarna.|

## <a name="configuration-options"></a>Konfigurationsalternativ
När Query Store är aktiverat sparas data i windows för 15 minuters aggregering, upp till 500 olika frågor per fönster. 

Följande alternativ är tillgängliga för att konfigurera Query Store-parametrar.

| **Parametern** | **Beskrivning** | **Standard** | **Adressintervall**|
|---|---|---|---|
| pg_qs.query_capture_mode | Anger vilka instruktioner spåras. | inga | Ingen, uppifrån, alla |
| pg_qs.max_query_text_length | Anger den maximala frågelängd som kan sparas. Längre frågor trunkeras. | 6000 | 100 - 10K |
| pg_qs.retention_period_in_days | Anger kvarhållningsperioden. | 7 | 1 - 30 |
| pg_qs.track_utility | Anger om verktygskommandon spåras | på | på, av |

Följande alternativ gäller specifikt för att vänta statistik.

| **Parametern** | **Beskrivning** | **Standard** | **Adressintervall**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Anger vilket uttryck spåras för vänta statistik. | inga | Ingen, alla|
| Pgms_wait_sampling.history_period | Ange frekvens, i millisekunder, vid vilken vänta samplas händelser. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** supersedes **pgms_wait_sampling.query_capture_mode**. Om pg_qs.query_capture_mode är NONE, har pgms_wait_sampling.query_capture_mode inställningen ingen effekt.


Använd den [Azure-portalen](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md) att hämta eller ange ett annat värde för en parameter.

## <a name="views-and-functions"></a>Vyer och funktioner
Visa och hantera Query Store med hjälp av följande vyer och funktioner. Alla i den allmänna rollen PostgreSQL kan använda dessa vyer för att se data i Query Store. Dessa vyer är bara tillgängliga i den **azure_sys** databas.

Frågor normaliserade genom att titta på deras struktur när du tar bort tidslitteraler och konstanter. Om två frågor är identiska förutom literalvärden, har de samma hash.

### <a name="querystoreqsview"></a>query_store.qs_view
Den här vyn returnerar alla data i Query Store. Det finns en rad för varje distinkt databas-ID, användar-ID och fråga-ID. 

|**Namn**   |**Typ** | **Referenser**  | **Beskrivning**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID: T från tabellen runtime_stats_entries|
|user_id    |oid    |pg_authid.oid  |OID för användare som har utfört instruktionen|
|db_id  |oid    |pg_database.oid    |OID för databasen där instruktionen kördes|
|query_id   |bigint  || Intern hash-koden som beräknas från utdragets parsningsträd|
|query_sql_text |Varchar(10000)  || Text för en representativ-instruktion. Olika frågor med samma struktur är klustrade tillsammans. den här texten är texten för först frågor i klustret.|
|plan_id    |bigint |   |ID för den plan som motsvarar den här frågan är inte tillgängligt ännu|
|Starttid |tidsstämpel  ||  Frågor samlas genom tid buckets - omfånget på en bucket är 15 minuter som standard. Det här är starttiden för enheten för den här posten.|
|end_time   |tidsstämpel  ||  Sluttid för enheten för den här posten.|
|anrop  |bigint  || Antal gånger som frågan körs|
|total_time |dubbel precision   ||  Totalt antal fråga körningstid, i millisekunder|
|min_time   |dubbel precision   ||  Minsta fråga körningstid, i millisekunder|
|max_time   |dubbel precision   ||  Maximal körningstid, i millisekunder|
|mean_time  |dubbel precision   ||  Innebär att fråga körningstid, i millisekunder|
|stddev_time|   dubbel precision    ||  Standardavvikelse fråga körningstid, i millisekunder |
|rader   |bigint ||  Totalt antal rader hämtas eller påverkas av instruktionen|
|shared_blks_hit|   bigint  ||  Totalt antal cacheträffar för delade block av instruktionen|
|shared_blks_read|  bigint  ||  Totalt antal delade läses av instruktionen|
|shared_blks_dirtied|   bigint   || Totalt antal delade dirtied av instruktionen |
|shared_blks_written|   bigint  ||  Totalt antal delade skrivits av instruktionen|
|local_blks_hit|    bigint ||   Totalt antal cacheträffar för lokala block av instruktionen|
|local_blks_read|   bigint   || Totalt antal lokala läses av instruktionen|
|local_blks_dirtied|    bigint  ||  Totalt antal lokala dirtied av instruktionen|
|local_blks_written|    bigint  ||  Totalt antal lokala skrivits av instruktionen|
|temp_blks_read |bigint  || Totalt antal temp läses av instruktionen|
|temp_blks_written| bigint   || Totalt antal temp skrivits av instruktionen|
|blk_read_time  |dubbel precision    || Total tid instruktionen läsning block i millisekunder (om track_io_timing är aktiverad, annars noll)|
|blk_write_time |dubbel precision    || Total tid instruktionen skrivs block i millisekunder (om track_io_timing är aktiverad, annars noll)|
    
### <a name="querystorequerytextsview"></a>query_store.query_texts_view
Den här vyn returnerar frågan textdata i Query Store. Det finns en rad för varje distinkt fråga.

|**Namn**|  **Typ**|   **Beskrivning**|
|---|---|---|
|query_text_id  |bigint     |ID för tabellen query_texts|
|query_sql_text |Varchar(10000)     |Text för en representativ-instruktion. Olika frågor med samma struktur är klustrade tillsammans. den här texten är texten för först frågor i klustret.|

### <a name="querystorepgmswaitsamplingview"></a>query_store.pgms_wait_sampling_view
Den här vyn returnerar vänta händelsedata i Query Store. Det finns en rad för varje distinkt databas-ID, användar-ID, fråge-ID och händelse.

|**Namn**|  **Typ**|   **Referenser**| **Beskrivning**|
|---|---|---|---|
|user_id    |oid    |pg_authid.oid  |OID för användare som har utfört instruktionen|
|db_id  |oid    |pg_database.oid    |OID för databasen där instruktionen kördes|
|query_id   |bigint     ||Intern hash-koden som beräknas från utdragets parsningsträd|
|event_type |text       ||Vilken typ av händelse som väntar på serverdelen|
|händelse  |text       ||Vänta händelsenamn om serverdelen väntar på|
|anrop  |Integer        ||Antal samma händelse avbildas|


### <a name="functions"></a>Functions
Query_store.qs_reset() returns void

`qs_reset` tar bort all statistik som samlas in hittills av Query Store. Den här funktionen kan endast utföras av administratörsrollen.

Query_store.staging_data_reset() returns void

`staging_data_reset` tar bort all statistik som samlas in i minnet av Query Store (d.v.s. data i minnet som inte har rensats ännu till databasen). Den här funktionen kan endast utföras av administratörsrollen.

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem
- Om en PostgreSQL-server har parametern-default_transaction_read_only, Query Store kan inte samla in data.
- Funktionen för Query Store kan avbrytas om det uppstår långa Unicode-frågor (> = 6000 byte).


## <a name="next-steps"></a>Nästa steg
- Läs mer om [scenarier där Query Store kan vara särskilt användbart](concepts-query-store-scenarios.md).
- Läs mer om [bästa praxis för att använda Query Store](concepts-query-store-best-practices.md).
