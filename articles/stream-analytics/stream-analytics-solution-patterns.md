---
title: Azure Stream Analytics-lösningsmönster
description: Lär dig mer om vanliga lösningsmönster för Azure Stream Analytics, till exempel instrumentpaneler, händelsemeddelanden, datalager, referensdataanrikning och övervakning.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3b95863c1ae53bd0642aec356f55aba1faf8ef09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535790"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics-lösningsmönster

Precis som många andra tjänster i Azure används Stream Analytics bäst med andra tjänster för att skapa en större på sluten lösning. I den här artikeln beskrivs enkla Azure Stream Analytics-lösningar och olika arkitektoniska mönster. Du kan bygga vidare på dessa mönster för att utveckla mer komplexa lösningar. De mönster som beskrivs i den här artikeln kan användas i en mängd olika scenarier. Exempel på scenariospecifika mönster är tillgängliga på [Azure-lösningsarkitekturer](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Skapa ett Stream Analytics-jobb för att driva instrumentpanelsupplevelsen i realtid

Med Azure Stream Analytics kan du snabbt ställa upp instrumentpaneler och aviseringar i realtid. En enkel lösning matar in händelser från Event Hubs eller IoT Hub och [matar Power BI-instrumentpanelen med en strömmande datauppsättning](/power-bi/service-real-time-streaming). Mer information finns i den detaljerade självstudien [Analysera telefonsamtalsdata med Stream Analytics och visualisera resultat i Power BI-instrumentpanelen](stream-analytics-manage-job.md).

![ASA Power BI-instrumentpanel](media/stream-analytics-solution-patterns/pbidashboard.png)

Den här lösningen kan byggas på bara några minuter från Azure-portalen. Det finns ingen omfattande kodning inblandade, och SQL-språk används för att uttrycka affärslogiken.

Det här lösningsmönstret erbjuder den lägsta svarstiden från händelsekällan till Power BI-instrumentpanelen i en webbläsare. Azure Stream Analytics är den enda Azure-tjänsten med den här inbyggda funktionen.

## <a name="use-sql-for-dashboard"></a>Använda SQL för instrumentpanelen

Power BI-instrumentpanelen erbjuder låg latens, men den kan inte användas för att producera fullfjädrade Power BI-rapporter. Ett vanligt rapporteringsmönster är att mata ut dina data till en SQL-databas först. Använd sedan Power BI:s SQL-anslutning för att fråga SQL efter de senaste data.

![ASA SQL-instrumentpanel](media/stream-analytics-solution-patterns/sqldashboard.png)

Genom att använda SQL-databasen får du större flexibilitet, men på bekostnad av en något högre latens. Den här lösningen är optimal för jobb med svarstidskrav som är större än en sekund. Med den här metoden kan du maximera Power BI-funktionerna för att ytterligare segmentera och tärna data för rapporter och mycket fler visualiseringsalternativ. Du får också flexibiliteten att använda andra instrumentpanelslösningar, till exempel Tableau.

SQL är inte ett datalager med högt dataflöde. Det maximala dataflödet till en SQL-databas från Azure Stream Analytics är för närvarande cirka 24 MB/s. Om händelsekällorna i din lösning producerar data med en högre hastighet måste du använda bearbetningslogik i Stream Analytics för att minska utdatahastigheten till SQL. Tekniker som filtrering, fönsterade aggregat, mönstermatchning med temporala kopplingar och analytiska funktioner kan användas. Utdatahastigheten till SQL kan optimeras ytterligare med hjälp av tekniker som beskrivs i [Azure Stream Analytics-utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Införliva insikter i realtid i ditt program med händelsemeddelanden

Den näst mest populära användningen av Stream Analytics är att generera aviseringar i realtid. I det här lösningsmönstret kan affärslogik i Stream Analytics användas för att identifiera [tidsmässiga och rumsliga mönster](stream-analytics-geospatial-functions.md) eller [avvikelser](stream-analytics-machine-learning-anomaly-detection.md)och sedan producera varningssignaler. Till skillnad från instrumentpanelslösningen där Stream Analytics använder Power BI som en önskad slutpunkt kan dock ett antal mellanliggande datamottagare användas. Dessa diskhoar inkluderar eventhubbar, servicebuss och Azure-funktioner. Du som programbyggare måste bestämma vilken datamottagare som fungerar bäst för ditt scenario.

Konsumentlogik för underordnade händelser måste implementeras för att generera aviseringar i ditt befintliga affärsarbetsflöde. Eftersom du kan implementera anpassad logik i Azure Functions är Azure Functions det snabbaste sättet du kan utföra den här integreringen. En självstudiekurs för att använda Azure-funktionen som utdata för ett Stream Analytics-jobb finns i [Kör Azure-funktioner från Azure Stream Analytics-jobb](stream-analytics-with-azure-functions.md). Azure Functions stöder också olika typer av meddelanden, inklusive text och e-post. Logic App kan också användas för sådan integration, med eventhubbar mellan Stream Analytics och Logic App.

![ASA-händelsemeddelandeapp](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Event Hubs, å andra sidan, erbjuder den mest flexibla integrationspunkten. Många andra tjänster, till exempel Azure Data Explorer och Time Series Insights, kan använda händelser från eventhubbar. Tjänster kan anslutas direkt till händelsehubbenmottagare från Azure Stream Analytics för att slutföra lösningen. Event Hubs är också den högsta dataflödesmeddelandemäklare som är tillgänglig på Azure för sådana integrationsscenarier.

## <a name="dynamic-applications-and-websites"></a>Dynamiska applikationer och webbplatser

Du kan skapa anpassade visualiseringar i realtid, till exempel instrumentpanel eller kartvisualisering, med Hjälp av Azure Stream Analytics och Azure SignalR-tjänst. Med SignalR kan webbklienter uppdateras och visa dynamiskt innehåll i realtid.

![ASA dynamisk app](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Införliva insikter i realtid i ditt program via datalager

De flesta webbtjänster och webbapplikationer använder idag ett förfråge-/svarsmönster för att betjäna presentationslagret. Mönstret för begäran/svar är enkelt att bygga och kan enkelt skalas med låg svarstid med hjälp av en tillståndslös klientdel och skalbara butiker, som Cosmos DB.

Hög datavolym skapar ofta flaskhalsar i prestanda i ett CRUD-baserat system. Lösningsmönstret för [händelseinköp](/azure/architecture/patterns/event-sourcing) används för att åtgärda flaskhalsarna i prestanda. Temporala mönster och insikter är också svåra och ineffektiva att extrahera från ett traditionellt datalager. Moderna datadrivna program med stora volymer använder ofta en dataflödesbaserad arkitektur. Azure Stream Analytics som beräkningsmotor för data i rörelse är en grundbult i den arkitekturen.

![ASA-app för händelseinköp](media/stream-analytics-solution-patterns/eventsourcingapp.png)

I det här lösningsmönstret bearbetas och sammanställs händelser i datalager av Azure Stream Analytics. Programlagret interagerar med datalager med hjälp av det traditionella mönstret för begäran/svar. På grund av Stream Analytics förmåga att bearbeta ett stort antal händelser i realtid är programmet mycket skalbart utan att behöva bulk upp datalagret. Datalagret är i huvudsak en materialiserad vy i systemet. [Azure Stream Analytics-utdata till Azure Cosmos DB](stream-analytics-documentdb-output.md) beskriver hur Cosmos DB används som en Stream Analytics-utdata.

I verkliga program där bearbetningslogiken är komplex och det finns behov av att uppgradera vissa delar av logiken oberoende av varandra kan flera Stream Analytics-jobb komponeras tillsammans med Event Hubs som mellanliggande händelsemäklare.

![ASA komplex händelseinköpsapp](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Det här mönstret förbättrar systemets motståndskraft och hanterbarhet. Men även om Stream Analytics garanterar exakt en gång bearbetning, finns det en liten chans att dubbletthändelser kan landa i mellanliggande Event Hubs. Det är viktigt att nedströms Stream Analytics-jobbet ska kunna skapa underordnade händelser med hjälp av logiktangenter i ett tillbakablicksfönster. Mer information om händelseleverans finns i referens [för händelseleveransgarantier.](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)

## <a name="use-reference-data-for-application-customization"></a>Använda referensdata för programanpassning

Referensdatafunktionen i Azure Stream Analytics är utformad speciellt för anpassning av slutanvändare som tröskelvärde för aviseringar, bearbetningsregler och [geofences](geospatial-scenarios.md). Programlagret kan acceptera parameterändringar och lagra dem i en SQL-databas. Stream Analytics-jobbet frågar regelbundet efter ändringar från databasen och gör anpassningsparametrarna tillgängliga via en referensdatakoppling. Mer information om hur du använder referensdata för programanpassning finns i [SQL-referensdata](sql-reference-data.md) och [referensdatakoppling](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Detta mönster kan också användas för att implementera en regelmotor där trösklarna i reglerna definieras från referensdata. Mer information om regler finns i [Processkonfigurerbara tröskelbaserade regler i Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![ASA-referensdataapp](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Lägg till maskininlärning i dina insikter i realtid

Azure Stream Analytics inbyggda [modell för avvikelseidentifiering](stream-analytics-machine-learning-anomaly-detection.md) är ett praktiskt sätt att introducera Machine Learning i realtidsprogrammet. För ett bredare utbud av machine learning-behov, se [Azure Stream Analytics integreras med Azure Machine Learnings bedömningstjänst](stream-analytics-machine-learning-integration-tutorial.md).

För avancerade användare som vill infoga onlineutbildning och poängsättning i samma Stream Analytics-pipeline läser du det här exemplet med hur det fungerar med [linjär regression](stream-analytics-high-frequency-trading.md).

![ASA-appen För maskininlärning](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Datalagring i nära realtid

Ett annat vanligt mönster är datalagring i realtid, även kallat strömmande datalager. Förutom händelser som anländer till Event Hubs och IoT Hub från ditt program kan [Azure Stream Analytics som körs på IoT Edge](stream-analytics-edge.md) användas för att uppfylla datarensning, datareducering och datalagrings- och framåtbehov. Stream Analytics körs på IoT Edge kan smidigt hantera bandbreddsbegränsning och anslutningsproblem i systemet. SQL-utdatakortet kan användas för att mata ut till SQL Data Warehouse. Det maximala dataflödet är dock begränsat till 10 MB/s.

![ASA-datalagring](media/stream-analytics-solution-patterns/datawarehousing.png)

Ett sätt att förbättra dataflödet med viss latens kompromiss är att arkivera händelserna i Azure Blob-lagring och sedan [importera dem till SQL Data Warehouse med Polybase](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Du måste manuellt sy ihop utdata från Stream Analytics till blob-lagring och indata från blob-lagring till SQL Data Warehouse genom [att arkivera data efter tidsstämpel](stream-analytics-custom-path-patterns-blob-storage-output.md) och importera regelbundet.

I det här användningsmönstret används Azure Stream Analytics som en ETL-motor i nära realtid. Nyanlända händelser omvandlas kontinuerligt och lagras för förbrukning av analystjänster i senare led.

![ASA-datalager med högt dataflöde](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Arkivering av realtidsdata för analys

De flesta datavetenskaps- och analysaktiviteter sker fortfarande offline. Data kan arkiveras av Azure Stream Analytics via Azure Data Lake Store Gen2-utdata och parquet-utdataformat. Den här funktionen tar bort friktionen för att mata data direkt till Azure Data Lake Analytics, Azure Databricks och Azure HDInsight. Azure Stream Analytics används som en ETL-motor i nära realtid i den här lösningen. Du kan utforska arkiverade data i Data Lake med hjälp av olika beräkningsmotorer.

![ASA-offlineanalys](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Använda referensdata för anrikning

Dataanrikning är ofta ett krav för ETL-motorer. Azure Stream Analytics stöder dataanrikning med [referensdata](stream-analytics-use-reference-data.md) från både SQL-databas och Azure Blob-lagring. Databerikande kan göras för datalandning i både Azure Data Lake och SQL Data Warehouse.

![ASA-offlineanalys med dataanrikning](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Operationalisera insikter från arkiverade data

Om du kombinerar offlineanalysmönstret med programmönstret nära realtid kan du skapa en feedbackloop. Feedbackloopen gör att programmet automatiskt kan justeras för att ändra mönster i data. Den här återkopplingsloopen kan vara så enkel som att ändra tröskelvärdet för aviseringar, eller lika komplex som omskolning av Machine Learning-modeller. Samma lösningsarkitektur kan tillämpas på både ASA-jobb som körs i molnet och på IoT Edge.

![ASA insikter operationalisering](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Så här övervakar du ASA-jobb

Ett Azure Stream Analytics-jobb kan köras 24/7 för att bearbeta inkommande händelser kontinuerligt i realtid. Dess drifttidsgaranti är avgörande för hälsan hos den övergripande tillämpningen. Stream Analytics är den enda tjänsten för direktuppspelningsanalys i branschen som erbjuder en [tillgänglighetsgaranti på 99,9 %,](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)men du kan fortfarande få en viss stilleståndstid. Under årens lopp har Stream Analytics infört mått, loggar och jobbtillstånd för att återspegla jobbens hälsa. Alla visas via Azure Monitor-tjänsten och kan exporteras vidare till OMS. Mer information finns i [Förstå övervakning av Stream Analytics-jobb och hur du övervakar frågor](stream-analytics-monitoring.md).

![ASA-övervakning](media/stream-analytics-solution-patterns/monitoring.png)

Det finns två viktiga saker att övervaka:

- [Jobbet misslyckades tillstånd](job-states.md)

    Först och främst måste du se till att jobbet körs. Utan jobbet i körtillståndet genereras inga nya mått eller loggar. Jobb kan ändras till ett misslyckat tillstånd av olika skäl, bland annat med en hög SU-utnyttjandenivå (dvs. på resurser).

- [Mätvärden för fördröjning av vattenstämpel](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Det här måttet visar hur långt efter din bearbetningspipeline är i väggklocka (sekunder). En del av fördröjningen tillskrivs den inneboende bearbetningslogiken. Därför är det mycket viktigare att övervaka den ökande trenden än att övervaka det absoluta värdet. Fördröjningen i stabilt tillstånd bör åtgärdas av programdesignen, inte genom övervakning eller aviseringar.

Vid fel är aktivitetsloggar och [diagnostikloggar](stream-analytics-job-diagnostic-logs.md) de bästa ställena att börja leta efter fel.

## <a name="build-resilient-and-mission-critical-applications"></a>Skapa motståndskraftiga och verksamhetskritiska program

Oavsett Azure Stream Analytics SLA-garanti och hur försiktig du kör ditt heltäckande program sker avbrott. Om ditt program är verksamhetskritiskt måste du vara förberedd för avbrott för att kunna återställas på ett smidigt sätt.

För att varna program är det viktigaste att upptäcka nästa avisering. Du kan välja att starta om jobbet från den aktuella tiden när du återställer, ignorera tidigare aviseringar. Jobbstartssemantiken är vid den första utmatningstiden, inte den första inmatningstiden. Ingången spolas bakåt en lämplig tid för att garantera att den första utgången vid den angivna tiden är fullständig och korrekt. Du får inte partiella aggregat och utlösa aviseringar oväntat som ett resultat.

Du kan också välja att starta utdata från en viss tid tidigare. Både Event Hubs och IoT Hubs lagringsprinciper innehåller en rimlig mängd data för att tillåta bearbetning från det förflutna. Avvägningen är hur snabbt du kan komma ikapp den aktuella tiden och börja generera nya aviseringar i tid. Data förlorar sitt värde snabbt med tiden, så det är viktigt att snabbt komma ikapp den aktuella tiden. Det finns två sätt att komma ikapp snabbt:

- Etablera mer resurser (SU) när du kommer ikapp.
- Starta om från aktuell tid.

Omstart från nuvarande tiden är enkel att göra, med avvägningen att lämna en lucka under bearbetningen. Omstart på det här sättet kan vara OK för att varna scenarier, men kan vara problematiskt för instrumentpanelsscenarier och är en icke-starter för arkivering och datalagring scenarier.

Etablering av mer resurser kan påskynda processen, men effekten av att ha en ökning av bearbetningshastigheten är komplex.

- Testa att jobbet är skalbart för ett större antal SUs.Test that your job is scalable to a larger number of SUs. Alla frågor är inte skalbara. Du måste se till att frågan [är parallelliserad](stream-analytics-parallelization.md).

- Kontrollera att det finns tillräckligt med partitioner i uppströms händelsehubbar eller IoT Hub som du kan lägga till fler dataflödesenheter (TUs) för att skala indataflödet. Kom ihåg att varje Event Hubs TU maxar med en utmatningshastighet på 2 MB/s.

- Se till att du har etablerat tillräckligt med resurser i utdatamottagare (dvs. SQL Database, Cosmos DB), så att de inte begränsar ökningen av produktionen, vilket ibland kan orsaka att systemet låser sig.

Det viktigaste är att förutse ändringen av bearbetningshastigheten, testa dessa scenarier innan du går in i produktion och vara redo att skala bearbetningen korrekt under återställningstiden för fel.

I det extrema scenariot att inkommande händelser är alla försenade, [är det möjligt att alla försenade händelser tas bort](stream-analytics-time-handling.md) om du har tillämpat ett sent ankomstfönster till ditt jobb. Att släppa händelserna kan tyckas vara ett mystiskt beteende i början; Med tanke på att Stream Analytics är en processormotor i realtid förväntar den sig dock att inkommande händelser ligger nära väggklockans tid. Det måste släppa händelser som bryter mot dessa begränsningar.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda Arkitekturer eller Backfill process

Lyckligtvis kan det tidigare dataarkiveringsmönstret användas för att bearbeta dessa sena händelser graciöst. Tanken är att arkiveringsjobbet bearbetar inkommande händelser i ankomsttid och arkiverar händelser till rätt tidshink i Azure Blob eller Azure Data Lake Store med sin händelsetid. Det spelar ingen roll hur sent en händelse anländer, kommer det aldrig att släppas. Det kommer alltid att landa i rätt tid hink. Under återställningen är det möjligt att bearbeta om de arkiverade händelserna och fylla i resultaten till det aktuella arkivet. Detta liknar hur lambda mönster genomförs.

![ASA-återfyllning](media/stream-analytics-solution-patterns/backfill.png)

Återfyllningsprocessen måste göras med ett offline batchbearbetningssystem, som troligen har en annan programmeringsmodell än Azure Stream Analytics. Det innebär att du måste implementera hela bearbetningslogiken igen.

För återfyllning är det fortfarande viktigt att åtminstone tillfälligt etablera mer resurs till utdatamottagare för att hantera högre dataflöde än behoven för bearbetning av steady state.

|Scenarier  |Starta om från nu bara  |Starta om från senaste stoppad tid |Starta om från och med nu + återfyllning med arkiverade händelser|
|---------|---------|---------|---------|
|**Instrumentpanel**   |Skapar gap    |OK för korta avbrott    |Använd för långa avbrott |
|**Aviseringar**   |Acceptabelt |OK för korta avbrott    |Inte nödvändigt |
|**Appen För händelseinköp** |Acceptabelt |OK för korta avbrott    |Använd för långa avbrott |
|**Datalagring**   |Förlust av data  |Acceptabelt |Inte nödvändigt |
|**Offlineanalys**  |Förlust av data  |Acceptabelt |Inte nödvändigt|

## <a name="putting-it-all-together"></a>Färdigställa allt

Det är inte svårt att föreställa sig att alla lösningsmönster som nämns ovan kan kombineras i ett komplext end-to-end-system. Det kombinerade systemet kan innehålla instrumentpaneler, aviseringar, händelseinköpsprogram, datalagring och offlineanalysfunktioner.

Nyckeln är att utforma ditt system i komposterbara mönster, så att varje delsystem kan byggas, testas, uppgraderas och återställas oberoende av varandra.

## <a name="next-steps"></a>Nästa steg

Du har nu sett en mängd olika lösningsmönster med Hjälp av Azure Stream Analytics. Härnäst kan du gå på djupet och skapa ditt första Stream Analytics-jobb:

* [Skapa ett Stream Analytics-jobb med hjälp av Azure Portal](stream-analytics-quick-create-portal.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md).
