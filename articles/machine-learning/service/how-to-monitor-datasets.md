---
title: Analysera och övervaka data vid data mängder (för hands version)
titleSuffix: Azure Machine Learning
description: Skapa Azure Machine Learning data uppsättnings övervakare (för hands version), övervaka för data drift i data uppsättningar och konfigurera aviseringar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 24b9b120240ffc6f7dd2252d12c9f8af2bcfafbc
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049178"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Identifiera data avvikelser (för hands version) på data uppsättningar
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig att skapa Azure Machine Learning data uppsättnings övervakare (för hands version), övervaka data drift och statistiska förändringar i data uppsättningar och konfigurera aviseringar.

Med Azure Machine Learning data uppsättnings övervakare kan du:
* **Analysera driften i dina data** för att förstå hur den ändras med tiden.
* **Övervaka modell data** för skillnader mellan utbildning och betjäning av data uppsättningar.
* **Övervaka nya data** för skillnader mellan alla data uppsättningar för bas linjen och målet.
* **Profil funktioner i data** för att spåra hur statistik egenskaper ändras med tiden.
* **Konfigurera aviseringar om data drift** för tidiga varningar till potentiella problem. 

Mått och insikter är tillgängliga via den [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) -resurs som är associerad med arbets ytan Azure Machine learnings tjänst.

> [!Important]
> Observera att övervakning av data med SDK är tillgängligt i alla versioner, samtidigt som du övervakar data genom att använda Studio på webben bara Enterprise Edition.

## <a name="prerequisites"></a>Krav

Om du vill skapa och arbeta med data uppsättnings övervakare behöver du:
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.
* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller paketet azureml-DataSets.
* Strukturerad (tabell) data med en tidsstämpel som anges i fil Sök vägen, fil namnet eller kolumnen i data.

## <a name="what-is-data-drift"></a>Vad är data avvikelser?

I samband med Machine Learning är data driften ändringen i modell indata som leder till modell prestanda försämring. Det är en av de främsta orsakerna till modell precisionen över tid, vilket innebär att övervakningen av data driften hjälper till att identifiera problem med modell prestanda.

Orsaker till data avvikelse är: 

- Ändringar av överordnade processer, till exempel en sensor som byts ut, ändrar mått enheterna från tum till centimeter. 
- Data kvalitets problem, till exempel en bruten sensor, läser alltid 0.
- Naturlig drift av data, t. ex. medel temperatur förändringar med säsongerna.
- Ändring i relationen mellan funktioner eller covariate Shift. 

Med Azure Machine Learning data uppsättnings övervakare kan du ställa in aviseringar som hjälper till med data avkänning i data uppsättningar över tid. 

### <a name="dataset-monitors"></a>Övervakare för data uppsättningar 

