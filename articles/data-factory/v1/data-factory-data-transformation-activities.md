---
title: 'Transformering av data: Bearbeta & Transformera data | Microsoft Docs'
description: Lär dig hur du omvandlar data eller bearbeta data i Azure Data Factory med hjälp av Hadoop-, Machine Learning- eller Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b1a99f2872a69e01232c69a73f36319552429ca0
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022452"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformera data i Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Lagrad procedur](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Anpassad .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Översikt
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [datatransformeringsaktiviteter i Data Factory](../transform-data.md).

Den här artikeln förklarar datatransformeringsaktiviteter i Azure Data Factory att du kan använda för att transformera och bearbetar dina rådata till förutsägelser och insikter. En transformeringsaktivitet körs i en miljö, till exempel Azure HDInsight-kluster eller ett Azure Batch. Det innehåller länkar till artiklar med detaljerad information om varje transformeringsaktivitet.

Följande datatransformeringsaktiviteter som kan läggas till i data Factory [pipelines](data-factory-create-pipelines.md) antingen individuellt eller härledda med en annan aktivitet.

> [!NOTE]
> En genomgång med stegvisa instruktioner finns i [skapa en pipeline med Hive-transformeringen](data-factory-build-your-first-pipeline.md) artikeln.  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
HDInsight Hive-aktiviteten i Data Factory-pipeline kör Hive-frågor på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran. Se [Hive-aktivitet](data-factory-hive-activity.md) nedan för information om den här aktiviteten. 

## <a name="hdinsight-pig-activity"></a>HDInsight-piggningsåtgärd
HDInsight-piggningsåtgärd i Data Factory-pipeline utför Pig frågor på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran. Se [Piggningsåtgärd](data-factory-pig-activity.md) nedan för information om den här aktiviteten. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-aktivitet
HDInsight MapReduce-aktivitet i en Data Factory-pipeline kör MapReduce-program på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran. Se [MapReduce-aktivitet](data-factory-map-reduce.md) nedan för information om den här aktiviteten.

## <a name="hdinsight-streaming-activity"></a>HDInsight-strömningsaktivitet
HDInsight-Strömningsaktivitet i Data Factory-pipeline utför Hadoop Streaming program på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran. Se [HDInsight-strömningsaktivitet](data-factory-hadoop-streaming-activity.md) mer information om den här aktiviteten.

## <a name="hdinsight-spark-activity"></a>HDInsight Apache Spark-aktivitet
HDInsight Spark-aktivitet i en Data Factory-pipeline kör Spark-program på ett eget HDInsight-kluster. Mer information finns i [anropa Spark-program från Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning-aktiviteter
Azure Data Factory kan du enkelt kan skapa pipelines som använder en publicerade Azure Machine Learning-webbtjänst för förutsägande analys. Med hjälp av den [Batchkörningsaktivitet](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) i en Azure Data Factory-pipeline kan du anropa en Machine Learning-webbtjänst för att göra förutsägelser på data i batch.

Framöver kommer måste förutsägande modeller i Maskininlärning bedömning experiment vara modellkomponenten med hjälp av nya indatauppsättningar. När du är klar med att träna, som du vill uppdatera bedömning av webbtjänsten med retrained Machine Learning-modellen. Du kan använda den [aktivitet uppdatera resurs](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) att uppdatera webbtjänsten med den nyligen tränade modellen.  

Se [Använd Machine Learning-aktiviteter](data-factory-azure-ml-batch-execution-activity.md) mer information om dessa Machine Learning-aktiviteter. 

## <a name="stored-procedure-activity"></a>Lagrad proceduraktivitet
Du kan använda SQL Server-lagrad procedur i en Data Factory-pipeline anropa en lagrad procedur i någon av följande datalager: Azure SQL Database, Azure SQL Data Warehouse, SQL Server-databas i ditt företag eller en Azure-dator. Se [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) nedan för information.  

## <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-aktivitet för Data Lake Analytics
Data Lake Analytics U-SQL-aktivitet kör en U-SQL-skript på ett Azure Data Lake Analytics-kluster. Se [Data Analytics U-SQL-aktivitet](data-factory-usql-activity.md) nedan för information. 

## <a name="net-custom-activity"></a>.NET-anpassad aktivitet
Om du vill omvandla data på ett sätt som inte stöds av Data Factory kan du skapa en anpassad aktivitet med egen logik för databearbetning och använda aktiviteten i pipelinen. Du kan konfigurera anpassad .NET-aktivitet ska köras med en Azure Batch-tjänst eller ett Azure HDInsight-kluster. Se [använda anpassade aktiviteter](data-factory-use-custom-activities.md) nedan för information. 

Du kan skapa en anpassad aktivitet som kör R-skript i ditt HDInsight-kluster med R installerat. Se [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Köra R-skript med Azure Data Factory). 

## <a name="compute-environments"></a>Compute-miljöer
Du skapar en länkad tjänst för compute-miljö och sedan använda den länkade tjänsten när du definierar en transformeringsaktivitet. Det finns två typer av beräkningsmiljöer som stöds av Data Factory. 

1. **På begäran**:  I det här fallet hanteras datormiljön fullständigt av Data Factory. Den skapas automatiskt av Data Factory-tjänsten innan ett jobb skickas för att bearbeta data och tas bort när jobbet har slutförts. Du kan konfigurera och kontrollera detaljerade inställningar av på begäran beräkningsmiljö för jobbkörning, klusterhantering och start av åtgärder. 
2. **Ta med din egen**: I det här fallet kan du registrera dina egna datormiljö (till exempel HDInsight-kluster) som en länkad tjänst i Datafabriken. Datormiljön hanteras av dig och Data Factory-tjänsten används för att köra aktiviteterna. 

Se [beräkna länkade tjänster](data-factory-compute-linked-services.md) artikeln om du vill veta mer om compute services som stöds av Data Factory. 

## <a name="summary"></a>Sammanfattning
Azure Data Factory stöder följande datatransformeringsaktiviteter och beräkningsmiljöer för aktiviteter. Transformeringsaktiviteter kan läggas till en pipeline antingen individuellt eller härledda med en annan aktivitet.

| Datatransformeringsaktivitet | Compute-miljö |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Machine Learning-aktiviteter: Batchkörning och resursuppdatering](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Lagrad procedur](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse eller SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] eller Azure Batch |

