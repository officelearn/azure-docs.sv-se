---
title: Automatisk träna en tids serie prognos modell
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder Azure Machine Learning-tjänsten för att träna en uppskattnings Regressions modell i Time Series med hjälp av automatisk maskin inlärning.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: e75de16d0e16bc639a0439220a1c9dfe53e1689b
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879055"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatisk träna en tids serie prognos modell

I den här artikeln får du lära dig hur du tränar en Regressions Regressions modell i Time Series med hjälp av automatisk maskin inlärning i Azure Machine Learning-tjänsten. Att konfigurera en prognos modell liknar att konfigurera en standard Regressions modell med hjälp av automatisk maskin inlärning, men vissa konfigurations alternativ och för bearbetnings steg finns för att arbeta med Time Series-data. I följande exempel visas hur du:

* Förbereda data för tids serie modellering
* Konfigurera angivna parametrar för tids serier i ett [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) objekt
* Köra förutsägelser med Time Series-data

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Du kan använda automatiserad ML för att kombinera teknik och metoder och få en rekommenderad prognos för tids serier med hög kvalitet. Ett automatiserat experiment med tids serier behandlas som ett multivarierad Regressions problem. Tidigare tids serie värden är "pivoterade" för att bli ytterligare dimensioner för modellerings regressor tillsammans med andra förutsägelser.

Den här metoden, till skillnad från klassiska Time Series-metoder, har en fördel med att använda flera sammanhangsbaserade variabler och deras relation till varandra under utbildningen. I verkliga prognos program kan flera faktorer påverka en prognos. Till exempel, när försäljnings prognoser används, interaktioner över historiska trender, utbytes pris och pris alla gemensamt styr försäljnings resultatet. En ytterligare förmån är att alla nya innovationer i Regressions modeller omedelbart tillämpas på prognoser.

