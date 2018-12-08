---
title: Skriv och Förbered data med Data Prep Python SDK
titleSuffix: Azure Machine Learning service
description: Lär dig mer om hur du skriver data med Azure Machine Learning Data Prep SDK. Du kan skriva ut data när som helst i ett dataflöde och till filer i någon av våra platser som stöds (lokalt filsystem, Azure Blob Storage och Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 1278d0c448b041849f477b55d8311f516c3bccf3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104449"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Skriva data med hjälp av Azure Machine Learning Data Prep SDK

I den här artikeln får du lära dig olika metoder för att skriva data med hjälp av Azure Machine Learning Data Prep SDK. Utdata kan skrivas när som helst i ett dataflöde och skrivningar läggs till som steg för att det resulterande dataflödet och körs varje gång dataflödet finns. Data skrivs till partitionsfiler med flera Tillåt parallella skrivningar.

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

Börja med att läsa in data i ett dataflöde i det här exemplet. Du kan återanvända dessa data med olika format.

```python
import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```

Exempel på utdata:
|   |  Kolumn1 |    Kolumn2 | Kolumn3 | Kolumn4  |Column5   | Kolumn6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   Ingen|       NO|     NO  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   Ingen|       NO|     NO  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    Ingen|   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    Ingen|   NO| NO| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    Ingen|   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    Ingen|   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   Ingen|   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    Ingen|   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    Ingen|   NO| SV|     |80050.0|   16250.0|    80,0|
|9| 10030.0|    99999.0|    Ingen|   NO| SV|     |77000.0|   15500.0|    120.0|

### <a name="delimited-file-example"></a>Avgränsad fil-exempel

I följande kod används den `write_to_csv` används för att skriva data till en avgränsad fil.

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
| 0 |   10000.0 |   99999.0 |   FEL |       NO|     NO  |   ENRS    |FEL    |   FEL |   FEL|    
|   1|      10003.0 |   99999.0 |   FEL |       NO|     NO  |   ENSO|       FEL|        FEL |FEL|   
|   2|  10010.0|    99999.0|    FEL |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    FEL |   NO| NO| |   FEL|    FEL|    FEL|
|4| 10014.0|    99999.0|    FEL |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    FEL |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   FEL |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    FEL |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    FEL |   NO| SV|     |80050.0|   16250.0|    80,0|
|9| 10030.0|    99999.0|    FEL |   NO| SV|     |77000.0|   15500.0|    120.0|

I den föregående utdatan visas flera fel i de numeriska kolumnerna på grund av tal som inte tolkades rätt. När skrivs till CSV, har null-värden ersatts med strängen ”ERROR” som standard.

Lägg till parametrar som en del av dina skrivåtgärder anropa och ange en sträng som ska använda för att representera null-värden.

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
| 0 |   10000.0 |   99999.0 |   BadData |       NO|     NO  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       NO|     NO  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   NO| NO| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   NO| SV|     |80050.0|   16250.0|    80,0|
|9| 10030.0|    99999.0|    BadData |   NO| SV|     |77000.0|   15500.0|    120.0|

### <a name="parquet-file-example"></a>Exempel på en parquet

Liknar `write_to_csv`, `write_to_parquet` funktionen returnerar ett nytt dataflöde med en skrivning Parquet steg som utförs när data flödeskörningar.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Kör dataflödet för att starta Skrivåtgärden.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

Föregående kod ger dessa utdata:
|   |  Kolumn1 |    Kolumn2 | Kolumn3 | Kolumn4  |Column5   | Kolumn6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       NO|     NO  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       NO|     NO  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   NO| SV|     |80050.0|   16250.0|    80,0|
|9| 10030.0|    99999.0|    MiscreantData |   NO| SV|     |77000.0|   15500.0|    120.0|
