---
title: "Anropa Spark-program från Azure Data Factory | Microsoft Docs"
description: "Lär dig mer om att anropa Spark-program från ett Azure data factory genom att använda MapReduce activity."
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
ms.date: 01/10/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: f03c3b6e275c0bc97df9e687a20acf45956664d2
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Anropa Spark-program från Azure Data Factory pipelines

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Pig-aktivitet](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop Streaming activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch Execution aktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update resurs aktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Aktiviteten lagrad procedur](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET-anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory som är allmänt tillgänglig. Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Transformera data med hjälp av aktiviteten Apache Spark i Data Factory version 2](../transform-data-using-spark.md).

## <a name="introduction"></a>Introduktion
Aktiviteten Spark är ett av de [data transformation aktiviteter](data-factory-data-transformation-activities.md) stöds av Data Factory. Den här aktiviteten körs det angivna Spark-programmet på Spark-kluster i Azure HDInsight. 

> [!IMPORTANT]
> - Spark-aktiviteten stöder inte HDInsight Spark-kluster som använder Azure Data Lake Store som primär lagring.
> - Spark-aktiviteten stöder endast befintliga (egna) HDInsight Spark-kluster. Det stöder inte en länkad HDInsight på begäran-tjänst.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Genomgång: Skapa en pipeline med ett Spark-aktivitet
Här följer vanliga stegen för att skapa ett data factory-pipelinen med Spark aktiviteter: 

* Skapa en datafabrik.
* Skapa en länkad Azure Storage-tjänst för att länka ditt lagringsutrymme som är associerad med HDInsight Spark-klustret till datafabriken.
* Skapa en länkad HDInsight-tjänst för att länka ditt Spark-kluster i HDInsight till datafabriken.
* Skapa en datamängd som refererar till den länkade lagringstjänsten. För närvarande måste du ange en datamängd för utdata för en aktivitet även om det finns inga utdata som skapas. 
* Skapa en pipeline med Spark-aktivitet som refererar till HDInsight länkade tjänst som du skapade. Aktiviteten har konfigurerats med den datamängd som du skapade i föregående steg som en datamängd för utdata. Datamängd för utdata är styr schemat (varje timme, dag). Därför måste du ange datamängd för utdata, även om aktiviteten inte verkligen ger utdata.

