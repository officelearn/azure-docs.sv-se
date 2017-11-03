---
title: "Ingående till förutsäga vehicle hälsotillstånd och andra vanor - Azure | Microsoft Docs"
description: "Använda funktionerna i Cortana Intelligence och få insikter om i realtid och förutsägbara på vehicle hälsa och köra vanor."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: 4050fdc2056df395bbcc37e3783f61eebd90f80a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Fordonstelemetrianalys, lösning, playbook: djupdykning i lösningen
Detta **menyn** länkar till avsnitt i den här playbook: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Det här avsnittet flyttar ned till var och en av de stegen som beskrivs i lösningsarkitektur med instruktioner och pekare för anpassning. 

## <a name="data-sources"></a>Datakällor
Lösningen använder två olika datakällor:

* **simulerade vehicle signaler och diagnostik dataset** och 
* **vehicle katalog**

Vehicle telematik simulator ingår som en del av den här lösningen. Den genererar diagnostisk information och signalerar till motsvarande tillstånd för programuppdatering och intresseväckande mönstret vid en viss tidpunkt. Klicka på [Vehicle telematik simulatorn](http://go.microsoft.com/fwlink/?LinkId=717075) att hämta den **Vehicle telematik Simulator lösning i Visual Studio** anpassningar baserat på dina krav. Vehicle katalogen innehåller en referens datamängd med Registreringsnumret för Modellmappning.

![Vehicle telematik simulator](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)

*Bild 1 – Vehicle telematik Simulator*

Det här är en JSON-formaterad datamängd som innehåller följande schema.

| Kolumn | Beskrivning | Värden |
| --- | --- | --- |
| VIN |Slumpmässigt genererat Vehicle ID-nummer |Detta hämtas från en övergripande lista med 10 000 slumpmässigt genererat vehicle ID-nummer. |
| Utanför temperatur |Utanför temperaturen där vehicle kör |Slumptal mellan 0-100 |
| Motorn temperatur |Motorn temperatur för programuppdatering |Slumpmässigt genererat tal mellan 0 och 500 |
| Hastighet |Motorns hastighet som som aktiverar för programuppdatering |Slumptal mellan 0-100 |
| Bränsle |Bränslenivå för för programuppdatering |Slumptal mellan 0-100 (anger bränsle nivån procent) |
| EngineOil |Motorn olja andelen för programuppdatering |Slumptal mellan 0-100 (anger motorn olja nivån procent) |
| Däck hög belastning |Däck tryck för programuppdatering |Slumpmässigt tal mellan 0-50 (anger däck trycket nivån procent) |
| Mätarställning |Mätarställning för för programuppdatering |Slumpmässigt genererat nummer från 0 200000 |
| Accelerator_pedal_position |Accelerator cyklar placering för programuppdatering |Slumptal mellan 0-100 (anger accelerator nivån procent) |
| Parking_brake_status |Anger om vehicle parkerade eller inte |True eller False |
| Headlamp_status |Anger om strålkastaren är på eller inte |True eller False |
| Brake_pedal_status |Anger om bromspedal är nedtryckt eller inte |True eller False |
| Transmission_gear_position |Överföring växeln placering för programuppdatering |Tillstånd: första, andra, tredje, fjärde, femte, sjätte, sjunde, åttonde |
| Ignition_status |Anger om är igång eller Stoppad |True eller False |
| Windshield_wiper_status |Anger om vindrutan vindrutetorkare är aktiverat eller inte |True eller False |
| ABS |Anger om ABS används eller inte |True eller False |
| tidsstämpel |Tidsstämpel när datapunkten som har skapats |Date |
| Ort |Platsen för programuppdatering |4 orter i den här lösningen: Bellevue, Redmond, Sammamish, Seattle |

Referensdatauppsättningen vehicle modellen innehåller VIN till modellen mappningen. 

| VIN | Modellen |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Sedan |
| 8J0U8XCPRGW4Z3NQE |Hybrid |
| WORG68Z2PLTNZDBI7 |Family sedan |
| JTHMYHQTEPP4WBMRN |Sedan |
| W9FTHG27LZN1YWO0Y |Hybrid |
| MHTP9N792PHK08WJM |Family sedan |
| EI4QXI2AXVQQING4I |Sedan |
| 5KKR2VB4WHQH97PF8 |Hybrid |
| W9NSZ423XZHAONYXB |Family sedan |
| 26WJSGHX4MA5ROHNL |Kan konverteras |
| GHLUB6ONKMOSI7E77 |Station vagn |
| 9C2RHVRVLMEJDBXLP |Bil |
| BRNHVMZOUJ6EOCP32 |Liten Stadsjeep |
| VCYVW0WUZNBTM594J |Sport bil |
| HNVCE6YFZSA5M82NY |Medelhög Stadsjeep |
| 4R30FOR7NUOBL05GJ |Station vagn |
| WYNIIY42VKV6OQS1J |Stora Stadsjeep |
| 8Y5QKG27QET1RBK7I |Stora Stadsjeep |
| DF6OX2WSRA6511BVG |Coupe |
| Z2EOZWZBXAEW3E60T |Sedan |
| M4TV6IEALD5QDS3IR |Hybrid |
| VHRA1Y2TGTA84F00H |Family sedan |
| R0JAUHT1L1R3BIKI0 |Sedan |
| 9230C202Z60XX84AU |Hybrid |
| T8DNDN5UDCWL7M72H |Family sedan |
| 4WPYRUZII5YV7YA42 |Sedan |
| D1ZVY26UV2BFGHZNO |Hybrid |
| XUF99EW9OIQOMV7Q7 |Family sedan |
| 8OMCL3LGI7XNCC21U |Kan konverteras |
| ……. | |

### <a name="references"></a>Referenser
[Vehicle telematik Simulator Visual Studio-lösning](http://go.microsoft.com/fwlink/?LinkId=717075) 

[Azure Event Hub](https://azure.microsoft.com/services/event-hubs/)

[Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)

## <a name="ingestion"></a>Införandet
Kombinationer av Händelsehubbar i Azure Stream Analytics och Data Factory utnyttjas för att mata in vehicle signaler, diagnostiska händelser och realtid och batch-analytics. Alla dessa komponenter skapas och konfigureras som en del av distributionen av lösningen. 

### <a name="real-time-analysis"></a>Analys i realtid
Händelser som genererats av Vehicle telematik Simulator publiceras till Händelsehubben med hjälp av Event Hub SDK. Stream Analytics-jobbet en dessa händelser från Event Hub och bearbetar data i realtid för att analysera vehicle hälsa. 

![Event hub instrumentpanelen](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

*Bild 4 - Event Hub-instrumentpanelen*

![Strömma Analytics-jobbet databearbetning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 

*Bild 5 - Stream Analytics-jobbet bearbetning av data*

Stream Analytics-jobb.

* en data från Event Hub 
* Utför en koppling med referensdata att mappa vehicle VIN till motsvarande modellen 
* sparar dem i Azure blob-lagring för omfattande batch analytics. 

Följande Stream Analytics-fråga används för att spara data i Azure blob storage. 

![Stream Analytics-jobbet frågan för datapåfyllning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 

*Bild 6 - Stream Analytics-jobbet frågan för datapåfyllning*

### <a name="batch-analysis"></a>Batchanalys
Vi också genererar en ytterligare volym av simulerade vehicle signaler och diagnostik datamängden för bättre batch analytics. Detta krävs för att säkerställa en god representativt datavolym för batch-bearbetning. För detta ändamål använder en pipeline med namnet ”PrepareSampleDataPipeline” i Azure Data Factory-arbetsflödet för att generera ett år kan du se simulerade vehicle signaler och diagnostik dataset. Klicka på [Data Factory anpassad aktivitet](http://go.microsoft.com/fwlink/?LinkId=717077) att hämta Data Factory anpassad DotNet aktivitet Visual Studio-lösning för anpassningar baserat på dina krav. 

![Förbereda exempeldata för batchbearbetning arbetsflöde](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 

*Figur 7 – förbereda exempeldata för arbetsflöde för batch-bearbetning*

Pipelinen består av en anpassad ADF .net aktivitet, visas här:

![PrepareSampleDataPipeline aktivitet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

*Figur 8 - PrepareSampleDataPipeline*

När pipelinen körs korrekt och ”RawCarEventsTable” dataset markeras ”klar” års värt simulerade vehicle signaler och diagnostiska som data produceras. Du kan se på följande mapp och fil som skapats i ditt lagringskonto i ”connectedcar”-behållaren:

![PrepareSampleDataPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

*Bild 9 - PrepareSampleDataPipeline utdata*

### <a name="references"></a>Referenser
[Azure Event Hub-SDK för inhämtning av dataströmmar](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Azure Data Factory data movement funktioner](../../data-factory/v1/data-factory-data-movement-activities.md)
[DotNet-aktivitet för Azure Data Factory](../../data-factory/v1/data-factory-use-custom-activities.md)

[Azure Data Factory DotNet aktivitet visual studio-lösning för att förbereda exempeldata](http://go.microsoft.com/fwlink/?LinkId=717077) 

## <a name="partition-the-dataset"></a>Partitionen datauppsättningen
Rådata halvstrukturerade vehicle signaler och diagnostik datamängd partitioneras i förberedelsen av data till formatet år/månad. Den här partitionering befordrar effektivare fråga och skalbar långsiktig lagring genom att aktivera fel-över från en blob-konto till en annan när det första kontot fylls. 

>[!NOTE] 
>Det här steget i lösningen gäller endast för batch-bearbetning.

Indata och utdata hantering av data:

* Den **utdata** (märkta *PartitionedCarEventsTable*) ska bevaras under en längre tidsperiod som grundläggande / ”rawest” form av data i kundens ”Data Lake”. 
* Den **indata** till den här pipelinen skulle normalt ignoreras som utdata har fullständig återgivning inkommande - lagras bara (partitionerad) bättre för senare användning.

![Partitionen bil händelser arbetsflöde](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)

*Figur 10 – Partition bil händelser arbetsflöde*

Rådata är partitionerad med hjälp av en HDInsight Hive-aktivitet i ”PartitionCarEventsPipeline”. Exempeldata genereras i steg 1 för ett år har partitionerats med år/månad. Partitionerna som används för att generera vehicle signaler och diagnostikdata för varje månad (totalt 12 partitioner) för ett år. 

![PartitionCarEventsPipeline aktivitet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)

*Figur 11 - PartitionCarEventsPipeline*

***PartitionConnectedCarEvents Hive-skript***

Följande Hive-skript, med namnet ”partitioncarevents.hql” används för partitionering och finns i mappen ”\demo\src\connectedcar\scripts” i den hämtade zip. 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

När pipelinen har körts, finns följande partitioner som skapas i ditt lagringskonto i ”connectedcar”-behållaren.

![Partitionerade utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

*Figur 12 - partitionerade utdata*

Data optimeras nu, hantera och redo för ytterligare bearbetning för att få omfattande batch insikter. 

## <a name="data-analysis"></a>Dataanalys
I det här avsnittet lär du dig att kombinera Azure Stream Analytics, Azure Machine Learning, Azure Data Factory och Azure HDInsight för omfattande avancerade analyser på vehicle hälsotillstånd och andra vanor. Det finns tre underavsnitt här:

1. **Maskininlärning**: detta avsnitt innehåller information om avvikelseidentifiering identifiering experiment som vi använde i den här lösningen för att förutsäga fordon kräver Underhåll underhåll och fordon som kräver återställning av på grund av problem med säkerheten.
2. **Realtid analysis**: detta avsnitt innehåller information om realtidsanalys med Stream Analytics Query Language och operationalizing machine learning-experiment i realtid med ett anpassat program.
3. **Batch-analys**: detta avsnitt innehåller information om den Omforma och bearbetning av batch-data med Azure HDInsight och Azure Machine Learning operationalized av Azure Data Factory.

### <a name="machine-learning"></a>Machine Learning
Vår avsikten är att förutsäga fordon som kräver underhåll eller återkallas baserat på vissa hed statistik. Vi göra följande antaganden

* Om något av följande tre villkor är uppfyllda, fordon kräver **servicing Underhåll**:
  
  * Däck trycket är låg
  * Motorn olja nivån är låg
  * Motorn är hög
* Om något av följande villkor är uppfyllda, fordon kan ha en **säkerhet problemet** och kräver **återkallning**:
  
  * Motorn temperatur är hög men utanför temperatur är låg
  * Motorn temperatur är låg men utanför temperatur är hög

Vi har skapat två separata modeller för att identifiera avvikelser, en för identifiering av vehicle underhåll och en för identifiering av vehicle återkallning baserat på de tidigare krav. I båda dessa modeller används algoritmen inbyggda huvudnamn komponenten analys (PCA) för identifiering av avvikelse. 

**Underhåll identifiering av modellen**

Om något av tre indikatorer däck trycket, motorolja eller motorn temperatur - uppfyller sitt respektive tillstånd, rapporterar Underhåll identifiering av modellen ett fel. Därför behöver vi bara att tänka på de här tre variablerna i att skapa modellen. I vårt experiment i Azure Machine Learning vi först använda en **Välj kolumner i datauppsättning** modulen att extrahera de här tre variablerna. Vi använder bredvid modulen PCA-baserad avvikelseidentifiering identifiering för att skapa avvikelseidentifiering identifiering modell. 

Huvudnamn komponenten analys (PCA) är en etablerad teknik i machine learning som kan tillämpas på val av funktioner, klassificering och avvikelseidentifiering. PCA konverterar en uppsättning fallet med eventuellt korrelerade variabler i en uppsättning värden som kallas huvudkomponenter. Viktiga uppfattning om PCA-baserad modellering är att projektdata till ett lägre endimensionell utrymme så att funktioner och avvikelser mer lätt kan identifieras.

För varje ny indata för identifiering av modellen avvikelseidentifiering detektorn först beräknar dess projektion på eigenvectors och beräknar normaliserade återuppbyggnad-fel. Felet normaliserade är avvikelseidentifiering poäng. Ju högre fel, mer avvikande instansen är. 

I Underhåll identifiering problemet, kan varje post ses som en punkt i 3-dimensionell kan definieras av däck tryck, motorolja och motorn temperatur koordinater. För att samla in dessa avvikelser kan vi projicera ursprungliga data i området 3-dimensionell till en 2-dimensionell utrymme med PCA. Därför ange vi parametern antal komponenter som ska användas i PCA 2. Den här parametern spelar en viktig roll vid tillämpning av PCA-baserad avvikelseidentifiering. Efter projicera data med hjälp av PCA, kan vi identifiera dessa avvikelser enklare.

**Återkalla avvikelseidentifiering identifiering av modellen** i modellen återkallning avvikelseidentifiering identifiering vi använda Välj kolumner i datauppsättning och PCA-baserad avvikelseidentifiering identifiering moduler på ett liknande sätt. Mer specifikt vi först extrahera tre variabler - motorn temperatur utanför temperatur- och hastighet - med hjälp av den **Välj kolumner i datauppsättning** modul. Vi kan också innehålla hastighet variabeln eftersom motorn temperaturen normalt korreleras till hastighet. Vi använder bredvid PCA-baserad avvikelseidentifiering modulen för villkorsidentifiering för att projicera data från 3-dimensionell utrymme på en 2-dimensionell utrymme. Återkalla villkoren är uppfyllda och för programuppdatering kräver återkallning när motorn temperatur- och utanför temperatur hög negativt korrelerade. Vi kan samla in avvikelser när du utför PCA med PCA-baserad algoritm för avvikelseidentifiering. 

När utbildning antingen modell, som vi behöver använda vanliga data, som inte kräver underhåll eller återkallas som indata för att träna modellen PCA-baserad avvikelseidentifiering identifiering. I bedömningsprofil experiment använda vi utbildade avvikelseidentifiering identifiering av modellen för att identifiera huruvida för programuppdatering kräver underhåll eller återkallas. 

### <a name="real-time-analysis"></a>Analys i realtid
Följande Stream Analytics SQL-fråga används för att hämta medelvärdet av alla viktig vehicle parametrar som hastigheten, bränslenivå, motorn temperatur, mätarställning, däck trycket, motorn olja nivå och andra. Medelvärden används för att identifiera avvikelser, utfärda aviseringar, och bestämma övergripande hälsa villkoren för fordon drivas på ett visst område och korrelera den till demografi. 

![Stream Analytics-fråga för realtidsbearbetning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

*Figur 13-Stream Analytics-fråga för realtidsbearbetning*

Alla medelvärden beräknas under en TumblingWindow 3 sekunder. Vi använder TubmlingWindow i det här fallet eftersom vi kräver inte överlappar och sammanhängande intervall. 

Mer information om alla funktioner för ”fönsterhantering” i Azure Stream Analytics klickar du på [fönsterhantering (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Realtid förutsägelse**

Ett program ingår som en del av lösningen för att operationalisera maskininlärning modellen i realtid. Det här programmet som kallas ”RealTimeDashboardApp” har skapats och konfigurerats som en del av distributionen av lösningen. Programmet gör följande:

1. Lyssnar på en Event Hub-instans där Stream Analytics publicerar händelser i ett mönster kontinuerligt. ![Stream Analytics-fråga för att publicera data](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) *figur 14 – Stream Analytics-fråga för att publicera data till ett utgående Event Hub-instans* 
2. För varje händelse som tar emot det här programmet: 
   
   * Bearbetar data med Machine Learning-svar på begäranden bedömningen (RR) slutpunkt. RR-slutpunkten publiceras automatiskt som en del av distributionen.
   * RR-utdata har publicerats till en Power BI-datamängd med push-API: er.

Det här mönstret gäller även för scenarier där du vill integrera en Line of Business (LoB)-program med realtidsanalys-flödet för scenarier, till exempel aviseringar, meddelanden och meddelanden.

Klicka på [RealtimeDashboardApp download](http://go.microsoft.com/fwlink/?LinkId=717078) att hämta RealtimeDashboardApp Visual Studio-lösning för anpassningar. 

**Att köra programmet realtid instrumentpanelen**
1. Extrahera och spara lokalt ![RealtimeDashboardApp mappen](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) *bild 16 – RealtimeDashboardApp mapp*  
2. Köra programmet RealtimeDashboardApp.exe
3. Ange giltiga autentiseringsuppgifter för Power BI, logga in och klicka på Acceptera ![Realtid instrumentpanelen app logga in på Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![Realtid instrumentpanelsapp Slutför logga in till Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Bild 17 – RealtimeDashboardApp: Logga in på Powerbi*

>[!NOTE] 
>Om du vill rensa Power BI-dataset kör RealtimeDashboardApp med parametern ”flushdata”: 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Batchanalys
Avsikten är att visa hur Contoso motorer använder Azure compute-funktioner för att utnyttja stordata för att få detaljerad information på körning mönster, användning beteende och vehicle hälsa. Detta gör det möjligt att:

* Förbättra kundupplevelsen och gör den billigare genom att ge insikter på Driver vanor och bränsle effektivt intresseväckande beteenden
* Lär dig att kunder och deras intresseväckande patters att styra affärsbeslut och tillhandahålla bästa i klassen produkter och tjänster

I den här lösningen utvecklar vi följande mått:

1. **Styr beteendet aggressivt**: identifierar trend i modeller, platser, intresseväckande villkor och tid för år och få insikter om aggressivt intresseväckande mönster. Contoso motorer kan använda dessa insikter för marknadsföringskampanjer, driver nya anpassade funktioner och användningsbaserad insurance.
2. **Bränsle effektivt intresseväckande beteende**: identifierar trend i modeller, platser, intresseväckande villkor och tid för år och få insikter på bränsle effektivt intresseväckande mönster. Contoso motorer kan använda dessa insikter för marknadsföringskampanjer, köra nya funktioner och proaktiv rapporterar till drivrutiner för effektiv och miljö eget intresseväckande vanor. 
3. **Återkalla modeller**: identifierar modeller som kräver återställning av operationalizing den avvikelseidentifiering identifiering maskininlärningsexperiment

Nu ska vi titta i informationen för var och en av de här måtten

**Aggressiv intresseväckande mönster**

Partitionerade vehicle signaler och diagnostiska data bearbetas i pipeline med namnet ”AggresiveDrivingPatternPipeline” med Hive för att avgöra modeller, plats, vehicle, intresseväckande villkor och andra parametrar som uppvisar aggressivt körning mönster.

![Aggressiv intresseväckande mönster arbetsflöde](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 
*bild 18 – aggressiv intresseväckande mönster-arbetsflöde*


***Aggressiv intresseväckande mönster Hive-fråga***

Hive-skript med namnet ”aggresivedriving.hql” används för att analysera aggressivt intresseväckande villkoret mönster finns i ”\demo\src\connectedcar\scripts” mappar för hämtade zip. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'


Kombinationen av fordon överföring växeln position bromsar cyklar status och hastighet används för att identifiera reckless/aggressivt intresseväckande beteende baserat på bromsar mönster med hög hastighet. 

När pipelinen har körts, finns följande partitioner som skapas i ditt lagringskonto i ”connectedcar”-behållaren.

![AggressiveDrivingPatternPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 

*Bild 19 – AggressiveDrivingPatternPipeline utdata*

**Bränsle effektivt intresseväckande mönster**

Partitionerade vehicle signaler och diagnostiska data bearbetas i pipeline med namnet ”FuelEfficientDrivingPatternPipeline”. Hive används för att fastställa modeller, plats, vehicle, intresseväckande villkor och andra egenskaper som uppvisar bränsle effektivt intresseväckande mönster.

![Bränsleeffektiva intresseväckande mönster](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

*Figur 20 – bränsleeffektiva intresseväckande mönster-arbetsflöde*

***Bränsle effektivt intresseväckande mönster Hive-fråga***

Hive-skript med namnet ”fuelefficientdriving.hql” används för att analysera aggressivt intresseväckande villkoret mönster finns i ”\demo\src\connectedcar\scripts” mappar för hämtade zip. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


Det används en kombination av fordon överföring växeln position, bromsar cyklar status, hastighet och accelerator pedal möjlighet att identifiera bränsle effektivt intresseväckande beteende baserat på acceleration, bromsar och processorhastighet mönster. 

När pipelinen har körts, finns följande partitioner som skapas i ditt lagringskonto i ”connectedcar”-behållaren.

![FuelEfficientDrivingPatternPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

*Figur 21 – FuelEfficientDrivingPatternPipeline utdata*

**Återkalla förutsägelser**

Den maskininlärningsexperiment etablerad och publiceras som en webbtjänst som en del av distributionen av lösningen. Slutpunkten för batchbedömningsjobbet utnyttjas i det här arbetsflödet, registrerats som data factory länkad tjänst och operationalized med hjälp av data factory-batchbedömningsaktivitet.

![Machine Learning-slutpunkt](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

*Figur 22 – Machine learning-slutpunkt som är registrerat som en länkad tjänst i data factory*

Den registrerade länkade tjänsten används i DetectAnomalyPipeline för att samla in data med hjälp av avvikelseidentifiering identifiering av modellen. 

![Datorn Learning batchbedömningsaktivitet i data factory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png) 

*Figur 23 – Azure Machine Learning-Batchbedömningen aktivitet i data factory* 

Det finns några steg utförs i den här pipelinen för förberedelse av data så att den kan operationalized med av webbtjänsten för batchbedömningsjobbet. 

![DetectAnomalyPipeline för att förutsäga fordon som kräver återställning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png) 

*Figur 24 – DetectAnomalyPipeline för att förutsäga fordon som kräver återställning* 

***Avvikelseidentifiering identifiering Hive-fråga***

När den bedömningen har slutförts för aktiviteten HDInsight att bearbeta och aggregera data som är kategoriserade som avvikelser av modellen med en sannolikhet poängen för 0,60 eller högre.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


När pipelinen har körts, finns följande partitioner som skapas i ditt lagringskonto i ”connectedcar”-behållaren.

![DetectAnomalyPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

*Bild 25 – DetectAnomalyPipeline utdata*

## <a name="publish"></a>Publicera

### <a name="real-time-analysis"></a>Analys i realtid
En av frågorna i Stream Analytics-jobbet publicerar händelser till utdata Event Hub-instans. 

![Stream Analytics-jobbet publicerar till utdata Event Hub-instans](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Bild 26 – Stream Analytics-jobbet publicerar till utdata Event Hub-instans*

![Stream Analytics-fråga för att publicera till utdata Event Hub-instans](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Bild 27 – Stream Analytics-fråga för att publicera till utdata Event Hub-instans*

Den här dataströmmen av händelser som förbrukas av RealTimeDashboardApp som ingår i lösningen. Det här programmet utnyttjar webbtjänsten för Machine Learning begäran och svar för realtid poäng och publicerar den resulterande data till en Power BI-datamängd för användning. 

### <a name="batch-analysis"></a>Batchanalys
Resultatet av batch- och realtidsbearbetning publiceras till Azure SQL Database-tabeller för användning. Azure SQL Server-databasen och tabeller skapas automatiskt som en del av installationsskriptet. 

![Kopiera resultaten till arbetsflödet för data mart-batchbearbetning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Bild 28 – kopiera resultaten till arbetsflödet för data mart-batchbearbetning*

![Stream Analytics-jobbet publicerar till dataarkiv](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Bild 29 – Stream Analytics-jobbet publicerar till dataarkiv*

![Data mart-inställningen i Stream Analytics-jobbet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Bild 30 – dataarkiv i Stream Analytics-jobbet*

## <a name="consume"></a>Förbruka
Powerbi ger den här lösningen en omfattande instrumentpanel för data i realtid och förutsägelseanalys visualiseringar. 

Klicka här för detaljerade anvisningar om hur du skapar Power BI-rapporter och på instrumentpanelen. Den slutliga instrumentpanelen ser ut så här:

![Power BI-instrumentpanel](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

*Bild 31 - Power BI-instrumentpanel*

## <a name="summary"></a>Sammanfattning
Det här dokumentet innehåller en detaljerad nedåt Vehicle telemetri Analytics lösning. Detta prov på ett lambda-arkitektur mönster för realtid och batch-analytics med förutsägelser och åtgärder. Det här mönstret som gäller för en mängd olika användningsområden som kräver varm sökväg (realtid) och kalla sökväg (batch) analyser. 

