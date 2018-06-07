---
title: 'Data Transformation: Processen & Transformera data | Microsoft Docs'
description: Lär dig mer om att omvandla data eller bearbeta data i Azure Data Factory med Hadoop, Machine Learning eller Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: aa51073395a1049cd78f13ba185119707a787b29
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622303"
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
> * [Anpassade .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Översikt
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [data transformation aktiviteter i Data Factory version 2](../transform-data.md).

Den här artikeln förklarar data transformation aktiviteter i Azure Data Factory att du kan använda för att omvandla och bearbetar dina rådata i förutsägelser och insikter. En omvandling aktivitet körs i en datormiljö, till exempel Azure HDInsight-kluster eller ett Azure Batch. Det innehåller länkar till artiklar med detaljerad information om varje aktivitet för omvandling.

Data Factory stöder följande data transformation-aktiviteter som kan läggas till [pipelines](data-factory-create-pipelines.md) antingen individuellt eller härledda med en annan aktivitet.

> [!NOTE]
> En genomgång med stegvisa instruktioner finns [skapar en pipeline med Hive omvandling](data-factory-build-your-first-pipeline.md) artikel.  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
HDInsight Hive-aktivitet i en Data Factory-pipelinen kör Hive-frågor på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. Se [Hive aktiviteten](data-factory-hive-activity.md) artikeln för information om den här aktiviteten. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-aktivitet
HDInsight Pig-aktiviteten i en Data Factory-pipelinen kör Pig frågor på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. Se [Pig aktiviteten](data-factory-pig-activity.md) artikeln för information om den här aktiviteten. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce activity
HDInsight MapReduce-aktiviteten i en Data Factory-pipelinen kör MapReduce program på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. Se [MapReduce Activity](data-factory-map-reduce.md) artikeln för information om den här aktiviteten.

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming activity
HDInsight Streaming Activity i Data Factory-pipelinen kör Hadoop Streaming program på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. Se [HDInsight Streaming activity](data-factory-hadoop-streaming-activity.md) för ytterligare information om den här aktiviteten.

## <a name="hdinsight-spark-activity"></a>HDInsight Apache Spark-aktivitet
HDInsight Spark-aktivitet i en Data Factory-pipelinen körs Spark-program på din egen HDInsight-kluster. Mer information finns i [anropa Spark-program från Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning-aktiviteter
Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad Azure Machine Learning-webbtjänst för förutsägelseanalys. Med hjälp av den [Batchkörningsaktivitet](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) i Azure Data Factory-pipelinen, du kan anropa en Machine Learning-webbtjänst för att göra förutsägelser på data i batch.

Över tiden måste förutsägelsemodeller i Maskininlärning bedömningen experiment vara retrained med nya indatauppsättningar. När du är klar med omtränings som du vill uppdatera bedömningsprofil webbtjänsten med retrained Machine Learning-modellen. Du kan använda den [uppdatera resurs aktiviteten](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) uppdatera webbtjänsten med den nyligen tränade modellen.  

Se [används Machine Learning aktiviteter](data-factory-azure-ml-batch-execution-activity.md) mer information om dessa Machine Learning-aktiviteter. 

## <a name="stored-procedure-activity"></a>Lagrad proceduraktivitet
Du kan använda aktiviteten lagrad procedur för SQL Server i en Data Factory-pipelinen för att anropa en lagrad procedur i någon av följande datalager: Azure SQL Database, Azure SQL Data Warehouse, SQL Server-databas i ditt företag eller en Azure VM. Se [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) artikeln för information.  

## <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-aktivitet för Data Lake Analytics
Data Lake Analytics U-SQL-aktivitet körs ett U-SQL-skript i ett Azure Data Lake Analytics-kluster. Se [Data Analytics U-SQL-aktivitet](data-factory-usql-activity.md) artikeln för information. 

## <a name="net-custom-activity"></a>.NET-anpassad aktivitet
Om du behöver transformera data på ett sätt som inte stöds av Data Factory kan du skapa en anpassad aktivitet med din egen databearbetning logik och använder aktiviteten i pipelinen. Du kan konfigurera den anpassade .NET-aktiviteten ska köras med Azure Batch-tjänsten eller ett Azure HDInsight-kluster. Se [använda anpassade aktiviteter](data-factory-use-custom-activities.md) artikeln för information. 

Du kan skapa en anpassad aktivitet som kör R-skript i ditt HDInsight-kluster med R installerat. Se [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Köra R-skript med Azure Data Factory). 

## <a name="compute-environments"></a>Compute-miljöer
Du skapar en länkad tjänst för beräkning miljö och sedan använda den länkade tjänsten när du definierar en transformation-aktivitet. Det finns två typer av beräknings-miljöer som stöds av Data Factory. 

1. **På begäran**: I det här fallet datormiljön hanteras helt av Data Factory. Den skapas automatiskt av tjänsten Data Factory innan ett jobb har skickats för bearbetning av data och tas bort när jobbet har slutförts. Du kan konfigurera och styra detaljerade inställningar på begäran beräknings-miljön för jobbkörningen klusterhantering och startprogram åtgärder. 
2. **Ta med din egen**: I det här fallet kan du registrera din egen datormiljö (till exempel HDInsight-kluster) som en länkad tjänst i Data Factory. Datormiljön hanteras av dig och Data Factory-tjänsten används för att utföra aktiviteter. 

Se [Compute länkade tjänster](data-factory-compute-linked-services.md) artikeln innehåller information om beräknings-tjänster som stöds av Data Factory. 

## <a name="summary"></a>Sammanfattning
Azure Data Factory stöder följande data transformation aktiviteter och beräknings-miljöer för aktiviteter. Omvandling av aktiviteterna kan läggas till pipelines antingen individuellt eller härledda med en annan aktivitet.

| Datatransformeringsaktivitet | Compute-miljö |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Machine Learning-aktiviteter: batchkörning och resursuppdatering](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Lagrad procedur](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse eller SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] eller Azure Batch |