### <a name="prerequisites"></a>Förutsättningar
1. Skapa ett allmänt lagringskonto genom att följa instruktionerna i [skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

2. Skapa ett Spark-kluster i HDInsight genom att följa anvisningarna i kursen [skapar ett Spark-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Associera storage-konto som du skapade i steg 1 till det här klustret.

3. Hämta och granska skriptfilen Python **test.py** på [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

4. Överför **test.py** till den **pyFiles** mapp i den **adfspark** behållare i blob storage. Skapa behållaren och mappen om de inte redan finns.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Följ dessa steg om du vill skapa en datafabrik:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **nya** > **Data + analys** > **Datafabriken**.

3. På den **nya datafabriken** bladet under **namn**, ange **SparkDF**.

   > [!IMPORTANT]
   > Namnet på Azure Data Factory måste vara globalt unikt. Om du ser felet ”datafabriksnamnet SparkDF är inte tillgänglig”, byta namn på datafabriken. Till exempel använda yournameSparkDFdate och skapa datafabriken igen. Mer information om namngivningsregler finns [Data Factory: namngivningsregler](data-factory-naming-rules.md).

4. Under **prenumeration**, Välj den Azure-prenumeration där du vill att datafabriken ska skapas.

5. Välj en befintlig resursgrupp eller skapa ett Azure-resursgrupp.

6. Välj den **fäst på instrumentpanelen** kryssrutan.

7. Välj **Skapa**.

   > [!IMPORTANT]
   > Om du vill skapa Data Factory-instanser måste du vara medlem av den [Data Factory-deltagare](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) rollen på prenumerationen/resursgruppsnivå.

8. Du kan se datafabriken när den har skapats på instrumentpanelen i Azure-portalen.

9. När datafabriken har skapats kan du se den **datafabriken** sidan som visar innehållet i datafabriken. Om du inte ser den **datafabriken** väljer panelen för din data factory på instrumentpanelen.

    ![Bladet Datafabrik](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget skapar du två länkade tjänster. En tjänst länkar Spark-kluster till din data factory och den andra tjänsten länkar ditt lagringsutrymme till din data factory. 

#### <a name="create-a-storage-linked-service"></a>Skapa en länkad lagringstjänst
I det här steget kan länka du ditt lagringskonto till din data factory. En datauppsättning som du skapar i ett steg senare i den här genomgången refererar till den här länkade tjänsten. Länkad HDInsight-tjänst som du definierar i nästa steg refererar till den här länkade tjänsten för. 

1. På den **datafabriken** bladet väljer **författare och distribuera**. Data Factory-redigeraren visas.

2. Välj **Nytt datalager**, och välj **Azure Storage**.

   ![Nytt datalager](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

3. JSON-skript som du använder för att skapa en lagringsplats för länkad tjänst visas i redigeraren.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

4. Ersätt **kontonamn** och **kontonyckel** med namn och åtkomstnyckel för ditt lagringskonto. Om du vill lära dig mer om att hämta din lagringsåtkomstnyckel, se hur du visa, kopiera och återskapa åtkomstnycklar för lagring i [hantera ditt lagringskonto](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

5. Om du vill distribuera den länkade tjänsten, Välj **distribuera** i kommandofältet. Utkast till-1-fönstret försvinner när den länkade tjänsten har distribuerats. Du ser **AzureStorageLinkedService** i trädvyn till vänster.

#### <a name="create-an-hdinsight-linked-service"></a>Skapa en länkad HDInsight-tjänst
I det här steget skapar du en länkad HDInsight-tjänst för att länka ditt HDInsight Spark-kluster till datafabriken. HDInsight-klustret används för att köra Spark-program som anges i aktiviteten Spark för pipeline i det här exemplet. 

1. I den Data Factory-redigeraren, Välj **mer** > **nya beräkning** > **HDInsight-kluster**.

    ![Skapa länkad HDInsight-tjänst](media/data-factory-spark/new-hdinsight-linked-service.png)

2. Kopiera och klistra in följande kodfragment till fönstret Draft-1. I JSON-redigerare gör du följande:

    a. Ange URI för HDInsight Spark-klustret. Till exempel: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Ange namnet på den användare som har åtkomst till Spark-klustret.

    c. Ange lösenord för användaren.

    d. Ange den länkade lagringstjänsten som är associerad med HDInsight Spark-klustret. I det här exemplet är det AzureStorageLinkedService.

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
    > - Spark-aktiviteten stöder inte HDInsight Spark-kluster som använder Azure Data Lake Store som primär lagring.
    > - Spark-aktiviteten stöder endast befintliga (egna) HDInsight Spark-kluster. Det stöder inte en länkad HDInsight på begäran-tjänst.

    Läs mer om tjänsten länkad HDInsight [HDInsight länkade tjänsten](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

3. Om du vill distribuera den länkade tjänsten, Välj **distribuera** i kommandofältet. 

### <a name="create-the-output-dataset"></a>Skapa datamängd för utdata
Datamängd för utdata är styr schemat (varje timme, dag). Därför måste du ange en datamängd för utdata för aktiviteten Spark i pipelinen även om aktiviteten inte producerar några utdata. Ange en inkommande datauppsättning för aktiviteten är valfritt.

1. I den Data Factory-redigeraren, Välj **mer** > **ny datamängd** > **Azure Blob storage**.

2. Kopiera och klistra in följande kodfragment till fönstret Draft-1. JSON-fragment definierar en datamängd som kallas **OutputDataset**. Dessutom kan du ange att resultatet lagras i blob-behållaren som kallas **adfspark** och mappen kallas **pyFiles-/ utdata**. Som tidigare nämnts är den här datauppsättningen dummy dataset. Spark-program i det här exemplet producerar inte några utdata. Den **tillgänglighet** avsnittet anger datamängd för utdata skapas varje dag. 

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
3. Om du vill distribuera dataset, Välj **distribuera** i kommandofältet.


### <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en HDInsightSpark aktivitet. Datamängd för utdata är för närvarande styr schemat, så du måste skapa en datamängd för utdata även om aktiviteten inte producerar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. Därför har inga indata datamängden angetts i det här exemplet.

1. I den Data Factory-redigeraren, Välj **mer** > **ny pipeline**.

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

    a. Den **typen** egenskap är inställd på **HDInsightSpark**.

    b. Den **rootPath** egenskap är inställd på **adfspark\\pyFiles** där adfspark är blob-behållaren och pyFiles filmapp i behållaren. I det här exemplet är blob-lagring den som är associerad med Spark-klustret. Du kan överföra filen till ett annat lagringskonto. Om du gör det, kan du skapa en länkad Storage-tjänst om du vill länka detta lagringskonto till datafabriken. Ange namnet på den länkade tjänsten som ett värde för den **sparkJobLinkedService** egenskapen. Mer information om den här egenskapen och andra egenskaper som stöds av aktiviteten Spark finns [Väck Aktivitetsegenskaper](#spark-activity-properties).

    c. Den **entryFilePath** egenskap är inställd på **test.py**, vilket är Python-fil.

    d. Den **getDebugInfo** egenskap är inställd på **alltid**, vilket betyder att filerna är alltid genereras (lyckade eller misslyckade).

    > [!IMPORTANT]
    > Vi rekommenderar att du inte ange egenskapen till `Always` i en produktionsmiljö om du felsöker ett problem.

    e. Den **matar ut** avsnittet innehåller en datamängd för utdata. Du måste ange en datamängd för utdata, även om Spark-program inte producerar några utdata. Datamängd för utdata enheter schemat för pipeline (varje timme, dag). 

    Mer information om de egenskaper som stöds av aktiviteten Spark finns i avsnittet [Väck Aktivitetsegenskaper](#spark-activity-properties).

3. Om du vill distribuera pipeline, Välj **distribuera** i kommandofältet.

### <a name="monitor-a-pipeline"></a>Övervaka en pipeline
1. På den **datafabriken** bladet väljer **övervaka och hantera** starta övervakning programmet i en annan flik.

    ![Ikonen Övervaka och hantera](media/data-factory-spark/monitor-and-manage-tile.png)

2. Ändra den **starttid** filter överst till **2/1/2017**, och välj **tillämpa**.

3. Endast en aktivitet visas eftersom det finns endast en dag mellan start (2017-02-01)- och sluttider (2017-02-02) av pipeline. Kontrollera att datasektorn i den **klar** tillstånd.

    ![Övervaka pipeline](media/data-factory-spark/monitor-and-manage-app.png)

4. I den **aktivitet windows** väljer du en aktivitet som körs för att se information om den. Om det finns ett fel, se information om den i den högra rutan.

### <a name="verify-the-results"></a>Kontrollera resultatet

1. Starta Jupyter-anteckningsboken för HDInsight Spark-kluster genom att gå till [webbplatsen](https://CLUSTERNAME.azurehdinsight.net/jupyter). Du kan även öppna en instrumentpanelen i klustret för HDInsight Spark-kluster och sedan starta Jupyter-anteckningsboken.

2. Välj **ny** > **PySpark** att starta en ny anteckningsbok.

    ![Ny Jupyter-anteckningsbok](media/data-factory-spark/jupyter-new-book.png)

3. Kör följande kommando genom att kopiera och klistra in texten och trycka på SKIFT + RETUR i slutet av den andra instruktionen:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Bekräfta att du ser data från tabellen hvac. 

    ![Jupyter frågeresultat](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Detaljerade anvisningar finns i avsnittet [köra Spark SQL-fråga](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Felsökning
Eftersom du angett getDebugInfo **alltid**, visas en logg undermapp i mappen pyFiles i blob-behållaren. Mer information finns i loggfilen i loggmappen. Den här loggfilen är användbart när det uppstår ett fel. I en produktionsmiljö kan du vill ange **fel**.

Ytterligare felsökningsinformation, gör du följande:


1. Gå till `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![YARN-Användargränssnittet program](media/data-factory-spark/yarnui-application.png)

2. Välj **loggar** för en av körningen försöker.

    ![Sidan program](media/data-factory-spark/yarn-applications.png)

3. Du ser på sidan Logga följande ytterligare felinformation:

    ![Logga fel](media/data-factory-spark/yarnui-application-error.png)

Följande avsnitt innehåller information om data factory-enheter att använda Spark-kluster och Spark aktivitet i din data factory.

## <a name="spark-activity-properties"></a>Spark Aktivitetsegenskaper
Här är exempel JSON-definitionen för en pipeline med Spark aktiviteter: 

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

I följande tabell beskrivs JSON-egenskaper som används i JSON-definitionen.

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| namn | Namnet på aktivitet i pipelinen. | Ja |
| description | Text som beskriver aktiviteten utför. | Nej |
| typ | Den här egenskapen måste anges till HDInsightSpark. | Ja |
| linkedServiceName | Namn på länkad HDInsight-tjänsten som körs på Spark-program. | Ja |
| rootPath | Blob-behållaren och mappen med Spark-filen. Filnamnet är skiftlägeskänsligt. | Ja |
| entryFilePath | Relativa sökvägen till rotmappen på Spark/kodpaketet. | Ja |
| Klassnamn | Programmets huvudsakliga Java/Spark klass. | Nej |
| Argument | En lista med kommandoradsargument till Spark-program. | Nej |
| proxyUser | Användarkontot som ska personifiera för att köra Spark-program. | Nej |
| sparkConfig | Ange värdena för konfigurationsegenskaperna Spark som anges i [Spark-konfiguration: programegenskaper](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nej |
| getDebugInfo | Anger när Spark loggfilerna kopieras till den lagring som används av HDInsight-klustret (eller) anges av sparkJobLinkedService. Tillåtna värden är None, alltid eller fel. Standardvärdet är ingen. | Nej |
| sparkJobLinkedService | Den länkade lagringstjänsten som innehåller Spark fil, beroenden och loggar. Om du inte anger ett värde för den här egenskapen används lagring som associerats med HDInsight-kluster. | Nej |

## <a name="folder-structure"></a>Mappstruktur
Spark-aktiviteten stöder inte ett infogat skript som Pig och gör Hive-aktiviteter. Spark jobb är också mer utökningsbar än Pig/Hive-jobb. För Spark jobb, du kan ange flera beroenden som jar-paket (placeras i java KLASSÖKVÄGEN), Python-filer (placerad i PYTHONPATH) och andra filer.

Skapa följande mappstruktur i blob-lagring som refereras av länkad HDInsight-tjänsten. Därefter kan du överföra beroende filer till lämpliga undermappar i rotmappen som representeras av **entryFilePath**. Till exempel ladda upp Python-filer till undermappen pyFiles och jar-filer till undermappen burkar i rotmappen. Vid körning förväntar Data Factory-tjänsten följande mappstruktur i blob storage: 

| Sökväg | Beskrivning | Krävs | Typ |
| ---- | ----------- | -------- | ---- |
| . | Rotsökvägen för Spark-jobb i den länkade lagringstjänsten. | Ja | Mapp |
| &lt;användardefinierade&gt; | Den sökväg som pekar på filen post för Spark-jobbet. | Ja | Fil |
| . / JAR: er | Alla filer under den här mappen överförs och placeras på Java-klassökvägen i klustret. | Nej | Mapp |
| . / pyFiles | Alla filer under den här mappen överförs och placeras på PYTHONPATH i klustret. | Nej | Mapp |
| . / filer | Alla filer under den här mappen överförs och placeras på utföraren arbetskatalogen. | Nej | Mapp |
| . / arkiveras | Alla filer under den här mappen dekomprimeras. | Nej | Mapp |
| . / loggar | Den mapp där loggar från Spark-kluster lagras.| Nej | Mapp |

Här är ett exempel för lagring som innehåller två Spark jobbfiler i blob-lagring som refereras av HDInsight länkade tjänsten:

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
