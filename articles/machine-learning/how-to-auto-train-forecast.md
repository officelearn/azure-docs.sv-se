---
title: Automatisk träna en tids serie prognos modell
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning för att träna en Regressions Regressions modell i Time Series med hjälp av automatisk maskin inlärning.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 78654dfd5a11219d39d53b4042157333656f9aa3
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834757"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatisk träna en tids serie prognos modell
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du tränar en uppskattnings Regressions modell i Time Series med hjälp av automatisk maskin inlärning i Azure Machine Learning. Att konfigurera en prognos modell liknar att konfigurera en standard Regressions modell med hjälp av automatisk maskin inlärning, men vissa konfigurations alternativ och för bearbetnings steg finns för att arbeta med Time Series-data. I följande exempel visas hur du:

* Förbereda data för tids serie modellering
* Konfigurera angivna parametrar för tids serier i ett [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) -objekt
* Köra förutsägelser med Time Series-data

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Du kan använda automatiserad ML för att kombinera teknik och metoder och få en rekommenderad prognos för tids serier med hög kvalitet. Ett automatiserat experiment med tids serier behandlas som ett multivarierad Regressions problem. Tidigare tids serie värden är "pivoterade" för att bli ytterligare dimensioner för modellerings regressor tillsammans med andra förutsägelser.

Den här metoden, till skillnad från klassiska Time Series-metoder, har en fördel med att använda flera sammanhangsbaserade variabler och deras relation till varandra under utbildningen. I verkliga prognos program kan flera faktorer påverka en prognos. Till exempel, när försäljnings prognoser används, interaktioner över historiska trender, utbytes pris och pris alla gemensamt styr försäljnings resultatet. En ytterligare förmån är att alla nya innovationer i Regressions modeller omedelbart tillämpas på prognoser.

