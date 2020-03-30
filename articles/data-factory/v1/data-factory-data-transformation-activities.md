---
title: 'Dataomvandling: Bearbeta & omvandla data '
description: Lär dig hur du omvandlar data eller bearbetar data i Azure Data Factory med Hadoop, Machine Learning eller Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5b3e2db9b9769dee7599a2446b272e04cc0bedf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703394"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformera data i Azure Data Factory
> [!div class="op_single_selector"]
> * [Registreringsdatafilen](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce (Kartreeduce)](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Maskininlärning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Lagrad procedur](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET anpassad](data-factory-use-custom-activities.md)

## <a name="overview"></a>Översikt
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [dataomvandlingsaktiviteter i Data Factory](../transform-data.md).

I den här artikeln beskrivs dataomvandlingsaktiviteter i Azure Data Factory som du kan använda för att omvandla och bearbeta dina rådata till förutsägelser och insikter. En omvandlingsaktivitet körs i en datormiljö som Azure HDInsight-kluster eller en Azure Batch. Den innehåller länkar till artiklar med detaljerad information om varje omvandlingsaktivitet.

Data Factory stöder följande dataomvandlingsaktiviteter som kan läggas till [i pipelines](data-factory-create-pipelines.md) antingen individuellt eller kedjat med en annan aktivitet.

> [!NOTE]
> En genomgång med steg-för-steg-instruktioner finns i [Skapa en pipeline med Hive-omvandlingsartikel.](data-factory-build-your-first-pipeline.md)  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
HDInsight Hive-aktiviteten i en Data Factory-pipeline kör Hive-frågor på ditt eget eller on-demand Windows/Linux-baserade HDInsight-kluster. Mer information om den här aktiviteten finns i artikeln [Hive-aktivitet.](data-factory-hive-activity.md) 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig aktivitet
HDInsight Pig-aktiviteten i en Pipeline för datafabrik kör Grisfrågor på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. Se artikeln [Grisaktivitet](data-factory-pig-activity.md) för mer information om den här aktiviteten. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce aktivitet
HDInsight MapReduce-aktiviteten i en Pipeline för Data Factory kör MapReduce-program på ditt eget eller on-demand Windows/Linux-baserade HDInsight-kluster. Mer information om den här aktiviteten finns i artikel [MapReduce Activity.](data-factory-map-reduce.md)

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming-aktivitet
HDInsight Streaming Activity i en Pipeline för datafabrik kör Hadoop Streaming-program på ditt eget eller on-demand Windows/Linux-baserade HDInsight-kluster. Se [HDInsight Streaming-aktivitet](data-factory-hadoop-streaming-activity.md) för mer information om den här aktiviteten.

## <a name="hdinsight-spark-activity"></a>HDInsight Apache Spark-aktivitet
HDInsight Spark-aktiviteten i en Pipeline för Data Factory kör Spark-program på ditt eget HDInsight-kluster. Mer information finns i [Anropa Spark-program från Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Maskininlärningsaktiviteter
Med Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad Azure Machine Learning-webbtjänst för prediktiv analys. Med hjälp av [batchkörningsaktiviteten](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) i en Azure Data Factory-pipeline kan du anropa en Machine Learning-webbtjänst för att göra förutsägelser om data i batch.

Med tiden måste de prediktiva modellerna i machine learning-bedömningsexperimenten omskolas med hjälp av nya indatauppsättningar. När du är klar med omskolningen vill du uppdatera bedömningswebbtjänsten med den omskolade Machine Learning-modellen. Du kan använda [uppdatera resursaktivitet](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) för att uppdatera webbtjänsten med den nyligen utbildade modellen.  

Se [Använda maskininlärningsaktiviteter](data-factory-azure-ml-batch-execution-activity.md) för mer information om dessa maskininlärningsaktiviteter. 

## <a name="stored-procedure-activity"></a>Lagrad proceduraktivitet
Du kan använda aktiviteten SQL Server Stored Procedure i en Data Factory-pipeline för att anropa en lagrad procedur i något av följande datalager: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database i företaget eller en Azure VM. Mer information finns i artikeln [Lagrad proceduraktivitet.](data-factory-stored-proc-activity.md)  

## <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-aktivitet för DataSjöanalys
U-SQL-aktivitet för DataSjöanalys kör ett U-SQL-skript i ett Azure Data Lake Analytics-kluster. Mer information finns i artikeln [U-SQL-aktivitet](data-factory-usql-activity.md) för Data Analytics. 

## <a name="net-custom-activity"></a>.NET-anpassad aktivitet
Om du behöver omvandla data på ett sätt som inte stöds av Data Factory kan du skapa en anpassad aktivitet med din egen databehandlingslogik och använda aktiviteten i pipelinen. Du kan konfigurera den anpassade .NET-aktiviteten så att den körs med antingen en Azure Batch-tjänst eller ett Azure HDInsight-kluster. Mer information finns i artikeln Använda [anpassade aktiviteter.](data-factory-use-custom-activities.md) 

Du kan skapa en anpassad aktivitet som kör R-skript i ditt HDInsight-kluster med R installerat. Se [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (Köra R-skript med Azure Data Factory). 

## <a name="compute-environments"></a>Beräkningsmiljöer
Du skapar en länkad tjänst för beräkningsmiljön och använder sedan den länkade tjänsten när du definierar en omvandlingsaktivitet. Det finns två typer av beräkningsmiljöer som stöds av Data Factory. 

1. **On-Demand**: I det här fallet hanteras datormiljön helt av Data Factory. Den skapas automatiskt av datafabrikstjänsten innan ett jobb skickas för att bearbeta data och tas bort när jobbet är slutfört. Du kan konfigurera och styra detaljerade inställningar för beräkningsmiljön på begäran för jobbkörning, klusterhantering och bootstrapping-åtgärder. 
2. **Ta med egna:** I det här fallet kan du registrera din egen datormiljö (till exempel HDInsight-kluster) som en länkad tjänst i Data Factory. Datormiljön hanteras av dig och Data Factory-tjänsten använder den för att utföra aktiviteterna. 

Se artikel [om Compute Linked Services](data-factory-compute-linked-services.md) om du vill veta mer om beräkningstjänster som stöds av Data Factory. 

## <a name="summary"></a>Sammanfattning
Azure Data Factory stöder följande dataomvandlingsaktiviteter och beräkningsmiljöer för aktiviteterna. Omvandlingsaktiviteterna kan läggas till i pipelines antingen individuellt eller kedjas med en annan aktivitet.

| Datatransformeringsaktivitet | Compute-miljö |
|:--- |:--- |
| [Registreringsdatafilen](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce (Kartreeduce)](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Machine Learning-aktiviteter: batchkörning och resursuppdatering](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Lagrad procedur](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse eller SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] eller Azure Batch |