Du kan [Konfigurera](#config) hur långt i framtiden prognosen ska utsträckas (prognos Horisont) samt lags med mera. Med automatisk ML får du en enda, men ofta ingrenad modell för alla objekt i data uppsättningen och förutsägelserna. Mer data är därför tillgängliga för att uppskatta modell parametrar och generalisering till osett-serien blir möjlig.

Funktioner som har extraherats från tränings data spelar en viktig roll. Och automatiserade ML utför standard för bearbetnings steg och genererar ytterligare tids serie funktioner för att fånga säsongs effekter och maximera förutsägelse noggrannhet.

## <a name="prerequisites"></a>Förutsättningar

* En arbetsyta för Azure Machine Learning-tjänsten. Information om hur du skapar arbets ytan finns i [skapa en Azure Machine Learning service-arbetsyta](how-to-manage-workspace.md).
* I den här artikeln förutsätter vi att du har konfigurerat ett automatiserat experiment för maskin inlärning. Följ [själv studie kursen](tutorial-auto-train-models.md) eller [anvisningar](how-to-configure-auto-train.md) för att se design mönster för det grundläggande automatiserade maskin inlärnings experimentet.

## <a name="preparing-data"></a>Förbereda data

Den viktigaste skillnaden mellan en typ av Regressions Regressions typ och Regressions aktivitet i Automatisk maskin inlärning är bland annat en funktion i dina data som representerar en giltig tids serie. En vanlig tids serie har en väldefinierad och konsekvent frekvens och har ett värde vid varje exempel punkt i ett kontinuerligt tidsintervall. Överväg följande ögonblicks bild av en `sample.csv`fil.

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

Den här data uppsättningen är ett enkelt exempel på dagliga försäljnings data för ett företag som har två olika butiker, a och B. Dessutom finns det en funktion `week_of_year` för som gör att modellen kan identifiera vecko säsongs beroende. Fältet `day_datetime` representerar en ren tids serie med den dagliga frekvensen och fältet `sales_quantity` är mål kolumnen för att köra förutsägelser. Läs data till en Pandas-dataframe och Använd `to_datetime` sedan funktionen för att se till att tids serien `datetime` är av typen.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

I det här fallet sorteras data redan stigande efter Time-fältet `day_datetime`. Men när du konfigurerar ett experiment ser du till att kolumnen önskad tid sorteras i stigande ordning för att skapa en giltig tids serie. Anta att data innehåller 1 000 poster och gör en deterministisk delning i data för att skapa utbildnings-och test data uppsättningar. Separera sedan fältet mål `sales_quantity` för att skapa förutsägelse-och test uppsättningarna.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> När du tränar en modell för att förutsäga framtida värden kan du se till att alla funktioner som används i träningen kan användas när du kör förutsägelser för din avsedda horisont. När du skapar en prognos för efter frågan kan du till exempel öka inlärnings precisionen med en funktion för det aktuella lager priset. Men om du planerar att prognostisera med lång horisont kanske du inte kan förutsäga framtida lager värden som motsvarar framtida tids serie punkter och modell precisionen kan bli lidande.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Konfigurera och kör experiment

För prognos uppgifter använder automatisk maskin inlärning för bearbetning och beräknings steg som är aktuella för Time Series-data. Följande steg för bearbetning utförs:

* Identifiera exempel frekvensen för tids serier (t. ex. varje timme, varje dag, varje vecka) och skapa nya poster för saknade tids punkter för att göra serien kontinuerlig.
* Ange värden som saknas i målet (via Forward-Fill) och funktions kolumner (med kolumn värden i median)
* Skapa korniga funktioner för att aktivera fasta effekter i olika serier
* Skapa tidsbaserade funktioner för att hjälpa till med utbildnings säsongs mönster
* Koda kategoriska-variabler till numeriska kvantiteter

`AutoMLConfig` Objektet definierar de inställningar och data som krävs för en automatiserad maskin inlärnings uppgift. Precis som med ett Regressions problem definierar du standard utbildnings parametrar som aktivitets typ, antal iterationer, tränings data och antalet kors valideringar. För prognos uppgifter finns det ytterligare parametrar som måste anges som påverkar experimentet. I följande tabell beskrivs varje parameter och dess användning.

| Param | Beskrivning | Obligatorisk |
|-------|-------|-------|
|`time_column_name`|Används för att ange kolumnen datetime i de indata som används för att bygga tids serien och härleda dess frekvens.|✓|
|`grain_column_names`|Namn (er) som definierar enskilda serie grupper i indata. Om kornig het inte har definierats antas data uppsättningen vara en tids serie.||
|`max_horizon`|Definierar den högsta önskade prognos horisonten i enheter för tids serie frekvens. Enheter baseras på tidsintervallet för dina utbildnings data, t. ex. varje månad, varje vecka att prognosen ska förutsäga.|✓|
|`target_lags`|*n* perioder för att vidarebefordra-fördröjnings mål före modell träning.||
|`target_rolling_window_size`|*n* historiska perioder som ska användas för att generera prognostiserade värden < = storlek för tränings uppsättning. Om det utelämnas är *n* den fullständiga inlärnings uppsättningens storlek.||

Skapa tids serie inställningarna som ett Dictionary-objekt. `time_column_name` Ange`day_datetime` till fältet i data uppsättningen. Definiera parametern för att se till att **två separata tids serie grupper** skapas för data, en för Store A och B. Ange `max_horizon` till 50 för att förutsäga för hela test uppsättningen. `grain_column_names` Ange en prognos period på 10 perioder med `target_rolling_window_size`och ange mål värden 2 perioder i förväg `target_lags` med parametern.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```

> [!NOTE]
> Automatiserad bearbetning av Machine Learning för bearbetning (funktions normalisering, hantering av saknade data, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma för bearbetnings steg som tillämpas på dina indata-data automatiskt.

Nu ska du skapa `AutoMLConfig` ett standard objekt, `forecasting` ange uppgifts typ och skicka experimentet. När modellen har slutförts hämtar du den bästa körnings iterationen.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Se [antecknings boken för energi förbrukning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) för detaljerade kod exempel för avancerad prognos konfiguration, inklusive:

* jul avkänning och funktionalisering
* kors validering av rullande ursprung
* konfigurerbar lags
* mängd funktioner för rullande fönster

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
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Du kan också använda `forecast()` funktionen i stället för `predict()`, vilket gör det möjligt att ange specifikationer för när förutsägelserna ska starta. I följande exempel ersätter du först alla värden i `y_pred` med. `NaN` Prognosens ursprung är i slutet av tränings data i det här fallet, eftersom det normalt skulle vara när det används `predict()`. Men om du bara ersatte den andra halvan av `y_pred` med `NaN`, lämnar funktionen de numeriska värdena i den första halvan `NaN` oförändrade, men prognoserar värdena i den andra halvan. Funktionen returnerar både de beräknade värdena och de justerade funktionerna.

Du kan också använda `forecast_destination` -parametern `forecast()` i funktionen för att prognostisera värden fram till ett angivet datum.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Beräkna rmse (rot genomsnitts fel) mellan de `y_test` faktiska värdena och de prognostiserade värdena i. `y_pred`

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Nu när den övergripande modell precisionen har fastställts är det mest realistiska nästa steg att använda modellen för att förutsäga okända framtida värden. Du behöver bara ange en data uppsättning i samma format som test uppsättningen `X_test` , men med framtida datetime-värden och den resulterande förutsägelse uppsättningen är de prognostiserade värdena för varje tids serie steg. Anta att de senaste tid serie posterna i data uppsättningen var i 12/31/2018. Om du vill prognostisera efter frågan för nästa dag (eller så många perioder som du behöver prognostisera, < `max_horizon`=) skapar du en enda tids serie post för varje butik för 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Upprepa de nödvändiga stegen för att läsa in framtida data till en dataframe och kör `best_run.predict(X_test)` sedan för att förutsäga framtida värden.

> [!NOTE]
> Det går inte att förutsäga värden för antalet perioder som är större `max_horizon`än. Modellen måste tränas om med en större horisont för att förutsäga framtida värden bortom den aktuella horisonten.

## <a name="next-steps"></a>Nästa steg

* Följ [själv studie kursen](tutorial-auto-train-models.md) för att lära dig hur du skapar experiment med automatiserad maskin inlärning.
* Visa [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -referens dokumentation.
