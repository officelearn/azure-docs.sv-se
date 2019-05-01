---
title: Automatisk – träna en prognosmodell med tidsserie
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder Azure Machine Learning-tjänsten för att träna en tidsserie prognosmodellen regression modell med hjälp av automatisk maskininlärning.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: d79154a8792b9017b98f9d21a2ab0360b7304d1c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697861"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatisk – träna en prognosmodell med tidsserie

I den här artikeln får du lära dig hur du tränar en time series-regression prognosmodell med automatiserade maskininlärning i Azure Machine Learning-tjänsten. Konfigurera en prognosmodell liknar hur du konfigurerar en standard regressionsmodell med automatiserade maskininlärning, men vissa konfigurationssteg för alternativ och bearbeta data i förväg finns för att arbeta med time series-data. I följande exempel visar hur du att:

* Förbereda data för time series modellering
* Konfigurera specifika time series-parametrar i en [ `AutoMLConfig` ](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) objekt
* Kör förutsägelser med time series-data

## <a name="prerequisites"></a>Nödvändiga komponenter

* En arbetsyta för Azure Machine Learning-tjänsten. Om du vill skapa arbetsytan [skapa en arbetsyta för Azure Machine Learning-tjänsten](setup-create-workspace.md).
* Den här artikeln förutsätter grundläggande kunskaper om hur du konfigurerar en automatiserad machine learning-experiment. Följ den [självstudien](tutorial-auto-train-models.md) eller [how-to](how-to-configure-auto-train.md) att se de grundläggande automatiserade machine learning-experiment designmönster.

## <a name="preparing-data"></a>Förbereda data

Den viktigaste skillnaden mellan en prognosmodellen regression Uppgiftstyp och regression Uppgiftstyp inom automatiserade maskininlärning omfattar en funktion i dina data som representerar en giltig tidsserie. En vanlig tidsserie har en väldefinierad och konsekvent frekvens och har ett värde i alla exempel steg i en kontinuerlig tidsrymd. Överväg följande ögonblicksbild av en fil `sample.csv`.

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

Den här datamängden är ett enkelt exempel på dagliga försäljningsdata för ett företag som har två olika butiker, A och B. Dessutom finns det en funktion för `week_of_year` som gör att modellen ska kunna identifiera veckovisa säsongsberoende. Fältet `day_datetime` representerar en ren tidsserie med dagliga frekvens och fältet `sales_quantity` är målkolumnen för körning av förutsägelser. Läsa in datan i en Pandas-dataframe och sedan använda den `to_datetime` funktion för att tillse tidsserien är en `datetime` typen.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

I det här fallet data är redan sorterad stigande efter fältet `day_datetime`. När du konfigurerar ett experiment, se till att den önskade tid kolumnen är sorterad i stigande ordning för att skapa en giltig tidsserie. Anta att data innehåller 1 000 poster och gör en deterministisk delning i datakällan för att skapa utbildning och testa datauppsättningar. Avgränsa målfältet `sales_quantity` att skapa förutsägelser träna och testa anger.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> När träna en modell för prognostisering framtida värden, kan du kontrollera att alla funktioner som används i utbildning kan användas när du kör förutsägelser för dina avsedda vyer. Till exempel när du skapar en Skapa prognoser för efterfrågan, kunde inklusive en funktion för aktuella aktiekursen massivt öka utbildning precision. Om du planerar att skapa prognoser för med en lång horizon, kan du inte kunna korrekt förutse framtida lagerartiklar värden som motsvarar framtida tidpunkter för time series- och modellens Precision kan påverkas.

## <a name="configure-and-run-experiment"></a>Konfigurera och köra experiment

För prognostisering uppgifter använder automatiska maskininlärning förbearbetning och uppskattning steg som är specifika för time series-data. I följande förbearbetning körs:

* Identifiera time series-exempel frekvens (t.ex. per timme, varje dag, varje vecka) och skapa nya poster för saknade tidpunkter så att serien kontinuerlig.
* Sedan imputera värden som saknas i funktionen kolumnerna (med median kolumnvärden) och mål (via vanlig-fyllning)
* Skapa grain-baserade funktioner för att aktivera fasta effekter över olika serier
* Skapa en tidsbaserad funktioner kan hjälpa lära säsongens mönster
* Koda kategoriska variabler till numeriska mängder

Den `AutoMLConfig` objektet definierar de inställningar och data som behövs för en automatiserad machine learning-uppgift. Liknar ett regressionsproblem, definiera standard utbildning parametrar som typ av aktivitet, antalet iterationer, utbildning data, och antalet mellan verifieringar. Det finns fler parametrar som måste anges som påverkar experimentet för prognostisering uppgifter. I följande tabell beskrivs varje parameter och dess användning.

