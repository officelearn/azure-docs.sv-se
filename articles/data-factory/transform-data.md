---
title: Transformera data med Azure Data Factory | Microsoft Docs
description: Lär dig hur du omvandlar data eller bearbeta data i Azure Data Factory med hjälp av Hadoop-, Machine Learning- eller Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: afd1944006a08811075e8af8b1a641d00ee3c352
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112822"
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
> * [Anpassad .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Översikt
Den här artikeln förklarar datatransformeringsaktiviteter i Azure Data Factory att du kan använda för att transformera och bearbetar dina rådata till förutsägelser och insikter. En transformeringsaktivitet körs i en miljö, till exempel Azure HDInsight-kluster eller ett Azure Batch. Det innehåller länkar till artiklar med detaljerad information om varje transformeringsaktivitet.

Följande datatransformeringsaktiviteter som kan läggas till i data Factory [pipelines](concepts-pipelines-activities.md) antingen individuellt eller härledda med en annan aktivitet.

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
HDInsight Hive-aktiviteten i Data Factory-pipeline kör Hive-frågor på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran. Se [Hive-aktivitet](transform-data-using-hadoop-hive.md) nedan för information om den här aktiviteten. 

## <a name="hdinsight-pig-activity"></a>HDInsight-piggningsåtgärd
HDInsight-piggningsåtgärd i Data Factory-pipeline utför Pig frågor på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran. Se [Apache Pig-aktivitet](transform-data-using-hadoop-pig.md) nedan för information om den här aktiviteten. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-aktivitet
HDInsight MapReduce-aktivitet i en Data Factory-pipeline kör MapReduce-program på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran. Se [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md) nedan för information om den här aktiviteten.

## <a name="hdinsight-streaming-activity"></a>HDInsight-strömningsaktivitet
HDInsight Streaming-aktivitet i Data Factory-pipeline utför Hadoop Streaming program på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran. Se [HDInsight-strömningsaktivitet](transform-data-using-hadoop-streaming.md) mer information om den här aktiviteten.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-aktivitet
HDInsight Spark-aktivitet i en Data Factory-pipeline kör Spark-program på ett eget HDInsight-kluster. Mer information finns i [anropa Spark-program från Azure Data Factory](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning-aktiviteter
Azure Data Factory kan du enkelt kan skapa pipelines som använder en publicerade Azure Machine Learning-webbtjänst för förutsägande analys. Med hjälp av den [batchkörningsaktivitet](transform-data-using-machine-learning.md) i en Azure Data Factory-pipeline kan du anropa en Machine Learning-webbtjänst för att göra förutsägelser på data i batch.

Framöver kommer måste förutsägande modeller i Maskininlärning bedömning experiment vara modellkomponenten med hjälp av nya indatauppsättningar. När du är klar med att träna, som du vill uppdatera bedömning av webbtjänsten med retrained Machine Learning-modellen. Du kan använda den [aktivitet uppdatera resurs](update-machine-learning-models.md) att uppdatera webbtjänsten med den nyligen tränade modellen.  

Se [Använd Machine Learning-aktiviteter](transform-data-using-machine-learning.md) mer information om dessa Machine Learning-aktiviteter. 

## <a name="stored-procedure-activity"></a>Lagrad proceduraktivitet
Du kan använda SQL Server-lagrad procedur i Data Factory-pipeline anropa en lagrad procedur i någon av följande datalager: Azure SQL Database, Azure SQL Data Warehouse, SQL Server-databas i ditt företag eller en Azure-dator. Se [lagringsprocedur-aktivitet](transform-data-using-stored-procedure.md) nedan för information.  

## <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-aktivitet för Data Lake Analytics
Data Lake Analytics U-SQL-aktivitet kör en U-SQL-skript på ett Azure Data Lake Analytics-kluster. Se [Data U-SQL-aktiviteten](transform-data-using-data-lake-analytics.md) nedan för information. 

## <a name="custom-activity"></a>Anpassad aktivitet
Om du vill omvandla data på ett sätt som inte stöds av Data Factory kan du skapa en anpassad aktivitet med egen logik för databearbetning och använda aktiviteten i pipelinen. Du kan konfigurera anpassad .NET-aktivitet ska köras med en Azure Batch-tjänst eller ett Azure HDInsight-kluster. Se [använda anpassade aktiviteter](transform-data-using-dotnet-custom-activity.md) nedan för information. 

Du kan skapa en anpassad aktivitet som kör R-skript i ditt HDInsight-kluster med R installerat. Se [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Köra R-skript med Azure Data Factory). 

## <a name="compute-environments"></a>Compute-miljöer
Du skapar en länkad tjänst för compute-miljö och sedan använda den länkade tjänsten när du definierar en transformeringsaktivitet. Det finns två typer av beräkningsmiljöer som stöds av Data Factory. 

- **På begäran**: I det här fallet datormiljön är fullständigt hanterad av Data Factory. Den skapas automatiskt av Data Factory-tjänsten innan ett jobb skickas för att bearbeta data och tas bort när jobbet har slutförts. Du kan konfigurera och kontrollera detaljerade inställningar av på begäran beräkningsmiljö för jobbkörning, klusterhantering och start av åtgärder. 
- **Ta med din egen**: I det här fallet kan du registrera dina egna datormiljö (till exempel HDInsight-kluster) som en länkad tjänst i Datafabriken. Datormiljön hanteras av dig och Data Factory-tjänsten används för att köra aktiviteterna. 

Se [beräkna länkade tjänster](compute-linked-services.md) artikeln om du vill veta mer om compute services som stöds av Data Factory. 

## <a name="next-steps"></a>Nästa steg
Se ett exempel på hur du använder en transformeringsaktivitet följande självstudie: [självstudie: Transformera data med Spark](tutorial-transform-data-spark-powershell.md)