Du kan skapa en data uppsättnings Övervakare för att upptäcka och varna på nya data i en data uppsättning, analysera historiska data för drift och profilera nya data över tid. Datanings algoritmen ger ett övergripande mått på förändringar i data och indikering av vilka funktioner som är ansvariga för ytterligare undersökning. Data uppsättnings övervakare producerar ett antal andra mått genom profilering av nya data i `timeseries` data uppsättningen. Anpassade aviseringar kan konfigureras för alla mått som genereras av övervakaren genom [Azure Application insikter](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Data uppsättnings övervakare kan användas för att snabbt fånga in data problem och minska tiden för att felsöka problemet genom att identifiera sannolika orsaker.  

Det finns tre huvudsakliga scenarier för att konfigurera data uppsättnings övervakare i Azure Machine Learning.

Scenario | Beskrivning
---|---
Övervaka en modells servar data för att gå från modellens utbildnings data | Resultat från det här scenariot kan tolkas som övervakning av en proxy för modellens riktighet, eftersom modell noggrannheten försämras om de betjänar data från tränings data.
Övervakning av en Time Series-datauppsättning för avvikelse från en tidigare tids period. | Det här scenariot är allmänt och kan användas för att övervaka data uppsättningar som är överordnade eller underordnade modell skapande.  Mål data uppsättningen måste ha en tidsstämpelkolumn, medan bas linje data uppsättningen kan vara en tabell data uppsättning som har funktioner gemensamt med mål data uppsättningen.
Utföra analyser på tidigare data. | Detta kan användas för att förstå historiska data och meddela beslut i inställningar för data uppsättnings övervakare.

## <a name="how-dataset-can-monitor-data"></a>Hur data uppsättningen kan övervaka data

Med hjälp av Azure Machine Learning övervakas data driften via data uppsättningar. För att övervaka data, en bas linje uppsättning – vanligt vis är inlärnings data uppsättningen för en modell-angiven. En mål data uppsättning – vanligt vis modell indata, jämförs med tiden för din bas linje data uppsättning. Det innebär att mål data uppsättningen måste ha en angiven tidsstämpelkolumn.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Ange `timeseries` trait i mål data uppsättningen

Mål data uppsättningen måste ha `timeseries` traiten inställd på den genom att ange timestamp-kolumnen från en kolumn i data eller en virtuell kolumn härledd från Sök vägs mönstret för filerna. Detta kan göras via python SDK eller Azure Machine Learning Studio. En kolumn som representerar en "fin kornig"-tidstämpel måste anges för att lägga till `timeseries` trait i data uppsättningen. Om dina data är partitionerade i mappstrukturen med tidsinformation, till exempel {ÅÅÅÅ/MM/DD}, kan du skapa en virtuell kolumn med hjälp av banans mönster inställning och ange den som "grov kornig"-tidsstämpel för att förbättra vikten av tids serie funktionen. 

#### <a name="python-sdk"></a>Python SDK

Metoden [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) definierar data uppsättningens tidsstämpel-kolumn. 

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

Ett komplett exempel på hur du använder `timeseries`s egenskaper för data uppsättningar finns i [exempel på Notebook](https://aka.ms/azureml-tsd-notebook) eller [data uppsättningarna SDK-dokumentationen](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning-studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

Om du skapar din data uppsättning med Azure Machine Learning Studio, se till att sökvägen till dina data innehåller tidsstämpel-information, inkludera alla undermappar med data och ange partitionens format. 

I följande exempel tas alla data under undermappen *NoaaIsdFlorida/2019* och partitions formatet anger Tidsstämpelns år, månad och dag. 

[![partition format](media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

I **schema** inställningarna anger du kolumnen tidsstämpelkolumn från en virtuell eller verklig kolumn i den angivna data uppsättningen:

![Tidsstämpel](media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Inställningar för data uppsättnings övervakare

När du har skapat din data uppsättning med de angivna inställningarna för tidsstämpel är du redo att konfigurera data uppsättnings övervakaren.

De olika inställningarna för data uppsättnings övervakaren är uppdelade i tre grupper: **grundläggande information, inställningar för övervakning** och bakgrunds **fyllning**.

### <a name="basic-info"></a>Grundläggande information

Den här tabellen innehåller grundläggande inställningar som används för data uppsättnings övervakaren.

| Inställning | Beskrivning | Tips | Föränderlig | 
| ------- | ----------- | ---- | ------- | 
| Namn | Namnet på data uppsättnings övervakaren. | | Nej |
| Bas linje data uppsättning | Tabell data uppsättning som ska användas som bas linje för jämförelse av mål data uppsättningen över tid. | Bas linjens data uppsättning måste ha funktioner som är gemensamma för mål data uppsättningen. I allmänhet bör bas linjen anges till en modells utbildnings data uppsättning eller en sektor av mål data uppsättningen. | Nej |
| Mål data uppsättning | Tabell data uppsättning med en tidsstämpel-kolumn angiven som ska analyseras för data avvikelse | Mål data uppsättningen måste ha funktioner gemensamt med bas linje data uppsättningen och måste vara en `timeseries` data uppsättning som nya data läggs till i. Historiska data i mål data uppsättningen kan analyseras eller också kan nya data övervakas. | Nej | 
| Frequency | Detta är den frekvens som används för att schemalägga pipeline-jobbet och analysera historiska data om en hel fyllning körs. Alternativen omfattar varje dag, varje vecka eller varje månad. | Justera den här inställningen för att inkludera en jämförbar data storlek till bas linjen. | Nej | 
| Funktioner | Lista över funktioner som kommer att analyseras för data drift över tid | Ställ in till en modells utmatnings funktion (er) för att mäta begrepps avvikelsen. Inkludera inte funktioner som används naturligt över tid (månad, år, index osv.). Du kan fylla på och befintlig data riktnings övervakning när du har justerat listan med funktioner. | Ja | 
| Beräkningsmål | Azure Machine Learning Compute Target för att köra data uppsättnings övervaknings jobben. | | Ja | 

### <a name="monitor-settings"></a>Övervaka inställningar

De här inställningarna gäller för den schemalagda data behandlings övervaknings pipelinen som ska skapas. 

| Inställning | Beskrivning | Tips | Föränderlig | 
| ------- | ----------- | ---- | ------- |
| Aktivera | Aktivera eller inaktivera schemat i pipelinen för data uppsättnings övervakaren | Inaktivera detta om du vill analysera historiska data med den egna fyllnings inställningen. Den kan aktive ras när data uppsättnings övervakaren har skapats. | Ja | 
| Svarstid | Tid i timmar tar det för data att komma in i data uppsättningen. Till exempel, om det tar tre dagar innan data kommer in i SQL DB Mina dataset-inkapslingar, ställer du in svars tiden på 72. | Kan inte ändras efter att data uppsättnings övervakaren har skapats | Nej | 
| E-postadresser | E-postadresser för aviseringar baserat på överträdelse av tröskelvärdet för data avvikelse i procent. | E-postmeddelanden skickas via Azure Monitor. | Ja | 
| Tröskelvärde | Tröskelvärde för data avvikelse i procent för e-postavisering. | Ytterligare aviseringar och händelser kan anges för många andra mått i arbets ytans associerade Application Insights-resurs. | Ja | 

### <a name="backfill-settings"></a>Inställningar för bakfyllning

De här inställningarna används för att köra en bakfyllning på tidigare data för data avvikelser.

| Inställning | Beskrivning | Tips |
| ------- | ----------- | ---- |
| Startdatum | Start datum för bakfyllnings jobbet. | | 
| Slutdatum | Slutdatum för bakfyllnings jobbet. | Detta får inte vara längre än 31 * frekvens tidsintervallen från start datumet. I en befintlig data uppsättnings övervakare kan måtten fyllas i för att analysera historiska data eller ersätta mått med uppdaterade inställningar. |

## <a name="create-dataset-monitors"></a>Skapa data uppsättnings övervakare 

Skapa data uppsättnings Övervakare för att identifiera och varna data vid en ny data uppsättning med Azure Machine Learning Studio eller python SDK. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning-studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

Om du vill konfigurera aviseringar på data uppsättnings övervakaren måste du ha Enterprise Edition-funktioner på arbets ytan som innehåller den data uppsättning som du vill skapa en Övervakare för. 

När du har bekräftat arbets ytans funktion går du till start sidan för Studio och väljer fliken Data uppsättningar till vänster. Välj data uppsättnings övervakare.

![Övervaka lista](media/how-to-monitor-datasets/monitor-list.png)

Klicka på knappen **+ Skapa Övervakare** och fortsätt att gå igenom guiden genom att klicka på **Nästa**.

![Konfigurationsguide](media/how-to-monitor-datasets/wizard.png)

Den resulterande data uppsättnings övervakaren kommer att visas i listan. Välj den för att gå till den övervakarens informations sida.

### <a name="from-python-sdk"></a>Från python SDK

Mer information finns i [referens dokumentationen för python SDK för data](/python/api/azureml-datadrift/azureml.datadrift) drift. 

Här följer ett exempel på hur du skapar en data uppsättnings övervakare med python SDK

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

Ett komplett exempel på hur du konfigurerar en `timeseries` data uppsättning och data riktnings detektor finns i vårt [exempel Notebook](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Förstå data avvikelse resultat

Data övervakaren producerar två resultat grupper: avvikelse översikt och funktions information. Följande animering visar tillgängliga riktnings övervaknings diagram baserat på vald funktion och mått. 

![Demonstrationsvideo](media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Avvikelse översikt

Avsnittet **avvikelse översikt** innehåller insikter på toppnivå för data drift och vilka funktioner som bör undersökas ytterligare. 

| Mått | Beskrivning | Tips | 
| ------ | ----------- | ---- | 
| Data riktnings storlek | Tilldelas i procent mellan bas linjen och mål data uppsättningen över tid. Sträcker sig från 0 till 100 där 0 anger identiska data mängder och 100 anger att den Azure Machine Learning data drifts kapacitet kan fullständigt meddela de två data uppsättningarna. | Brus i exakt uppmätt procents ATS förväntas på grund av maskin inlärnings tekniker som används för att generera den här storleken. | 
| Drift bidrag efter funktion | Bidraget för varje funktion i mål data uppsättningen till uppmätt drifts storlek. |  På grund av covariate Shift behöver inte den underliggande distributionen av en funktion nödvändigt vis ändra för att ha en relativt hög funktions betydelse. | 

Följande bild är ett exempel på diagram som visas i **avvikelse översikten** i Azure Machine Learning Studio, vilket resulterar i en [NOAA integrerad Surface-data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Data samplades till `stationName contains 'FLORIDA'`, med januari 2019 som används som bas linje data uppsättning och alla 2019-data som används som mål.
 
![Avvikelse översikt](media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Funktions information

Avsnittet **funktions information** innehåller insikter på funktions nivå i ändringen av den valda funktionens distribution, samt annan statistik, över tid. 

Mål data uppsättningen är också profilerad över tid. Det statistiska avståndet mellan bas linje fördelningen för varje funktion jämförs med mål data uppsättningen över tid, vilket är konceptuellt likt datans omfattnings storlek med undantag för en enskild funktion. Min, max och medelvärde är också tillgängliga. 

Om du klickar på en data punkt i diagrammet i Azure Machine Learning Studio ändras distributionen av funktionen som visas. Som standard visas distribution av bas linjens data uppsättning och den senaste körnings distributionen av samma funktion. 

Dessa mått kan också hämtas i python SDK via metoden `get_metrics()` på ett `DataDriftDetector`-objekt. 

#### <a name="numeric-features"></a>Numeriska funktioner 

Numeriska funktioner profilerade i varje data uppsättnings övervakare körs. Följande visas i Azure Machine Learning Studio. Sannolikhets täthet visas för fördelningen.

| Mått | Beskrivning |  
| ------ | ----------- |  
| Wasserstein avstånd | Minsta arbets mängd för att transformera bas linje distribution till mål distributionen. |
| Genomsnitts värde | Genomsnittligt värde för funktionen. |
| Minsta värde | Det minsta värdet för funktionen. |
| Max värde | Det maximala värdet för funktionen. |

![Funktions information numerisk](media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Kategoriska-funktioner 

Numeriska funktioner profilerade i varje data uppsättnings övervakare körs. Följande visas i Azure Machine Learning Studio. Ett histogram visas för fördelningen.

| Mått | Beskrivning |  
| ------ | ----------- |  
| Euclidian avstånd | Geometriskt avstånd mellan bas linje-och mål distributioner. |
| Unika värden | Antal unika värden (kardinalitet) för funktionen. |


![Funktions information kategoriska](media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Mått, aviseringar och händelser

Mått kan frågas i den [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) -resurs som är kopplad till Machine Learning-arbetsytan. Som ger åtkomst till alla funktioner i Application Insights, inklusive inställningar för anpassade aviserings regler och åtgärds grupper för att utlösa en åtgärd som, till exempel en e-post/SMS/push/röst eller Azure function. Mer information finns i den fullständiga Application Insights dokumentationen. 

Kom igång genom att gå till Azure Portal och välja **översikts** sidan för din arbets yta.  Den tillhör ande Application Insights resursen är längst till höger:

[Översikt över ![Azure Portal](media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Välj loggar (analys) under övervakning i den vänstra rutan:

![Översikt över Application Insights](media/how-to-monitor-datasets/ai-overview.png)

Data uppsättnings övervaknings måtten lagras som `customMetrics`. Du kan skriva och köra en enkel fråga när du har konfigurerat en data uppsättnings Övervakare för att visa dem:

[![Log Analytics-fråga](media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

När du har identifierat mått för att ställa in aviserings regler skapar du en ny aviserings regel:

![Ny varnings regel](media/how-to-monitor-datasets/alert-rule.png)

Du kan använda en befintlig åtgärds grupp eller skapa en ny för att definiera vilken åtgärd som ska vidtas när angivna villkor uppfylls:

![Ny åtgärds grupp](media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Felsökning

Begränsningar och kända problem:

* Tidsintervallet för bakfyllnings jobb är begränsat till 31 intervall för övervaknings frekvens inställningen. 
* Begränsning av 200 funktioner, om inte en funktions lista inte har angetts (alla funktioner används).
* Beräknings storleken måste vara tillräckligt stor för att data ska kunna hanteras. 
* Se till att data uppsättningen har data inom start-och slutdatum för en specifik övervaknings körning.

Kolumner eller funktioner i data uppsättningen klassificeras som kategoriska eller numeriska baserat på villkoren i följande tabell. Om funktionen inte uppfyller dessa villkor, t. ex. en kolumn av typen String med > 100 unika värden, släpps funktionen från vår algoritm för data avvikelser, men är fortfarande profilerad. 

| Funktions typ | Datatyp | Tillstånd | Begränsningar | 
| ------------ | --------- | --------- | ----------- |
| Kategoriska | sträng, bool, int, Float | Antalet unika värden i funktionen är mindre än 100 och mindre än 5% av antalet rader. | Null behandlas som sin egen kategori. | 
| Numeriskt | int, Float | Av en numerisk datatyp och inte uppfyller villkoren för en kategoriska-funktion. | Funktionen utelämnas om > 15% av värdena är null. | 

## <a name="next-steps"></a>Nästa steg

* Gå till [Azure Machine Learning Studio](https://ml.azure.com) eller [python Notebook](https://aka.ms/datadrift-notebook) för att ställa in en data uppsättnings övervakare.
* Se hur du konfigurerar data drift på [modeller som har distribuerats till Azure Kubernetes-tjänsten](how-to-monitor-data-drift.md).
* Konfigurera data uppsättnings riktnings övervakare med [Event Grid](how-to-use-event-grid.md). 