---
title: Skriva data med Azure Machine Learning Data Prep SDK – Python
description: Lär dig mer om hur du skriver data med Azure Machine Learning Data Prep SDK. Du kan skriva ut data när som helst i ett dataflöde och till filer i någon av våra platser som stöds (lokalt filsystem, Azure Blob Storage och Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 81344d388fbba0db034b8adb06adab6797ec2ce1
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166756"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Skriva data med hjälp av Azure Machine Learning Data Prep SDK
Du kan skriva ut data när som helst i ett dataflöde. Dessa skrivningar läggs till som steg för att det resulterande dataflödet och körs varje gång dataflödet finns. Data skrivs till partitionsfiler med flera Tillåt parallella skrivningar.

Eftersom det finns inga begränsningar för hur många skriva steg finns i en pipeline, kan du enkelt lägga till ytterligare skrivning steg för att få resultat för felsökning eller andra pipeliner. 

Varje gång du kör ett skrivning steg inträffar för en fullständig hämtning av data i dataflödet. Ett dataflöde med tre steg för skrivning kommer till exempel läsa och bearbeta varje post i datauppsättningen tre gånger.

## <a name="supported-data-types-and-location"></a>Datatyper som stöds och plats

Följande filformat som stöds
-   Avgränsade filer (CSV, TVS osv.)
-   Parquet-filer

Med hjälp av den [Azure Machine Learning Data Prep python SDK](https://aka.ms/data-prep-sdk), du kan skriva data till:
+ ett lokalt filsystem
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Spark-överväganden
När du kör ett dataflöde i Spark, måste du skriva till en tom mapp. Försök att köra en skrivning till en befintlig mapp resulterar i ett fel. Kontrollera att målmappen är tom eller Använd en annan målplats för varje körning eller Skrivåtgärden misslyckas.

## <a name="monitoring-write-operations"></a>Övervaka skrivåtgärder
För din bekvämlighet genereras en sentinel-fil med namnet lyckades när en skrivning har slutförts. Sin närvaro hjälper dig att identifiera när en mellanliggande skrivning har slutförts utan att behöva vänta på att slutföra hela pipelinen.

## <a name="example-write-code"></a>Exempelkod för skrivning

Börja med att läsa in data i ett dataflöde i det här exemplet. Vi ska återanvända dessa data med olika format.

```python
import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

Exempel på utdata:
|   |  Kolumn1 |    Kolumn2 | Kolumn3 | Kolumn4  |Column5   | Kolumn6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   Ingen|       NEJ|     NEJ  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   Ingen|       NEJ|     NEJ  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    Ingen|   NEJ| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    Ingen|   NEJ| NEJ| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    Ingen|   NEJ| NEJ| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    Ingen|   NEJ| NEJ| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   Ingen|   NEJ| NEJ|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    Ingen|   NEJ| NEJ| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    Ingen|   NEJ| SA|     |80050.0|   16250.0|    80,0|
|9| 10030.0|    99999.0|    Ingen|   NEJ| SA|     |77000.0|   15500.0|    120.0|

### <a name="delimited-file-example"></a>Avgränsad fil-exempel

I det här avsnittet kan du se ett exempel som använder den `write_to_csv` används för att skriva med en avgränsad fil.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Exempel på utdata:
|   |  Kolumn1 |    Kolumn2 | Kolumn3 | Kolumn4  |Column5   | Kolumn6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   FEL |       NEJ|     NEJ  |   ENRS    |FEL    |   FEL |   FEL|    
|   1|      10003.0 |   99999.0 |   FEL |       NEJ|     NEJ  |   ENSO|       FEL|        FEL |FEL|   
|   2|  10010.0|    99999.0|    FEL |   NEJ| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    FEL |   NEJ| NEJ| |   FEL|    FEL|    FEL|
|4| 10014.0|    99999.0|    FEL |   NEJ| NEJ| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    FEL |   NEJ| NEJ| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   FEL |   NEJ| NEJ|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    FEL |   NEJ| NEJ| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    FEL |   NEJ| SA|     |80050.0|   16250.0|    80,0|
|9| 10030.0|    99999.0|    FEL |   NEJ| SA|     |77000.0|   15500.0|    120.0|

Du kan se i de föregående utdata som flera fel visas i de numeriska kolumnerna på grund av tal som inte tolkades rätt. När skrivs till CSV, ersätts dessa null-värden med strängen ”ERROR” som standard. 

Du kan lägga till parametrar som en del av dina skrivåtgärder anropa och ange en sträng som ska använda för att representera null-värden. Exempel:

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Föregående kod ger dessa utdata:
|   |  Kolumn1 |    Kolumn2 | Kolumn3 | Kolumn4  |Column5   | Kolumn6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BadData |       NEJ|     NEJ  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       NEJ|     NEJ  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   NEJ| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   NEJ| NEJ| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   NEJ| NEJ| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   NEJ| NEJ| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   NEJ| NEJ|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   NEJ| NEJ| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   NEJ| SA|     |80050.0|   16250.0|    80,0|
|9| 10030.0|    99999.0|    BadData |   NEJ| SA|     |77000.0|   15500.0|    120.0|


### <a name="parquet-file-example"></a>Exempel på en parquet

Liknar `write_to_csv`, `write_to_parquet` funktionen returnerar ett nytt dataflöde med en skrivning Parquet steg som utförs när data flödeskörningar.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Du kan sedan köra dataflödet för att starta Skrivåtgärden.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

Föregående kod ger dessa utdata:
|   |  Kolumn1 |    Kolumn2 | Kolumn3 | Kolumn4  |Column5   | Kolumn6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       NEJ|     NEJ  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       NEJ|     NEJ  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   NEJ| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   NEJ| NEJ| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   NEJ| NEJ| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   NEJ| NEJ| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   NEJ| NEJ|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   NEJ| NEJ| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   NEJ| SA|     |80050.0|   16250.0|    80,0|
|9| 10030.0|    99999.0|    MiscreantData |   NEJ| SA|     |77000.0|   15500.0|    120.0|
