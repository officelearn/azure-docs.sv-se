---
title: 'Skriva: data prepare python SDK'
titleSuffix: Azure Machine Learning service
description: Lär dig mer om hur du skriver data med Azure Machine Learning Data Prep SDK. Du kan skriva ut data när som helst i ett dataflöde och till filer i någon av våra platser som stöds (lokalt filsystem, Azure Blob Storage och Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6753be5613b10b64936cddaafbb9859aad837b02
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358634"
---
# <a name="write-and-configure-data--with-the-azure-machine-learning-data-prep-sdk"></a>Skriv och konfigurera data med Azure Machine Learning data prep SDK

I den här artikeln får du lära dig olika metoder för att skriva data med hjälp av [Azure Machine Learning data prepare-SDK](https://aka.ms/data-prep-sdk) och hur du konfigurerar data för experimentering med [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Utdata kan skrivas när som helst i ett data flöde. Skrivningar läggs till som steg till det resulterande data flödet och de här stegen körs varje gång data flödet körs. Data skrivs till partitionsfiler med flera Tillåt parallella skrivningar.

> [!Important]
> Om du skapar en ny lösning kan du prova [Azure Machine Learning data uppsättningar](how-to-explore-prepare-data.md) (för hands version) för att transformera data, ögonblicks bild data och definitioner för data uppsättning för data lager. Data uppsättningar är nästa version av data prep SDK och erbjuder utökade funktioner för hantering av data uppsättningar i AI-lösningar.
> Om du använder `azureml-dataprep` paketet för att skapa ett data flöde med dina omvandlingar i stället för att `azureml-datasets` använda paketet för att skapa en data uppsättning kan du inte använda ögonblicks bilder eller versioner av data uppsättningar senare.

Eftersom det finns inga begränsningar för hur många skriva steg finns i en pipeline, kan du enkelt lägga till ytterligare skrivning steg för att få resultat för felsökning eller andra pipeliner.

Varje gång du kör ett skrivning steg inträffar för en fullständig hämtning av data i dataflödet. Ett dataflöde med tre steg för skrivning kommer till exempel läsa och bearbeta varje post i datauppsättningen tre gånger.

## <a name="supported-data-types-and-location"></a>Datatyper som stöds och plats

Följande filformat som stöds
-   Avgränsade filer (CSV, TVS osv.)
-   Parquet-filer

Med hjälp av Azure Machine Learning data prepare python SDK kan du skriva data till:
+ ett lokalt filsystem
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Spark-överväganden

När du kör ett dataflöde i Spark, måste du skriva till en tom mapp. Försök att köra en skrivning till en befintlig mapp resulterar i ett fel. Kontrollera att målmappen är tom eller Använd en annan målplats för varje körning eller Skrivåtgärden misslyckas.

## <a name="monitoring-write-operations"></a>Övervaka skrivåtgärder

För din bekvämlighet genereras en sentinel-fil med namnet lyckades när en skrivning har slutförts. Sin närvaro hjälper dig att identifiera när en mellanliggande skrivning har slutförts utan att behöva vänta på att slutföra hela pipelinen.

## <a name="example-write-code"></a>Exempelkod för skrivning

I det här exemplet börjar du med att läsa in data i ett `auto_read_file()`data flöde med hjälp av. Du kan återanvända dessa data med olika format.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Exempel på utdata:

| | Kolumn1 | Kolumn2 | Kolumn3 | Kolumn4 | Column5 | Kolumn6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | Ingen | NO | NO | ENRS | NaN | NaN | NaN |   
|1| 10003.0 | 99999.0 | Ingen | NO | NO | ENSO | NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | Ingen | NO | JN | ENJA | 70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | Ingen | NO | NO |      | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | Ingen | NO | NO | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>Avgränsad fil-exempel

I följande kod används [`write_to_csv()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-csv-directory-path--datadestination--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) funktionen för att skriva data till en avgränsad fil.

```python
# Create a new data flow using `write_to_csv`
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Exempel på utdata:

| | Kolumn1 | Kolumn2 | Kolumn3 | Kolumn4 | Column5 | Kolumn6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | FEL | NO | NO | ENRS | NaN    | NaN | NaN |   
|1| 10003.0 | 99999.0 | FEL | NO | NO | ENSO |    NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | FEL | NO | JN | ENJA |    70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | FEL | NO | NO |     | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | FEL | NO | NO | ENSO |    59783.0 | 5350.0 |  500.0|

I den föregående utdatan visas flera fel i de numeriska kolumnerna på grund av tal som inte tolkades rätt. När skrivs till CSV, har null-värden ersatts med strängen ”ERROR” som standard.

Lägg till parametrar som en del av dina skrivåtgärder anropa och ange en sträng som ska använda för att representera null-värden.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'),
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Föregående kod ger dessa utdata:

| | Kolumn1 | Kolumn2 | Kolumn3 | Kolumn4 | Column5 | Kolumn6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | NO | NO | ENRS | NaN  | NaN | NaN |   
|1| 10003.0 | 99999.0 | BadData | NO | NO | ENSO |  NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BadData | NO | JN | ENJA |  70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BadData | NO | NO |   | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BadData | NO | NO | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Exempel på en parquet

`write_to_csv()` [På`write_to_parquet()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-parquet-file-path--typing-union--datadestination--nonetype----none--directory-path--typing-union--datadestination--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) liknande sätt returnerar funktionen ett nytt data flöde med ett Skriv Parquet-steg som körs när data flödet körs.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
                                     error='MiscreantData')
```

Kör dataflödet för att starta Skrivåtgärden.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

Föregående kod ger dessa utdata:

|   | Kolumn1 | Kolumn2 | Kolumn3 | Kolumn4 | Column5 | Kolumn6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | NO | NO | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | NO | NO | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0 | 99999.0 | MiscreantData | NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | NO| NO| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Konfigurera data för automatisk utbildning av Machine Learning

Skicka din nyligen skrivna datafil till ett [`AutoMLConfig`](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) objekt som förberedelse för automatisk utbildning av maskin inlärning. 

Följande kod exempel illustrerar hur du konverterar ditt data flöde till en Pandas-dataframe och sedan delar in det i utbildning och test data uppsättningar för automatisk utbildning av maskin inlärning.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

Om du inte behöver några steg för förberedelse av mellanliggande data som i föregående exempel, kan du skicka ditt data `AutoMLConfig`flöde direkt till.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>Nästa steg
* Se [Översikt över](https://aka.ms/data-prep-sdk) SDK för design mönster och användnings exempel 
* Se den automatiska självstudien om maskin inlärning för en Regressions modell exempel [](tutorial-auto-train-models.md)
