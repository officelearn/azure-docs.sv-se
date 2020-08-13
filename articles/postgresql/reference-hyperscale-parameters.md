---
title: Server parametrar – storskalig (citus)-Azure Database for PostgreSQL
description: Parametrar i den storskaliga (citus) SQL API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136966"
---
# <a name="server-parameters"></a>Serverparametrar

Det finns olika Server parametrar som påverkar funktions skalans beteende (citus), båda parametrarna från standard-PostgreSQL och parametrar som är speciella för citus (). Om du vill veta mer om konfigurations parametrar för PostgreSQL kan du gå till avsnittet [Kör tids konfiguration](http://www.postgresql.org/docs/current/static/runtime-config.html) i postgresql-dokumentationen.

Resten av den här referensen syftar på att diskutera citus-speciella konfigurations parametrar. Dessa parametrar kan anges i Azure Portal under Inställningar för **Work Node-parametrar** under **Inställningar** för en citus-Server (för en storskalig).

> [!NOTE]
>
> De storskaliga Server grupperna som kör äldre versioner av citus-motorn kanske inte erbjuder alla parametrar som anges nedan.

## <a name="general-configuration"></a>Allmän konfiguration

### <a name="citususe_secondary_nodes-enum"></a>citus. Använd \_ sekundär \_ noder (Enum)

Anger den princip som ska användas när du väljer noder för URVALs frågor. Om den är inställd på Always, kommer Planner bara att fråga noder som har marker ATS som sekundära noderole i [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

De värden som stöds för den här uppräkningen är:

-   **aldrig:** (standard) alla läsningar sker på primära noder.
-   **Always:** Läser in kör mot sekundära noder i stället, och INSERT/Update-instruktioner är inaktiverade.

### <a name="cituscluster_name-text"></a>citus. Cluster \_ -namn (text)

Informerar Coordinator-nodens planerare som kluster IT samordnar. När kluster \_ namnet har angetts kommer Planner att fråga arbetsnoder i det klustret separat.

### <a name="citusenable_version_checks-boolean"></a>citus. Aktivera \_ versions \_ kontroller (boolesk)

För uppgradering av citus-version (för storskalig) krävs omstart av servern (för att hämta det nya delade biblioteket) följt av kommandot ALTER EXTENSION UPDATE.  Det gick inte att köra båda stegen om du skulle kunna orsaka fel eller krascher.
Storskalig (citus) validerar därför versionen av koden och tilläggets matchning, och fel uppstår om de inte är det \' .

Standardvärdet är true och det gäller koordinatorn. I sällsynta fall kan komplexa uppgraderings processer kräva att den här parametern anges till false, vilket innebär att kontrollen inaktive ras.

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus. log \_ distribuerad \_ deadlock \_ upptäcktes (Boolean)

Om distribution av distribuerad deadlock ska loggas i Server loggen. Standardvärdet är false.

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus. distributions faktor för distribuerad \_ deadlock \_ \_ (flytt ALS)

Anger vänte tiden innan ett distribuerat död läge kontrol leras. I synnerhet är vänte tiden detta värde multiplicerat med PostgreSQL \' s timeout-inställning för [död läge \_ ](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) . Standardvärdet är `2`. Värdet `-1` inaktiverar identifiering av distribuerat död läge.

### <a name="citusnode_connection_timeout-integer"></a>\_ \_ tids gräns för citus. Node-anslutning (heltal)

`citus.node_connection_timeout`Guc anger maximal varaktighet (i millisekunder) för att vänta på anslutnings etablering. Citus (storskalig) aktiverar ett fel om tids gränsen förflutit innan minst en arbets anslutning upprättas. Den här GUC påverkar anslutningar från koordinatorn till arbetare och anställda till varandra.

-   Standard: fem sekunder
-   Minst: 10 millisekunder
-   Max: en timme

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>Fråga efter statistik

### <a name="citusstat_statements_purge_interval-integer"></a>rensnings intervall för citus. stat \_ \_ -satser \_ (heltal)

Anger med vilken frekvens som underhålls demon tar bort poster från [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) som inte matchar i `pg_stat_statements` . Det här konfiguration svärdet anger tidsintervallet mellan rensningar i sekunder, med ett standardvärde på 10. Värdet 0 inaktiverar rensningarna.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Den här parametern är effektiv i koordinatorn och kan ändras vid körning.

## <a name="data-loading"></a>Data inläsning

### <a name="citusmulti_shard_commit_protocol-enum"></a>citus. \_ Shard (Multi \_ commit \_ Protocol)

Anger vilket protokoll som ska användas vid kopiering av en hash-distribuerad tabell. På varje enskild Shard placering utförs KOPIAn i ett transaktions block för att säkerställa att inga data matas in om ett fel inträffar under KOPIERINGen. Det finns dock ett visst fel fall där KOPIERINGen lyckas på alla platser, men ett (maskin varu fel) inträffar innan alla transaktioner har genomförts. Den här parametern kan användas för att förhindra data förlust i detta fall genom att välja mellan följande commit-protokoll:

-   **2PC:** (standard) de transaktioner där kopiering utförs på Shard-placeringarna förbereds först med postgresql \' s genomförande i [två faser](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) och sedan allokeras. Misslyckade incheckningar kan återställas manuellt eller avbrytas med genomför för beredd eller återställning.
    När du använder 2PC bör [Max för \_ beredda \_ transaktioner](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) ökas för alla arbetare, vanligt vis till samma värde som maximalt antal \_ anslutningar.
-   **1PC:** De transaktioner där KOPIERINGen utförs på Shard-placeringarna allokeras i en enda runda. Data kan gå förlorade om ett genomförande Miss lyckas när KOPIERINGen lyckas på alla platser (sällsynt).

### <a name="citusshard_replication_factor-integer"></a>citus. Shard \_ \_ (integer)

Anger Shards för det antal noder där Shards ska placeras och standardvärdet är 1. Den här parametern kan anges i körnings tid och är effektiv i koordinatorn. Det idealiska värdet för den här parametern beror på klustrets storlek och frekvensen av nodfel.  Du kanske till exempel vill öka den här replikeringslänken om du kör stora kluster och studerar nodfel oftare.

### <a name="citusshard_count-integer"></a>antal citus. Shard \_ (heltal)

Anger antalet Shard för hash-partitionerade tabeller och standardvärdet 32.  Det här värdet används av [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF när du skapar hash-partitionerade tabeller. Den här parametern kan anges i körnings tid och är effektiv i koordinatorn.

### <a name="citusshard_max_size-integer"></a>citus. Shard \_ Max \_ storlek (heltal)

Anger den maximala storlek som en Shard ska växa till innan den delas och standardvärdet är 1 GB. När käll filens \' storlek (som används för mellanlagring) för en Shard överstiger detta konfigurations värde, säkerställer databasen att en ny Shard skapas. Den här parametern kan anges i körnings tid och är effektiv i koordinatorn.

## <a name="planner-configuration"></a>Planner-konfiguration

### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus. Limit- \_ sats \_ rad \_ hämtnings \_ antal (heltal)

Anger antalet rader som ska hämtas per uppgift för optimering av begränsnings satser.
I vissa fall väljer du frågor med begränsnings klausuler kan behöva hämta alla rader från varje aktivitet för att generera resultat. I dessa fall, och där en uppskattning skulle ge meningsfulla resultat, anger det här konfiguration svärdet antalet rader som ska hämtas från varje Shard. Limit-uppskattningar är inaktiverade som standard och den här parametern har värdet-1. Det här värdet kan ställas in i körnings tid och tillämpas på koordinatorn.

### <a name="cituscount_distinct_error_rate-floating-point"></a>citus. räkna \_ distinkt \_ fel \_ frekvens (flytt ALS)

Storskalig (citus) kan beräkna antalet (distinkt) ungefärlig med hjälp av postgresql-HLL-tillägget. Den här konfigurations posten anger önskad fel frekvens vid beräkning av antal (distinkta). 0,0, som är standard, inaktive ras approximationer för Count (DISTINCT). och 1,0 ger inga garantier om resultatet av resultaten. Vi rekommenderar att du anger den här parametern till 0,005 för bästa möjliga resultat. Det här värdet kan ställas in i körnings tid och tillämpas på koordinatorn.

### <a name="citustask_assignment_policy-enum"></a>citus. princip för aktivitets \_ tilldelning \_ (Enum)

> [!NOTE]
> Den här GUC gäller endast när [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) är större än en eller för frågor mot [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Anger den princip som ska användas när aktiviteter tilldelas till arbetare. Koordinatorn tilldelar uppgifter till arbetare baserat på Shard-platser. Detta konfigurations värde anger den princip som ska användas för att utföra dessa uppgifter.
Det finns för närvarande tre möjliga principer för aktivitets tilldelning som kan användas.

-   **girig:** Girig-principen är standard och syftar till att fördela aktiviteter mellan arbetare.
-   **resursallokering:** Principen för resursallokering tilldelar uppgifter till arbetare i en växel för resursallokering mellan olika repliker. Den här principen möjliggör bättre kluster användning när antalet Shard för en tabell är lågt jämfört med antalet arbetare.
-   **första replik:** Principen för den första repliken tilldelar uppgifter utifrån insättnings ordningen för placeringar (repliker) för Shards. Med andra ord tilldelas fragment frågan för en Shard till den arbets tagare som har den första repliken av Shard.
    Med den här metoden kan du ha starka garantier om vilka Shards som kommer att användas på vilka noder (det vill säga starkare minnes placering garantier).

Den här parametern kan anges i körnings tid och är effektiv i koordinatorn.

## <a name="intermediate-data-transfer"></a>Mellanliggande Dataöverföring

### <a name="citusbinary_worker_copy_format-boolean"></a>formatet citus. Binary \_ Worker \_ copy \_ (boolesk)

Använd formatet binär kopia för att överföra mellanliggande data mellan arbetare.
Vid stor koppling av tabeller kan storskalig (citus) behöva partitionera om och blanda data mellan olika arbetare. Som standard överförs dessa data i text format. Om du aktiverar den här parametern instrueras databasen att använda PostgreSQL-formatet för binär serialisering för att överföra dessa data. Den här parametern är effektiv för arbets tagarna och måste ändras i filen postgresql. conf. När du har redigerat konfigurations filen kan användarna skicka en SIGHUP-signal eller starta om servern för att ändringen ska börja gälla.

### <a name="citusbinary_master_copy_format-boolean"></a>citus. binär \_ huvud \_ kopierings \_ format (Boolean)

Använd formatet binär kopia för att överföra data mellan koordinatorn och arbets tagarna. När du kör distribuerade frågor överför arbets tagarna sina mellanliggande resultat till koordinatorn för slutlig agg regering. Som standard överförs dessa data i text format. Om du aktiverar den här parametern instrueras databasen att använda PostgreSQL-formatet för binär serialisering för att överföra dessa data.
Den här parametern kan anges vid körning och är giltig för koordinatorn.

### <a name="citusmax_intermediate_result_size-integer"></a>citus. Max \_ storlek för mellanliggande \_ resultat \_ (heltal)

Den maximala storleken i KB av mellanliggande resultat för CTE som inte kan flyttas ned till arbetsnoder för körning och för komplexa under frågor. Standardvärdet är 1 GB och värdet-1 innebär ingen gräns.
Frågor som överskrider gränsen avbryts och genererar ett fel meddelande.

## <a name="ddl"></a>-

### <a name="citusenable_ddl_propagation-boolean"></a>citus. Aktivera \_ DDL \_ -spridning (boolesk)

Anger om DDL-ändringar ska spridas automatiskt från koordinatorn till alla arbetare. Standardvärdet är True. Eftersom vissa schema ändringar kräver ett åtkomst exklusivt lås i tabeller, och eftersom den automatiska spridningen gäller för alla arbetare i turordning, kan det göra ett citus-kluster (för hög skalning) tillfälligt mindre. Du kan välja att inaktivera den här inställningen och sprida ändringar manuellt.

## <a name="executor-configuration"></a>Utförar-konfiguration

### <a name="general"></a>Allmänt

#### <a name="citusall_modifications_commutative"></a>citus. alla \_ ändringar \_ commutative

Citus (storskalig) framtvingar commutativity-regler och hämtar lämpliga lås för ändrings åtgärder för att garantera att beteendet är korrekt. Till exempel förutsätter vi att en INSERT-instruktion följer en annan INSERT-instruktion, men inte med en UPDATE-eller DELETE-instruktion. På samma sätt förutsätter vi att en UPDATE-eller DELETE-instruktion inte återkommer till en annan UPDATE-eller DELETE-instruktion. Den här försiktighets åtgärden innebär att uppdateringar och borttagningar kräver storskalig skalning (citus) för att få starkare lås.

Om du har UPPDATERINGs instruktioner som är commutative med dina infogningar eller andra uppdateringar kan du minska dessa commutativity-antaganden genom att ange den här parametern till true. När den här parametern har angetts till True betraktas alla kommandon som commutative och gör anspråk på ett delat lås, vilket kan förbättra det totala data flödet. Den här parametern kan anges vid körning och är giltig för koordinatorn.

#### <a name="citusremote_task_check_interval-integer"></a>citus. \_ kontroll intervall för fjärraktivitet \_ \_ (heltal)

Anger med vilken frekvens citus ska kontrol lera status för jobb som hanteras av aktivitets spåraren utförar. Standardvärdet är 10 MS. Koordinatorn tilldelar uppgifter till arbetare och kontrollerar sedan regelbundet med dem om varje aktivitet \' s förlopp. Detta konfigurations värde anger tidsintervallet mellan två efterföljande kontroller. Den här parametern är effektiv i koordinatorn och kan ställas in vid körning.

#### <a name="citustask_executor_type-enum"></a>citus. Task \_ utförar \_ -typ (Enum)

Citus (storskalig) har tre utförar-typer för att köra distribuerade URVALs frågor.  Du kan välja önskad utförar genom att ange den här konfigurations parametern. De godkända värdena för den här parametern är:

-   **adaptiv:** Standardvärdet. Det är optimalt för snabba svar på frågor som omfattar agg regeringar och samplacerade kopplingar över flera Shards.
-   **uppgifts Spårare:** Utförar för aktivitetens spår passar bra för långvariga, komplexa frågor som kräver blandning av data över arbetsnoder och effektiv resurs hantering.
-   **real tid:** (inaktuell) fungerar på ett liknande sätt som den anpassade utförar, men det är mindre flexibelt och kan orsaka mer anslutnings belastning på arbetsnoder.

Den här parametern kan anges i körnings tid och är effektiv i koordinatorn.

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus. \_ fråga efter \_ loggnings nivå för flera aktiviteter \_ \_ {#multi_task_logging}

Anger en loggnings nivå för alla frågor som genererar fler än en aktivitet (det vill säga fler än en Shard). Loggning är användbart under en programmigrering för flera innehavare, eftersom du kan välja att fel eller varna för sådana frågor, för att hitta dem och lägga till ett klient \_ -ID-filter till dem. Den här parametern kan anges vid körning och är giltig för koordinatorn. Standardvärdet för den här parametern är \' inaktiverat \' .

De värden som stöds för den här uppräkningen är:

-   **av:** Inaktivera loggning av frågor som genererar flera aktiviteter (det vill säga flera Shards)
-   **fel sökning:** Logs-instruktioner vid allvarlighets grad för fel sökning.
-   **logg:** Loggar-instruktion på allvarlighets nivå för logg. Logg raden kommer att innehålla SQL-frågan som kördes.
-   **meddelande:** Loggar instruktion vid MEDDELANDEts allvarlighets grad.
-   **Varning:** Loggar uttryck på VARNINGs nivå.
-   **fel:** Loggar-instruktion vid allvarlighets grad för fel.

Det kan vara användbart att använda `error` under utvecklings testningen och en lägre loggnivå som `log` under den faktiska produktions distributionen.
Om du väljer `log` så visas frågor med flera aktiviteter i databas loggarna med frågan som visas efter \" instruktionen.\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

#### <a name="citusenable_repartition_joins-boolean"></a>citus. Aktivera \_ ompartitionering \_ kopplingar (boolesk)

Normalt kommer försök att utföra ompartitionering av kopplingar med den anpassade utförar att Miss lyckas med ett fel meddelande.  Om du däremot anger `citus.enable_repartition_joins` true kan citus (Scale Scale) tillfälligt växla till utförar i aktivitets spåraren för att utföra kopplingen.  Standardvärdet är false.

### <a name="task-tracker-executor-configuration"></a>Konfiguration av aktivitets spår utförar

#### <a name="citustask_tracker_delay-integer"></a>citus. fördröjning för uppgifts \_ spår \_ (heltal)

Den här parametern anger inströms tiden för aktivitets Spårare mellan aktivitets hanteringen avrundas och är som standard 200 MS. Processen för uppgifts Spårare aktive ras regelbundet, går igenom alla uppgifter som har tilldelats den och schemalägger och kör dessa uppgifter.  Sedan inaktive aktivitets Spårare för en tids period innan du går vidare över aktiviteterna igen.
Detta konfigurations värde bestämmer längden på den vilande perioden. Den här parametern är effektiv för arbets tagarna och måste ändras i filen postgresql. conf. När du har redigerat konfigurations filen kan användarna skicka en SIGHUP-signal eller starta om servern för att ändringen ska börja gälla.

Den här parametern kan minskas för att trimma fördröjningen som orsakas av aktivitets spårets utförar genom att minska tids skillnaden mellan hanterings avrundning.
Att minska fördröjningen är användbart i fall när Shard-frågorna är korta och uppdaterar sedan sin status regelbundet.

#### <a name="citusmax_assign_task_batch_size-integer"></a>citus. max. \_ tilldela \_ uppgiftens \_ batchstorlek \_ (heltal)

Utförar för uppgifts spårare i koordinatorn tilldelar synkrona uppgifter i batchar till daemonen på arbets tagarna. Den här parametern anger det maximala antalet uppgifter som ska tilldelas i en enskild batch. Att välja en större batchstorlek möjliggör snabbare aktivitets tilldelning. Men om antalet arbetare är stort kan det ta längre tid för alla arbetare att hämta uppgifter.  Den här parametern kan anges vid körning och är giltig för koordinatorn.

#### <a name="citusmax_running_tasks_per_node-integer"></a>citus. Max \_ aktiviteter som körs \_ \_ per \_ nod (heltal)

Aktivitets spåraren bearbetar scheman och kör de uppgifter som har tilldelats dem efter behov. Det här konfiguration svärdet anger det maximala antalet aktiviteter som ska köras samtidigt på en nod och standardvärdet är 8.

Gränsen garanterar att du inte \' har många aktiviteter som når disk samtidigt, och hjälper till att undvika disk-I/O-konkurrens. Om dina frågor skickas från minnes-eller SSD kan du öka högsta antalet \_ aktiva \_ aktiviteter \_ per \_ nod utan att det är mycket viktigt.

#### <a name="cituspartition_buffer_size-integer"></a>citus. partitionens \_ Buffertstorlek \_ (heltal)

Anger buffertstorleken som ska användas för partition åtgärder och standardvärdet är 8 MB.
Storskalig (citus) gör att tabell data kan partitioneras om i flera filer när två stora tabeller kopplas. När den här bufferten fylls, töms ompartitionerade data i filer på disk.  Den här konfigurations posten kan ställas in i körnings tid och gäller för arbets tagarna.

### <a name="explain-output"></a>Förklara utdata

#### <a name="citusexplain_all_tasks-boolean"></a>citus. förklara \_ alla \_ uppgifter (booleska)

Som standard visar disscale (citus) utdata från en enskild, valfri aktivitet när du kör en [förklaring](http://www.postgresql.org/docs/current/static/sql-explain.html) i en distribuerad fråga. I de flesta fall liknar förklaringen resultatet av olika uppgifter. Ibland kommer vissa av uppgifterna att planeras annorlunda eller ha mycket högre körnings tider. I dessa fall kan det vara praktiskt att aktivera den här parametern, efter vilken förklarar resultatet kommer att innehålla alla uppgifter. Att förklara alla aktiviteter kan göra att förklaringen tar längre tid.

## <a name="next-steps"></a>Nästa steg

* En annan typ av konfiguration, förutom Server parametrar, är resurs [konfigurations alternativen](concepts-hyperscale-configuration-options.md) i en citus-Server (storskalig).
* Den underliggande data basen för PostgreSQL har också [konfigurations parametrar](http://www.postgresql.org/docs/current/static/runtime-config.html).
