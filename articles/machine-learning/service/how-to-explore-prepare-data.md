---
title: Utforska och förbereda data (datauppsättningen klassen)
titleSuffix: Azure Machine Learning service
description: Utforska data med hjälp av sammanfattande statistik och förbereda data via datarensning, omvandling och funktioner
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: f4e7fcbe403017a6d957a60a8e5664f2e6c5ba26
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409828"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Utforska och förbereda data med klassen datauppsättning (förhandsgranskning)

Lär dig att utforska och förbereda data med den [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Den [datauppsättning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) klass (förhandsversion) kan du utforska och förbereda dina data genom att tillhandahålla funktioner som: sampling, sammanfattande statistik och intelligent transformationer. Transformeringssteg sparas i [datauppsättning definitioner](how-to-manage-dataset-definitions.md) möjlighet att hantera flera stora filer med olika scheman i en mycket skalbar.

> [!Important]
> Vissa datauppsättning (förhandsversion) som har beroenden på Data Prep SDK (GA). Även om omvandlingen funktioner kan göras direkt med GA'ed [Data Prep SDK-funktioner](how-to-transform-data.md), rekommenderar vi datauppsättning paketet omslutningar som beskrivs i den här artikeln om du skapar en ny lösning. Azure Machine Learning datauppsättningar (förhandsversion) kan du inte bara omvandla data, utan även [domänögonblicksdata](how-to-create-dataset-snapshots.md) och lagra [version datauppsättning definitioner](how-to-manage-dataset-definitions.md). Datauppsättningar är nästa version av Data Prep-SDK, som erbjuder fler funktioner för att hantera datauppsättningar i AI-lösningar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att utforska och förbereda dina data, behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En arbetsyta för Azure Machine Learning-tjänsten. Se [skapa en arbetsyta för Azure Machine Learning-tjänsten](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Azure Machine Learning-SDK för Python (version 1.0.21 eller senare). Om du vill installera eller uppdatera till den senaste versionen av SDK, se [installera eller uppdatera SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* I Azure Machine Learning Dataförberedelser SDK. Om du vill installera eller uppdatera till den senaste versionen, se [installera eller uppdatera Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Ladda ned exempelfilerna du följa exemplen: [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) och [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Samling

Ta ett exempel på dina data för att få ett inledande förståelse för dina dataarkitektur och innehåll. För närvarande den [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) metod från klassen datauppsättning stöder Top N enkel slumpmässiga och Stratified sampling strategier.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Högsta N-exempel

För Top N sampling är de första n posterna av din datauppsättning ditt exempel. Det här är användbart om du bara vill få en uppfattning om vad din data poster Se som eller för att se vilka fält som används i dina data.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||ID|Ärendenummer|Date|Blockera|IUCR|Primär typ|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|BEDRÄGERIFÖRSÖK|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|STÖLD|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO PARA|1154|BEDRÄGERIFÖRSÖK|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE PARA|1120|BEDRÄGERIFÖRSÖK|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|STÖLD|...

### <a name="simple-random-sample"></a>Enkel slumpmässigt urval

I enkla stickprov tas har alla medlemmar i ifyllnad av data samma chans väljs som en del av exemplet. I den `simple_random` samplingsstrategi, poster från din datauppsättning baseras på sannolikheten anges och returnerar en ändrad datauppsättning. Seed-parametern är valfri.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||ID|Ärendenummer|Date|Blockera|IUCR|Primär typ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|STÖLD|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO PARA|1154|BEDRÄGERIFÖRSÖK|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|STÖLD|...

### <a name="stratified-sample"></a>Stratified exempel

Stratified exempel se till att vissa grupper från en population visas i exemplet. I den `stratified` samplingsstrategi, populationen är uppdelat i strata eller undergrupper, baserat på likheter, och poster väljs slumpmässigt från varje strata enligt strata vikterna anges av den `fractions` parametern.

I följande exempel vi gruppera posterna efter de angivna kolumnerna och inkludera detta register baserat på vikt strata X informationen i `fractions`. Om en strata inte har angetts eller posten kan inte grupperas, är standard-vikt till exempel 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||ID|Ärendenummer|Date|Blockera|IUCR|Primär typ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|STÖLD|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|STÖLD|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN PARA|810|STÖLD|...

## <a name="explore-with-summary-statistics"></a>Utforska med sammanfattande statistik

 Identifiera avvikelser, saknar värden, eller fel antal med den [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) metod. Hämtar profilen för den här funktionen och statistisk information om dina data, vilket i sin tur hjälper fastställa nödvändiga data förberedelse åtgärder att tillämpa.

```Python
dataset.get_profile()
```

||Typ|Min|Max|Antal|Antal saknas|Antal saknas inte|Procent saknas|Antal fel|Tomt antal|0,1 % kvantil|1 % kvantil|5 % kvantil|25 % kvantil|50 % kvantil|75 % kvantil|95 % kvantil|99 % kvantil|99,9 % kvantil|Medelvärde|Standardavvikelse|Varians|Snedhet|Toppighet
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e+07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Ärendenummer|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Blockera|FieldType.STRING|004XX S KILBOURN PARA|113XX S PRAIRIE PARA|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Primär typ|FieldType.STRING|BEDRÄGERIFÖRSÖK|STÖLD|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Beskrivning|FieldType.STRING|MUSENHETEN KONTROLL|ÖVER 500 USD|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Platsbeskrivning|FieldType.STRING||SKOLAN, OFFENTLIG, ATT SKAPA|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Kvarhållande|FieldType.BOOLEAN|Falskt|Falskt|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Inrikes|FieldType.BOOLEAN|Falskt|Falskt|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Distrikt|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0,0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Community-området|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI: S kod|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
X-koordinat|FieldType.INTEGER|1.16309e+06|1.18336e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401e+06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108e + 06|10793.5|1.165e + 08|0.335126|-2.33333
Y-koordinaten|FieldType.INTEGER|1.8315e+06|1.908e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e+06|1.8315e+06|1.8315e+06|1.83614e+06|1.85005e+06|1.89352e+06|1.908e + 06|1.908e + 06|1.908e + 06|1.86319e+06|39905.2|1.59243e+09|0.293465|-2.33333
År|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Uppdaterad den|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Latitud|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Longitud|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Sedan imputera värden som saknas

I datauppsättningar anses null-värden, Nans och värden som innehåller inget innehåll värden som saknas. Dessa kan påverka prestandan för dina maskininlärningsmodeller eller leda till ogiltig slutsatser. Uppräkning är ett vanligt sätt att åtgärda saknade värden och är användbart när du har en hög andel saknar värde poster som du inte kan helt enkelt ta bort.

Från den datauppsättning profilen som genererats i föregående avsnitt, ser vi att `Latitude` och `Longitude` kolumner har en hög andel värden som saknas. I det här exemplet vi beräkna medelvärdet och sedan imputera saknas värden för dessa två kolumner.

Först hämtar de senaste definitionerna av datauppsättningen med [ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) och skära ned data med [ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#keep-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow), så att vi endast visa de kolumner vi vill adress.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||ID|Kvarhållande| Latitud|Longitud|
-|---------|-----|---------|----------|
|0|10498554|Falskt|41.692834|-87.604319|
|1|10516598|Falskt| 41.744107 |-87.664494|
|2|10519196|Falskt| NaN|NaN|

Därefter kontrollerar du den `MEAN` värdet för en latitud kolumn med hjälp av den [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) funktion. Den här funktionen accepterar en matris med kolumner i den `group_by_columns` parametern för att ange nivå för aggregering. Den `summary_columns` parametern accepterar den `SummaryColumnsValue` som anger kolumnnamnet på aktuella nya beräknade fältnamn och `SummaryFunction` att utföra.

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Kvarhållande|Latitude_MEAN|
--|-----|--------|
|0|Falskt|41.780049|

När vi Kontrollera värdena sedan imputera använda [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) att lära dig en fast uttryck som imputes kolumner med antingen ett beräknat `MIN`, `MAX`, `MEAN` värde, eller en `CUSTOM` värde. När `group_by_columns` har angetts värden som saknas kommer imputeras gruppvis med `MIN`, `MAX`, och `MEAN` beräknas per grupp.

Den [ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) godkänner en column_id sträng och en `ReplaceValueFunction` kan ange vilken typ av impute. Sedan imputera det med-87 baserat på externa kunskapskällor för longitudvärdet saknas.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Sedan imputera steg kan sammanlänkas i en `ImputeMissingValuesBuilder` objekt med hjälp av den [ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) klassen funktionen [ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder). Den `impute_columns` parametern accepterar en matris med `ImputeColumnArguments` objekt.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Anropa den [ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) för att lagra impute steg och koppla dem till en dataflöde objekt med [ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow).

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

I följande utdatatabell visas saknas latitud har tillräknade med den `MEAN` värdet för `Arrest==False` grupp och saknas longitud har tillräknade med-87.

||ID|Kvarhållande|Latitud|Longitud
-|---------|-----|---------|----------
0|10498554|Falskt|41.692834|-87.604319
1|10516598|Falskt|41.744107|-87.664494
2|10519196|Falskt|41.780049|-87.000000

Uppdatera definitionen för datauppsättningen med, [ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) att hålla utförs transformeringssteg.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||ID|Kvarhållande|Latitud|Longitud
-|---------|-----|---------|----------
0|10498554|Falskt|41.692834|-87.604319
1|10516598|Falskt|41.744107|-87.664494
2|10519196|Falskt|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Skapa regler för försäkran

Ofta, data vi arbetar med vid rensning och förbereda data är bara en delmängd av den totala mängden data som vi behöver för produktion. Därför kan visa vissa av de antaganden som vi gör som en del av vår rensning sig ha värdet FALSKT. Exempelvis kan en kolumn som ursprungligen endast innehåller siffror inom ett visst intervall i en datamängd som uppdaterar kontinuerligt, innehålla ett bredare spektrum av värdena i senare körningar. Dessa fel leder ofta bruten pipelines eller felaktiga data.

Stöd för datauppsättningar Skapa intyg för data som utvärderas som pipeline utför. Dessa intyg låta oss att verifiera att vår antaganden på data fortsätter att vara korrekta och, om det inte, kan hantera fel på lämpligt sätt.

Exempel: Om du vill begränsa `Latitude` och `Longitude` värden i din specifika numeriska intervall i [ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) metoden garanterar detta gäller alltid.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Typ|Min|Max|Antal|Antal saknas|Antal saknas inte|Procent saknas|Antal fel|Tomt antal|0,1 % kvantil|1 % kvantil|5 % kvantil|25 % kvantil|50 % kvantil|75 % kvantil|95 % kvantil|99 % kvantil|99,9 % kvantil|Medelvärde|Standardavvikelse|Varians|Snedhet|Toppighet
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e+07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Kvarhållande|FieldType.BOOLEAN|Falskt|Falskt|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Latitud|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1,05
Longitud|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

Från profilen, ser du att den `Error Count` för den `Longitude` kolumnen är 3. Följande kod filtrerar datauppsättningen, hämtar felet och ser vilken värde orsakar kontrollen misslyckas. Härifrån kan du justera din kod och rensa dina data på rätt sätt.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Härled kolumner med exempel

En av de mer avancerade verktyg för datauppsättningar är möjligheten att härleda kolumner med hjälp av exempel på önskat resultat. På så sätt kan du ge SDK exempelvis så att det kan generera kod för att uppnå avsedda omvandlingarna.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||ID|Ärendenummer|Date|Blockera|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO PARA|...

Anta att du vill omvandla formatet datum och tid till 2016-04-04 10 PM - 12: 00 ”. I den [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) argument, innehåller exempel på önskade utdata i den `example_data` parametern i det här formatet: *(ursprungliga utdata, önskad utdata)*.

Följande kod innehåller två exempel på önskade utdata (”2016-04-04 23:56:00” ”, 2016-04-04 10 PM-kl. 12”) och (”2016-04-15 17:00:00” ”, 2016-04-15 4 PM - 18: 00”)

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Observera att en ny kolumn Date_Time_Range innehåller poster i det format som anges i tabellen nedan.

||ID|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Fuzzy grupperingar

När du samlar in data från olika källor kan som uppstå variationer i stavning, versaler eller förkortningar av samma entiteter. Standardisera och stämma av dessa varianter med SDK: er fuzzy gruppering eller text klustring, funktionen automatiskt.

Till exempel kolumnen `inspections.business.city` innehåller flera typer av den stad namnet ”San Francisco”.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.Business.City|...|
-|-----|-------------------------|------------|--|---
0|16162|Snabbstart-N-Ezee indiska livsmedel|3861 24th St|SF|...
1|67565|King av thailändska nudlar Cafe|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|King av thailändska nudlar Cafe|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 clement St|SF|...
4|69186|Premier restaurang & händelser, Inc.|1255 22nd St|S.F.|...

Nu ska vi använda den [ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) metod för att lägga till en kolumn med den automatiskt identifierade kanoniska formen av ”San Francisco”. Argumenten `source_column` och `new_column_name` krävs. Du har också möjlighet att:

* Skapa en ny kolumn `similarity_score_column_name`, som visar poängen likheter mellan varje par med ursprungliga och kanoniska värden.

* Ange en `similarity_threshold`, vilket är den minsta likhet poängen för värdena som ska grupperas tillsammans.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.Business.City|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Snabbstart-N-Ezee indiska livsmedel|3861 24th St|SF|San Francisco|0.814806|...
1|67565|King av thailändska nudlar Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|King av thailändska nudlar Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|832 clement St|SF|San Francisco|0.814806|...
4|69186|Premier restaurang & händelser, Inc.|1255 22nd St|S.F.|San Francisco|0.814806|...

I den resulterande datauppsättningen har olika varianter av som representerar ”San Francisco” data normaliseras till samma sträng, ”San Francisco”.

Spara fuzzy gruppering steget i den senaste datauppsättningsdefinitionen med `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Nästa steg

* [Hantera livscykeln för datauppsättningen definitioner](how-to-manage-dataset-definitions.md).

* Se den automatiserade machine learning [självstudien](tutorial-auto-train-models.md) till exempel en regression-modellen.

* Finns i SDK [översikt](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) designmönster och användningsexempel.

* Ett exempel på hur du använder datauppsättningar finns i den [exempel anteckningsböcker](https://aka.ms/dataset-tutorial).