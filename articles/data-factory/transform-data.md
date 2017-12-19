---
title: "Transformera data med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig mer om att omvandla data eller bearbeta data i Azure Data Factory med Hadoop, Machine Learning eller Azure Data Lake Analytics."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: shengc
ms.openlocfilehash: 88ff71956c82d79fb244f1a687debeb726291ae8
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="transform-data-in-azure-data-factory"></a>Transformera data i Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Hadoop Streaming](transform-data-using-hadoop-streaming.md)
> * [Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Lagrad procedur](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Anpassade .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Översikt
Den här artikeln förklarar data transformation aktiviteter i Azure Data Factory att du kan använda för att omvandla och bearbetar dina rådata i förutsägelser och insikter. En omvandling aktivitet körs i en datormiljö, till exempel Azure HDInsight-kluster eller ett Azure Batch. Det innehåller länkar till artiklar med detaljerad information om varje aktivitet för omvandling.

Data Factory stöder följande data transformation-aktiviteter som kan läggas till [pipelines](concepts-pipelines-activities.md) antingen individuellt eller härledda med en annan aktivitet.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [omvandling aktiviteter i Data Factory version 1](v1/data-factory-data-transformation-activities.md).
 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
HDInsight Hive-aktivitet i en Data Factory-pipelinen kör Hive-frågor på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. Se [Hive aktiviteten](transform-data-using-hadoop-hive.md) artikeln för information om den här aktiviteten. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-aktivitet
HDInsight Pig-aktiviteten i en Data Factory-pipelinen kör Pig frågor på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. Se [svin aktiviteten](transform-data-using-hadoop-pig.md) artikeln för information om den här aktiviteten. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce activity
HDInsight MapReduce-aktiviteten i en Data Factory-pipelinen kör MapReduce program på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. Se [MapReduce activity](transform-data-using-hadoop-map-reduce.md) artikeln för information om den här aktiviteten.

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming activity
Aktiviteten HDInsight strömning i en Data Factory-pipelinen kör Hadoop Streaming program på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. Se [HDInsight Streaming activity](transform-data-using-hadoop-streaming.md) för ytterligare information om den här aktiviteten.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-aktivitet
HDInsight Spark-aktivitet i en Data Factory-pipelinen körs Spark-program på din egen HDInsight-kluster. Mer information finns i [anropa Spark-program från Azure Data Factory](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning-aktiviteter
Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad Azure Machine Learning-webbtjänst för förutsägelseanalys. Med hjälp av den [Batch Execution aktiviteten](transform-data-using-machine-learning.md) i Azure Data Factory-pipelinen, du kan anropa en Machine Learning-webbtjänst för att göra förutsägelser på data i batch.

Över tiden måste förutsägelsemodeller i Maskininlärning bedömningen experiment vara retrained med nya indatauppsättningar. När du är klar med omtränings som du vill uppdatera bedömningsprofil webbtjänsten med retrained Machine Learning-modellen. Du kan använda den [uppdatera resurs aktiviteten](update-machine-learning-models.md) uppdatera webbtjänsten med den nyligen tränade modellen.  

Se [används Machine Learning aktiviteter](transform-data-using-machine-learning.md) mer information om dessa Machine Learning-aktiviteter. 

## <a name="stored-procedure-activity"></a>Lagrad proceduraktivitet
Du kan använda aktiviteten lagrad procedur för SQL Server i en Data Factory-pipelinen för att anropa en lagrad procedur i någon av följande datalager: Azure SQL Database, Azure SQL Data Warehouse, SQL Server-databas i ditt företag eller en Azure VM. Se [lagrade proceduraktiviteten](transform-data-using-stored-procedure.md) artikeln för information.  

## <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-aktivitet för Data Lake Analytics
Data Lake Analytics U-SQL-aktivitet körs ett U-SQL-skript i ett Azure Data Lake Analytics-kluster. Se [Data Analytics U-SQL-aktivitet](transform-data-using-data-lake-analytics.md) artikeln för information. 

## <a name="net-custom-activity"></a>.NET-anpassad aktivitet
Om du behöver transformera data på ett sätt som inte stöds av Data Factory kan du skapa en anpassad aktivitet med din egen databearbetning logik och använder aktiviteten i pipelinen. Du kan konfigurera den anpassade .NET-aktiviteten ska köras med Azure Batch-tjänsten eller ett Azure HDInsight-kluster. Se [använda anpassade aktiviteter](transform-data-using-dotnet-custom-activity.md) artikeln för information. 

Du kan skapa en anpassad aktivitet som kör R-skript i ditt HDInsight-kluster med R installerat. Se [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Köra R-skript med Azure Data Factory). 

## <a name="compute-environments"></a>Compute-miljöer
Du skapar en länkad tjänst för beräkning miljö och sedan använda den länkade tjänsten när du definierar en transformation-aktivitet. Det finns två typer av beräknings-miljöer som stöds av Data Factory. 

- **På begäran**: I det här fallet datormiljön hanteras helt av Data Factory. Den skapas automatiskt av tjänsten Data Factory innan ett jobb har skickats för bearbetning av data och tas bort när jobbet har slutförts. Du kan konfigurera och styra detaljerade inställningar på begäran beräknings-miljön för jobbkörningen klusterhantering och startprogram åtgärder. 
- **Ta med din egen**: I det här fallet kan du registrera din egen datormiljö (till exempel HDInsight-kluster) som en länkad tjänst i Data Factory. Datormiljön hanteras av dig och Data Factory-tjänsten används för att utföra aktiviteter. 

Se [Compute länkade tjänster](compute-linked-services.md) artikeln innehåller information om beräknings-tjänster som stöds av Data Factory. 

## <a name="next-steps"></a>Nästa steg
Se följande självstudierna för ett exempel på hur en omvandling av aktivitet: [Självstudier: Transformera data med hjälp av Spark](tutorial-transform-data-spark-powershell.md)