Du kan [Konfigurera](#config) hur långt i framtiden prognosen ska utsträckas (prognos Horisont) samt lags med mera. Med automatisk ML får du en enda, men ofta ingrenad modell för alla objekt i data uppsättningen och förutsägelserna. Mer data är därför tillgängliga för att uppskatta modell parametrar och generalisering till osett-serien blir möjlig.

Funktioner som har extraherats från tränings data spelar en viktig roll. Och automatiserade ML utför standard för bearbetnings steg och genererar ytterligare tids serie funktioner för att fånga säsongs effekter och maximera förutsägelse noggrannhet.

## <a name="time-series-and-deep-learning-models"></a>Tids serie-och djup inlärnings modeller


Med automatisk ML får användare både interna Time-och djup inlärnings modeller som en del av rekommendations systemet. Dessa lärare är:
+ Prophet
+ ARIMA automatiskt
+ ForecastTCN

Med automatiserad MLs djup inlärning kan du prognostisera univariate-och multivarierad Time Series-data.

Djup inlärnings modeller har tre inbyggda funktioner:
1. De kan lära sig från valfria mappningar från indata till utdata
1. De stöder flera indata och utdata
1. De kan automatiskt extrahera mönster i indata som sträcker sig över långa sekvenser

Med större data kan djup inlärnings modeller, till exempel Microsofts ForecastTCN, förbättra poängen i den resulterande modellen. 

Interna Time Series-läraare tillhandahålls också som en del av automatiserad ML. Prophet fungerar bäst med tids serier som har starka säsongs effekter och flera säsonger av historiska data. Prophet är korrekt & snabbt, robust för att kunna avvika, saknade data och dramatiska ändringar i din tids serie. 

Autoregressivt Integrated glidande medelvärde (ARIMA) är en populär statistisk metod för tids serie prognoser. Den här metoden för Prognosticering används ofta på kort sikts scenarier där data visar bevis på trender, till exempel cykler, som kan vara oförutsägbara och svåra för modeller eller prognoser. AutoARIMA omvandlar dina data till station ära data för att få konsekventa, pålitliga resultat.

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta. Information om hur du skapar arbets ytan finns i [skapa en Azure Machine Learning arbets yta](how-to-manage-workspace.md).
* I den här artikeln förutsätter vi att du har konfigurerat ett automatiserat experiment för maskin inlärning. Följ [själv studie kursen](tutorial-auto-train-models.md) eller [anvisningar](how-to-configure-auto-train.md) för att se design mönster för det grundläggande automatiserade maskin inlärnings experimentet.

## <a name="preparing-data"></a>Förbereda data

Den viktigaste skillnaden mellan en typ av Regressions Regressions typ och Regressions aktivitet i Automatisk maskin inlärning är bland annat en funktion i dina data som representerar en giltig tids serie. En vanlig tids serie har en väldefinierad och konsekvent frekvens och har ett värde vid varje exempel punkt i ett kontinuerligt tidsintervall. Överväg följande ögonblicks bild av en fil `sample.csv`.

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

Den här data uppsättningen är ett enkelt exempel på dagliga försäljnings data för ett företag som har två olika butiker, A och B. Dessutom finns det en funktion för `week_of_year` som gör att modellen kan identifiera varje veckas säsongs beroende. Fältet `day_datetime` representerar en ren tids serie med den dagliga frekvensen och fältet `sales_quantity` är mål kolumnen för att köra förutsägelser. Läs data till en Pandas-dataframe och Använd sedan funktionen `to_datetime` för att se till att tids serien är en `datetime` typ.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

I det här fallet sorteras data redan stigande efter Time-fältet `day_datetime`. Men när du konfigurerar ett experiment ser du till att kolumnen önskad tid sorteras i stigande ordning för att skapa en giltig tids serie. Anta att data innehåller 1 000 poster och gör en deterministisk delning i data för att skapa utbildnings-och test data uppsättningar. Identifiera etikettens kolumn namn och ange den som etikett. I det här exemplet kommer etiketten att `sales_quantity`. Separera sedan etikett fältet från `test_data` för att skapa `test_target`s uppsättningen.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> När du tränar en modell för att förutsäga framtida värden kan du se till att alla funktioner som används i träningen kan användas när du kör förutsägelser för din avsedda horisont. När du skapar en prognos för efter frågan kan du till exempel öka inlärnings precisionen med en funktion för det aktuella lager priset. Men om du planerar att prognostisera med lång horisont kanske du inte kan förutsäga framtida lager värden som motsvarar framtida tids serie punkter och modell precisionen kan bli lidande.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Konfigurera och kör experiment

För prognos uppgifter använder automatisk maskin inlärning för bearbetning och beräknings steg som är aktuella för Time Series-data. Följande steg för bearbetning utförs:

* Identifiera exempel frekvensen för tids serier (till exempel varje timme, varje dag, varje vecka) och skapa nya poster för frånvaro tids punkter för att göra serien kontinuerlig.
* Ange värden som saknas i målet (via Forward-Fill) och funktions kolumner (med kolumn värden i median)
* Skapa korniga funktioner för att aktivera fasta effekter i olika serier
* Skapa tidsbaserade funktioner för att hjälpa till med utbildnings säsongs mönster
* Koda kategoriska-variabler till numeriska kvantiteter

[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) -objektet definierar de inställningar och data som krävs för en automatiserad maskin inlärnings uppgift. Precis som med ett Regressions problem definierar du standard utbildnings parametrar som aktivitets typ, antal iterationer, tränings data och antalet kors valideringar. För prognos uppgifter finns det ytterligare parametrar som måste anges som påverkar experimentet. I följande tabell beskrivs varje parameter och dess användning.

| Param | Beskrivning | Krävs |
|-------|-------|-------|
|`time_column_name`|Används för att ange kolumnen datetime i de indata som används för att bygga tids serien och härleda dess frekvens.|✓|
|`grain_column_names`|Namn (er) som definierar enskilda serie grupper i indata. Om kornig het inte har definierats antas data uppsättningen vara en tids serie.||
|`max_horizon`|Definierar den högsta önskade prognos horisonten i enheter för tids serie frekvens. Enheter baseras på tidsintervallet för dina utbildnings data, till exempel varje månad, varje vecka att prognosen ska förutsäga.|✓|
|`target_lags`|Antal rader att ange för fördröjning av målvärdena baserat på data frekvensen. Fördröjningen visas som en lista eller ett enda heltal. Fördröjning ska användas när relationen mellan oberoende variabler och beroende variabel inte matchar eller korrelerar som standard. När du till exempel försöker prognostisera efter frågan för en produkt kan efter frågan i någon månad bero på priset för vissa råvaruer 3 månader tidigare. I det här exemplet kanske du vill ange ett negativt värde för målet (efter frågan) med tre månader, så att modellen är en utbildning på rätt relation.||
|`target_rolling_window_size`|*n* historiska perioder som ska användas för att generera prognostiserade värden < = storlek för tränings uppsättning. Om det utelämnas är *n* den fullständiga inlärnings uppsättningens storlek. Ange den här parametern när du bara vill ta hänsyn till en viss mängd historik när du tränar modellen.||
|`enable_dnn`|Aktivera Prognosticering av Hyperoptimerade.||

Mer information finns i [referens dokumentationen](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Skapa tids serie inställningarna som ett Dictionary-objekt. Ange `time_column_name` till fältet `day_datetime` i data uppsättningen. Definiera parametern `grain_column_names` för att se till att **två separata tids serie grupper** skapas för data. en för Store A och B. Ange slutligen `max_horizon` till 50 för att förutsäga för hela test uppsättningen. Ange en prognos period på 10 perioder med `target_rolling_window_size`och ange en enda fördröjning på målvärdena för två perioder i förväg med parametern `target_lags`. Vi rekommenderar att du ställer in `max_horizon``target_rolling_window_size` och `target_lags` till "Auto", vilket automatiskt identifierar dessa värden. I exemplet nedan har inställningarna "Auto" använts för dessa paramaters. 

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
> Automatiserad bearbetning av Machine Learning för bearbetning (funktions normalisering, hantering av saknade data, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma för bearbetnings steg som tillämpas på dina indata-data automatiskt.

Genom att definiera `grain_column_names` i kodfragmentet ovan skapar AutoML två separata Time-Series-grupper, även kallat flera tids serier. Om ingen kornig het har definierats kommer AutoML att anta att data uppsättningen är en enda tids serie. Mer information om engångs-serien finns i [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Skapa nu ett standard `AutoMLConfig`-objekt, ange aktivitets typen `forecasting` och skicka experimentet. När modellen har slutförts hämtar du den bästa körnings iterationen.

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

Se [exempel antecknings böcker för Prognosticering](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) för detaljerade kod exempel på avancerad prognos konfiguration, inklusive:

* [jul avkänning och funktionalisering](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [kors validering av rullande ursprung](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurerbar lags](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [mängd funktioner för rullande fönster](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Konfigurera en DNN aktivera prognos experiment

> [!NOTE]
> DNN-stöd för Prognosticering i automatiserade Machine Learning är en för hands version.

För att kunna utnyttja Hyperoptimerade för prognostisering måste du ange parametern `enable_dnn` i AutoMLConfig till true. 

För att kunna använda Hyperoptimerade rekommenderar vi att du använder ett AML beräknings kluster med GPU SKU: er och minst två noder som beräknings mål. Mer information finns i [AML Compute-dokumentationen](how-to-set-up-training-targets.md#amlcompute). Se [GPU-optimerade storlekar på virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) för mer information om VM-storlekar som innehåller GPU: er.

För att få tillräckligt med tid för att DNN-utbildningen ska slutföras rekommenderar vi att du ställer in experiment tids gränsen på minst ett par timmar.

### <a name="view-feature-engineering-summary"></a>Visa sammanfattning av funktions teknik

För aktivitets typer för Time-serien i Automatisk maskin inlärning kan du Visa information från funktions teknik processen. Följande kod visar varje RAW-funktion tillsammans med följande attribut:

* Oformaterat funktions namn
* Antalet funktioner som skapats av den här RAW-funktionen
* Typ identifierad
* Om funktionen har släppts
* Lista över funktions omvandlingar för RAW-funktionen

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognosticering med bästa modell

Använd den bästa modellen iterationer för att beräkna prognos värden för data uppsättningen test.

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Du kan också använda funktionen `forecast()` i stället för `predict()`, vilket gör det möjligt att ange specifikationer för när förutsägelserna ska starta. I följande exempel ersätter du först alla värden i `y_pred` med `NaN`. Prognosens ursprung är i slutet av tränings data i det här fallet, eftersom det normalt skulle vara när `predict()`används. Men om du bara ersatte den andra halvan av `y_pred` med `NaN`, lämnar funktionen de numeriska värdena i den första halvan oförändrade, men prognoserar `NaN` värdena i den andra halvan. Funktionen returnerar både de beräknade värdena och de justerade funktionerna.

Du kan också använda parametern `forecast_destination` i `forecast()`-funktionen för att beräkna värden fram till ett visst datum.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Beräkna RMSE (rot genomsnitts fel) mellan `actual_labels` faktiska värdena och de prognostiserade värdena i `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_lables, predict_labels))
rmse
```

Nu när den övergripande modell precisionen har fastställts är det mest realistiska nästa steg att använda modellen för att förutsäga okända framtida värden. Ange en data uppsättning i samma format som test uppsättningen `test_data` men med framtida datetime-värden och den resulterande förutsägelse uppsättningen är de prognostiserade värdena för varje tids serie steg. Anta att de senaste tid serie posterna i data uppsättningen var i 12/31/2018. Om du vill prognostisera efter frågan för nästa dag (eller så många perioder som du behöver prognos, < = `max_horizon`), skapar du en post för en tids serie för varje butik för 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Upprepa de nödvändiga stegen för att läsa in framtida data till en dataframe och kör sedan `best_run.predict(test_data)` för att förutsäga framtida värden.

> [!NOTE]
> Det går inte att förutsäga värden för antalet perioder som är större än `max_horizon`. Modellen måste tränas om med en större horisont för att förutsäga framtida värden bortom den aktuella horisonten.

## <a name="next-steps"></a>Nästa steg

* Följ [själv studie kursen](tutorial-auto-train-models.md) för att lära dig hur du skapar experiment med automatiserad maskin inlärning.
* Visa [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -referens dokumentation.
