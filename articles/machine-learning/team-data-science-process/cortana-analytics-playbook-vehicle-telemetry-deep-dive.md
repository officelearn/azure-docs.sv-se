---
title: Djupdykning i hur att förutsäga vehicle hälsa och köra vanor - Azure | Microsoft Docs
description: Använda funktionerna i Cortana Intelligence och få insikter om i realtid och förutsägbara på vehicle hälsa och köra vanor.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: a856c59191a1e35b78d1ab6c2c88ab5288ea1419
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836491"
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Vehicle telemetri Analytics lösning playbook: djup fördjupa dig i lösningen
Den här menyn innehåller länkar till avsnitt i den här playbook: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Det här dokumentet flyttar ned till var och en av de stegen som beskrivs i lösningsarkitekturen. Anvisningar och pekare för anpassning ingår. 

## <a name="data-sources"></a>Datakällor
Lösningen använder två olika datakällor:

* Simulerade vehicle signaler och diagnostiska data
* Vehicle katalog

Vehicle telematik simulator ingår som en del av den här lösningen som visas i följande skärmbild. Den skickar diagnostikinformation och signalerar till att motsvarar tillståndet för programuppdatering och intresseväckande mönstret vid en viss tidpunkt. Om du vill hämta den Vehicle telematik Simulator Visual Studio-lösningen för anpassningar baserat på dina krav, gå till den [Vehicle telematik simulator](http://go.microsoft.com/fwlink/?LinkId=717075) webbsidan. Vehicle katalogen innehåller en referens datauppsättning som mappar vehicle identifikationsnummer (VINs) till modeller.

![Vehicle telematik simulator](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Den här JSON-formaterad datamängden innehåller följande schema.

| Kolumn | Beskrivning | Värden |
| --- | --- | --- |
| VIN |Slumpmässigt genererat VIN |Hämtas från en övergripande lista med 10 000 slumpmässigt genererat VINs |
| Utanför temperatur |Utanför temperaturen där vehicle kör |Slumptal mellan 0 och 100 |
| Motorn temperatur |Motorn temperatur för programuppdatering |Slumptal mellan 0 och 500 |
| Hastighet |Motorns hastighet som som aktiverar för programuppdatering |Slumptal mellan 0 och 100 |
| Bränsle |Bränslenivå för för programuppdatering |Slumpmässigt tal mellan 0 och 100 (anger bränsle nivån procent) |
| EngineOil |Motorn olja andelen för programuppdatering |Slumpmässigt tal mellan 0 och 100 (anger motorn olja nivån procent) |
| Däck hög belastning |Däck tryck för programuppdatering |Slumpmässigt nummer som skapas från 0 till 50 (anger däck trycket nivån procent) |
| Mätarställning |Mätarställning för för programuppdatering |Slumpmässigt genererat nummer från 0 till 200 000 |
| Accelerator_pedal_position |Accelerator cyklar placering för programuppdatering |Slumpmässigt tal mellan 0 och 100 (anger accelerator nivån procent) |
| Parking_brake_status |Anger om vehicle parkerade eller inte |True eller False |
| Headlamp_status |Anger om strålkastaren är på eller inte |True eller False |
| Brake_pedal_status |Anger om bromspedal är nedtryckt eller inte |True eller False |
| Transmission_gear_position |Överföring växeln placering för programuppdatering |Tillstånd: första, andra, tredje, fjärde, femte, sjätte, sjunde, åttonde |
| Ignition_status |Anger om är igång eller Stoppad |True eller False |
| Windshield_wiper_status |Anger om vindrutan vindrutetorkare är aktiverad eller inte |True eller False |
| ABS |Anger om ABS används eller inte |True eller False |
| Tidsstämpel |Tidsstämpel när datapunkten som har skapats |Date |
| Ort |Platsen för programuppdatering |Fyra orter i den här lösningen: Bellevue, Redmond, Sammamish, Seattle |

Vehicle modellen referens datauppsättningen mappar VINs till modeller. 

| VIN | Modell |
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

## <a name="ingestion"></a>Införandet
Kombinationer av Händelsehubbar i Azure, Azure Stream Analytics och Azure Data Factory används för att mata in vehicle signaler, diagnostiska händelser och realtid och batch-analytics. Alla dessa komponenter skapas och konfigureras som en del av distributionen av lösningen. 

### <a name="real-time-analysis"></a>Analys i realtid
Händelser som genererats av vehicle telematik simulator publiceras till händelsehubben med hjälp av händelsehubben SDK.  

![Event hub instrumentpanelen](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

Stream Analytics-jobbet en dessa händelser från event hub och bearbetar data i realtid för att analysera vehicle hälsa.

![Strömma Analytics-jobbet databearbetning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


Stream Analytics-jobbet:

* En data från event hub.
* Utför en koppling med referensdata att mappa vehicle VIN till motsvarande modellen. 
* Sparar dem i Azure Blob storage för omfattande batch analytics. 

Följande Stream Analytics-fråga används för att spara data till Blob storage: 

![Stream Analytics-jobbet frågan för datapåfyllning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Batchanalys
En ytterligare volym av simulerade vehicle signaler och diagnostiska data genereras även för bättre batch analytics. Denna ytterligare volym krävs så att en bra representativt datavolym för batch-bearbetning. För detta ändamål används PrepareSampleDataPipeline i Data Factory-arbetsflödet för att generera ett år kan du se simulerade vehicle signaler och diagnostiska data. Om du vill hämta Data Factory anpassad .NET aktivitet Visual Studio-lösning för anpassningar baserat på dina krav, gå till den [Data Factory anpassad aktivitet](http://go.microsoft.com/fwlink/?LinkId=717077) webbsidan. 

Det här arbetsflödet visar exempeldata förberedd för batch-bearbetning.

![Exempeldata förberedd för arbetsflöde för batch-bearbetning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


Pipelinen består av en anpassad Data Factory .NET-aktivitet.

![PrepareSampleDataPipeline aktivitet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

När pipelinen körs korrekt och datauppsättningen RawCarEventsTable markeras ”klar”, genereras ett år kan du se simulerade vehicle signaler och diagnostikdata. Du kan se på följande mapp och fil som skapats i ditt lagringskonto i connectedcar-behållaren:

![PrepareSampleDataPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Partitionen datauppsättningen
I förberedelsen av data partitioneras rådata halvstrukturerade vehicle signaler och diagnostik datauppsättning till formatet år/månad. Den här partitionering befordrar effektivare fråga och skalbar långsiktig lagring genom att aktivera fel-över. Till exempel när det första blob-kontot fylls hos den över till nästa konto. 

>[!NOTE] 
>Det här steget i lösningen gäller enbart för batch-bearbetning.

Indata och utdata datahantering:

* **Utdata** (märkt PartitionedCarEventsTable) lagras under en längre tidsperiod som grundläggande / ”rawest” form av data i kundens data lake. 
* **Indata för** för den här pipeline vanligtvis ignoreras eftersom utdata har fullständig återgivning till indata. Den lagras (partitionerad) bättre för senare användning.

Arbetsflöde för partition bil händelser.

![Partitionen bil händelser arbetsflöde](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Rådata är partitionerad med hjälp av en aktivitet användarstruktur Azure HDInsight i PartitionCarEventsPipeline, som visas i följande skärmbild. Exempeldata som genererats för ett år i förberedelsen av data har partitionerats med år/månad. Partitionerna som används för att generera vehicle signaler och diagnostikdata för varje månad (totalt 12 partitioner) för ett år. 

![PartitionCarEventsPipeline aktivitet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**PartitionConnectedCarEvents Hive-skript**

Partitioncarevents.hql för Hive-skript används för partitionering. Det finns i mappen \demo\src\connectedcar\scripts på hämtade zip-filen. 
    
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

När pipeline utförs korrekt, visas följande partitioner som skapas i ditt lagringskonto i connectedcar-behållaren:

![Partitionerade utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Data optimeras nu mer användarvänlig och redo för vidare bearbetning för att få omfattande batch insikter. 

## <a name="data-analysis"></a>Dataanalys
I det här avsnittet lär du dig att kombinera Stream Analytics, Azure Machine Learning, Data Factory och HDInsight för omfattande avancerade analyser på vehicle hälsotillstånd och andra vanor.

### <a name="machine-learning"></a>Maskininlärning
Avsikten är att förutsäga fordon som kräver underhåll eller återkallas baserat på vissa hed statistik, baserat på följande förutsättningar:

* Om någon av följande tre villkor är uppfyllda, kräver fordon servicing Underhåll:
  
  * Trycket däck är låg.
  * Motorn olja nivån är låg.
  * Motorn är hög.

* Om någon av följande villkor är uppfyllda, kan fordon har ett problem med säkerhet och behöver återkalla:
  
  * Motorn är hög, men utanför är låg.
  * Motorn är låg, men utanför är hög.

Två separata modeller identifiera baserat på de tidigare krav, avvikelser. En modell är för identifiering av vehicle underhåll och en modell är för vehicle återkallning identifiering. I båda modellerna används algoritmen inbyggda huvudnamn komponenten analys (PCA) för identifiering av avvikelse. 

#### <a name="maintenance-detection-model"></a>**Underhåll identifiering av modellen**

Om en av tre indikatorer--däck trycket, motorolja eller motorn temperaturen--uppfyller sitt respektive tillstånd, Underhåll identifiering av modellen rapporterar ett fel. Därför måste bara de här tre variablerna du överväga att skapa modellen. I experiment i machine learning i **Välj kolumner i datauppsättning** modulen används för att extrahera de här tre variablerna. Därefter används PCA-baserad avvikelseidentifiering identifiering modulen för att skapa avvikelseidentifiering identifiering modell. 

PCA är en etablerad teknik i machine learning som kan tillämpas på val av funktioner, klassificering och avvikelseidentifiering. PCA konverterar en uppsättning fall som innehåller eventuellt korrelerade variabler i en uppsättning värden som kallas huvudkomponenter. Viktiga uppfattning om PCA-baserad modellering är att projektdata till en lägre endimensionell utrymme för att lättare att identifiera funktioner och avvikelser.

För varje ny indata för identifiering av modellen beräknar avvikelseidentifiering detektorn först dess projektion på eigenvectors. Sedan beräknar normaliserade återuppbyggnad-fel. Felet normaliserade är avvikelseidentifiering poäng: ju högre fel, mer avvikande instansen. 

Underhåll identifiering problem anses varje post enligt en punkt i en tredimensionell utrymme däck tryck, motorolja och motorn temperatur koordinater. Om du vill samla in dessa avvikelser, används PCA för att projicera ursprungliga data i området tredimensionell på en tvådimensionell utrymme. Därför har parametern antalet komponenter i PCA angetts till två. Den här parametern spelar en viktig roll vid tillämpning av PCA-baserad avvikelseidentifiering. När du använder PCA projektinformationen identifieras dessa avvikelser enklare.

#### <a name="recall-anomaly-detection-model"></a>**Återkalla avvikelseidentifiering identifiering av modellen**

I modellen återkallning avvikelseidentifiering identifiering av **Välj kolumner i datauppsättning** och PCA-baserad avvikelseidentifiering identifiering moduler som används på liknande sätt. Mer specifikt tre variabler--motorn temperatur, utanför temperatur- och hastighet--extraheras först med hjälp av den **Välj kolumner i datauppsättning** modul. Variabeln hastighet också ingår, eftersom motorn temperatur motsvarar vanligtvis hastighet. Därefter används PCA-baserad avvikelseidentifiering identifiering modulen för att projicera data från tredimensionell utrymme på en tvådimensionell utrymme. Återkalla villkor är uppfyllda. För programuppdatering kräver återkallning när motorn temperatur- och utanför temperatur hög negativt korrelerade. När PCA utförs används den PCA-baserad algoritmen för avvikelseidentifiering för att hämta avvikelser. 

Vid inlärning antingen modellen används normal som indata för att träna modellen PCA-baserad avvikelseidentifiering identifiering. (Normalt data inte kräver underhåll eller återkallning.) I bedömningsprofil experimentet för utbildade avvikelseidentifiering identifiering av modellen att identifiera om vehicle kräver underhåll eller återkallas. 

### <a name="real-time-analysis"></a>Analys i realtid
Följande Stream Analytics SQL-fråga används för att hämta medelvärdet av alla viktig vehicle-parametrar. Parametrarna inkluderar hastigheten, bränslenivå, motorn temperatur, mätarställning, däck trycket, motorn olja nivå och andra. Medelvärden används för att identifiera avvikelser, utfärda aviseringar och bestämma övergripande hälsa villkoren för fordon som används i en viss region. Medelvärden kopplas sedan ihop till demografi. 

![Stream Analytics-fråga för realtidsbearbetning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Alla medelvärden beräknas under en tre sekunder rullande fönster. En rullande fönster används eftersom det inte överlappar och sammanhängande intervall krävs. 

Läs mer om funktionerna i Stream Analytics fönsterhantering i [fönsterhantering (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**Realtid förutsägelse**

Ett program ingår som en del av lösningen för att operationalisera maskininlärning modellen i realtid. Programmet RealTimeDashboardApp har skapats och konfigurerats som en del av distributionen av lösningen. Program:

* Lyssnar på en händelse NAV-instans där Stream Analytics publicerar händelser i ett mönster kontinuerligt.

    ![Stream Analytics-fråga för att publicera data](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Tar emot händelser. För varje händelse som tar emot det här programmet: 
   
   * Data bearbetas med hjälp av machine learning-slutpunkt för bedömningsprofil (RR) i begäran och svar. RR-slutpunkten publiceras automatiskt som en del av distributionen.
   * RR-utdata publiceras till en datauppsättning för Power BI genom att använda push-API: er.

Det här mönstret gäller även för scenarier där du vill integrera en line-of-business-program med den analys i realtid. Dessa scenarier som inkluderar aviseringar, meddelanden och meddelanden.

Om du vill hämta RealtimeDashboardApp Visual Studio-lösning för anpassningar finns i [RealtimeDashboardApp download](http://go.microsoft.com/fwlink/?LinkId=717078) webbsidan. 

#### <a name="execute-the-real-time-dashboard-application"></a>**Köra programmet realtid instrumentpanelen**
1. Extrahera RealtimeDashboardApp och spara den lokalt.

    ![RealTimeDashboardApp mapp](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Köra programmet RealtimeDashboardApp.exe.

3. Ange giltig Power BI-autentiseringsuppgifter och välj **logga in**.  

    ![Realtid instrumentpanelen app inloggning fönster](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Välj **acceptera**.

    ![Realtid instrumentpanelen app slutligt inloggning fönster](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Om du vill rensa datauppsättningen Power BI kan du köra RealtimeDashboardApp med parametern ”flushdata”. 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Batchanalys
Avsikten är att visa hur Contoso motorer använder Azure compute-funktioner för att utnyttja stordata. Dessa data visar detaljerad information om intresseväckande mönster, användning beteende och vehicle hälsa. Denna information gör det möjligt att:

* Förbättra kundupplevelsen och gör den billigare genom att ge insikter på Driver vanor och bränsleeffektiva intresseväckande beteenden.
* Lär dig att kunder och deras intresseväckande mönster för att styra affärsbeslut och tillhandahålla bästa i klassen produkter och tjänster.

I den här lösningen riktas följande mått:

* **Styr beteendet aggressivt**: identifierar trend i modeller, platser, intresseväckande villkor och tid för år och få insikter om aggressivt intresseväckande mönster. Contoso motorer kan använda dessa insikter för marknadsföringskampanjer för att införa nya anpassade funktioner och användningsbaserad insurance.
* **Bränsleeffektiva intresseväckande beteende**: identifierar trend i modeller, platser, intresseväckande villkor och tid för år och få insikter om bränsleeffektiva intresseväckande mönster. Contoso motorer kan använda dessa insikter för marknadsföringskampanjer för att införa nya funktioner och proaktiv rapporterar till drivrutiner för kostnadseffektiv och miljö eget intresseväckande vanor.
* **Återkalla modeller**: identifierar modeller som kräver återställning av operationalizing den avvikelseidentifiering identifiering maskininlärningsexperiment.

Nu ska vi titta i informationen för var och en av de här måtten.

#### <a name="aggressive-driving-behavior-patterns"></a>**Aggressiv intresseväckande beteende mönster**

Partitionerade vehicle signaler och diagnostiska data bearbetas i AggresiveDrivingPatternPipeline, som visas i följande arbetsflöde. Hive används för att fastställa modeller, plats, vehicle, intresseväckande villkor och andra parametrar som uppvisar aggressivt intresseväckande mönster.

![Aggressiv intresseväckande mönster-arbetsflöde](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Aggressiv intresseväckande mönster Hive-fråga***

Aggresivedriving.hql för Hive-skript används för att analysera aggressivt intresseväckande villkoret mönster. Det finns i mappen \demo\src\connectedcar\scripts på hämtade zip-filen. 

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


Skriptet använder kombinationen av ett fordon överföring växeln position bromsar cyklar status och hastighet för att identifiera reckless/aggressivt intresseväckande beteende baserat på bromsar mönster med hög hastighet. 

När pipeline utförs korrekt, visas följande partitioner som skapas i ditt lagringskonto i connectedcar-behållaren:

![AggressiveDrivingPatternPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Bränsleeffektiva intresseväckande beteende mönster**

Partitionerade vehicle signaler och diagnostiska data bearbetas i FuelEfficientDrivingPatternPipeline, som visas i följande arbetsflöde. Hive används för att fastställa modeller, plats, vehicle, intresseväckande villkor och andra egenskaper som uppvisar bränsleeffektiva intresseväckande mönster.

![Bränsleeffektiva intresseväckande mönster](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Bränsleeffektiva intresseväckande mönster Hive-fråga***

Fuelefficientdriving.hql för Hive-skript används för att analysera bränsleeffektiva intresseväckande villkoret mönster. Det finns i mappen \demo\src\connectedcar\scripts på hämtade zip-filen. 

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


Skriptet använder kombinationen av ett fordon överföring växeln position, bromsar cyklar status, hastighet och accelerator pedal möjlighet att identifiera bränsleeffektiva intresseväckande beteende baserat på acceleration, bromsar och snabba mönster. 

När pipeline utförs korrekt, visas följande partitioner som skapas i ditt lagringskonto i connectedcar-behållaren:

![FuelEfficientDrivingPatternPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Återkalla modellen förutsägelser**

Den maskininlärningsexperiment etablerad och publiceras som en webbtjänst som en del av distributionen av lösningen. Slutpunkten för batchbedömningsjobbet används i det här arbetsflödet. Den har registrerats som data factory länkad tjänst och operationalized med hjälp av den data factory-batchbedömningsaktivitet.

![Machine learning-slutpunkt](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

Den registrerade länkade tjänsten används i DetectAnomalyPipeline för att samla in data med hjälp av avvikelseidentifiering identifiering av modellen. 

![Machine learning-batchbedömningsaktivitet i data factory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Några steg utförs i denna pipeline för förberedelse av data så att den kan operationalized med av webbtjänsten för batchbedömningsjobbet. 

![DetectAnomalyPipeline för återkallning förutsägelse](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Avvikelseidentifiering identifiering Hive-fråga***

När den bedömningen är klar, bearbetar ett HDInsight-aktiviteten och samlar in data som modellen kategoriserade som avvikelser. Modellen använder en sannolikhet poäng på 0.60 eller högre.

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


När pipeline utförs korrekt, visas följande partitioner som skapas i ditt lagringskonto i connectedcar-behållaren:

![DetectAnomalyPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Publicera

### <a name="real-time-analysis"></a>Analys i realtid
En av frågorna i Stream Analytics-jobbet publicerar händelser till en utdata event hub-instans. 

![Stream Analytics-jobbet publiceras till en utdata event hub-instans](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

Följande Stream Analytics-fråga används för att publicera till utdata event hub instans:

![Stream Analytics-fråga för att publicera till utdata event hub instans](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Den här dataströmmen av händelser som förbrukas av RealTimeDashboardApp som ingår i lösningen. Det här programmet använder maskininlärning för webbtjänsten för begäran och svar för realtid poäng. Den publicerar resulterande data till en Power BI datauppsättning för användning. 

### <a name="batch-analysis"></a>Batchanalys
Resultatet av batch- och realtidsbearbetning publiceras till Azure SQL Database-tabeller för användning. SQLServer, databasen och tabeller skapas automatiskt som en del av installationsskriptet. 

Resultat för batch-bearbetning kopieras till arbetsflödet för data mart.

![Batchbearbetning resultat som kopieras till arbetsflödet för data mart](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

Stream Analytics-jobbet har publicerats till dataarkivet.

![Stream Analytics-jobbet har publicerats till dataarkiv](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Inställningen för data mart är i Stream Analytics-jobbet.

![Data mart-inställningen i Stream Analytics-jobbet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Förbruka
Powerbi ger den här lösningen en omfattande instrumentpanel för data i realtid och förutsägelseanalys visualiseringar. 

Det ser ut som i det här exemplet slutliga instrumentpanelen:

![Power BI-instrumentpanel](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Sammanfattning
Det här dokumentet innehåller en detaljerad nedåt Vehicle telemetri Analytics lösning. Lambda-arkitektur mönstret används för realtid och batch-analytics med förutsägelser och åtgärder. Det här mönstret som gäller för en mängd olika användningsområden som kräver varm sökväg (realtid) och kalla sökväg (batch) analyser. 

### <a name="references"></a>Referenser

* [Vehicle telematik Simulator Visual Studio-lösning](http://go.microsoft.com/fwlink/?LinkId=717075) 
* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [Azure Event Hubs SDK för inhämtning av dataströmmar](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Azure Data Factory data movement funktioner](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Azure Data Factory .NET-aktiviteten](../../data-factory/v1/data-factory-use-custom-activities.md)
* [Azure Data Factory .NET-aktiviteten Visual Studio-lösning som används för att förbereda exempeldata](http://go.microsoft.com/fwlink/?LinkId=717077) 
