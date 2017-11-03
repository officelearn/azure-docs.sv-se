---
title: "Anropa Spark-program från Azure Data Factory | Microsoft Docs"
description: "Lär dig mer om att anropa Spark-program från ett Azure data factory med MapReduce Activity."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 5220ca664d5c7584f3aada0bb707099f91d5650f
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Anropa Spark-program från Azure Data Factory pipelines

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Pig-aktivitet](data-factory-pig-activity.md)
> * [MapReduce Activity](data-factory-map-reduce.md)
> * [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [Anpassad aktivitet för .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Transformera data med spark aktivitet från Data Factory version 2](../transform-data-using-spark.md).

## <a name="introduction"></a>Introduktion
Spark aktivitet är en av de [data transformation aktiviteter](data-factory-data-transformation-activities.md) stöds av Azure Data Factory. Den här aktiviteten körs det angivna Spark-programmet på Apache Spark-kluster i Azure HDInsight.    

> [!IMPORTANT]
> - Spark aktiviteten stöder inte HDInsight Spark-kluster som använder ett Azure Data Lake Store som primär lagring.
> - Spark aktiviteten stöder endast befintliga (egna) HDInsight Spark-kluster. Det stöder inte en länkad HDInsight på begäran-tjänst.

## <a name="walkthrough-create-a-pipeline-with-spark-activity"></a>Genomgång: skapa en pipeline med Spark-aktivitet
Här följer vanliga stegen för att skapa Data Factory-pipelinen med ett Spark-aktivitet.  

1. Skapa en datafabrik.
2. Skapa en länkad Azure Storage-tjänst om du vill länka ditt Azure storage som är associerad med HDInsight Spark-klustret till datafabriken.     
2. Skapa en Azure HDInsight länkad tjänst för att länka Apache Spark-kluster i Azure HDInsight till datafabriken.
3. Skapa en datamängd som refererar till länkad Azure Storage-tjänsten. För närvarande måste du ange en datamängd för utdata för en aktivitet även om det finns inga utdata som skapas.  
4. Skapa en pipeline med Spark-aktivitet som refererar till den HDInsight länkade tjänst som skapades i #2. Aktiviteten har konfigurerats med den datamängd som du skapade i föregående steg som en datamängd för utdata. Datamängd för utdata är styr schemat (varje timme, varje dag, osv.). Därför måste du ange datamängd för utdata, även om aktiviteten inte verkligen ger utdata.

### <a name="prerequisites"></a>Krav
1. Skapa en **allmänna Azure Storage-konto** genom att följa anvisningarna i den här genomgången: [skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account).  
2. Skapa en **Apache Spark-kluster i Azure HDInsight** genom att följa anvisningarna i kursen: [skapa Apache Spark-kluster i Azure HDInsight](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Koppla Azure storage-konto som du skapade i steg #1 med det här klustret.  
3. Hämta och granska skriptfilen python **test.py** finns på: [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).  
3.  Överför **test.py** till den **pyFiles** mapp i den **adfspark** behållare i Azure Blob storage. Skapa behållaren och mappen om de inte finns.

### <a name="create-data-factory"></a>Skapa en datafabrik
Låt oss börja med att skapa datafabriken i det här steget.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **NY** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**.
3. I den **nya datafabriken** bladet ange **SparkDF** för namnet.

   > [!IMPORTANT]
   > Namnet på Azure Data Factory måste vara **globalt unikt**. Om du ser felet: **datafabriksnamnet ”SparkDF” är inte tillgänglig**. Ändra namnet på data factory (till exempel yournameSparkDFdate och försök att skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.   
4. Välj den **Azure-prenumeration** där du vill att datafabriken ska skapas.
5. Välj en befintlig **resursgruppen** eller skapa ett Azure-resursgrupp.
6. Välj **fäst på instrumentpanelen** alternativet.  
6. Klicka på **Skapa** på bladet **Ny datafabrik**.

   > [!IMPORTANT]
   > Om du vill skapa Data Factory-instanser måste du vara medlem i [Data Factory-deltagarrollen](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) på gruppnivå/resursgrupp.
7. Du ser datafabriken som skapas i den **instrumentpanelen** på Azure-portalen på följande sätt:   
8. När datafabriken har skapats visas datafabrikssidan med innehållet i datafabriken. Om data factory-sidan inte visas klickar du på panelen för din data factory på instrumentpanelen.

    ![Bladet Datafabrik](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget skapar du två länkade tjänster, en länka ditt Spark-kluster till din data factory och andra länka ditt Azure storage till din data factory.  

#### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
I det här steget länkar du ditt Azure-lagringskonto till datafabriken. En datauppsättning som du skapar i ett steg senare i den här genomgången refererar till den här länkade tjänsten. Länkad HDInsight-tjänst som du definierar i nästa steg refererar till den här länkade tjänsten för.  

1. Klicka på **författare och distribuera** på den **Data Factory** bladet för din data factory. Du bör se Data Factory-redigeraren.
2. Klicka på **Nytt datalager** och välj **Azure-lagring**.

   ![Ny datalagring - Azure Storage - meny](./media/data-factory-spark/new-data-store-azure-storage-menu.png)
3. Du bör se den **JSON-skript** länkade tjänsten i Redigeraren för att skapa ett Azure Storage.

   ![Länkad Azure-lagringstjänst](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. Ersätt **kontonamn** och **kontonyckel** med namn och åtkomstnyckel för Azure storage-konto. Information om hur du hämtar lagringsåtkomstnyckeln finns i avsnitten om hur du visar, kopierar och återskapar åtkomstnycklar i [Manage your storage account](../../storage/common/storage-create-storage-account.md#manage-your-storage-account) (Hantera ditt lagringskonto).
5. Om du vill distribuera den länkade tjänsten klickar du på **distribuera** i kommandofältet. När den länkade tjänsten har distribuerats, bör fönstret **Draft-1** försvinna och du ser **AzureStorageLinkedService** i trädvyn till vänster.

#### <a name="create-hdinsight-linked-service"></a>Skapa länkad HDInsight-tjänst
I det här steget skapar du Azure HDInsight länkad tjänst för att länka ditt HDInsight Spark-kluster till datafabriken. HDInsight-klustret används för att köra Spark-program som anges i aktiviteten Spark för pipeline i det här exemplet.  

1. Klicka på **... Flera** i verktygsfältet klickar du på **nya beräkning**, och klicka sedan på **HDInsight-kluster**.

    ![Skapa länkad HDInsight-tjänst](media/data-factory-spark/new-hdinsight-linked-service.png)
2. Kopiera och klistra in följande kodfragment till fönstret **Draft-1**. I JSON-redigerare gör du följande steg:
    1. Ange den **URI** för HDInsight Spark-klustret. Till exempel: `https://<sparkclustername>.azurehdinsight.net/`.
    2. Ange namnet på den **användaren** vem har tillgång till Spark-klustret.
    3. Ange den **lösenord** för användaren.
    4. Ange den **Azure länkade lagringstjänsten** som är associerad med HDInsight Spark-klustret. I det här exemplet är: **AzureStorageLinkedService**.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Spark aktiviteten stöder inte HDInsight Spark-kluster som använder ett Azure Data Lake Store som primär lagring.
    > - Spark aktiviteten stöder endast befintliga (egna) HDInsight Spark-kluster. Det stöder inte en länkad HDInsight på begäran-tjänst.

    Se [länkad HDInsight-tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) för ytterligare information om HDInsight länkade tjänsten.
3.  Om du vill distribuera den länkade tjänsten klickar du på **distribuera** i kommandofältet.  

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
Datamängd för utdata är styr schemat (varje timme, varje dag, osv.). Därför måste du ange en datamängd för utdata för aktiviteten spark i pipelinen även om aktiviteten verkligen inte producerar några utdata. Ange en inkommande datauppsättning för aktiviteten är valfritt.

1. I **Data Factory-redigeraren**, klicka på **... Fler** på kommandofältet klickar du på **Ny datamängd** och välj **Azure Blob-lagring**.  
2. Kopiera och klistra in följande kodfragment till fönstret Draft-1. JSON-fragment definierar en datamängd som kallas **OutputDataset**. Dessutom kan du ange att resultatet lagras i blob-behållaren som kallas **adfspark** och mappen kallas **pyFiles-/ utdata**. Som tidigare nämnts är den här datauppsättningen dummy dataset. Spark-program i det här exemplet skapar inte några utdata. Den **tillgänglighet** avsnittet anger datamängd för utdata skapas varje dag.  

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
3. Om du vill distribuera datauppsättningen klickar du på **distribuera** i kommandofältet.


### <a name="create-pipeline"></a>Skapa pipeline
I det här steget skapar du en pipeline med en **HDInsightSpark** aktivitet. För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. Därför har inga indata datamängden angetts i det här exemplet.

1. I den **Data Factory-redigeraren**, klickar du på **... Flera** i kommandofältet och klicka sedan på **ny pipeline**.
2. Ersätt skript i ett utkast till-1-fönster med följande skript:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Observera följande punkter:
    - Den **typen** egenskap är inställd på **HDInsightSpark**.
    - Den **rootPath** är inställd på **adfspark\\pyFiles** där adfspark är Azure Blob-behållaren och pyFiles är bra mapp i behållaren. I det här exemplet är det som är associerad med Spark-kluster i Azure Blob Storage. Du kan överföra filen till en annan Azure Storage. Om du gör det, kan du skapa en länkad Azure Storage-tjänst om du vill länka detta lagringskonto till datafabriken. Ange namnet på den länkade tjänsten som ett värde för den **sparkJobLinkedService** egenskapen. Se [Spark Aktivitetsegenskaper](#spark-activity-properties) för ytterligare information om den här egenskapen och andra egenskaper som stöds av aktiviteten Spark.  
    - Den **entryFilePath** anges till den **test.py**, vilket är python-fil.
    - Den **getDebugInfo** egenskap är inställd på **alltid**, vilket betyder att filerna är alltid genereras (lyckade eller misslyckade).

        > [!IMPORTANT]
        > Vi rekommenderar att du inte ange egenskapen till `Always` i en produktionsmiljö om du felsöker ett problem.
    - Den **matar ut** avsnittet innehåller en datamängd för utdata. Du måste ange en datamängd för utdata, även om spark-program inte producerar några utdata. Datamängd för utdata enheter schemat för pipeline (varje timme, varje dag, osv.).  

        Mer information om de egenskaper som stöds av Spark-aktiviteten finns [Väck Aktivitetsegenskaper](#spark-activity-properties) avsnitt.
3. Om du vill distribuera pipeline, klickar du på **distribuera** i kommandofältet.

### <a name="monitor-pipeline"></a>Övervaka pipeline
1. Klicka på **X** Stäng Data Factory-redigeraren blad och gå tillbaka till startsidan Data Factory. Klicka på **övervaka och hantera** starta övervakning program i en annan flik.

    ![Övervaka och hantera sida vid sida](media/data-factory-spark/monitor-and-manage-tile.png)
2. Ändra den **starttid** filter överst till **2/1/2017**, och klicka på **tillämpa**.
3. Du bör se endast en aktivitetsfönstret eftersom det finns endast en dag mellan start (2017-02-01)- och sluttider (2017-02-02) av pipeline. Kontrollera att datasektorn i **klar** tillstånd.

    ![Övervaka pipeline](media/data-factory-spark/monitor-and-manage-app.png)    
4. Välj den **aktivitetsfönstret** att se information om aktiviteten körs. Om det finns ett fel, se information om den i den högra rutan.

### <a name="verify-the-results"></a>Kontrollera resultatet

1. Starta **Jupyter-anteckningsbok** för HDInsight Spark-kluster genom att gå till: https://CLUSTERNAME.azurehdinsight.net/jupyter. Du kan också starta instrumentpanelen klustret för HDInsight Spark-klustret och sedan starta **Jupyter-anteckningsbok**.
2. Klicka på **ny** -> **PySpark** att starta en ny anteckningsbok.

    ![Ny Jupyter-anteckningsbok](media/data-factory-spark/jupyter-new-book.png)
3. Kör följande kommando genom att kopiera/klistra in texten och trycka på **SKIFT + RETUR** i slutet av den andra instruktionen.  

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Bekräfta att du ser data från tabellen hvac:  

    ![Jupyter frågeresultat](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Se [köra Spark SQL-fråga](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) avsnittet detaljerade anvisningar. 

### <a name="troubleshooting"></a>Felsökning
Eftersom du ställer in **getDebugInfo** till **alltid**, visas en **loggen** undermapp i den **pyFiles** mapp i Azure Blob-behållare. I loggfilen i loggmappen innehåller ytterligare information. Den här loggfilen är användbart när det uppstår ett fel. I en produktionsmiljö kan du vill ange **fel**.

Ytterligare felsökningsinformation, gör du följande steg:


1. Navigera till `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![YARN-Användargränssnittet program](media/data-factory-spark/yarnui-application.png)  
2. Klicka på **loggar** för en av körningen försöker.

    ![Sidan program](media/data-factory-spark/yarn-applications.png)
3. Du bör se ytterligare information om fel på sidan logga.

    ![Logga fel](media/data-factory-spark/yarnui-application-error.png)

Följande avsnitt innehåller information om Data Factory-enheter ska använda Apache Spark-kluster och Spark aktivitet i din data factory.

## <a name="spark-activity-properties"></a>Spark Aktivitetsegenskaper
Här är exempel JSON-definitionen för en pipeline med Spark aktivitet:    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

I följande tabell beskrivs JSON-egenskaper som används i JSON-definitionen:

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| namn | Namnet på aktivitet i pipelinen. | Ja |
| description | Text som beskriver hur aktiviteten ska hantera. | Nej |
| typ | Den här egenskapen måste anges till HDInsightSpark. | Ja |
| linkedServiceName | Namn på länkad HDInsight-tjänsten som körs på Spark-program. | Ja |
| rootPath | Azure Blob-behållaren och mappen som innehåller filen Spark. Filnamnet är skiftlägeskänslig. | Ja |
| entryFilePath | Relativa sökvägen till rotmappen på Spark/kodpaketet. | Ja |
| Klassnamn | Programmets Java/Spark huvudsakliga klass | Nej |
| Argument | En lista med kommandoradsargument till Spark-program. | Nej |
| proxyUser | Användarkontot som ska personifiera för att köra Spark-program | Nej |
| sparkConfig | Ange värden för egenskaper för Spark-konfiguration visas i avsnittet: [Spark-konfiguration – programegenskaper](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nej |
| getDebugInfo | Anger när Spark loggfilerna kopieras till Azure storage som används av HDInsight-kluster (eller) anges av sparkJobLinkedService. Tillåtna värden: None, alltid eller fel. Standardvärde: Ingen. | Nej |
| sparkJobLinkedService | Azure Storage länkade tjänst som äger Spark fil, beroenden och loggar.  Om du inte anger ett värde för den här egenskapen används lagring som är associerade med HDInsight-kluster. | Nej |

## <a name="folder-structure"></a>Mappstruktur
Spark-aktiviteten stöder inte en infogad skriptet som Pig och gör Hive-aktiviteter. Spark jobb är också mer utökningsbar än Pig/Hive-jobb. För Spark jobb, du kan ange flera beroenden som jar-paket (placeras i java KLASSÖKVÄGEN), python-filer (placerad i PYTHONPATH) och andra filer.

Skapa följande mappstruktur i Azure Blob-lagring som refereras av länkad HDInsight-tjänsten. Därefter kan du överföra beroende filer till lämpliga undermappar i rotmappen som representeras av **entryFilePath**. Till exempel överföra python till undermappen pyFiles och jar-filer till undermappen burkar i rotmappen. Vid körning kommer Data Factory-tjänsten följande mappstruktur i Azure Blob storage:     

| Sökväg | Beskrivning | Krävs | Typ |
| ---- | ----------- | -------- | ---- |
| . | Rotsökvägen för Spark-jobb i den länkade lagringstjänsten  | Ja | Mapp |
| &lt;användardefinierade&gt; | Den sökväg som pekar på filen post för Spark-jobb | Ja | Fil |
| . / JAR: er | Alla filer under den här mappen överförs och placeras på java-klassökvägen i klustret | Nej | Mapp |
| . / pyFiles | Alla filer under den här mappen överförs och placeras på PYTHONPATH i klustret | Nej | Mapp |
| . / filer | Alla filer under den här mappen överförs och placeras på utföraren arbetskatalogen | Nej | Mapp |
| . / arkiveras | Alla filer under den här mappen dekomprimeras | Nej | Mapp |
| . / loggar | Den mapp där loggar från Spark-kluster lagras.| Nej | Mapp |

Här är ett exempel på en lagringsplats som innehåller två Spark jobbfiler i Azure Blob Storage som refereras av länkad HDInsight-tjänsten.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