| Param | Beskrivning | Krävs |
|-------|-------|-------|
|`time_column_name`|Används för att ange datetime-kolumn i indata som används för att skapa tidsserier och härledning av frekvensen.|✓|
|`grain_column_names`|Namnen definiera enskilda Seriegrupper i indata. Om grain inte har definierats, antas datauppsättningen är en tidsserie.||
|`max_horizon`|Maximalt önskade prognoser horizon i enheter för time series-frekvens.|✓|
|`target_lags`|*n* perioder att vidarebefordra fördröjning målvärden innan modellen.||
|`target_rolling_window_size`|*n* historiska perioder som ska användas för att generera förväntade värden < = utbildning storlek. Om det utelämnas används *n* anges fullständig utbildningen storlek.||

Skapa time series-inställningar som ett katalogobjekt. Ange den `time_column_name` till den `day_datetime` i datauppsättningen. Definiera den `grain_column_names` parametern för att se till att **två separata grupper för time series-** skapas för data, en för store A och slutligen B. den `max_horizon` att 50 för att förutsäga för hela testet. Ange ett prognoser fönster för 10 perioder med `target_rolling_window_size`, och fördröjning målet värden 2 punkter framåt med den `target_lags` parametern.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

Skapa nu en standard `AutoMLConfig` objekt att ange den `forecasting` aktivitetstyp och skicka experimentet. Hämta den bästa kör iterationen när modellen har slutförts.

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

> [!NOTE]
> Korsvalidering (KA) anvisningar finns time series-data kan bryta mot grundläggande statistisk antaganden av canonical K-vikning korsvalidering strategi så automatiserade machine learning implementerar en löpande ursprung verifiering proceduren för att skapa korsvalidering vikningar för time series-data. Om du vill använda den här proceduren, ange den `n_cross_validations` parametern i den `AutoMLConfig` objekt. Du kan kringgå verifiering och Använd din egen verifiering uppsättningar med den `X_valid` och `y_valid` parametrar.

### <a name="view-feature-engineering-summary"></a>Visa funktionen engineering sammanfattning

Du kan visa information från funktionen tekniska processen för time series-aktivitetstyper i automatiserade machine learning. Följande kod visar varje raw funktion tillsammans med följande attribut:

* Rå funktionsnamnet
* Antal bakåtkompilerade funktioner som skapats utanför den här raw funktionen
* Typen som har identifierats
* Om funktionen har tagits bort
* Lista över funktionen transformationer för raw-funktionen

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognostisering med bästa modellen

Använd den bästa modell iterationen framtida värden för test-datauppsättning.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Du kan också använda den `forecast()` i stället för `predict()`, så att specifikationer av när förutsägelser ska starta. I följande exempel ersätter du först alla värden i `y_pred` med `NaN`. Prognoser ursprunget blir i slutet av träningsdata i det här fallet eftersom det skulle vara när du använder normalt `predict()`. Men om du har ersatt den andra halvan av `y_pred` med `NaN`, funktionen skulle innebära de numeriska värdena i den första halvan oförändrade men prognoser den `NaN` värden i den andra halvan. Funktionen returnerar både förväntade värden och justerade funktioner.

Du kan också använda den `forecast_destination` parametern i den `forecast()` funktionen framtida värden fram till ett visst datum.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Beräkna RMSE (rot medelvärdet cirkels fel) mellan den `y_test` faktiska värden och förväntade värden i `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Nu när totala modellens Precision har fastställts, mest realistisk nästa steg är att använda modellen framtida okänd framtida värden. Bara ange en uppsättning data i samma format som testmängd `X_test` men med framtida datum och tid och den resulterande förutsägelsen är förväntade värden för varje tidsserie-steg. Anta att de senaste time series-posterna i datauppsättningen har för 12/31 januari 2018. Till att prognostisera efterfrågan för nästa dag (eller så många punkter som du behöver att skapa prognoser, < = `max_horizon`), skapa en enda time series-post för varje butik för 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Upprepa de nödvändiga stegen för att läsa in den här framtida data till en dataram och kör sedan `best_run.predict(X_test)` att förutsäga framtida värden.

> [!NOTE]
> Går inte att förutse värden för antalet perioder som är större än den `max_horizon`. Modellen måste vara utbildade igen med ett större horizon att förutsäga framtida värden utöver den aktuella perioden.

## <a name="next-steps"></a>Nästa steg

* Följ den [självstudien](tutorial-auto-train-models.md) och lär dig att skapa experiment med automatiserad maskininlärning.
* Visa den [Azure Machine Learning-SDK för Python](https://aka.ms/aml-sdk) referensdokumentation.
