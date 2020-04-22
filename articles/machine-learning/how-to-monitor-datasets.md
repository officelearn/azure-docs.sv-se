---
title: Analysera och övervaka datadrift på datauppsättningar (förhandsgranskning)
titleSuffix: Azure Machine Learning
description: Skapa Azure Machine Learning-datauppsättningar (förhandsversion), övervaka datadrift i datauppsättningar och konfigurera aviseringar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: e49c621d92a8aa604b5f95291c5d80c0141f41dd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682727"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Identifiera datadrift (förhandsgranskning) på datauppsättningar
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar Azure Machine Learning-datauppsättningsövervakare (förhandsversion), övervakar för datadrift och statistiska ändringar i datauppsättningar och ställer in aviseringar.

Med Azure Machine Learning-datauppsättningsövervakare kan du:
* **Analysera drift i dina data** för att förstå hur det ändras med tiden.
* **Övervaka modelldata** för skillnader mellan utbildning och serveringsdatauppsättningar.
* **Övervaka nya data** för skillnader mellan baslinje- och måldatauppsättning.
* **Profilfunktioner i data** för att spåra hur statistiska egenskaper förändras med tiden.
* **Ställ in aviseringar om datadrift** för tidiga varningar till potentiella problem. 

Mått och insikter är tillgängliga via Azure [Application Insights-resursen](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) som är associerad med Azure Machine Learning-arbetsytan.

> [!Important]
> Observera att övervakning av data drift med SDK är tillgänglig i alla utgåvor, medan övervakning data glida genom studion på webben är Enterprise edition bara.

## <a name="prerequisites"></a>Krav

