---
title: 'Regressionshandledning: Automatiserad ML'
titleSuffix: Azure Machine Learning
description: I den här självstudien får du lära dig hur du skapar en maskininlärningsmodell med hjälp av automatiserad maskininlärning. Azure Machine Learning kan förbearbeta data, välja algoritm och hyperparameter på ett automatiserat sätt åt dig.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: 75e61ea3f4fa6c2b346f912a9effd66ad94e7e93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77116443"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Handledning: Använd automatiserad maskininlärning för att förutsäga taxipriser
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här självstudien använder du automatiserad maskininlärning i Azure Machine Learning för att skapa en regressionsmodell för att förutsäga priser för NYC-taxipriser. Den här processen accepterar träningsdata och konfigurationsinställningar och iterar automatiskt genom kombinationer av olika funktionsnormaliserings-/standardiseringsmetoder, modeller och hyperparameterinställningar för att komma fram till den bästa modellen.

![Flödesdiagram](./media/tutorial-auto-train-models/flow2.png)

I den här självstudien lär du dig följande uppgifter:

> [!div class="checklist"]
> * Hämta, transformera och rensa data med Hjälp av Azure Open Datasets
> * Träna en automatiserad regressionsmodell för maskininlärning
> * Beräkna modellnoggrannhet

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen](https://aka.ms/AMLFree) av Azure Machine Learning idag.

## <a name="prerequisites"></a>Krav

* Slutför [installationshandledningen](tutorial-1st-experiment-sdk-setup.md) om du inte redan har en Azure Machine Learning-arbetsyta eller virtuell anteckningsbok.
* När du har slutfört installationshandledningen öppnar du *självstudierna/regression-automl-nyc-taxi-data/regression-automated-ml.ipynb-anteckningsboken* med samma bärbara server.

Den här självstudien är också tillgänglig på [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) om du vill köra den i din egen [lokala miljö](how-to-configure-environment.md#local). Kör `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` för att hämta de nödvändiga paketen.

## <a name="download-and-prepare-data"></a>Ladda ner och förbereda data

Importera nödvändiga paket. Paketet Open Dataset innehåller en klass som`NycTlcGreen` representerar varje datakälla (till exempel) för att enkelt filtrera datumparametrar innan du hämtar.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Börja med att skapa en dataram för att lagra taxidata. När du arbetar i en icke-Spark-miljö tillåter Open Datasets endast nedladdning av `MemoryError` en månad med data i taget med vissa klasser att undvika med stora datauppsättningar.

Om du vill hämta taxidata hämtar du iterativt en `green_taxi_df` månad i taget och innan du lägger till den för att slumpmässigt ta prov på 2 000 poster från varje månad för att undvika uppblåsthet i dataramen. Förhandsgranska sedan data.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>leverantörSID</th>
      <th>lpepPickupDatetime lpepPickupDatetime lpepPickupDatetime lpep</th>
      <th>lpepDropoffDatetime lpepDropoffdatetime lpepDropoffdatetime lpep</th>
      <th>passagerareCount</th>
      <th>tripDistance (tripDistance)</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLångt</th>
      <th>pickupLatitude</th>
      <th>avlämningSten</th>
      <th>...</th>
      <th>betalningstyp</th>
      <th>fareAmount</th>
      <th>Extra</th>
      <th>mtaTax (på)</th>
      <th>förbättringSurcharge</th>
      <th>tipAmount</th>
      <th>vägtullarFäste</th>
      <th>ehailFee (på väg)</th>
      <th>totaltAmount</th>
      <th>tripType (tripType)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4.84</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.88</td>
      <td>40.84</td>
      <td>-73.94</td>
      <td>...</td>
      <td>2</td>
      <td>15.00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>16.30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0.69</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.96</td>
      <td>40.81</td>
      <td>-73.96</td>
      <td>...</td>
      <td>2</td>
      <td>4.50</td>
      <td>1,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0.45</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.91</td>
      <td>...</td>
      <td>2</td>
      <td>4.00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>4.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
      <td>...</td>
      <td>2</td>
      <td>4.00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>5.00</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1,10</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.95</td>
      <td>...</td>
      <td>2</td>
      <td>6.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>7.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>...</td>
      <td>2</td>
      <td>6.00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3.30</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.91</td>
      <td>...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1.19</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.95</td>
      <td>...</td>
      <td>1</td>
      <td>7.00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>1.75</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>9.55</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0.65</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.94</td>
      <td>...</td>
      <td>2</td>
      <td>5.00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
    </tr>
  </tbody>
</table>
<p>10 rader × 23 kolumner</p>
</div>


Nu när de första data läses in definierar du en funktion för att skapa olika tidsbaserade funktioner från fältet upphämtningsdatumtid. Detta skapar nya fält för månadsnummer, dag i månaden, veckodag och timme på dagen, och gör det möjligt för modellen att ta hänsyn till tidsbaserade säsongsvariationer. Använd `apply()` funktionen på dataramen för att iterativt använda `build_time_features()` funktionen på varje rad i taxidata.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>leverantörSID</th>
      <th>lpepPickupDatetime lpepPickupDatetime lpepPickupDatetime lpep</th>
      <th>lpepDropoffDatetime lpepDropoffdatetime lpepDropoffdatetime lpep</th>
      <th>passagerareCount</th>
      <th>tripDistance (tripDistance)</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLångt</th>
      <th>pickupLatitude</th>
      <th>avlämningSten</th>
      <th>...</th>
      <th>förbättringSurcharge</th>
      <th>tipAmount</th>
      <th>vägtullarFäste</th>
      <th>ehailFee (på väg)</th>
      <th>totaltAmount</th>
      <th>tripType (tripType)</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4.84</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.88</td>
      <td>40.84</td>
      <td>-73.94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>16.30</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0.69</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.96</td>
      <td>40.81</td>
      <td>-73.96</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
      <td>1</td>
      <td>20</td>
      <td>1</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0.45</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>4.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>17</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
      <td>...</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>5.00</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1,10</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.95</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>7.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>4</td>
      <td>6</td>
      <td>19</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3.30</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>23</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1.19</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.95</td>
      <td>...</td>
      <td>0.3</td>
      <td>1.75</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>9.55</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>17</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0.65</td>
      <td>Inget</td>
      <td>Inget</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
      <td>1</td>
      <td>22</td>
      <td>3</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
<p>10 rader × 27 kolumner</p>
</div>

Ta bort några av de kolumner som du inte behöver för utbildning eller ytterligare funktionsuppbyggnad.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Rensa data

Kör `describe()` funktionen på den nya dataramen för att se sammanfattningsstatistik för varje fält.

```python
green_taxi_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>leverantörSID</th>
      <th>passagerareCount</th>
      <th>tripDistance (tripDistance)</th>
      <th>pickupLångt</th>
      <th>pickupLatitude</th>
      <th>avlämningSten</th>
      <th>avlämningLatitude</th>
      <th>totaltAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
    </tr>
    <tr>
      <th>medelvärde</th>
      <td>1.78</td>
      <td>1.37</td>
      <td>2.87</td>
      <td>-73.83</td>
      <td>40.69</td>
      <td>-73.84</td>
      <td>40.70</td>
      <td>14.75</td>
      <td>6.50</td>
      <td>15.13</td>
      <td>3.27</td>
      <td>13.52</td>
    </tr>
    <tr>
      <th>Std</th>
      <td>0,41</td>
      <td>1,04</td>
      <td>2.93</td>
      <td>2.76</td>
      <td>1,52</td>
      <td>2.61</td>
      <td>1.44</td>
      <td>12.08</td>
      <td>3.45</td>
      <td>8.45</td>
      <td>1.95</td>
      <td>6.83</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>-74.66</td>
      <td>0,00</td>
      <td>-74.66</td>
      <td>0,00</td>
      <td>-300.00</td>
      <td>1,00</td>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>25 %</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>1.06</td>
      <td>-73.96</td>
      <td>40.70</td>
      <td>-73.97</td>
      <td>40.70</td>
      <td>7.80</td>
      <td>3.75</td>
      <td>8.00</td>
      <td>2,00</td>
      <td>9.00</td>
    </tr>
    <tr>
      <th>50 %</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>1.90</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>11.30</td>
      <td>6.50</td>
      <td>15.00</td>
      <td>3.00</td>
      <td>15.00</td>
    </tr>
    <tr>
      <th>75 %</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>3.60</td>
      <td>-73.92</td>
      <td>40.80</td>
      <td>-73.91</td>
      <td>40.79</td>
      <td>17.80</td>
      <td>9.25</td>
      <td>22.00</td>
      <td>5.00</td>
      <td>19.00</td>
    </tr>
    <tr>
      <th>Max</th>
      <td>2,00</td>
      <td>9.00</td>
      <td>97.57</td>
      <td>0,00</td>
      <td>41.93</td>
      <td>0,00</td>
      <td>41.94</td>
      <td>450.00</td>
      <td>12.00</td>
      <td>30.00</td>
      <td>6.00</td>
      <td>23.00</td>
    </tr>
  </tbody>
</table>
</div>


I sammanfattningsstatistiken ser du att det finns flera fält som har extremvärden eller värden som minskar modellens noggrannhet. Filtrera först lat /long fälten för att vara inom gränserna för Manhattan-området. Detta kommer att filtrera bort längre taxiresor eller resor som är extremvärden i förhållande till deras relation med andra funktioner.

Dessutom filtrera `tripDistance` fältet vara större än noll men mindre än 31 miles (haversine avståndet mellan de två lat / långa par). Detta eliminerar långa avvikande resor som har inkonsekvent resekostnad.

Slutligen har `totalAmount` fältet negativa värden för taxipriserna, vilket inte är meningsfullt i samband med `passengerCount` vår modell, och fältet har dåliga data med minimivärdena noll.

Filtrera bort dessa avvikelser med hjälp av frågefunktioner och ta sedan bort de sista kolumnerna som inte är nödvändiga för träning.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Ring `describe()` igen på data för att säkerställa rengöring fungerade som förväntat. Du har nu en förberedd och rengjord uppsättning taxi, semester och väderdata att använda för maskininlärning modell utbildning.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Konfigurera arbetsyta

Skapa ett arbetsyteobjekt från den befintliga arbetsytan. En [arbetsyta](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) är en klass som accepterar din Azure-prenumeration och resursinformation. Den skapar också en molnresurs för att övervaka och spåra dina körningar i modellen. `Workspace.from_config()`läser filen **config.json** och läser in autentiseringsinformationen i ett objekt med namnet `ws`. `ws` används i resten av koden i den här självstudien.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Dela data till uppsättningar för träning och testning

Dela upp data i tränings- `train_test_split` och testuppsättningar med hjälp av funktionen i biblioteket. `scikit-learn` Den här funktionen segregerar data i datauppsättningen x **(funktioner)** för modellutbildning och datauppsättningen y **(för att förutsäga)** för testning.

Parametern `test_size` anger procentandelen av data som ska allokeras till testning. Parametern `random_state` ställer in ett frö till den slumpmässiga generatorn, så att dina tågtestsplitter är deterministiska.

```python
from sklearn.model_selection import train_test_split

y_df = final_df.pop("totalAmount")
x_df = final_df

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
```

Syftet med det här steget är att ha datapunkter för att testa den färdiga modell som inte har använts för att träna modellen, detta för att mäta den äkta noggrannheten.

Med andra ord bör en korrekt tränad modell kunna göra noggranna förutsägelser från data som den inte redan har sett. Du har nu data som förberetts för automatisk utbildning av en maskininlärningsmodell.

## <a name="automatically-train-a-model"></a>Träna en modell automatiskt

För att träna en modell automatiskt gör du följande:
1. Definiera inställningar för körningen av experimentet. Koppla dina träningsdata till konfigurationen och ändra de inställningar som styr träningsprocessen.
1. Skicka experimentet för modelljustering. När experimentet har skickats itererar processen genom olika maskininlärningsalgoritmer och inställningar för hyperparametrar enligt dina definierade begränsningar. Den väljer den modell som passar bäst genom att optimera ett noggrannhetsmått.

### <a name="define-training-settings"></a>Definiera utbildningsinställningar

Definiera experimentparametern och modellinställningarna för träning. Visa hela listan med [inställningar](how-to-configure-auto-train.md). Det tar ungefär 5-20 minuter att skicka experimentet med dessa standardinställningar, men `experiment_timeout_minutes` om du vill ha en kortare körtid minskar du parametern.

|Egenskap| Värde i den här självstudien |Beskrivning|
|----|----|---|
|**iteration_timeout_minutes**|2|Tidsgräns i minuter för varje iteration. Minska det här värdet om du vill minska den totala körningstiden.|
|**experiment_timeout_minutes**|20|Maximal tid i minuter som alla kombinationer kan ta innan experimentet avslutas.|
|**enable_early_stopping**|True|Flagga för att möjliggöra förtida uppsägning om poängen inte förbättras på kort sikt.|
|**primary_metric**| spearman_correlation | Mått som du vill optimera. Den modell som passar bäst väljs utifrån det här måttet.|
|**medarisering**| Auto | Genom att använda **auto**kan experimentet förbehandla indata (hantering av data som saknas, konvertera text till numerisk, etc.)|
|**verbosity**| logging.INFO | Styr loggningsnivån.|
|**n_cross_validations**|5|Det antal delningar av korsvalidering som ska utföras när verifieringsdata inte har angetts.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_minutes": 20,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Använd de definierade träningsinställningarna som en `**kwargs` parameter för ett `AutoMLConfig` objekt. Ange även dina träningsdata och modelltypen, som i det här fallet är `regression`.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             X=x_train.values,
                             y=y_train.values.flatten(),
                             **automl_settings)
```

> [!NOTE]
> Automatiserade förbearbetningssteg för maskininlärning (funktionsnormalisering, hantering av data som saknas, konvertering av text till numeriska osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma förbearbetningssteg som tillämpas under träningen automatiskt på dina indata.

### <a name="train-the-automatic-regression-model"></a>Träna den automatiska regressionsmodellen

Skapa ett experimentobjekt på arbetsytan. Ett experiment fungerar som en behållare för dina enskilda körningar. Skicka det `automl_config` definierade objektet till experimentet `True` och ställ in utdata på att visa förloppet under körningen.

När du har startat experimentet visas uppdateringarna live när experimentet körs. För varje iteration ser du modelltypen, körningens varaktighet samt träningens noggrannhet. Fältet `BEST` spårar den bästa löpande körningspoängen utifrån din måttyp.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
    Current status: DatasetFeaturization. Beginning to featurize the dataset.
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
             1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
             3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
             4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
             5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
             6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
             7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
             8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
             9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
            10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
            11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
            12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
            13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
            14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
            15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
            17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
            18   VotingEnsemble                                 0:00:23       0.9471    0.9471
            19   StackEnsemble                                  0:00:27       0.9463    0.9471

## <a name="explore-the-results"></a>Utforska resultaten

Utforska resultaten av automatisk utbildning med en [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Widgeten kan du se ett diagram och tabell över alla enskilda kör iterationer, tillsammans med utbildning noggrannhet mått och metadata. Dessutom kan du filtrera på olika noggrannhetsmått än ditt primära mått med listrutan.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter Widget-körningsinformation](./media/tutorial-auto-train-models/automl-dash-output.png)
![Jupyter Widget-diagram](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Hämta den bästa modellen

Välj den bästa modellen från dina iterationer. Funktionen `get_output` returnerar den bästa körningen och den monterade modellen för den sista passformen åkallan. Genom att använda `get_output`överbelastningarna på kan du hämta den bästa körningen och den monterade modellen för alla loggade mått eller en viss iteration.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Testa den bästa modellens precision

Använd den bästa modellen för att köra förutsägelser på testdatauppsättningen för att förutsäga taxipriser. Funktionen `predict` använder den bästa modellen och förutsäger värdena för `x_test` y, **resekostnad**, från datauppsättningen. Skriv ut de 10 första förutsagda kostnadsvärdena från `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Beräkna `root mean squared error` för resultatet. Konvertera `y_test` dataramen till en lista för att jämföra med de förväntade värdena. Funktionen `mean_squared_error` tar emot två matriser med värden och beräknar det genomsnittliga kvadratfelet mellan dem. Att ta kvadratroten ur resultatet ger ett fel i samma enheter som y-variabeln, **kostnad**. Det visar ungefär hur långt taxibiljettprognoserna är från de faktiska biljettpriserna.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Kör följande kod för att beräkna medelvärdet för absolut `y_actual` `y_predict` procentfel (MAPE) med hjälp av fullständiga och datauppsättningar. Det här måttet beräknar en absolut skillnad mellan varje förväntat och faktiskt värde och summerar alla skillnaderna. Sedan uttrycker den summan som en procent av summan av de faktiska värdena.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.14353867606052823

    Model Accuracy:
    0.8564613239394718


Från de två prognos noggrannhet mått, ser du att modellen är ganska bra på att förutsäga taxipriser från datauppsättningens funktioner, vanligtvis inom + - $ 4,00 och cirka 15% fel.

Utvecklingsprocessen för maskininlärningsmodeller är mycket resurskrävande och fordrar betydande domänkunskap och tid för att köra och jämföra resultat från flera olika modeller. Användning av automatisk maskininlärning är ett bra sätt att snabbt testa flera olika modeller för ett scenario.

## <a name="clean-up-resources"></a>Rensa resurser

Fyll inte i det här avsnittet om du planerar att köra andra Azure Machine Learning-självstudier.

### <a name="stop-the-compute-instance"></a>Stoppa beräkningsinstansen

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Ta bort allt

Om du inte planerar att använda de resurser du har skapat tar du bort dem så att du inte debiterar några avgifter.

1. I Azure-portalen väljer du **Resursgrupper** längst till vänster.
1. Välj den resursgrupp i listan som du har skapat.
1. Välj **Ta bort resursgrupp**.
1. Ange resursgruppsnamnet. Välj sedan **Ta bort**.

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här automatiserade självstudiekursen om maskininlärning har du gjort följande uppgifter:

> [!div class="checklist"]
> * Konfigurerat en arbetsyta och förberett data för ett experiment.
> * Tränat med hjälp av en automatiserad regressionsmodell lokalt med anpassade parametrar.
> * Utforskat och granskat träningsresultat.

[Distribuera modellen](tutorial-deploy-models-with-aml.md) med Azure Machine Learning.
