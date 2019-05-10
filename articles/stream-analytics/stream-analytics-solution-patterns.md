---
title: Azure Stream Analytics-lösningsmönster
description: Läs mer om lösningsmönster för Azure Stream Analytics.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 4f4f22628d2c2a6beb7974aa9b776a2148a3fee0
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65238069"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics-lösningsmönster

Liksom många andra tjänster i Azure passar Stream Analytics bäst med andra tjänster för att skapa en större slutpunkt till slutpunkt-lösning. Den här artikeln beskrivs enkla Azure Stream Analytics-lösningar och olika arkitektoniska mönstren. Du kan skapa på dessa mönster för att utveckla mer komplexa lösningar. De mönster som beskrivs i den här artikeln kan användas i en mängd olika scenarier. Exempel på scenariospecifika mönster är tillgängliga på [Azures lösningsarkitekturer](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-with-a-real-time-dashboard"></a>Skapa ett Stream Analytics-jobb med en realtidsinstrumentpanel

Med Azure Stream Analytics användarvänlighet, kan du snabbt använda realtidsinstrumentpaneler och aviseringar. En enkel lösning matar in händelser från Event Hubs eller IoT-hubb och [feeds Power BI-instrumentpanel med en strömmande datauppsättning](/power-bi/service-real-time-streaming). Mer information finns i den detaljerade självstudien [analysera telefonsamtalsdata med Stream Analytics och visualisera resultat i Power BI-instrumentpanel](stream-analytics-manage-job.md).

![ASA Power BI-instrumentpanel](media/stream-analytics-solution-patterns/pbidashboard.png)

Den här lösningen kan byggas på bara några minuter från Azure-portalen. Det finns ingen omfattande kodning inblandade och SQL-språket som används för att uttrycka affärslogik.

Det här mönstret för realtidsinstrumentpanel-lösningen ger kortast svarstid från händelsekällan till Power BI-instrumentpanel i en webbläsare. Azure Stream Analytics är bara Azure-tjänsten med den här inbyggda funktionen.

## <a name="use-sql-for-dashboard"></a>Använda SQL för instrumentpanelen

Power BI-instrumentpanelen erbjuder låg fördröjning, men den kan inte användas för att skapa fullständiga fledged Power BI-rapporter. Ett vanligt reporting mönster är att mata ut data till en SQL-databas först. Använd sedan Power BI: s SQL-anslutning att fråga SQL för den senaste informationen.

![ASA SQL-instrumentpanel](media/stream-analytics-solution-patterns/sqldashboard.png)

Med hjälp av SQL får database du mer flexibilitet på bekostnad av ökad latens. Den här lösningen är optimalt för jobb med svarstidskrav som är större än en sekund. Du kan använda den här metoden för att maximera Power BI-verktyg för att ytterligare segment och segmentera data för rapporter. Du får även flexibilitet för att använda andra instrumentpanel-lösningar, bland annat Tableau.

SQL är inte ett datalager med högt dataflöde och maximalt dataflöde till en SQL-databas från Azure Stream Analytics är 24 MB/s. Om händelsekällorna i din lösning ger data till en högre kostnad kan behöva du använda standardbearbetningslogiken i Stream Analytics för att minska frekvensen av utgående till SQL. Tekniker som filtrering, fönsteraggregeringar, mönstret matchar med temporala kopplingar och analysfunktioner kan användas. Frekvens av utgående till SQL kan optimeras ytterligare med hjälp av tekniker som beskrivs i [Azure Stream Analytics-utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Införliva realtidsinsikter i ditt program med event-meddelanden

Andra populäraste användning av Stream Analytics är att generera aviseringar i realtid. I den här lösningen mönstret affärslogik i Stream Analytics kan användas för att identifiera [temporal och spatial mönster](stream-analytics-geospatial-functions.md) eller [avvikelser](stream-analytics-machine-learning-anomaly-detection.md), och sedan skapa aviseringar signaler. Till skillnad från instrumentpanelen för lösningen där Stream Analytics använder Power BI som en önskad slutpunkt, men användas ett antal mellanliggande datamottagare. Dessa mottagare är Event Hubs, Service Bus och Azure Functions. Som program-builder måste du bestämma vilka datamellanlagringsplats fungerar bäst för ditt scenario.

Underordnade händelse konsument logic måste implementeras för att generera aviseringar i din befintliga business-arbetsflöde. Eftersom du kan implementera anpassade logiken i Azure Functions, Functions är det snabbaste sättet som du kan utföra den här integreringen. En självstudiekurs för att använda Azure Function som utdata för ett Stream Analytics-jobb kan hittas i [köra Azure Functions från Azure Stream Analytics-jobb](stream-analytics-with-azure-functions.md). Azure Functions har också stöd för olika typer av meddelanden, inklusive text- och e-post. Logikappen kan också användas för sådana integrering med Event Hubs mellan Stream Analytics och Logic App.

![ASA händelse meddelanden-app](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Event Hubs, å andra sidan, erbjuder den mest flexibla integration punkten. Många andra tjänster som Azure Data Explorer och Time Series Insight kan förbruka händelser från Event Hubs. Tjänster kan anslutas direkt till Event Hubs-mottagare från Azure Stream Analytics för att slutföra lösningen. Händelsehubbar är också det högsta dataflöde asynkron meddelandetjänst tillgängliga på Azure för sådana integrationsscenarier.

## <a name="dynamic-applications-and-websites"></a>Dynamiska program och webbplatser

Du kan skapa anpassade realtidsvisualiseringar, till exempel instrumentpanelen eller kartvisualisering, med hjälp av Azure Stream Analytics och Azure SignalR Service. Med SignalR webbklienter kan uppdateras och visar dynamiskt innehåll i realtid.

![ASA dynamisk app](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Lägga till information i realtid i ditt program via datalager

De flesta webbtjänster och webbprogram använder idag ett begäran/svar-mönster för att betjäna presentationslagret. Begäran/svar-mönstret är enkelt att bygga och skalas enkelt med låg svarstid med hjälp av en tillståndslösa klientdelen och skalbar Arkiv som Cosmos DB.

Hög datavolym skapar ofta flaskhalsar i prestanda i en CRUD-dator. Den [lösning mönstret händelsekällor](/azure/architecture/patterns/event-sourcing.md) används för att lösa flaskhalsar för prestandan. Den temporala mönster och insikter är också svårt och ineffektivt att extrahera från en traditionell datalager. Moderna omfattande datadrivna program ofta anta en arkitektur som bygger på dataflöde. Azure Stream Analytics som databearbetningsmotor för data i rörelse är en linchpin i arkitekturen.

![ASA event sourcing app](media/stream-analytics-solution-patterns/eventsourcingapp.png)

I den här lösningen mönstret händelser bearbetas och aggregeras till datalager som Azure Stream Analytics. Programnivån samverkar med datalager med hjälp av traditionella begäran/svar-mönstret. På grund av Stream Analytics kan bearbeta ett stort antal händelser i realtid och programmet är mycket skalbart utan att behöva samla ihop data store lagret. Datalager för store är i grunden en materialiserad vy i systemet. [Azure Stream Analytics-utdata till Azure Cosmos DB](stream-analytics-documentdb-output.md) beskriver hur Cosmos DB används som en Stream Analytics-utdata.

I verkliga program där bearbetningslogiken är komplexa och det uppstår ofta behovet av att uppgradera vissa delar av logiken oberoende av varandra, flera Stream Analytics-jobb kan sammanställas tillsammans med Event Hubs som mellanliggande händelsekoordinator.

![ASA komplexa event sourcing app](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Det här mönstret ger bättre flexibilitet och hanterbarhet i systemet. Men även om Stream Analytics garanterar engångsbearbetning, finns en liten risk att duplicerade händelser kan hamna i mellanliggande Event Hubs. Det är viktigt för att underordnade Stream Analytics-jobbet ska dedup-händelser med hjälp av logic nycklar i ett lookback fönster. Läs mer på händelseleverans [händelse Leveransgarantier](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) referens.

## <a name="use-reference-data-for-application-customization"></a>Använd referensdata för anpassning av programmet

Azure Stream Analytics-referensdatafunktionen är utformat särskilt för slutanvändaren anpassning som tröskelvärde, bearbetar regler, för avisering och [geofence-områdena](geospatial-scenarios.md). Programlagret kan acceptera parametern ändringar och lagra dem i en SQL-databas. Stream Analytics-jobb med jämna mellanrum frågar om ändringarna från databasen och gör parametrarna anpassning som är tillgängliga via en referens för data-koppling. Mer information om hur du använder referensdata för anpassning av program finns i [SQL referensdata](sql-reference-data.md) och [referens data join](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Det här mönstret kan också användas till att implementera en regelmotor där tröskelvärden för regler har definierats från referensdata. Mer information om regler finns [bearbeta konfigurerbara tröskelbaserade regler i Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![ASA referens data app](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Lägg till Machine Learning i dina insikter i realtid

Azure Stream Analytics inbyggda [Avvikelseidentifiering modellen](stream-analytics-machine-learning-anomaly-detection.md) är ett praktiskt sätt att introducera Machine Learning till programmet i realtid. För en bredare intervallet av Machine Learning behöver, se [Azure Stream Analytics integreras med Azure Machine Learning bedömnings-tjänsten](stream-analytics-machine-learning-integration-tutorial.md).

Mer avancerade användare som vill införliva online träning och bedömning i samma Stream Analytics-pipelinen finns i det här exemplet på hur gör det med [linjär regression](stream-analytics-high-frequency-trading.md).

![ASA Machine Learning-app](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Nära realtid datalager

Ett annat vanligt mönster är realtidsdata informationslager, även kallat strömmande data warehouse. Förutom händelser anländer till Event Hubs och IoT Hub från ditt program, [Azure Stream Analytics som körs på IoT Edge](stream-analytics-edge.md) kan användas för att utföra datarensning, dataminskning, datalagret och vidarebefordra behov. Stream Analytics som körs på IoT Edge kan hantera bandbredd, begränsning och problem med nätverksanslutningen i systemet. SQL-utdataadapter kan användas till utdata till SQL Data Warehouse; Maximalt dataflöde är dock begränsad till 10 MB/s.

![ASA-datalagring](media/stream-analytics-solution-patterns/datawarehousing.png)

Ett sätt att förbättra dataflödet med vissa svarstid kompromiss är att arkivera händelser till Azure Blob storage och sedan [importera dem till SQL Data Warehouse med Polybase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Du måste sätta ihop manuellt utdata från Stream Analytics till blob storage och indata från blob storage till SQL Data Warehouse med [arkivering av data efter tidsstämpel](stream-analytics-custom-path-patterns-blob-storage-output.md) och importera regelbundet.

I det här användningsmönstret används Azure Stream Analytics som en nästan i realtid ETL-motor. Dessa data anländer händelser är nyligen kontinuerligt omvandlas och lagras för användning av underordnade analytics tjänster.

![ASA dataflöden datalager](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Arkivering av data för analys i realtid

De flesta data science och analys aktiviteter sker fortfarande offline. Data kan arkiveras av Azure Stream Analytics via Azure Data Lake Store Gen2 utdata och Parquet-utdataformat. Den här funktionen tar bort friktionen för att mata in data direkt i Azure Data Lake Analytics, Azure Databricks och Azure HDInsight. Azure Stream Analytics används som en nästan i realtid ETL-motor i den här lösningen. Du kan utforska arkiverade data i Data Lake med hjälp av olika bearbetningsmotorer.

![ASA offline analytics](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Använd referensdata för berikande

Databerikande är ofta ett krav för ETL-motorer. Azure Stream Analytics stöder databerikande med [referensdata](stream-analytics-use-reference-data.md) från både SQL database och Azure Blob storage. Databerikande kan göras för data hamnar i både Azure Data Lake och SQL Data Warehouse.

![ASA offline analyser med databerikande](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Operationalisera insikter från arkiverade data

Om du kombinerar offline analytics-mönstret med nästan i realtid programmet mönster, kan du skapa en feedbackloop. Feedback-loop kan programmet automatiskt justera för att ändra mönster i data. Denna feedbackloop kan vara så enkla som ändrar tröskelvärdet för aviseringar eller så komplext som träna Machine Learning-modeller. Samma lösningsarkitekturen kan tillämpas på både ASA-jobb som körs i molnet och på IoT Edge.

![ASA insikter driftsättning](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Så här övervakar du ASA-jobben

Azure Stream Analytics-jobb kan köras 24/7 för att bearbeta inkommande händelser kontinuerligt i realtid. Dess driftgaranti är avgörande för övergripande programmets hälsa. Stream Analytics är den enda strömmande analytics-tjänsten i branschen som erbjuder en [garanterad tillgänglighet på 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), fortfarande kan tillkomma för någon nivå av stillestånd. Under åren har har Stream Analytics introducerade mått, loggar och status för jobb att visa hälsotillståndet för jobb. Alla exponeras via Azure Monitor-tjänsten och ytterligare kan exporteras till OMS. Mer information finns i [förstå Stream Analytics-jobbet övervakning och hur du övervakar frågor](stream-analytics-monitoring.md).

![ASA-övervakning](media/stream-analytics-solution-patterns/monitoring.png)

Det finns två viktiga saker att övervaka:

- [Jobbet misslyckades tillstånd](job-states.md)

    Först och främst kan behöva du se till att jobbet körs. Inga nya mått eller loggar genereras utan jobbet i körläge. Jobb kan ändra till ett felaktigt tillstånd för olika orsaker, bland annat att ha en hög SU användningsnivån (d.v.s. körs slut på resurser).

- [Vattenstämpel fördröjning mått](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Det här måttet visar hur långt bakom bearbetningen pipeline är i klocktid (sekunder). Några av fördröjningen fördelas på inneboende standardbearbetningslogiken. Övervaka ökande trend är därför mycket viktigare än övervakning det absoluta värdet. Fördröjningen stabilt bör åtgärdas genom utformningen av programmet, inte av övervakning eller aviseringar.

Vid fel, aktivitetsloggar och [diagnostikloggar](stream-analytics-job-diagnostic-logs.md) är ett bra sätt att börja söka efter fel.

## <a name="build-resilient-and-mission-critical-applications"></a>Skapa elastiska och verksamhetskritiska program

Oavsett Azure Stream Analytics SLA-garantin och noga med hur kör du slutpunkt till slutpunkt-programmet, avbrott sker. Om ditt program är verksamhetskritiska, måste förberedas för avbrott för att återställa ett smidigt sätt.

Aviseringar program, är det viktigaste är att identifiera nästa aviseringen. Du kan välja att starta om jobbet från aktuellt när du återställer, Ignorera redan aviseringar. Jobbet start gång-semantik är av den första utdata, inte den första indatatiden. Indata spolas bakåt en lämplig mängd tid att garantera första utdata vid den angivna tidpunkten är fullständig och korrekt. Du kommer inte få partiella aggregeringar och utlöser aviseringar oväntat som ett resultat.

Du kan också välja att starta utdata från vissa lång tid i förflutna. Både Event Hubs och IoT-hubbens bevarandeprinciper håller en rimlig mängd data för att tillåta bearbetning från tidigare. Nackdelen är hur snabbt du kan fånga upp till aktuell tid och börja att skapa snabbt nya aviseringar. Data förlorar dess värde snabbt med tiden, så det är viktigt att fånga upp till den aktuella tiden snabbt. Det finns två sätt att komma ikapp snabbt:

- Etablera fler resurser (SU) när du registrerar.
- Starta om efter den aktuella tiden.

Startar om från den aktuella är tiden enkelt att göra, du måste lämna ett uppehåll under bearbetning. Starta om det här sättet kan vara OK aviseringar scenarier, men kan vara problematiskt för instrumentpanelen scenarier och är en icke-starter för arkivering och informationslager-scenarier.

Etablera flera resurser kan påskynda processen, men effekten av att en bearbetning rate ökning är komplexa.

- Testa att ditt jobb är skalbar till ett större antal su: er. Inte alla frågor är skalbara. Du måste se till att din fråga är [parallelliseras](stream-analytics-parallelization.md).

- Kontrollera att det finns tillräckligt med partitioner i den överordnade Event Hubs eller IoT-hubben som du kan lägga till fler enheter (Dataflödesenheter) för att skala inkommande dataflöde. Kom ihåg att varje Event Hubs-Dataflödesenheter maxas ut med en utdata hastighet på 2 MB/s.

- Se till att du har etablerat tillräckligt med resurser i kanalmottagarna (t.ex, SQL Database, Cosmos DB), så att de inte begränsa ökning av utdata, vilket kan ibland medföra att systemet är låst.

Viktigaste är att förutse ändring av bearbetning, testa dessa scenarier innan du gå till produktion och var redo att skala bearbetning korrekt under tiden för återställning av fel.

I extrema scenariot att inkommande händelser är alla fördröjda, [det är möjligt alla försenade händelser ignoreras](stream-analytics-time-handling.md) om du har använt ett sent dessa data anländer fönster för dina jobb. Släppa händelser kan verka vara ett mystiska beteende i början; considering Stream Analytics är en motor för händelsebearbetning i realtid, förväntas den inkommande händelser som ska vara nära wall-clock-tid. Det har att ta bort händelser som bryter mot dessa villkor.

### <a name="backfilling-process"></a>Backfilling process

Som tur är kan kan tidigare datamönster för arkivering användas för att bearbeta händelserna sent ett smidigt sätt. Tanken är att arkivering jobbet bearbetar inkommande händelser i ankomsttid och arkiverar händelser i rätt tid bucket i Azure Blob eller Azure Data Lake Store med sina tidpunkt för händelsen. Det spelar ingen roll hur sent en händelse tas emot, den aldrig tas bort. Det hamnar alltid i bucketen rätt tidpunkt. Det är möjligt att Ombearbeta arkiverade händelser och återfyller resultaten till arkivet val under återställningen måste.

![ASA återfyller](media/stream-analytics-solution-patterns/backfill.png)

Återfyller processen måste göras med en offline-systemet, vilket sannolikt har en annan programmeringsmodell än Azure Stream Analytics för batchbearbetning. Detta innebär att du behöver implementera hela standardbearbetningslogiken igen.

För backfilling är det fortfarande är viktigt att åtminstone temporärt etablera mer resursen till utdata egenskaperna för att hantera högre dataflöde än i stabilt tillstånd bearbetning av behov.

|Scenarier  |Starta om nu endast från  |Starta om från senast stoppad |Starta om nu + återfyller med arkiverade händelser|
|---------|---------|---------|---------|
|**Dashboarding**   |Skapar uppehåll    |OK för korta avbrott    |För långt avbrott |
|**Aviseringar**   |Godkänd |OK för korta avbrott    |Inte nödvändigt |
|**Event sourcing app** |Godkänd |OK för korta avbrott    |För långt avbrott |
|**Datalagring**   |Förlust av data  |Godkänd |Inte nödvändigt |
|**Offline analytics**  |Förlust av data  |Godkänd |Inte nödvändigt|

## <a name="putting-it-all-together"></a>Färdigställa allt

Det är inte svårt att föreställa sig att alla lösningsmönster som nämns ovan kan kombineras tillsammans i ett komplext system för slutpunkt till slutpunkt. Kombinerade systemet kan inkludera instrumentpaneler, aviseringar, event sourcing program, datalagring och offline analysfunktioner.

Nyckeln är att utforma systemet i sammansättningsbar mönster så att varje undersystem kan byggas, testas, uppgraderas, och återställa oberoende av varandra.

## <a name="next-steps"></a>Nästa steg

Du har nu sett en mängd med hjälp av Azure Stream Analytics-lösningsmönster. Härnäst kan du gå på djupet och skapa ditt första Stream Analytics-jobb:

* [Skapa ett Stream Analytics-jobb med hjälp av Azure Portal](stream-analytics-quick-create-portal.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md).
