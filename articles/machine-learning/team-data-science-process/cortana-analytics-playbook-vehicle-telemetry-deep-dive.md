---
title: Ingående information om hur du kan förutsäga fordonshälsa och körvanor – Azure | Microsoft Docs
description: Använda funktionerna i Cortana Intelligence insikter i realtid och förutsägande på fordonshälsa och vanor.
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
ms.openlocfilehash: fc4b2d0beef1c91cd937ba901974796441657441
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234773"
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Vehicle Telemetry Analytics-lösning, playbook: fördjupa dig i lösningen
Den här menyn länkar till avsnitt i den här spelbok: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Det här dokumentet flyttar ned till var och en av de steg som visas i lösningsarkitekturen. Instruktioner och pekare för anpassning ingår. 

## <a name="data-sources"></a>Datakällor
Lösningen använder två olika datakällor:

* Simulerade vehicle signaler och diagnostiska datamängd
* Vehicle catalog

En vehicle telematics simulator ingår som en del av den här lösningen enligt följande skärmbild. Den genererar diagnostisk information och signalerar till att motsvarar tillståndet fordonets och utveckla mönster vid en viss tidpunkt.  Vehicle katalogen innehåller en referensdatauppsättning som mappar vehicle identifikationsnummer (VINs) till modeller. Obs: Vehicle Telematics Simulator Visual Studio-lösning med datauppsättningen är inte längre tillgänglig. 

