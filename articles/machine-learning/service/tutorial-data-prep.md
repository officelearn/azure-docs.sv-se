---
title: 'Självstudie om regressionsmodell: Förbereda data'
titleSuffix: Azure Machine Learning service
description: I den första delen av den här självstudien lär du dig hur du förbereder data i Python för regressionsmodellering med hjälp av Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sihhu
ms.author: MayMSFT
ms.reviewer: trbye
ms.date: 02/04/2019
ms.custom: seodec18
ms.openlocfilehash: 0ee49299dbbe8095ef98be51dc8619c28891abc5
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361395"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Självstudier: Förbereda data för regressionsmodellering

I den här självstudien lär du dig hur du förbereder data för regressionsmodellering med hjälp av [Azure Machine Learning Data Prep SDK för Python](https://aka.ms/data-prep-sdk). Du kör olika transformeringar för att filtrera och kombinera två olika datauppsättningar för taxiresor i New York.

Den här självstudien är **del ett i en självstudieserie i två delar**. När du har slutfört den här självstudieserien kan du förutsäga kostnaden för en taxiresa genom att träna en modell med datafunktioner. Exempel på dessa funktioner är dag och tidpunkt för upphämtning, antalet passagerare och upphämtningsplats.

I den här kursen för du göra följande:

> [!div class="checklist"]
> * Konfigurera en Python-miljö och importera paketen.
> * Läsa in två datauppsättningar med olika fältnamn.
> * Rensa data för att ta bort avvikelser.
> * Transformera data med hjälp av intelligent transformering för att skapa nya funktioner.
> * Spara dataflödesobjektet som ska användas i en regressionsmodell.

## <a name="prerequisites"></a>Förutsättningar

Gå vidare till [Ställ in din utvecklingsmiljö](#start) och läs igenom stegen för notebook eller följ instruktionerna nedan för att hämta din notebook och kör den på Azure Notebooks eller din egen Notebook-server. För att köra anteckningsboken behöver du:

* En notebook-server för Python 3.6 med följande installerat:
    * Azure Machine Learning-SDK för dataförberedelse för Python
* Anteckningsboken för självstudie

Hämta alla dessa förutsättningar från något av avsnitten nedan.

* Använd [Azure Notebooks](#azure)
* Använd [din egen Notebook-server](#server)

### <a name="azure"></a>Använd Azure Notebooks: Kostnadsfria Jupyter-anteckningsböcker i molnet

Det är lätt att komma igång med Azure Notebooks! Azure Machine Learning Data Prep SDK har redan installerats och konfigurerats i [Azure Notebooks](https://notebooks.azure.com/). Installationen och framtida uppdateringar hanteras automatiskt via Azure-tjänster.

När du har slutfört stegen nedan kan du köra anteckningsboken **tutorials/regression-part1-data-prep.ipynb** i ditt **Komma igång**-projekt.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>Använda en egen Jupyter Notebook-server

Skapa en lokal Jupyter Notebook-server på datorn enligt nedan.  När du har slutfört stegen kan du köra anteckningsboken **tutorials/regression-part1-data-prep.ipynb**.

1. Slutför installationen av stegen i [ [Snabbstart för Azure Machine Learning Python](quickstart-run-local-notebook.md) ](setup-create-workspace.md#python) att skapa en Miniconda-miljö.  Passa på att hoppa över avsnittet **Skapa en arbetsyta** om du vill, men du behöver det för [del 2](tutorial-auto-train-models.md) i den här självstudieserien.
1. Installera SDK för dataförberedelser i din miljö med `pip install azureml-dataprep`.
1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starta notebook-servern från den klonade katalogen.

    ```shell
    jupyter notebook
    ```

## <a name="start"></a>Konfigurera din utvecklingsmiljö

All konfiguration under utvecklingsarbetet kan utföras i en Python-anteckningsbok. Konfigurationen inkluderar följande åtgärder:

* Installera SDK:n
* Importera Python-paket

### <a name="install-and-import-packages"></a>Installera och importera paket

Använd följande för att installera nödvändiga paket om du inte redan har dem.

```shell
pip install azureml-dataprep
```

Importera SDK:n.

```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>Läsa in data

Ladda ned två olika datauppsättningar för New York-taxi till dataflödesobjekt. Datauppsättningarna har lite olika fält. Metoden `auto_read_file()` identifierar automatiskt indatafiltypen.

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

Ett `Dataflow`-objekt liknar en dataram och representerar en serie Lazy-utvärderade, oföränderliga åtgärder med data. Åtgärder kan läggas till genom anrop till de olika tillgängliga transformerings- och filtreringsmetoderna. När du lägger till en åtgärd till ett `Dataflow` resulterar det alltid i ett nytt `Dataflow`-objekt.

## <a name="cleanse-data"></a>Rensa data

Nu fyller du i några variabler med genvägstransformeringar som ska gälla för alla dataflöden. Variabeln `drop_if_all_null` används för att ta bort poster där alla fält är null. Variabeln `useful_columns` innehåller en matris med kolumnbeskrivningar som finns kvar i varje dataflöde.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Du arbetar först med ”grön taxi”-data och gör dem till en giltig form som kan kombineras med ”gul taxi”-data. Anropa funktionerna `replace_na()`, `drop_nulls()` och `keep_columns()` med hjälp av de genvägstransformeringsvariabler som du har skapat. Byt också namn på alla kolumner i dataramen så att de matchar namnen i variabeln `useful_columns`.


```python
green_df = (green_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "VendorID": "vendor",
        "lpep_pickup_datetime": "pickup_datetime",
        "Lpep_dropoff_datetime": "dropoff_datetime",
        "lpep_dropoff_datetime": "dropoff_datetime",
        "Store_and_fwd_flag": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Pickup_longitude": "pickup_longitude",
        "Pickup_latitude": "pickup_latitude",
        "Dropoff_longitude": "dropoff_longitude",
        "Dropoff_latitude": "dropoff_latitude",
        "Passenger_count": "passengers",
        "Fare_amount": "cost",
        "Trip_distance": "distance"
     })
    .keep_columns(columns=useful_columns))
green_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>avstånd</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 08:14:37</td>
      <td>2013-08-01 09:09:06</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21.25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 09:13:00</td>
      <td>2013-08-01 11:38:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01 09:48:00</td>
      <td>2013-08-01 09:49:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01 10:38:35</td>
      <td>2013-08-01 10:38:51</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01 12:03:52</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

Utför samma transformeringssteg för ”gul taxi”-data. Dessa funktioner säkerställer att null-data tas bort från datauppsättningen, vilket förbättrar maskininlärningsmodellens precision.

```python
yellow_df = (yellow_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "vendor_name": "vendor",
        "VendorID": "vendor",
        "vendor_id": "vendor",
        "Trip_Pickup_DateTime": "pickup_datetime",
        "tpep_pickup_datetime": "pickup_datetime",
        "Trip_Dropoff_DateTime": "dropoff_datetime",
        "tpep_dropoff_datetime": "dropoff_datetime",
        "store_and_forward": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Start_Lon": "pickup_longitude",
        "Start_Lat": "pickup_latitude",
        "End_Lon": "dropoff_longitude",
        "End_Lat": "dropoff_latitude",
        "Passenger_Count": "passengers",
        "passenger_count": "passengers",
        "Fare_Amt": "cost",
        "fare_amount": "cost",
        "Trip_Distance": "distance",
        "trip_distance": "distance"
    })
    .keep_columns(columns=useful_columns))
yellow_df.head(5)
```

Anropa funktionen `append_rows()` för ”grön taxi”-data för att lägga till ”gul taxi”-data. En ny kombinerad dataram skapas.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Konvertera typer och filter

Undersök sammanfattande statistik för koordinaterna för upphämtning och avlämning för att se hur data är fördelade. Definiera först ett `TypeConverter`-objekt för att ändra fälten för latitud och longitud till decimaltyp. Anropa sedan funktionen `keep_columns()` för att begränsa utdata till endast fälten för latitud och longitud, och anropa sedan funktionen `get_profile()`. Dessa funktionsanrop skapar en komprimerad vy av dataflödet så att endast fälten för latitud och longitud visas, vilket gör det lättare att utvärdera koordinater som saknas eller som ligger utanför området i fråga.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Antal</th>
      <th>Antal saknas</th>
      <th>Antal saknas inte</th>
      <th>Procent saknas</th>
      <th>Antal fel</th>
      <th>Tomt antal</th>
      <th>0,1 % kvantil</th>
      <th>1 % kvantil</th>
      <th>5 % kvantil</th>
      <th>25 % kvantil</th>
      <th>50 % kvantil</th>
      <th>75 % kvantil</th>
      <th>95 % kvantil</th>
      <th>99 % kvantil</th>
      <th>99,9 % kvantil</th>
      <th>Standardavvikelse</th>
      <th>Medelvärde</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88.114046</td>
      <td>-73.961840</td>
      <td>-73.961964</td>
      <td>-73.947693</td>
      <td>-73.922097</td>
      <td>-73.846670</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.792672</td>
      <td>-68.833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>40.919121</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.682889</td>
      <td>40.675541</td>
      <td>40.721075</td>
      <td>40.756159</td>
      <td>40.803909</td>
      <td>40.849406</td>
      <td>40.870681</td>
      <td>40.891244</td>
      <td>10.345967</td>
      <td>37.936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87.699611</td>
      <td>-73.984734</td>
      <td>-73.985777</td>
      <td>-73.956250</td>
      <td>-73.928948</td>
      <td>-73.866208</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.696526</td>
      <td>-68.896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>41.008934</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.662763</td>
      <td>40.654851</td>
      <td>40.717821</td>
      <td>40.756534</td>
      <td>40.784688</td>
      <td>40.852437</td>
      <td>40.879289</td>
      <td>40.937291</td>
      <td>10.290780</td>
      <td>37.963774</td>
    </tr>
  </tbody>
</table>



I statistiksammanfattningen ser du att det finns koordinater som saknas och koordinater som inte ligger i New York City (detta fastställs från en subjektiv analys). Filtrera bort koordinater för platser som ligger utanför stadsgränsen. Kedja ihop kommandona för kolumnfilter i `filter()`-funktionen och definiera de lägsta och högsta gränserna för varje fält. Anropa sedan funktionen `get_profile()` igen för att verifiera transformeringen.


```python
latlong_filtered_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
    )
    .filter(dprep.f_and(
        dprep.col("pickup_longitude") <= -73.72,
        dprep.col("pickup_longitude") >= -74.09,
        dprep.col("pickup_latitude") <= 40.88,
        dprep.col("pickup_latitude") >= 40.53,
        dprep.col("dropoff_longitude") <= -73.72,
        dprep.col("dropoff_longitude") >= -74.09,
        dprep.col("dropoff_latitude") <= 40.88,
        dprep.col("dropoff_latitude") >= 40.53
    )))
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Antal</th>
      <th>Antal saknas</th>
      <th>Antal saknas inte</th>
      <th>Procent saknas</th>
      <th>Antal fel</th>
      <th>Tomt antal</th>
      <th>0,1 % kvantil</th>
      <th>1 % kvantil</th>
      <th>5 % kvantil</th>
      <th>25 % kvantil</th>
      <th>50 % kvantil</th>
      <th>75 % kvantil</th>
      <th>95 % kvantil</th>
      <th>99 % kvantil</th>
      <th>99,9 % kvantil</th>
      <th>Standardavvikelse</th>
      <th>Medelvärde</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.078156</td>
      <td>-73.736481</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.076314</td>
      <td>-73.962542</td>
      <td>-73.962893</td>
      <td>-73.948975</td>
      <td>-73.927856</td>
      <td>-73.866662</td>
      <td>-73.830438</td>
      <td>-73.823160</td>
      <td>-73.769750</td>
      <td>0.048711</td>
      <td>-73.913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.575485</td>
      <td>40.879852</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632884</td>
      <td>40.713105</td>
      <td>40.711600</td>
      <td>40.721403</td>
      <td>40.758142</td>
      <td>40.805145</td>
      <td>40.848855</td>
      <td>40.867567</td>
      <td>40.877690</td>
      <td>0.048348</td>
      <td>40.765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.085747</td>
      <td>-73.720871</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.078828</td>
      <td>-73.985650</td>
      <td>-73.985813</td>
      <td>-73.959041</td>
      <td>-73.936681</td>
      <td>-73.884846</td>
      <td>-73.815507</td>
      <td>-73.776697</td>
      <td>-73.733471</td>
      <td>0.055961</td>
      <td>-73.920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.583530</td>
      <td>40.879734</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.597741</td>
      <td>40.695376</td>
      <td>40.695115</td>
      <td>40.727549</td>
      <td>40.758160</td>
      <td>40.788378</td>
      <td>40.850372</td>
      <td>40.867968</td>
      <td>40.878586</td>
      <td>0.050462</td>
      <td>40.759487</td>
    </tr>
  </tbody>
</table>

### <a name="split-and-rename-columns"></a>Dela upp och byta namn på kolumner

Titta på dataprofilen för kolumnen `store_forward`. Det här fältet är en boolesk flagga som är `Y` om taxin inte har en anslutning till servern efter resan, och därför måste lagra resedata i minnet och skicka dem till servern när den är ansluten igen.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Antal</th>
      <th>Antal saknas</th>
      <th>Antal saknas inte</th>
      <th>Procent saknas</th>
      <th>Antal fel</th>
      <th>Tomt antal</th>
      <th>0,1 % kvantil</th>
      <th>1 % kvantil</th>
      <th>5 % kvantil</th>
      <th>25 % kvantil</th>
      <th>50 % kvantil</th>
      <th>75 % kvantil</th>
      <th>95 % kvantil</th>
      <th>99 % kvantil</th>
      <th>99,9 % kvantil</th>
      <th>Standardavvikelse</th>
      <th>Medelvärde</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>Y</td>
      <td>7059.0</td>
      <td>99.0</td>
      <td>6960.0</td>
      <td>0.014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



Observera att dataprofilens utdata i kolumnen `store_forward` visar att data är inkonsekventa och att det finns null-värden eller värden som saknas. Använd funktionerna `replace()` och `fill_nulls()` för att ersätta dessa värden med strängen ”N”:


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Kör funktionen `replace` på fältet `distance`. Funktionen formaterar om avståndsvärden som är felaktigt märkta som `.00`, och fyller eventuella null-värden med nollor. Konvertera fältet `distance` till numeriskt format. Dessa felaktiga datapunkter är sannolikt avvikelser i taxibilarnas datainsamlingssystem.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

Dela upp datum/tid-värdena för upphämtning eller lämning i deras respektive datum- och tidskolumner. Använd funktionen `split_column_by_example()` för att göra uppdelningen. I det här fallet utelämnas den valfria parametern `example` för `split_column_by_example()`-funktionen. Därför avgör funktionen automatiskt var delningen ska ske utifrån data.


```python
time_split_df = (replaced_distance_vals_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>avstånd</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25:00</td>
      <td>2013-08-01</td>
      <td>17:25:00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Byt namn på kolumnerna som genererats av funktionen `split_column_by_example()` till meningsfulla namn.

```python
renamed_col_df = (time_split_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
renamed_col_df.head(5)
```

Anropa funktionen `get_profile()` för att visa den fullständiga statistiksammanfattningen efter alla rensningssteg.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Omvandla data

Dela upp hämtnings- och lämningsdatumet ytterligare i värdena för dag i veckan, dag i månaden och månad. Hämta värdet för dagen i veckan med hjälp av funktionen `derive_column_by_example()`. Funktionen använder en matrisparameter med exempelobjekt som definierar indata, och önskade utdata. Funktionen avgör automatiskt vilken transformering du föredrar. För kolumnerna för upphämtnings- och lämningstid delar du upp tiden i timme, minut och sekund med hjälp av funktionen `split_column_by_example()` utan någon exempelparameter.

När du har genererat de nya funktionerna använder du funktionen `drop_columns()` för att ta bort de ursprungliga fälten eftersom de nygenererade funktionerna föredras. Byt namn på resten av fälten till meningsfulla namn.

Genom att transformera data på det här sättet för att skapa nya tidsbaserade funktioner kan maskininlärningsmodellens precision förbättras. Om en ny funktion för veckodagar exempelvis genereras skapar det en relation mellan dagen i veckan och taxiavgiften, som ofta är dyrare vissa dagar i veckan på grund av hög efterfrågan.


```python
transformed_features_df = (renamed_col_df
    .derive_column_by_example(
        source_columns="pickup_date",
        new_column_name="pickup_weekday",
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )

    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
    .split_column_by_example(source_column="pickup_time_1")
    .split_column_by_example(source_column="dropoff_time_1")
    .drop_columns(columns=[
        "pickup_date", "pickup_time", "dropoff_date", "dropoff_time",
        "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
    ])

    .rename_columns(column_pairs={
        "pickup_date_2": "pickup_month",
        "pickup_date_3": "pickup_monthday",
        "pickup_time_1_1": "pickup_hour",
        "pickup_time_1_2": "pickup_minute",
        "pickup_time_2": "pickup_second",
        "dropoff_date_2": "dropoff_month",
        "dropoff_date_3": "dropoff_monthday",
        "dropoff_time_1_1": "dropoff_hour",
        "dropoff_time_1_2": "dropoff_minute",
        "dropoff_time_2": "dropoff_second"
    }))

transformed_features_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>avstånd</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>Torsdag</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>Torsdag</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Torsdag</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25:00</td>
      <td>Torsdag</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>Tisdag</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>Tisdag</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Tisdag</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Tisdag</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Tisdag</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Tisdag</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Observera att data visar att tidskomponenterna för upphämtning och lämning som genererats från de härledda transformeringarna är korrekta. Ta bort kolumnerna `pickup_datetime` och `dropoff_datetime` eftersom de inte längre behövs (detaljerade tidsfunktioner som timme, minut och sekund är mer användbara för modellträning).


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Använd typinferensfunktionen för att automatiskt kontrollera datatypen för varje fält och visa inferensresultatet.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

Resultatet av `type_infer` är som följer.

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

Inferensresultatet ser ur att stämma baserat på data. Nu tillämpar du typkonverteringarna på dataflödet.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

Innan du paketerar dataflödet ska du köra två sista filter på datauppsättningen. För att minimera felaktigt registrerade datapunkter måste du filtrera dataflödet på poster där både `cost`- och `distance`-variabelvärdena är större än noll. Det här steget förbättrar avsevärt maskininlärningsmodellens precision eftersom datapunkter vars kostnader eller avstånd är noll representerar avvikande värden som snedvrider förutsägelsernas precision.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

Nu har du ett fullständigt transformerat och förberett dataflödesobjekt som ska användas i en maskininlärningsmodell. SDK innehåller funktioner för objektserialisering, som används som du ser i följande kod.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

package = dprep.Package([final_df])
package.save(file_path)
```

## <a name="clean-up-resources"></a>Rensa resurser

För att du ska kunna fortsätta med del två i självstudien måste filen **dflows.dprep** finnas i den aktuella katalogen.

Om du inte planerar att fortsätta till del två tar du bort filen **dflows.dprep** i den aktuella katalogen. Ta bort den här filen oavsett om du utför körningen lokalt eller i [Azure Notebooks](https://notebooks.azure.com/).

## <a name="next-steps"></a>Nästa steg

I del ett av den här självstudien har du:

> [!div class="checklist"]
> * Konfigurera din utvecklingsmiljö.
> * Inlästa och rensade datauppsättningar.
> * Använt smarta transformeringar för att förutsäga din logik baserat på ett exempel.
> * Slagit samman och paketerat datauppsättningar för maskininlärningsträning.

Nu är du redo att använda träningsdata i del två av självstudien:

> [!div class="nextstepaction"]
> [Självstudie (del två): Träna regressionsmodellen](tutorial-auto-train-models.md)
