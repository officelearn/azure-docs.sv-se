---
title: Auto-träna en prognosmodell för tidsserier
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning för att träna en regressionsmodell för prognostisering i tidsserier med hjälp av automatiserad maskininlärning.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d4e36c0d3838af85768453496a51ecd295c22b93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081853"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Auto-träna en prognosmodell för tidsserier
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du tränar en regressionsmodell för prognostisering i tidsserier med hjälp av automatiserad maskininlärning i Azure Machine Learning. Konfigurera en prognosmodell liknar att ställa in en standard regressionsmodell med hjälp av automatiserad maskininlärning, men vissa konfigurationsalternativ och förbearbetningssteg finns för att arbeta med tidsseriedata. Följande exempel visar hur du:

* Förbereda data för tidsseriemodellering
* Konfigurera specifika tidsserieparametrar i ett [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objekt
* Kör förutsägelser med tidsseriedata

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Du kan använda automatiserad ML för att kombinera tekniker och metoder och få en rekommenderad, högkvalitativ tidsserieprognos. Ett automatiserat tidsserieexperiment behandlas som ett multivariat regressionsproblem. Tidigare tidsserievärden är "pivoterade" för att bli ytterligare dimensioner för regressor tillsammans med andra prediktorer.

Detta tillvägagångssätt, till skillnad från klassiska tidsseriemetoder, har en fördel av att naturligt införliva flera kontextuella variabler och deras förhållande till varandra under träning. I verkliga prognosprogram kan flera faktorer påverka en prognos. Till exempel vid prognostisering av försäljning, interaktioner mellan historiska trender, växelkurs och pris alla gemensamt driva försäljningsutfallet. En ytterligare fördel är att alla nya innovationer i regressionsmodeller omedelbart gäller för prognoser.

Du kan [konfigurera](#config) hur långt in i framtiden prognosen ska sträcka sig (prognoshorisonten), samt fördröjningar med mera. Automatiserad ML lär sig en enda, men ofta internt grenade modell för alla objekt i datauppsättningen och förutsägelsehorisonterna. Mer data är således tillgängliga för att uppskatta modellparametrar och generalisering till osynliga serier blir möjligt.

Funktioner som extraheras från träningsdata spelar en avgörande roll. Och automatiserade ML utför standardförbehandlingssteg och genererar ytterligare funktioner i tidsserier för att fånga säsongseffekter och maximera prediktiv noggrannhet.

## <a name="time-series-and-deep-learning-models"></a>Time-series och Deep Learning modeller


Automatiserad ML ger användare med både inbyggda tidsserier och djupinlärningsmodeller som en del av rekommendationssystemet. Bland dessa elever finns:
+ Profeten
+ Auto-ARIMA
+ PrognosTCN

Automatiserad ML: s djupinlärning möjliggör prognoser univariat och multivariata tidsserier data.

Deep learning-modeller har tre inneboende funktioner:
1. De kan lära sig av godtyckliga mappningar från ingångar till utdata
1. De stöder flera ingångar och utgångar
1. De kan automatiskt extrahera mönster i indata som sträcker sig över långa sekvenser

Med tanke på större data kan djupinlärningsmodeller, till exempel Microsofts ForecastTCN, förbättra poängen för den resulterande modellen. 

Inbyggda tidsserier elever tillhandahålls också som en del av automatiserade ML. Profeten fungerar bäst med tidsserier som har starka säsongseffekter och flera säsonger av historiska data. Profeten är korrekt & snabb, robust för avvikare, saknade data och dramatiska förändringar i din tidsserie. 

ArIMA (AutoRegressive Integrated Moving Average) är en populär statistisk metod för tidsserieprognoser. Denna teknik för prognoser används ofta i kortsiktiga prognosscenarier där data visar tecken på trender som cykler, som kan vara oförutsägbara och svåra att modellera eller förutse. Auto-ARIMA omvandlar dina data till stationära data för att få konsekventa och tillförlitliga resultat.

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta. Information om hur du skapar arbetsytan finns i [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* Den här artikeln förutsätter grundläggande förtrogenhet med att konfigurera ett automatiserat maskininlärningsexperiment. Följ [självstudien](tutorial-auto-train-models.md) eller instruktioner för [att](how-to-configure-auto-train.md) se de grundläggande automatiserade maskininlärningsexperimentdesignmönster.

## <a name="preparing-data"></a> Förbereda data

Den viktigaste skillnaden mellan en prognosregressionsaktivitetstyp och regressionsaktivitetstyp inom automatiserad maskininlärning är att inkludera en funktion i dina data som representerar en giltig tidsserie. En vanlig tidsserie har en väldefinierad och konsekvent frekvens och har ett värde vid varje provpunkt under en kontinuerlig tidsperiod. Tänk på följande ögonblicksbild `sample.csv`av en fil .

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Denna datauppsättning är ett enkelt exempel på dagliga försäljningsdata för ett företag som har två `week_of_year` olika butiker, A och B. Dessutom finns det en funktion för som gör det möjligt för modellen att upptäcka säsongsvariationer varje vecka. Fältet `day_datetime` representerar en ren tidsserie med `sales_quantity` daglig frekvens och fältet är målkolumnen för att köra förutsägelser. Läs data i en Pandas-dataram `to_datetime` och använd sedan funktionen `datetime` för att säkerställa att tidsserien är en typ.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

I det här fallet sorteras data redan stigande `day_datetime`efter tidsfältet . När du ställer in ett experiment ska du dock se till att den önskade tidskolumnen sorteras i stigande ordning för att skapa en giltig tidsserie. Anta att data innehåller 1 000 poster och gör en deterministisk delning av data för att skapa tränings- och testdatauppsättningar. Identifiera etikettkolumnens namn och ange det till etikett. I det här exemplet `sales_quantity`kommer etiketten att vara . Separera sedan etikettfältet `test_data` från `test_target` för att bilda uppsättningen.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> När du tränar en modell för att prognostisera framtida värden, se till att alla funktioner som används i utbildningen kan användas när du kör förutsägelser för din avsedda horisont. När du till exempel skapar en efterfrågeprognos, inklusive en funktion för aktuell aktiekurs, kan det öka utbildningsnoggrannheten avsevärt. Men om du tänker prognos med en lång horisont, kanske du inte kan exakt förutsäga framtida lagervärden som motsvarar framtida tidsserier punkter, och modellen noggrannhet kan lida.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Konfigurera och köra experiment

För prognosuppgifter använder automatiserad maskininlärning förbearbetnings- och uppskattningssteg som är specifika för tidsseriedata. Följande förbearbetningssteg kommer att utföras:

* Identifiera exempelfrekvens i tidsserier (till exempel timme, dag, vecka) och skapa nya poster för frånvarande tidspunkter för att göra serien kontinuerlig.
* Tillskriv saknade värden i målet (via vidarefyllning) och funktionskolumner (med hjälp av mediankolumnvärden)
* Skapa kornbaserade funktioner för att aktivera fasta effekter i olika serier
* Skapa tidsbaserade funktioner som hjälper dig att lära sig säsongsmönster
* Koda kategoriska variabler till numeriska kvantiteter

Objektet [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) definierar de inställningar och data som krävs för en automatiserad maskininlärningsuppgift. I likhet med ett regressionsproblem definierar du standardträningsparametrar som aktivitetstyp, antal iterationer, träningsdata och antal korsvalideringar. För prognosaktiviteter finns det ytterligare parametrar som måste anges som påverkar experimentet. I följande tabell förklaras varje parameter och dess användning.

| Parameternamn&nbsp; | Beskrivning | Krävs |
|-------|-------|-------|
|`time_column_name`|Används för att ange datetime-kolumnen i indata som används för att skapa tidsserierna och dra slutsatsen att dess frekvens.|✓|
|`grain_column_names`|Namn som definierar enskilda seriegrupper i indata. Om kornet inte har definierats antas datauppsättningen vara en tidsserie.||
|`max_horizon`|Definierar den maximala önskade prognoshorisonten i enheter av tidsseriefrekvens. Enheter baseras på tidsintervallet för dina träningsdata, till exempel månadsvis, varje vecka som prognosmakaren ska förutsäga ut.|✓|
|`target_lags`|Antal rader som ska fördröjs målvärdena baserat på datafrekvensen. Fördröjningen representeras som en lista eller ett enda heltal. Fördröjning bör användas när förhållandet mellan de oberoende variablerna och den beroende variabeln inte matchar eller korrelerar som standard. Till exempel, när man försöker prognostisera efterfrågan på en produkt, efterfrågan i en månad kan bero på priset på specifika råvaror 3 månader före. I det här exemplet kanske du vill släpa målet (efterfrågan) negativt med 3 månader så att modellen tränar på rätt relation.||
|`target_rolling_window_size`|*n* historiska perioder att använda för att generera prognostiserade värden, <= träningsuppsättningsstorlek. Om utelämnas, *n* är full utbildning inställd storlek. Ange den här parametern när du bara vill ta hänsyn till en viss mängd historik när du tränar modellen.||
|`enable_dnn`|Aktivera prognostisering DNN.||

Mer information finns i [referensdokumentationen.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)

Skapa tidsserieinställningarna som ett ordlisteobjekt. Ange `time_column_name` fältet `day_datetime` i datauppsättningen. Definiera `grain_column_names` parametern för att säkerställa att **två separata tidsseriegrupper** skapas för data. en för butik A och B. `max_horizon` Slutligen ställer du in 50 för att förutsäga för hela testuppsättningen. Ange ett prognosfönster till 10 perioder med `target_rolling_window_size`och ange en enda fördröjning `target_lags` på målvärdena för två perioder framåt med parametern. Det rekommenderas att `max_horizon` `target_rolling_window_size` ställa `target_lags` in , och att "auto" som automatiskt kommer att upptäcka dessa värden för dig. I exemplet nedan har "auto"-inställningar använts för dessa parametrar. 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> Automatiserade förbearbetningssteg för maskininlärning (funktionsnormalisering, hantering av data som saknas, konvertering av text till numeriska osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma förbearbetningssteg som tillämpas under träningen automatiskt på dina indata.

Genom att `grain_column_names` definiera kodavsnittet ovan skapar AutoML två separata tidsseriegrupper, även kallade flera tidsserier. Om inget korn har definierats antar AutoML att datauppsättningen är en enda tidsserie. Mer information om enstaka tidsserier finns i [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Skapa nu `AutoMLConfig` ett standardobjekt, `forecasting` ange aktivitetstypen och skicka experimentet. När modellen är klar hämtar du den bästa körningsiterationen.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Se [prognostiseringsprovets anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) för detaljerade kodexempel på avancerad prognoskonfiguration, inklusive:

* [semester upptäckt och featurization](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [validering av korsvalidering av rullande ursprung](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurerbara fördröjningar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [sammanlagt funktioner för rullande fönster](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Dnn](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Konfigurera ett DNN-aktiverat prognosexperiment

> [!NOTE]
> DNN-stöd för prognoser i automatiserad maskininlärning finns i förhandsversion och stöds inte för lokala körningar.

För att kunna utnyttja DNN för prognoser måste `enable_dnn` du ställa in parametern i AutoMLConfig till true. 

Vi rekommenderar att du använder ett AML Compute-kluster med GPU SKU:er och minst två noder som beräkningsmål. För att ge tillräckligt med tid för att DNN-träningen ska slutföras rekommenderar vi att du ställer in tidsgränsen för experimentet på minst ett par timmar.
Mer information om AML-beräknings- och VM-storlekar som innehåller GPU:er finns i [dokumentationen för AML Compute](how-to-set-up-training-targets.md#amlcompute) och [GPU-optimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Visa [anteckningsboken dryckesproduktionsprognoser](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) för ett detaljerat kodexempel som använder DNN.

### <a name="view-feature-engineering-summary"></a>Visa sammanfattning av funktionsteknik

För tidsserieuppgifter i automatiserad maskininlärning kan du visa information från funktionsteknikprocessen. Följande kod visar varje rå funktion tillsammans med följande attribut:

* Namn på funktionen Rå
* Antal konstruerade funktioner som bildats av den här råa funktionen
* Typ har upptäckts
* Om funktionen har tagits bort
* Lista över funktionsomvandlingar för råfunktionen

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognostisering med bästa modell

Använd den bästa modelliterationen för att prognostisera värden för testdatauppsättningen.

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Alternativt kan du använda `forecast()` funktionen `predict()`i stället för , vilket tillåter specifikationer för när förutsägelser ska starta. I följande exempel ersätter du `y_pred` först `NaN`alla värden med . Det prognostiserade ursprunget kommer att vara i slutet av utbildningsdata i detta fall, som det normalt skulle vara när du använder `predict()`. Men om du bara ersatte `y_pred` den `NaN`andra halvan av med , skulle funktionen lämna de `NaN` numeriska värdena i första halvlek oförändrade, men prognostisera värdena i andra halvlek. Funktionen returnerar både de prognostiserade värdena och de justerade funktionerna.

Du kan också `forecast_destination` använda `forecast()` parametern i funktionen för att prognostisera värden fram till ett angivet datum.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Beräkna RMSE (rotmedelsrutat `actual_labels` fel) mellan de faktiska `predict_labels`värdena och de prognostiserade värdena i .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Nu när den övergripande modellens noggrannhet har fastställts är det mest realistiska nästa steget att använda modellen för att prognostisera okända framtida värden. Ange en datauppsättning i samma format `test_data` som testuppsättningen men med framtida datumtider, och den resulterande förutsägelseuppsättningen är de prognostiserade värdena för varje steg i tidsserierna. Anta att de senaste tidsserieposterna i datauppsättningen var för 12/31/2018. Om du vill prognostisera efterfrågan för nästa dag (eller så `max_horizon`många perioder som du behöver prognostisera, <= ) skapar du en enda tidsseriepost för varje butik för 2019-01-01.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Upprepa de nödvändiga stegen för att läsa in `best_run.predict(test_data)` framtida data till en dataram och kör sedan för att förutsäga framtida värden.

> [!NOTE]
> Värden kan inte förutsägas för ett `max_horizon`antal perioder som är större än . Modellen måste omformas med en större horisont för att förutsäga framtida värden bortom den nuvarande horisonten.

## <a name="next-steps"></a>Nästa steg

* Följ [självstudien](tutorial-auto-train-models.md) för att lära dig hur du skapar experiment med automatiserad maskininlärning.
* Visa referensdokumentationen [för Azure Machine Learning SDK för Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
