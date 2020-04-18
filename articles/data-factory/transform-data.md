---
title: Omvandla data
description: Lär dig hur du omvandlar data eller bearbetar data i Azure Data Factory med Hadoop, Machine Learning eller Azure Data Lake Analytics.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: fdb10894ab9d1c6b805b8b43c90e54126d67b8dd
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606619"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformera data i Azure Data Factory

> [!div class="op_single_selector"]
> * [Mappa dataflöde](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce (Kartreeduce)](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Maskininlärning](transform-data-using-machine-learning.md) 
> * [Lagrad procedur](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks anteckningsbok](transform-data-databricks-notebook.md)
> * [Databricks Burk](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET anpassad](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Översikt
I den här artikeln beskrivs dataomvandlingsaktiviteter i Azure Data Factory som du kan använda för att omvandla och bearbeta dina rådata till förutsägelser och insikter i stor skala. En omvandlingsaktivitet körs i en datormiljö som Azure Databricks eller Azure HDInsight. Den innehåller länkar till artiklar med detaljerad information om varje omvandlingsaktivitet.

Data Factory stöder följande dataomvandlingsaktiviteter som kan läggas till [i pipelines](concepts-pipelines-activities.md) antingen individuellt eller kedjat med en annan aktivitet.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Omvandla inbyggt i Azure Data Factory med dataflöden

### <a name="mapping-data-flows"></a>Mappa dataflöden

Mappning av dataflöden är visuellt utformade dataomvandlingar i Azure Data Factory. Dataflöden gör det möjligt för datatekniker att utveckla grafisk dataomvandlingslogik utan att skriva kod. De resulterande dataflödena körs som aktiviteter i Azure Data Factory-pipelines som använder utskalade Spark-kluster. Dataflödesaktiviteter kan användas via befintliga funktioner för schemaläggning, kontroll, flöde och övervakning av datafabriken. Mer information finns [i mappning av dataflöden](concepts-data-flow-overview.md).

### <a name="wrangling-data-flows"></a>Käbbel dataflöden

Med käbbeldataflöden i Azure Data Factory kan du göra kodfria dataförberedelser i molnskala iterativt. Käbbel dataflöden integreras med [Power Query Online](https://docs.microsoft.com/power-query/) och gör Power Query M-funktioner tillgängliga för data käbbel i molnskala via sparkkörning. Mer information finns i [käbbel dataflöden](wrangling-data-flow-overview.md).

## <a name="external-transformations"></a>Externa omvandlingar

Du kan också handkodsomformningar och hantera den externa beräkningsmiljön själv.

### <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
HDInsight Hive-aktiviteten i en Data Factory-pipeline kör Hive-frågor på ditt eget eller on-demand Windows/Linux-baserade HDInsight-kluster. Mer information om den här aktiviteten finns [i hive-aktivitetsartikeln.](transform-data-using-hadoop-hive.md) 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig aktivitet
HDInsight Pig-aktiviteten i en Pipeline för datafabrik kör Grisfrågor på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. Se [Grisaktivitetsartikel](transform-data-using-hadoop-pig.md) för mer information om den här aktiviteten. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce aktivitet
HDInsight MapReduce-aktiviteten i en Pipeline för Data Factory kör MapReduce-program på ditt eget eller on-demand Windows/Linux-baserade HDInsight-kluster. Mer information om den här aktiviteten finns i [den](transform-data-using-hadoop-map-reduce.md) här aktiviteten.

### <a name="hdinsight-streaming-activity"></a>HDInsight Streaming-aktivitet
HDInsight Streaming-aktiviteten i en Pipeline för Data Factory kör Hadoop Streaming-program på ditt eget eller on-demand Windows/Linux-baserade HDInsight-kluster. Se [HDInsight Streaming-aktivitet](transform-data-using-hadoop-streaming.md) för mer information om den här aktiviteten.

### <a name="hdinsight-spark-activity"></a>HDInsight Spark-aktivitet
HDInsight Spark-aktiviteten i en Pipeline för Data Factory kör Spark-program på ditt eget HDInsight-kluster. Mer information finns i [Anropa Spark-program från Azure Data Factory](transform-data-using-spark.md). 

### <a name="machine-learning-activities"></a>Maskininlärningsaktiviteter
Med Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad Azure Machine Learning-webbtjänst för prediktiv analys. Med hjälp av [batchkörningsaktiviteten](transform-data-using-machine-learning.md) i en Azure Data Factory-pipeline kan du anropa en Machine Learning-webbtjänst för att göra förutsägelser om data i batch.

Med tiden måste de prediktiva modellerna i machine learning-bedömningsexperimenten omskolas med hjälp av nya indatauppsättningar. När du är klar med omskolningen vill du uppdatera bedömningswebbtjänsten med den omskolade Machine Learning-modellen. Du kan använda [aktiviteten Uppdatera resurs](update-machine-learning-models.md) för att uppdatera webbtjänsten med den nyligen utbildade modellen.  

Se [Använda maskininlärningsaktiviteter](transform-data-using-machine-learning.md) för mer information om dessa maskininlärningsaktiviteter. 

### <a name="stored-procedure-activity"></a>Lagrad proceduraktivitet
Du kan använda aktiviteten SQL Server Stored Procedure i en Data Factory-pipeline för att anropa en lagrad procedur i något av följande datalager: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database i företaget eller en Azure VM. Mer information finns i artikeln [Lagrad proceduraktivitet.](transform-data-using-stored-procedure.md)  

### <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-aktivitet för DataSjöanalys
U-SQL-aktivitet för DataSjö Analytics kör ett U-SQL-skript i ett Azure Data Lake Analytics-kluster. Mer information finns i [dataanalys-U-SQL-aktivitetsartikeln.](transform-data-using-data-lake-analytics.md) 

### <a name="databricks-notebook-activity"></a>Databricks Bärbar dator aktivitet

Azure Databricks Notebook Activity i en Data Factory-pipeline kör en Databricks-anteckningsbok på din Azure Databricks-arbetsyta. Azure Databricks är en hanterad plattform för att köra Apache Spark. Se [Omvandla data genom att köra en Databricks-anteckningsbok](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Databricks Jar aktivitet

Azure Databricks Jar-aktivitet i en Data Factory-pipeline kör en Spark Jar i ditt Azure Databricks-kluster. Azure Databricks är en hanterad plattform för att köra Apache Spark. Se [Omvandla data genom att köra en Jar-aktivitet i Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Databricks Python-aktivitet

Azure Databricks Python-aktivitet i en Data Factory-pipeline kör en Python-fil i ditt Azure Databricks-kluster. Azure Databricks är en hanterad plattform för att köra Apache Spark. Se [Omvandla data genom att köra en Python-aktivitet i Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Anpassad aktivitet
Om du behöver omvandla data på ett sätt som inte stöds av Data Factory kan du skapa en anpassad aktivitet med din egen databehandlingslogik och använda aktiviteten i pipelinen. Du kan konfigurera den anpassade .NET-aktiviteten så att den körs med antingen en Azure Batch-tjänst eller ett Azure HDInsight-kluster. Mer information finns i artikeln Använda [anpassade aktiviteter.](transform-data-using-dotnet-custom-activity.md) 

Du kan skapa en anpassad aktivitet som kör R-skript i ditt HDInsight-kluster med R installerat. Se [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (Köra R-skript med Azure Data Factory). 

### <a name="compute-environments"></a>Beräkningsmiljöer
Du skapar en länkad tjänst för beräkningsmiljön och använder sedan den länkade tjänsten när du definierar en omvandlingsaktivitet. Det finns två typer av beräkningsmiljöer som stöds av Data Factory. 

- **On-Demand**: I det här fallet hanteras datormiljön helt av Data Factory. Den skapas automatiskt av datafabrikstjänsten innan ett jobb skickas för att bearbeta data och tas bort när jobbet är slutfört. Du kan konfigurera och styra detaljerade inställningar för beräkningsmiljön på begäran för jobbkörning, klusterhantering och bootstrapping-åtgärder. 
- **Ta med egna:** I det här fallet kan du registrera din egen datormiljö (till exempel HDInsight-kluster) som en länkad tjänst i Data Factory. Datormiljön hanteras av dig och Data Factory-tjänsten använder den för att utföra aktiviteterna. 

Se artikel [om Compute Linked Services](compute-linked-services.md) om du vill veta mer om beräkningstjänster som stöds av Data Factory. 

## <a name="next-steps"></a>Nästa steg
Se följande självstudiekurs för ett exempel på hur du använder en omvandlingsaktivitet: [Självstudiekurs: omvandla data med Spark](tutorial-transform-data-spark-powershell.md)
