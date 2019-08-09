---
title: Utforska och transformera data (data uppsättnings klass)
titleSuffix: Azure Machine Learning service
description: Utforska data med hjälp av sammanfattnings statistik och Förbered data genom data rensning, transformering och funktions teknik
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: dbdd088e5a78a4f78eec27b5ee74856c6aecc209
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847913"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Utforska och förbereda data med data uppsättnings klassen (förhands granskning)

Lär dig hur du utforskar och förbereder data med azureml-DataSets-paketet i [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Med [](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) data uppsättnings klassen (för hands version) kan du utforska och förbereda dina data genom att tillhandahålla funktioner som exempel: sampling, sammanfattnings statistik och intelligenta transformeringar. Omvandlings steg sparas i [data uppsättnings definitioner](how-to-manage-dataset-definitions.md) med möjlighet att hantera flera stora filer med olika scheman på ett mycket skalbart sätt.

> [!Important]
> Vissa data uppsättnings klasser (för hands version) är beroende av [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet (ga). När omvandlings funktioner kan utföras direkt med GA'ed- [data prepare](how-to-transform-data.md)rekommenderar vi paket för data uppsättnings paket som beskrivs i den här artikeln om du skapar en ny lösning. Azure Machine Learning data uppsättningar (för hands version) kan du inte bara transformera dina data, utan även [ögonblicks bild data och data](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) uppsättnings definitioner för [data](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)lager. Data uppsättningar är nästa version av data prep SDK och erbjuder utökade funktioner för hantering av data uppsättningar i AI-lösningar.

## <a name="prerequisites"></a>Förutsättningar

För att utforska och förbereda dina data behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En arbetsyta för Azure Machine Learning-tjänsten. Se [skapa en Azure Machine Learning service-arbetsyta](how-to-manage-workspace.md).

* Azure Machine Learning SDK för python (version 1.0.21 eller senare), som innehåller paketet azureml-data uppsättningar. Information om hur du installerar eller uppdaterar till den senaste versionen av SDK finns i [Installera eller uppdatera SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Azure Machine Learning data prep SDK. Information om hur du installerar eller uppdaterar till den senaste versionen finns i [Installera eller uppdatera data prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py).

* Hämta exempelfilerna som ska följas med exemplen: [brottslighet. csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) och [City. JSON](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Samling

Ta ett exempel på dina data för att få en inledande förståelse för din data arkitektur och ditt innehåll. Vid det här tillfället [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) stöder metoden från data uppsättnings klassen Top N, enkla slumpmässiga och Stratifieda samplings strategier.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Översta N-exemplet

För de N främsta samplingarna är de första n posterna i din data uppsättning ditt exempel. Detta är användbart om du bara försöker få en uppfattning om hur dina data poster ser ut eller för att se vilka fält som finns i dina data.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Ärendenummer|Date|Blockera|IUCR|Primär typ|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|BEDRÄGLIG METOD|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|STÖLD|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO ARA|1154|BEDRÄGLIG METOD|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE ARA|1120|BEDRÄGLIG METOD|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE ARA|890|STÖLD|...

### <a name="simple-random-sample"></a>Enkelt slumpmässigt exempel

I enkla Stick prov har alla medlemmar i data populationen samma möjlighet att väljas som en del av exemplet. `simple_random` I exempel strategin väljs posterna från din data uppsättning baserat på sannolikheten som anges och returnerar en modifierad data uppsättning. Parametern Seed är valfri.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Ärendenummer|Date|Blockera|IUCR|Primär typ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|STÖLD|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO ARA|1154|BEDRÄGLIG METOD|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE ARA|890|STÖLD|...

### <a name="stratified-sample"></a>Stratified-exempel

Stratified-exempel ser till att vissa grupper av en population representeras i exemplet. I exempel strategin är populationen indelad i Strata eller under grupper, baserat på likheter och poster väljs slumpmässigt från varje Strata enligt de Strata vikter `fractions` som anges av parametern. `stratified`

I följande exempel grupperar vi varje post efter de angivna kolumnerna och inkluderar denna post baserat på strata vikt-informationen i `fractions`. Om en Strata inte har angetts eller om posten inte kan grupperas, är standard vikt för att sampla 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|Ärendenummer|Date|Blockera|IUCR|Primär typ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|STÖLD|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE ARA|890|STÖLD|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN ARA|810|STÖLD|...

## <a name="explore-with-summary-statistics"></a>Utforska med sammanfattnings statistik

 Identifiera avvikelser, saknade värden eller antal fel med [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) metoden. Den här funktionen hämtar profil-och sammanfattnings statistik för dina data, vilket i sin tur hjälper dig att avgöra vilka data förberedelse åtgärder som krävs.

```Python
dataset.get_profile()
```

||type|Min|Max|Antal|Antal saknas|Antal saknas inte|Procent saknas|Antal fel|Tomt antal|0,1 % kvantil|1 % kvantil|5 % kvantil|25 % kvantil|50 % kvantil|75 % kvantil|95 % kvantil|99 % kvantil|99,9 % kvantil|Medelvärde|Standardavvikelse|Varians|Snedhet|Toppighet
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|– 0,495701|– 1,02814
Ärendenummer|FieldType.STRING|HZ239907|HZ278872|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Date|FieldType. DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Blockera|FieldType.STRING|004XX S KILBOURN ARA|113XX S PRAIRIE ARA|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType. INTEGER|810|1154|10,0|0.0|10,0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058,5|137,285|18847,2|– 0,785501|– 1,3543
Primär typ|FieldType.STRING|BEDRÄGLIG METOD|STÖLD|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Beskrivning|FieldType.STRING|FALSK KONTROLL|ÖVER $500|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Platsbeskrivning|FieldType.STRING||SKOLA, OFFENTLIG, BYGGNAD|10,0|0.0|10,0|0.0|0.0|1.0||||||||||||||
Kvarhållande|FieldType. BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Inrikes|FieldType. BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Taktslag|FieldType. INTEGER|531|2433|10,0|0.0|10,0|0.0|0.0|0.0|531|531|531|614|1318,5|1911|2433|2433|2433|1371,1|692,094|478994|0,105418|– 1,60684
Distrikt|FieldType. INTEGER|5|24|10,0|0.0|10,0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6,94822|48,2778|0,0930109|– 1,62325
Ward|FieldType. INTEGER|1|48|10,0|0.0|10,0|0.0|0.0|0,0|1|5|1|9|22.5|40|48|48|48|24.5|16,2635|264,5|0,173723|– 1,51271
Community-området|FieldType. INTEGER|4|77|10,0|0.0|10,0|0.0|0.0|0.0|4|8.5|4|24|37,5|71|77|77|77|41,2|26,6366|709,511|0,112157|– 1,73379
FBI: S kod|FieldType. INTEGER|6|11|10,0|0.0|10,0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2,36643|5.6|– 0,702685|– 1,59582
X-koordinat|FieldType. INTEGER|1.16309 e + 06|1.18336 e + 06|10,0|7.0|3.0|0,7|0.0|0.0|1.16309 e + 06|1.16309 e + 06|1.16309 e + 06|1.16401e+06|1.16678 e + 06|1.17921e+06|1.18336 e + 06|1.18336 e + 06|1.18336 e + 06|1.17108 e + 06|10793,5|1.165 e + 08|0,335126|– 2,33333
Y-koordinaten|FieldType. INTEGER|1.8315 e + 06|1.908 e + 06|10,0|7.0|3.0|0,7|0.0|0.0|1.8315 e + 06|1.8315 e + 06|1.8315 e + 06|1.83614e+06|1.85005 e + 06|1.89352 e + 06|1.908 e + 06|1.908 e + 06|1.908 e + 06|1.86319 e + 06|39905,2|1.59243 e + 09|0,293465|– 2,33333
År|FieldType. INTEGER|2016|2016|10,0|0.0|10,0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Uppdaterad den|FieldType. DATE|2016-05-11 15:48:00 + 00:00|2016-05-27 15:45:00 + 00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Latitud|FieldType.DECIMAL|41,6928|41,9032|10,0|7.0|3.0|0,7|0.0|0.0|41,6928|41,6928|41,6928|41,7057|41,7441|41,8634|41,9032|41,9032|41,9032|41,78|0,109695|0,012033|0,292478|– 2,33333
Longitud|FieldType.DECIMAL|– 87,6764|– 87,6043|10,0|7.0|3.0|0,7|0.0|0.0|– 87,6764|– 87,6764|– 87,6764|– 87,6734|– 87,6645|– 87,6194|– 87,6043|– 87,6043|– 87,6043|– 87,6484|0,0386264|0,001492|0,344429|– 2,33333
Location|FieldType.STRING||(41,903206037,-87,676361925)|10,0|0.0|10,0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Sedan imputera värden som saknas

I data uppsättningar, null-värden, NaN och värden som inte innehåller något innehåll betraktas värden som saknas. Dessa kan påverka prestandan för dina maskin inlärnings modeller eller leda till ogiltiga slut satser. Imputation är en vanlig metod för att hantera värden som saknas och är användbart när du har en hög procent av saknade värde poster som du inte bara tar bort.

Från den data uppsättnings profil som genererades i föregående avsnitt ser `Latitude` vi `Longitude` att och kolumnerna har en hög procent andel av saknade värden. I det här exemplet beräknar vi medelvärde-och tillräknade värden som saknas för dessa två kolumner.

Först hämtar du den senaste definitionen av data uppsättningen med [`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) och avrundar data med [`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow), så vi visar bara de kolumner som vi vill adressera.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|Kvarhållande| Latitud|Longitud|
-|---------|-----|---------|----------|
|0|10498554|False|41,692834|– 87,604319|
|1|10516598|False| 41,744107 |– 87,664494|
|2|10519196|False| NaN|NaN|

Sedan kontrollerar du `MEAN` värdet för kolumnen Latitude [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) med funktionen. Den här funktionen accepterar en matris med kolumner i den `group_by_columns` parametern för att ange nivå för aggregering. Parametern godkänner funktionen, som anger det aktuella kolumn namnet, det nya beräknade `SummaryFunction` fält namnet och för att utföra. `SummaryColumnsValue` `summary_columns`

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
|0|False|41,780049|

När vi har verifierat värdena för att räkna med [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) , använder du för att lära dig ett fast uttryck som tillräknar `MIN`kolumnerna `MEAN` med antingen ett `MAX`beräknat `CUSTOM` värde, värde eller ett värde. När `group_by_columns` har angetts värden som saknas kommer imputeras gruppvis med `MIN`, `MAX`, och `MEAN` beräknas per grupp.

Accepterar en column_id-sträng och en `ReplaceValueFunction` för att ange imputerade typ. [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) För det saknade värdet longitud tillräknar du det med-87 baserat på extern kunskap.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Imputerade steg kan sammanställas tillsammans i ett `ImputeMissingValuesBuilder` objekt med hjälp av [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) klass funktionen. [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder) Den `impute_columns` parametern accepterar en matris med `ImputeColumnArguments` objekt.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Anropa funktionen för att lagra de imputerade stegen och tillämpa dem på ett data flödes objekt med [`to_dataflow()`.](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow) [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none)

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Som du ser i följande utdataparameter räknades den saknade latitud med `MEAN` värdet för `Arrest==False` gruppen och den saknade longitud tillräknades med-87.

||id|Kvarhållande|Latitud|Longitud
-|---------|-----|---------|----------
0|10498554|False|41,692834|– 87,604319
1|10516598|False|41,744107|– 87,664494
2|10519196|False|41,780049|– 87,000000

Uppdatera data uppsättnings definitionen med [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) , för att behålla de utförda omvandlings stegen.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Kvarhållande|Latitud|Longitud
-|---------|-----|---------|----------
0|10498554|False|41,692834|– 87,604319
1|10516598|False|41,744107|– 87,664494
2|10519196|False|41,780049|– 87,000000

## <a name="create-assertion-rules"></a>Skapa regler för kontroll

Data vi arbetar ofta med när du rensar och förbereder data är bara en del av de totala data som vi behöver för produktion. Därför kan vissa av de antaganden som vi har gjort som en del av vår rengöring bli falsk. I en data uppsättning som uppdateras kontinuerligt, kan en kolumn som ursprungligen bara innehöll siffror inom ett visst intervall innehålla ett bredare värde intervall i senare körningar. Dessa fel resulterar ofta i antingen brutna pipeliner eller felaktiga data.

Data uppsättningar har stöd för att skapa kontroller av data som utvärderas när pipelinen körs. Dessa intyg gör att vi kan kontrol lera att våra antaganden om data fortsätter att vara korrekta och, om inte, för att hantera fel i enlighet med detta.

Om du till exempel vill begränsa `Latitude` och `Longitude` värden i data uppsättningen till [`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) vissa numeriska intervall säkerställer metoden att det alltid är fallet.

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

||type|Min|Max|Antal|Antal saknas|Antal saknas inte|Procent saknas|Antal fel|Tomt antal|0,1 % kvantil|1 % kvantil|5 % kvantil|25 % kvantil|50 % kvantil|75 % kvantil|95 % kvantil|99 % kvantil|99,9 % kvantil|Medelvärde|Standardavvikelse|Varians|Snedhet|Toppighet
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|– 0,495701|– 1,02814
Kvarhållande|FieldType. BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Latitud|FieldType.DECIMAL|41,6928|41,9032|10,0|0.0|10,0|0.0|0.0|0.0|41,6928|41,7185|41,6928|41,78|41,78|41,78|41,9032|41,9032|41,9032|41,78|0,0517107|0,002674|0,837593|1,05
Longitud|FieldType. INTEGER|-87|-87|10,0|0.0|10,0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

Från profilen ser du att `Error Count` `Longitude` kolumnen för är 3. Följande kod filtrerar data uppsättningen, hämtar felet och ser vilket värde som gör att kontrollen inte fungerar. Härifrån kan du justera koden och rensa dina data på rätt sätt.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Härled kolumner med exempel

Ett av de mer avancerade verktygen för data uppsättningar är möjligheten att härleda kolumner med exempel på önskade resultat. På så sätt kan du ge SDK ett exempel, så det kan generera kod för att uppnå avsedda transformeringar.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Ärendenummer|Date|Blockera|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO ARA|...

Anta att du måste transformera datum-och tids formatet till "2016-04-04 10PM-12AM". I argumentet anger du exempel på önskade utdata `example_data` i parametern i det här formatet: *(ursprungligt utdata, önskade utdata)* . [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow)

Följande kod ger två exempel på önskade utdata ("2016-04-04 23:56:00", "2016-04-04 10PM-12AM") och ("2016-04-15 17:00:00", "2016-04-15 4PM-. 18:00")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

I följande tabell ser du att en ny kolumn, Date_Time_Range innehåller poster i det angivna formatet.

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Fuzzy-grupperingar

När du samlar in data från olika källor kan du stöta på variationer i stavning, versaler eller förkortningar för samma entiteter. Standardisera och Stäm av dessa varianter automatiskt med SDK: n för fuzzy Grouping eller text klustring, funktionalitet.

Kolumnen `inspections.business.city` innehåller till exempel flera former av Orts namnet "San Francisco".

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspektioner. business_name|inspections.business.address|inspektioner. Business. City|...|
-|-----|-------------------------|------------|--|---
0|16162|Quick-N-Ezee indiska livsmedel|3861 24 st|SF|...
1|67565|Kung av Thai Noodles-Cafe|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|Kung av Thai Noodles-Cafe|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 Clement St|SF|...
4|69186|Premier catering & Events, Inc.|1255 22nd St|S.F.|...

Vi använder [`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) metoden för att lägga till en kolumn med den automatiskt identifierade kanoniska formen "San Francisco". Argumenten `source_column` och `new_column_name` är obligatoriska. Du kan också välja att:

* Skapa en ny kolumn, `similarity_score_column_name`som visar likhets poängen mellan varje par med ursprungliga och kanoniska värden.

* Ange ett `similarity_threshold`, vilket är den minsta likhets poängen för värdena som ska grupperas tillsammans.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspektioner. business_name|inspections.business.address|inspektioner. Business. City|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Quick-N-Ezee indiska livsmedel|3861 24 st|SF|San Francisco|0,814806|...
1|67565|Kung av Thai Noodles-Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1,000000|...
2|67565|Kung av Thai Noodles-Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1,000000|...
3|68701|Grindz|832 Clement St|SF|San Francisco|0,814806|...
4|69186|Premier catering & Events, Inc.|1255 22nd St|S.F.|San Francisco|0,814806|...

I den resulterande data uppsättnings definitionen var alla olika variationer som representerar "San Francisco" i data normaliserade till samma sträng, "San Francisco".

Spara det här fuzzy Grouping-steget i den senaste `update_definition()`definitionen av data uppsättningen med.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Nästa steg

* I den automatiska självstudien om Machine Learning finns ett exempel på en Regressions modell. [](tutorial-auto-train-models.md)

* Se [Översikt över](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) SDK för design mönster och användnings exempel.

* Ett exempel på hur du använder data uppsättningar finns i [exempel antecknings böckerna](https://aka.ms/dataset-tutorial).