![Vehicle telematics-simulator](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Den här JSON-formaterade datamängden innehåller följande schema.

| Kolumn | Beskrivning | Värden |
| --- | --- | --- |
| VIN |Slumpmässigt genererat VIN |Hämtas från en master över 10 000 slumpmässigt genererat VINs |
| Extern temperatur |Extern temperatur där fordonets kör |Slumpmässigt genererat nummer mellan 0 och 100 |
| Motorn temperatur |Motorn temperaturen fordonets |Slumpmässigt genererat nummer mellan 0 och 500 |
| Hastighet |Motorn hastighet som som aktiverar fordonets |Slumpmässigt genererat nummer mellan 0 och 100 |
| Bränsle |Fordonets bränsle höjd |Slumpmässigt genererat nummer mellan 0 och 100 (anger bränsle nivå procent) |
| EngineOil |Motorn olja nivå fordonets |Slumpmässigt genererat nummer mellan 0 och 100 (anger motorn olja nivå procent) |
| Däck hög belastning |Däck trycket fordonets |Slumpmässigt genererat nummer från 0 till 50 (anger däck trycket på procent) |
| Mätarställning |Mätarställning fordonets |Slumpmässigt genererat nummer mellan 0 till 200 000 |
| Accelerator_pedal_position |Accelerator cyklar positionen fordonets |Slumpmässigt genererat nummer mellan 0 och 100 (anger accelerator nivå procent) |
| Parking_brake_status |Anger om fordonets parkeras eller inte |SANT eller FALSKT |
| Headlamp_status |Anger om strålkastaren är på eller inte |SANT eller FALSKT |
| Brake_pedal_status |Anger om bromspedal trycks eller inte |SANT eller FALSKT |
| Transmission_gear_position |Överföringen gear positionen fordonets |Tillstånd: första, andra, tredje, fjärde, femte, sjätte, sjunde, åttonde |
| Ignition_status |Anger om fordonets är igång eller Stoppad |SANT eller FALSKT |
| Windshield_wiper_status |Anger om vindrutan vindrutetorkare är aktiverat eller inte |SANT eller FALSKT |
| ABS |Anger om ABS engagerade eller inte |SANT eller FALSKT |
| Tidsstämpel |Tidsstämpeln när datapunkten skapas |Date |
| Ort |Platsen för fordonets |Fyra städer i den här lösningen: Bellevue, Redmond, Sammamish, Seattle |

Referensdatauppsättningen modellen vehicle motsvarar modeller VINs. 

| VIN | Modell |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Sedan |
| 8J0U8XCPRGW4Z3NQE |Hybrid |
| WORG68Z2PLTNZDBI7 |Programvarufamiljer sedan |
| JTHMYHQTEPP4WBMRN |Sedan |
| W9FTHG27LZN1YWO0Y |Hybrid |
| MHTP9N792PHK08WJM |Programvarufamiljer sedan |
| EI4QXI2AXVQQING4I |Sedan |
| 5KKR2VB4WHQH97PF8 |Hybrid |
| W9NSZ423XZHAONYXB |Programvarufamiljer sedan |
| 26WJSGHX4MA5ROHNL |Konvertera |
| GHLUB6ONKMOSI7E77 |Station vagn |
| 9C2RHVRVLMEJDBXLP |Bil |
| BRNHVMZOUJ6EOCP32 |Små Stadsjeep |
| VCYVW0WUZNBTM594J |Sport bil |
| HNVCE6YFZSA5M82NY |Medelhög Stadsjeep |
| 4R30FOR7NUOBL05GJ |Station vagn |
| WYNIIY42VKV6OQS1J |Stora Stadsjeep |
| 8Y5QKG27QET1RBK7I |Stora Stadsjeep |
| DF6OX2WSRA6511BVG |Coupe |
| Z2EOZWZBXAEW3E60T |Sedan |
| M4TV6IEALD5QDS3IR |Hybrid |
| VHRA1Y2TGTA84F00H |Programvarufamiljer sedan |
| R0JAUHT1L1R3BIKI0 |Sedan |
| 9230C202Z60XX84AU |Hybrid |
| T8DNDN5UDCWL7M72H |Programvarufamiljer sedan |
| 4WPYRUZII5YV7YA42 |Sedan |
| D1ZVY26UV2BFGHZNO |Hybrid |
| XUF99EW9OIQOMV7Q7 |Programvarufamiljer sedan |
| 8OMCL3LGI7XNCC21U |Konvertera |
| ……. | |

## <a name="ingestion"></a>Datainmatning
Kombinationer av Azure Event Hubs, Azure Stream Analytics och Azure Data Factory används för att mata in vehicle signaler, diagnostiska händelser i realtid och batch-analys. Alla dessa komponenter skapas och konfigureras som en del av distributionen av lösningen. 

### <a name="real-time-analysis"></a>Realtidsanalys
Händelser som genererats av vehicle telematics-simulator publiceras till event hub med hjälp av event hub SDK.  

![Event hub-instrumentpanel](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

Stream Analytics-jobbet matar in dessa händelser från event hub och bearbetar data i realtid för att analysera fordonshälsa.

![Stream Analytics-jobbet databearbetning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


Stream Analytics-jobb:

* Matar in data från händelsehubben.
* Skapar en koppling med referensdata att mappa vehicle VIN till motsvarande modellen. 
* Håller kvar dem till Azure Blob storage för omfattande batchanalyser. 

Följande Stream Analytics-frågan används för att spara data till Blob-lagring: 

![Stream Analytics-jobb-fråga för datainmatning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Batchanalys
En ytterligare volym av simulerade vehicle signaler och diagnostiska data genereras även för mer omfattande batchanalyser. Det krävs ytterligare volymen så att en bra representativa datavolym för batchbearbetning. För detta ändamål används PrepareSampleDataPipeline i Data Factory-arbetsflöde för att generera ett års tillhandahåller simulerade vehicle signaler och diagnostiska datamängd. För att hämta de Data Factory anpassad .NET-aktiviteten Visual Studio-lösningen anpassas efter dina behov, går du till den [anpassad aktivitet för Data Factory](https://go.microsoft.com/fwlink/?LinkId=717077) webbsidan. 

Det här arbetsflödet visar exempeldata har förberetts för batchbearbetning.

![Exempeldata har förberetts för arbetsflöde för batch-bearbetning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


Pipelinen består av en anpassad Data Factory .NET-aktivitet.

![PrepareSampleDataPipeline aktivitet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

När pipelinen körs rätt och RawCarEventsTable datauppsättningen är märkt ”klar”, genereras ett år som simulerade vehicle signaler och diagnostikdata. Du ser följande mapp och fil som skapats i ditt storage-konto i connectedcar-behållaren:

![PrepareSampleDataPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Partitionera datauppsättningen
I förberedelsen data partitioneras raw halvstrukturerade vehicle signaler och diagnostiska datauppsättning till formatet år/månad. Den här partitionering främjar mer effektiva frågor och skalbar långsiktig lagring genom att aktivera över fel. Till exempel när det första blob-kontot fylls faults den över till nästa konto. 

>[!NOTE] 
>Det här steget i lösningen gäller endast för batchbearbetning.

Indata och utdata datahantering:

* **Mata ut data** (märkta PartitionedCarEventsTable) lagras under en längre tidsperiod som grundläggande / ”rawest” form av data i kundens data lake. 
* **Indata** till denna pipeline ignoreras vanligtvis eftersom utdata har fullständig exakthet till indata. Den är lagrad (partitionerad) bättre för senare användning.

Arbetsflöde för partition bil händelser.

![Partition bil händelser arbetsflöde](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Rådata är partitionerad med hjälp av en Hive-Azure HDInsight-aktivitet i PartitionCarEventsPipeline, enligt följande skärmbild. Exempeldata som genererats för ett år i dataförberedningssteg har partitionerats med år/månad. Partitionerna används för att generera vehicle signaler och diagnostikdata för varje månad (totalt 12 partitioner) för ett år. 

![PartitionCarEventsPipeline aktivitet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**PartitionConnectedCarEvents Hive-skript**

Hive-skriptet partitioncarevents.hql används för partitionering. Det finns i mappen \demo\src\connectedcar\scripts hämtade zip-filen. 
    
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

När pipelinen körs har, kan du se följande partitioner som skapas i ditt lagringskonto i connectedcar-behållaren:

![Partitionerade utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Data har nu optimerats mer hanterbara och redo för vidare bearbetning för att få omfattande batch insikter. 

## <a name="data-analysis"></a>Dataanalys
I det här avsnittet lär du dig att kombinera Stream Analytics, Azure Machine Learning, Data Factory och HDInsight för omfattande avancerad analys av fordonshälsa och driver på vanor.

### <a name="machine-learning"></a>Maskininlärning
Avsikten är att förutsäga fordon som kräver underhåll eller återkallas baserat på vissa hälsoavsökning statistik, baserat på följande antaganden:

* Om något av följande tre villkor uppfylls, kräver fordon servicing Underhåll:
  
  * Däck trycket är låg.
  * Motorn olja nivå är låg.
  * Motorn temperaturen är hög.

* Om någon av följande villkor föreligger fordonen har ett problem med säkerhet och kräver återkallande:
  
  * Motorn temperaturen är hög, men utanför temperaturen är låg.
  * Motorn temperaturen är låg, men utanför temperaturen är hög.

Två olika modeller identifiera baserat på föregående krav, avvikelser. En modell är för identifiering av vehicle underhåll och en modell är för vehicle återkallande identifiering. I båda modellerna används inbyggda huvudnamn komponenten analysis (PCA) algoritm för avvikelseidentifiering. 

#### <a name="maintenance-detection-model"></a>**Modell för identifiering av underhåll**

Om en av tre indikatorer--däck hög belastning, motorolja eller motorn temperatur--uppfyller sitt respektive tillstånd, Underhåll identifiering av modellen rapporterar ett fel. Därför måste bara de här tre variablerna du vara överväga att skapa modellen. I experiment i machine learning, den **Välj kolumner i datauppsättning** modulen används för att extrahera de här tre variablerna. Därefter används PCA-baserad avvikelseidentifiering identifiering av modulen för att skapa modellen för identifiering av avvikelser. 

PCA är en etablerad teknik i machine learning som kan tillämpas på val av funktioner, klassificering och avvikelseidentifiering. PCA konverterar en uppsättning fall som innehåller eventuellt korrelerade variabler i en uppsättning värden som kallas huvudkomponenter. Viktiga uppfattning om PCA-baserad modellering är att projektdata till en lägre-dimensionell utrymme för att enkelt identifiera funktioner och avvikelser.

För varje ny indata för identifiering av modellen beräknar avvikelseidentifiering detektor först dess projektion på eigenvectors. Sedan beräknar normaliserade återuppbyggnad-fel. Felet normaliserade kan avvikelsepoäng: desto högre felet, mer avvikande instansen. 

I Underhåll identifiering av problemet anses varje post som en punkt i 3D kan definieras av däck tryck, motorolja och motor temperatur koordinater. Om du vill samla in dessa avvikelser, används PCA för att projicera den ursprungliga informationen i området 3D till ett tvådimensionellt utrymme. Därför har parametern antalet komponenter att använda i PCA angetts till två. Den här parametern spelar en viktig roll vid tillämpning av PCA-baserad avvikelseidentifiering. När du har använt PCA till projektdata, identifieras dessa avvikelser enklare.

#### <a name="recall-anomaly-detection-model"></a>**Återkalla modell för identifiering av avvikelser**

I återkallande avvikelseidentifiering identifiering av modellen, de **Välj kolumner i datauppsättning** och PCA-baserad avvikelseidentifiering identifiering av moduler som används på liknande sätt. Mer specifikt tre variablerna--motorn temperatur, utanför temperatur och hastighet--extraheras först genom att använda den **Välj kolumner i datauppsättning** modulen. Variabeln hastighet också tas med, eftersom motorn temperaturen motsvarar vanligtvis hastighet. Därefter används PCA-baserad avvikelseidentifiering identifiering av modulen för att projicera data från 3D utrymmet på ett tvådimensionellt blanksteg. Återkallande villkor är uppfyllda. Fordonets kräver återkallande när motorn temperatur och extern temperatur kopplas ihop med hög negativt. När PCA utförs, används algoritmen för PCA-baserad avvikelseidentifiering att samla in avvikelser. 

När träna antingen modell används normala data som indata för att träna modellen PCA-baserad avvikelseidentifiering identifiering. (Normalt data kräver inte underhåll eller återkallas.) I experimentet bedömnings används tränade avvikelseidentifiering identifiering av modellen för att identifiera om fordonets kräver underhåll eller återkallas. 

### <a name="real-time-analysis"></a>Realtidsanalys
Följande Stream Analytics SQL-fråga används för att hämta medelvärdet av alla viktiga vehicle parametrar. De här parametrarna är fordonets hastighet, bränslenivå, motorn temperatur, mätarställning, däck tryck, motorn olja nivå och andra. Medelvärden används för att identifiera avvikelser, utfärda aviseringar och fastställa övergripande hälsa villkoren i fordon som drivs i en viss region. I genomsnitt är sedan korreleras mot demografi. 

![Stream Analytics-fråga för bearbetning i realtid](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Alla medelvärden beräknas över en tre sekunder rullande fönster. Ett rullande fönster används eftersom inte överlappar sammanhängande tidsintervaller krävs. 

Läs mer om fönsterhantering funktionerna i Stream Analytics i [fönsterhantering (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**I realtid förutsägelse**

Ett program ingår som en del av lösningen för att utföra åtgärder för machine learning-modellen i realtid. Programmet RealTimeDashboardApp har skapats och konfigurerats som en del av distributionen av lösningen. Program:

* Lyssnar efter en event hub-instansen som Stream Analytics för att publicera händelser i ett kontinuerligt.

    ![Stream Analytics-fråga för att publicera data](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Tar emot händelser. För varje händelse som tar emot det här programmet: 
   
   * Data bearbetas med hjälp av en machine learning-begäranden och svar bedömnings (RR) slutpunkt. RRS-slutpunkten publiceras automatiskt som en del av distributionen.
   * RRS-utdata har publicerats i en Power BI-datauppsättning genom att använda push-API: er.

Det här mönstret gäller även för scenarier där du vill integrera en line-of-business-program med analyser i realtid flödet. Några vanliga scenarier aviseringar, meddelanden och meddelanden.

Obs: data för RealtimeDashboardApp Visual Studio-lösningen är inte längre tillgänglig.

#### <a name="execute-the-real-time-dashboard-application"></a>**Kör realtidsinstrumentpanel-program**
1. Extrahera RealtimeDashboardApp och spara den lokalt.

    ![RealTimeDashboardApp mapp](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Kör programmet RealtimeDashboardApp.exe.

3. Ange din giltiga autentiseringsuppgifter för Power BI och välj **logga in**.  

    ![Realtidsinstrumentpanel app inloggningsfönstret](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Välj **Acceptera**.

    ![Realtidsinstrumentpanel slutliga inloggning-fönstret](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Om du vill att tömma Power BI-datauppsättning, kan du köra RealtimeDashboardApp med parametern ”flushdata”. 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Batchanalys
Avsikten är att visa hur Contoso motorer använder Azure compute-funktioner för att dra nytta av big data. Dessa data visar detaljerad information på utveckla mönster och användningsbeteende fordonshälsa. Den här informationen gör det möjligt att:

* Förbättra kundupplevelsen och blir billigare genom att tillhandahålla insikter om Driver vanor och bränsleeffektiva utveckla beteenden.
* Läs mer proaktivt om kunder och deras utveckla mönster för att styra affärsbeslut och ge bästa klassens produkter och tjänster.

I den här lösningen tillämpas följande mått:

* **Aggressivt Driver beteende**: identifierar trenden för den modeller, platser, utveckla villkor och tid att få insikter om aggressiva utveckla mönster. Contoso motorer kan använda dessa insikter för marknadsföringskampanjer för att introducera nya anpassade funktioner och användningsbaserad försäkringsbranschen.
* **Bränsleeffektiva utveckla beteende**: identifierar trenden för den modeller, platser, utveckla villkor och tid att få insikter om bränsleeffektiva utveckla mönster. Contoso motorer kan använda dessa insikter för marknadsföringskampanjer för att introducera nya funktioner och proaktiva rapporterar till drivrutiner för kostnadseffektiv och miljövänliga utveckla vanor.
* **Återkalla modeller**: identifierar modeller som kräver konsumenternas genom att operationalisera avvikelseidentifiering identifiering-machine learning-experiment.

Låt oss titta i detaljerna för var och en av de här måtten.

#### <a name="aggressive-driving-behavior-patterns"></a>**Aggressiva utveckla beteendemönster**

Den partitionerade vehicle signaler och diagnostiska data bearbetas i AggresiveDrivingPatternPipeline, som visas i följande arbetsflöde. Hive används för att fastställa modellerna, plats, fordon, utveckla villkor och andra parametrar som uppvisar aggressiva utveckla mönster.

![Aggressiva utveckla mönster för arbetsflöde](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Aggressiva utveckla mönster för Hive-fråga***

Hive-skriptet aggresivedriving.hql används för att analysera aggressiva utveckla mönster för villkoret. Det finns i mappen \demo\src\connectedcar\scripts hämtade zip-filen. 

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


Skriptet använder en kombination av en vehicle överföring gear position, bromsar cyklar status och hastighet för att identifiera reckless/aggressiva utveckla beteende baserat på bromsar mönster i hög hastighet. 

När pipelinen körs har, kan du se följande partitioner som skapas i ditt lagringskonto i connectedcar-behållaren:

![AggressiveDrivingPatternPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Bränsleeffektiva utveckla beteendemönster**

Den partitionerade vehicle signaler och diagnostiska data bearbetas i FuelEfficientDrivingPatternPipeline, som visas i följande arbetsflöde. Hive används för att fastställa modellerna, plats, fordon, utveckla villkor och andra egenskaper som uppvisar bränsleeffektiva utveckla mönster.

![Bränsleeffektiva utveckla mönster](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Bränsleeffektiva utveckla mönster för Hive-fråga***

Hive-skriptet fuelefficientdriving.hql används för att analysera bränsleeffektiva utveckla mönster för villkoret. Det finns i mappen \demo\src\connectedcar\scripts hämtade zip-filen. 

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


Skriptet använder en kombination av en vehicle överföring gear position, bromsar cyklar status, hastighet och accelerator pedal möjlighet att identifiera bränsleeffektiva utveckla beteende baserat på acceleration, bromsar och öka hastigheten på mönster. 

När pipelinen körs har, kan du se följande partitioner som skapas i ditt lagringskonto i connectedcar-behållaren:

![FuelEfficientDrivingPatternPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Återkalla modellens förutsägelser**

Machine learning-experiment är etablerad och publiceras som en webbtjänst som en del av distributionen av lösningen. Batchbedömnings-slutpunkten används i det här arbetsflödet. Den har registrerats som en data factory-länkade tjänst och operationaliserat med hjälp av data factory batchbedömnings-aktivitet.

![Machine learning-slutpunkt](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

Den registrerade länkade tjänsten används i DetectAnomalyPipeline och använda till med hjälp av modellen för identifiering av avvikelser. 

![Machine learning batchbedömningsaktivitet i data factory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Några steg utförs i denna pipeline för förberedelse av data så att den kan ska operationaliseras med batchbedömnings-webbtjänsten. 

![DetectAnomalyPipeline för förutsägelse återkallanden](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Avvikelseidentifiering identifiering av Hive-fråga***

När poängsättningen är klar, bearbetar en HDInsight-aktivitet och sammanställer data som modellen som kategoriserats som avvikelser. Modellen använder en sannolikhetspoäng på 0.60 eller högre.

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


När pipelinen körs har, kan du se följande partitioner som skapas i ditt lagringskonto i connectedcar-behållaren:

![DetectAnomalyPipeline utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Publicera

### <a name="real-time-analysis"></a>Realtidsanalys
Någon av frågorna i Stream Analytics-jobbet publicerar händelser till en utdata event hub-instansen. 

![Stream Analytics-jobb som publiceras till en händelsehubbinstans för utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

Följande Stream Analytics-frågan används för att publicera till event hub-instansen för utdata:

![Stream Analytics-fråga för att publicera till event hub-instansen för utdata](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Den här strömmen av händelser som förbrukas av RealTimeDashboardApp som ingår i lösningen. Det här programmet använder de machine learning-webbtjänst för begäranden och svar för bedömning i realtid. Den publicerar gällande data till en Power BI-datauppsättning för användning. 

### <a name="batch-analysis"></a>Batchanalys
Resultatet av batch- och realtidsbearbetning publiceras till Azure SQL Database-tabeller för användning. SQL-servern, databasen och tabeller skapas automatiskt som en del av installationsskriptet. 

Resultat för batch-bearbetning kopieras till arbetsflödet för data mart.

![Satsvis bearbetning resultaten till arbetsflödet för data mart](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

Stream Analytics-jobbet har publicerats till dataarkivet.

![Stream Analytics-jobb som publicerats till dataarkivet](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Data mart-inställningen är i Stream Analytics-jobb.

![Data mart-inställningen i Stream Analytics-jobb](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Förbruka
Powerbi ger den här lösningen en omfattande instrumentpanel för data i realtid och visualisering för förutsägelseanalys. 

Sista instrumentpanelen ser ut som i följande exempel:

![Power BI-instrumentpanel](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Sammanfattning
Det här dokumentet innehåller en detaljerad nedåt i Vehicle Telemetry Analytics-lösning. Lambda-arkitekturmönster används för att i realtid och batch-analys med förutsägelser och åtgärder. Det här mönstret som gäller för en mängd olika användningsområden som kräver heta sökvägen (realtid) och kalla sökvägen (batch). 

### <a name="references"></a>Referenser

* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [Azure Event Hubs-SDK för inhämtning](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Azure Data Factory data movement-funktioner](../../data-factory/copy-activity-overview.md)
* [Azure Data Factory .NET-aktivitet](../../data-factory/transform-data-using-dotnet-custom-activity.md)
* [Azure Data Factory .NET-aktivitet Visual Studio-lösning som används för att förbereda exempeldata](https://go.microsoft.com/fwlink/?LinkId=717077) 