Om du vill skapa och arbeta med datauppsättningsövervakare behöver du:
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.
* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* [Azure Machine Learning SDK för Python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller azureml-dataset-paketet.
* Strukturerade (tabell) data med en tidsstämpel som anges i filsökvägen, filnamnet eller kolumnen i data.

## <a name="what-is-data-drift"></a>Vad är datadrift?

I samband med maskininlärning är datadrift förändringen i modellindata som leder till försämrad modellprestanda. Det är en av de främsta anledningarna till att modellens noggrannhet försämras med tiden, vilket innebär att övervakning av datadrift hjälper till att identifiera prestandaproblem för modeller.

Orsaker till datadrift inkluderar: 

- Uppströms processändringar, till exempel en sensor som ersätts som ändrar måttenheterna från tum till centimeter. 
- Problem med datakvalitet, till exempel en trasig sensor som alltid läser 0.
- Naturlig drift i data, såsom medeltemperatur förändras med årstiderna.
- Ändra i relation mellan funktioner eller kovvariatskift. 

Med Azure Machine Learning-datauppsättningsövervakare kan du ställa in aviseringar som hjälper till att upptäcka datadrift i datauppsättningar över tid. 

### <a name="dataset-monitors"></a>Övervakare av datauppsättning 

Du kan skapa en datauppsättningsövervakare för att identifiera och avisera data drift på nya data i en datauppsättning, analysera historiska data för drift och profilera nya data över tid. Datadriftalgoritmen ger ett övergripande mått på förändring i data och indikation på vilka funktioner som är ansvariga för vidare undersökning. Dataset övervakare producera ett antal andra mått genom `timeseries` att profilera nya data i datauppsättningen. Anpassad avisering kan ställas in på alla mått som genereras av övervakaren via [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Dataset övervakare kan användas för att snabbt fånga dataproblem och minska tiden för att felsöka problemet genom att identifiera sannolika orsaker.  

Begreppsmässigt finns det tre primära scenarier för att konfigurera datauppsättningsövervakare i Azure Machine Learning.

Scenario | Beskrivning
---|---
Övervaka en modells serveringsdata för drift från modellens träningsdata | Resultat från det här scenariot kan tolkas som att övervaka en proxy för modellens noggrannhet, med tanke på att modellens noggrannhet försämras om serveringsdata driver från träningsdata.
Övervaka en tidsseriedatauppsättning för drift från en tidigare tidsperiod. | Det här scenariot är mer allmänt och kan användas för att övervaka datauppsättningar som är involverade uppströms eller nedströms modellbyggen.  Måldatauppsättningen måste ha en tidsstämpelkolumn, medan baslinjedatauppsättningen kan vara valfri tabelldatauppsättning som har funktioner som är gemensamma med måldatauppsättningen.
Utför analyser på tidigare data. | Det här scenariot kan användas för att förstå historiska data och informera beslut i inställningar för datauppsättningsövervakare.

## <a name="how-dataset-can-monitor-data"></a>Hur datauppsättning kan övervaka data

Med hjälp av Azure Machine Learning övervakas datadrift via datauppsättningar. För att övervaka för datadrift anges en baslinjedatauppsättning - vanligtvis träningsdatauppsättningen för en modell. En måldatauppsättning - vanligtvis modellindata - jämförs med tiden med baslinjedatauppsättningen. Den här jämförelsen innebär att din måldatauppsättning måste ha en tidsstämpelkolumn angiven.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Ange `timeseries` egenskapen i måldatauppsättningen

Måldatauppsättningen måste ha `timeseries` egenskapen inställd på den genom att ange tidsstämpelkolumnen antingen från en kolumn i data eller en virtuell kolumn som härleds från filernas sökvägsmönster. Detta kan göras via Python SDK eller Azure Machine Learning studio. En kolumn som representerar en tidsstämpel för `timeseries` "finkornig" måste anges för att lägga till egenskaper i datauppsättningen. Om dina data är uppdelade i mappstrukturen med tidsinformation, till exempel {yyyy/MM/dd}, kan du skapa en virtuell kolumn genom sökvägsmönstret och ställa in den som tidsstämpeln "grovkorn" för att förbättra betydelsen av tidsseriefunktioner. 

#### <a name="python-sdk"></a>Python SDK

Klassens [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) metod definierar tidsstämpelkolumnen för datauppsättningen. 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Ett fullständigt exempel på `timeseries` hur du använder datauppsättningarnas egenskap finns i [exempelboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) eller [datauppsättningar SDK-dokumentationen](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning-studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Om du skapar din datauppsättning med Azure Machine Learning studio kontrollerar du att sökvägen till dina data innehåller tidsstämpelinformation, inkluderar alla undermappar med data och anger partitionsformatet. 

I följande exempel tas alla data under undermappen *NoaaIsdFlorida/2019* och partitionsformatet anger tidsstämpelns år, månad och dag. 

[![Partitionsformat](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

I **schemainställningarna** anger du tidsstämpelkolumnen från en virtuell eller verklig kolumn i den angivna datauppsättningen:

![Tidsstämpel](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Inställningar för datauppsättningsövervakaren

När du har skapat datauppsättningen med de angivna tidsstämpelinställningarna är du redo att konfigurera datauppsättningsövervakaren.

De olika inställningarna för datauppsättningsövervakaren är uppdelade i tre grupper: **Grundläggande information, Bildskärmsinställningar** och **Inställningar för återfyllning**.

### <a name="basic-info"></a>Grundläggande information

Den här tabellen innehåller grundläggande inställningar som används för datauppsättningsövervakaren.

| Inställning | Beskrivning | Tips | Föränderlig | 
| ------- | ----------- | ---- | ------- | 
| Namn | Namnet på datauppsättningsövervakaren. | | Inga |
| Datauppsättning för baslinje | Tabelldatauppsättning som ska användas som baslinje för jämförelse av måldatauppsättningen över tid. | Baslinjedatauppsättningen måste ha funktioner som är gemensamma med måldatauppsättningen. I allmänhet bör baslinjen anges till en modells träningsdatauppsättning eller en del av måldatauppsättningen. | Inga |
| Måldatauppsättning | Tabelldatauppsättning med angiven tidsstämpelkolumn som ska analyseras för datadrift. | Måldatauppsättningen måste ha funktioner som är gemensamma med baslinjedatauppsättningen och bör vara en `timeseries` datauppsättning som nya data läggs till. Historiska data i måldatauppsättningen kan analyseras eller så kan nya data övervakas. | Inga | 
| Frequency | Frekvensen som ska användas för att schemalägga pipeline-jobbet och analysera historiska data om du kör en återfyllning. Alternativen inkluderar dagligen, veckovis eller månadsvis. | Justera den här inställningen så att den innehåller en jämförbar datastorlek till baslinjen. | Inga | 
| Funktioner | Lista över funktioner som kommer att analyseras för data drift över tiden. | Ställ in på en modells utdatafunktion(er) för att mäta konceptdrift. Ta inte med funktioner som naturligt driver över tiden (månad, år, index, etc.). Du kan fylla på igen och befintliga data drift övervaka efter justering av listan över funktioner. | Ja | 
| Beräkningsmål | Azure Machine Learning beräkningsmål för att köra datauppsättningen övervaka jobb. | | Ja | 

### <a name="monitor-settings"></a>Bildskärmsinställningar

Dessa inställningar gäller för den schemalagda datauppsättningsövervakarpipelen, som kommer att skapas. 

| Inställning | Beskrivning | Tips | Föränderlig | 
| ------- | ----------- | ---- | ------- |
| Aktivera | Aktivera eller inaktivera schemat på datauppsättningsövervakarpipelinen | Inaktivera schemat för att analysera historiska data med inställningen för återfyllning. Det kan aktiveras när datauppsättningsövervakaren har skapats. | Ja | 
| Svarstid | Tid, i timmar, det tar för data att komma fram i datauppsättningen. Om det till exempel tar tre dagar för data att komma fram till SQL DB-datauppsättningen kapslar in, anger du svarstiden till 72. | Det går inte att ändra när datauppsättningsövervakaren har skapats | Inga | 
| E-postadresser | E-postadresser för aviseringar baserat på brott mot tröskelvärdet för dataavdriftprocent. | E-postmeddelanden skickas via Azure Monitor. | Ja | 
| Tröskelvärde | Tröskelvärdet för datadriftprocent för e-postavisering. | Ytterligare aviseringar och händelser kan ställas in på många andra mått i arbetsytans associerade Application Insights-resurs. | Ja | 

### <a name="backfill-settings"></a>Inställningar för återfyllning

Dessa inställningar är för att köra en återfyllning på tidigare data för data drift mått.

| Inställning | Beskrivning | Tips |
| ------- | ----------- | ---- |
| Startdatum | Startdatum för återfyllningsjobbet. | | 
| Slutdatum | Slutdatum för återfyllningsjobbet. | Slutdatumet får inte vara mer än 31*frekvensenheter från startdatumet. På en befintlig datauppsättningsövervakare kan mått fyllas i för att analysera historiska data eller ersätta mått med uppdaterade inställningar. |

## <a name="create-dataset-monitors"></a>Skapa datauppsättningsövervakare 

Skapa datauppsättningsövervakare för att identifiera och avisera data drift på en ny datauppsättning med Azure Machine Learning studio eller Python SDK. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning-studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Om du vill konfigurera aviseringar på datauppsättningsövervakaren måste arbetsytan som innehåller den datauppsättning som du vill skapa en övervakare för ha Enterprise Edition-funktioner. 

När arbetsytans funktioner har bekräftats navigerar du till studions startsida och väljer fliken Datauppsättningar till vänster. Välj Dataset-övervakare.

![Övervaka lista](./media/how-to-monitor-datasets/monitor-list.png)

Klicka på knappen **+Skapa bildskärm** och fortsätt genom guiden genom att klicka på **Nästa**.

![Guiden](./media/how-to-monitor-datasets/wizard.png)

Den resulterande datauppsättningsövervakaren visas i listan. Välj den för att gå till den bildskärmens informationssida.

### <a name="from-python-sdk"></a>Från Python SDK

Mer information finns i [Python SDK:s referensdokumentation om datadrift.](/python/api/azureml-datadrift/azureml.datadrift) 

I följande exempel visas hur du skapar en datauppsättningsövervakare med Python SDK

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Ett fullständigt exempel på `timeseries` hur du konfigurerar en datauppsättning och dataavdriftdetektor finns i vår [exempelboks notebook](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Förstå datadrift resultat

Dataövervakaren producerar två grupper av resultat: Drift översikt och Funktionsinformation. Följande animering visar tillgängliga driftövervakardiagram baserat på den valda funktionen och måttet. 

![Demo video](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Drift översikt

Avsnittet **Drift översikt** innehåller insikter på högsta nivå om omfattningen av datadrift och vilka funktioner som bör undersökas ytterligare. 

| Mått | Beskrivning | Tips | 
| ------ | ----------- | ---- | 
| Data drift magnitud | Anges som en procentsats mellan original- och måldatauppsättningen över tid. Allt från 0 till 100 där 0 anger identiska datauppsättningar och 100 anger Azure Machine Learning data drift kapacitet kan helt skilja de två datauppsättningarna isär. | Buller i den exakta procentuella uppmätta förväntas på grund av att maskininlärningstekniker används för att generera denna magnitud. | 
| Drift bidrag efter funktion | Bidraget för varje funktion i måldatauppsättningen till den uppmätta avdriftsstorleken. |  På grund av kovvariat skift behöver den underliggande fördelningen av en funktion inte nödvändigtvis ändras för att ha relativt hög funktionsvikt. | 

Följande bild är ett exempel på diagram som visas i **Översiktsresultaten** i Azure Machine Learning, till följd av en återfyllning av [NOAA-integrerade Surface-data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Data togs prover `stationName contains 'FLORIDA'`på , med januari 2019 som baslinjedatauppsättning och alla 2019-data som användes som mål.
 
![Drift översikt](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Information om funktioner

Avsnittet **Funktionsinformation** innehåller insikter på funktionsnivå om ändringen i den valda funktionens distribution, liksom annan statistik, över tid. 

Måldatauppsättningen profileras också med tiden. Det statistiska avståndet mellan baslinjefördelningen för varje funktion jämförs med måldatauppsättningens övertid, vilket är begreppsmässigt likt datadriftsstorleken med undantag för att detta statistiska avstånd är för en enskild funktion. Min, max och medelvärde finns också. 

I Azure Machine Learning-studion justeras fördelningen av funktionen som visas i enlighet med detta om du klickar på en datapunkt i diagrammet. Som standard visas baslinjedatauppsättningens distribution och den senaste körningens distribution av samma funktion. 

Dessa mått kan också hämtas i Python SDK via `get_metrics()` metoden på ett `DataDriftDetector` objekt. 

#### <a name="numeric-features"></a>Numeriska funktioner 

Numeriska funktioner profileras i varje datauppsättningsövervakarkörning. Följande visas i Azure Machine Learning-studion. Sannolikhetstäthet visas för fördelningen.

| Mått | Beskrivning |  
| ------ | ----------- |  
| Wasserstein avstånd | Minsta mängd arbete för att omvandla baslinjefördelning till målfördelningen. |
| Medelvärde | Genomsnittligt värde för funktionen. |
| Minvärde | Minsta värde för funktionen. |
| Maxvärde | Maximalt värde för funktionen. |

![Numeriskt med funktionsinformation](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Kategoriska funktioner 

Numeriska funktioner profileras i varje datauppsättningsövervakarkörning. Följande visas i Azure Machine Learning-studion. Ett histogram visas för distributionen.

| Mått | Beskrivning |  
| ------ | ----------- |  
| Euklidisk avstånd | Geometriskt avstånd mellan baslinje- och målfördelningar. |
| Unika värden | Antal unika värden (kardinalitet) för funktionen. |


![Funktionsinformation kategorisk](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Mått, aviseringar och händelser

Mått kan efterfrågas i Azure [Application Insights-resursen](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) som är associerad med din maskininlärningsarbetsyta. Vilket ger åtkomst till alla funktioner i Application Insights, inklusive konfigurera för anpassade varningsregler och åtgärdsgrupper för att utlösa en åtgärd som en e-post/SMS/Push/Voice eller Azure-funktion. Mer information finns i den fullständiga dokumentationen för Application Insights. 

För att komma igång navigerar du till Azure-portalen och väljer din arbetsytas **översiktssida.**  Den associerade Application Insights-resursen finns längst till höger:

[![Översikt över Azure Portal](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Välj Loggar (Analytics) under Övervakning i den vänstra rutan:

![Översikt över programinsikter](./media/how-to-monitor-datasets/ai-overview.png)

Datauppsättningens övervakningsmått lagras `customMetrics`som . Du kan skriva och köra en fråga när du har konfigurerat en datauppsättningsövervakare för att visa dem:

[![Logganalysfråga](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

När du har identifierat mått för att ställa in varningsregler skapar du en ny varningsregel:

![Ny varningsregel](./media/how-to-monitor-datasets/alert-rule.png)

Du kan använda en befintlig åtgärdsgrupp eller skapa en ny för att definiera vilken åtgärd som ska vidtas när de inställda villkoren är uppfyllda:

![Ny åtgärdsgrupp](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Felsökning

Begränsningar och kända problem:

* Tidsintervallet för återfyllningsjobb är begränsade till 31 intervall av bildskärmens frekvensinställning. 
* Begränsning av 200 funktioner, såvida inte en funktionslista inte anges (alla funktioner används).
* Beräkningsstorleken måste vara tillräckligt stor för att hantera data. 
* Se till att datauppsättningen har data inom start- och slutdatum för en viss bildskärmskörning.
* Datauppsättningsövervakare fungerar bara på datauppsättningar som innehåller 50 rader eller fler. 

Kolumner eller funktioner i datauppsättningen klassificeras som kategoriska eller numeriska baserat på villkoren i följande tabell. Om funktionen inte uppfyller dessa villkor - till exempel en kolumn av typsträng med >100 unika värden - tas funktionen bort från vår datadriftalgoritm, men är fortfarande profilerad. 

| Funktionstyp | Datatyp | Villkor | Begränsningar | 
| ------------ | --------- | --------- | ----------- |
| Kategoriska | sträng, bool, int, flyta | Antalet unika värden i funktionen är mindre än 100 och mindre än 5 % av antalet rader. | Null behandlas som sin egen kategori. | 
| Numeriska | int, flyta | Värdena i funktionen är av en numerisk datatyp och uppfyller inte villkoret för en kategorisk funktion. | Funktionen har tappats om >15 % av värdena är null. | 

## <a name="next-steps"></a>Nästa steg

* Gå till [Azure Machine Learning studio](https://ml.azure.com) eller [Python-anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) för att konfigurera en datauppsättningsövervakare.
* Se hur du konfigurerar datadrift på [modeller som distribueras till Azure Kubernetes Service](how-to-monitor-data-drift.md).
* Konfigurera datauppsättningsavdriftövervakare med [händelserutnät](how-to-use-event-grid.md). 
