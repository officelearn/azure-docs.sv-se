---
title: Anropa Spark-program från Azure Data Factory
description: Lär dig hur du anropar Spark-program från en Azure Data Factory med hjälp av MapReduce-aktiviteten.
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
ms.openlocfilehash: ce5fb014c7d954b3e8430a86430c6a666adff204
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969232"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Anropa Spark-program från Azure Data Factory pipelines

> [!div class="op_single_selector" title1="Omvandlings aktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Aktivitet i gris](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop streaming-aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning batch-körning, aktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning uppdatera resurs aktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad procedur aktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET-anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig. Om du använder den aktuella versionen av tjänsten Data Factory, se [transformera data med hjälp av Apache Spark-aktiviteten i Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introduktion
Spark-aktiviteten är en av de [data omvandlings aktiviteter](data-factory-data-transformation-activities.md) som stöds av Data Factory. Den här aktiviteten kör det angivna Spark-programmet på ditt Spark-kluster i Azure HDInsight.

> [!IMPORTANT]
> - Spark-aktiviteten stöder inte HDInsight Spark-kluster som använder Azure Data Lake Store som primär lagring.
> - Spark-aktiviteten stöder bara befintliga (dina egna) HDInsight Spark-kluster. Den har inte stöd för en länkad HDInsight-tjänst på begäran.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Genom gång: skapa en pipeline med en spark-aktivitet
Här är de vanligaste stegen för att skapa en Data Factory-pipeline med en spark-aktivitet:

* Skapa en datafabrik.
* Skapa en Azure Storage länkad tjänst för att länka lagringen som är kopplad till ditt HDInsight Spark-kluster till data fabriken.
* Skapa en länkad HDInsight-tjänst för att länka ditt Spark-kluster i HDInsight till data fabriken.
* Skapa en data uppsättning som refererar till den länkade lagrings tjänsten. För närvarande måste du ange en data uppsättning för utdata för en aktivitet även om det inte finns några utdata som skapas.
* Skapa en pipeline med Spark-aktivitet som refererar till den länkade HDInsight-tjänsten som du skapade. Aktiviteten konfigureras med den data uppsättning som du skapade i föregående steg som en data uppsättning för utdata. Data uppsättningen för utdata är det som driver schemat (varje timme, varje dag). Därför måste du ange data uppsättningen för utdata även om aktiviteten inte faktiskt skapar utdata.

### <a name="prerequisites"></a>Krav
1. Skapa ett allmänt lagrings konto genom att följa instruktionerna i [skapa ett lagrings konto](../../storage/common/storage-account-create.md).

1. Skapa ett Spark-kluster i HDInsight genom att följa anvisningarna i självstudien [skapa ett Spark-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Koppla lagrings kontot som du skapade i steg 1 med det här klustret.

1. Hämta och granska python- **test.py** som finns på [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

1. Ladda upp **test.py** till mappen **pyFiles** i behållaren **adfspark** i Blob Storage. Skapa behållaren och mappen om de inte finns.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Gör så här för att skapa en datafabrik:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **Nytt** > **Data och analys** > **Data Factory**.

1. På bladet **ny data fabrik** , under **namn**, anger du **SparkDF**.

   > [!IMPORTANT]
   > Namnet på Azure Data Factory måste vara globalt unikt. Om du ser felet "Data Factory Name SparkDF är inte tillgängligt" ändrar du namnet på data fabriken. Använd till exempel yournameSparkDFdate och skapa data fabriken igen. Mer information om namngivningsregler finns i [Data Factory: namngivningsregler](data-factory-naming-rules.md).

1. Under **Prenumeration** väljer du den Azure-prenumeration där du vill att datafabriken ska skapas.

1. Välj en befintlig resurs grupp eller skapa en Azure-resurs grupp.

1. Markera kryssrutan **Fäst på instrumentpanelen**.

1. Välj **Skapa**.

   > [!IMPORTANT]
   > Om du vill skapa Data Factory-instanser måste du vara medlem i [rollen Deltagare för Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) på prenumerations-/resursgruppnivå.

1. Du ser data fabriken när den skapas på instrument panelen för Azure Portal.

1. När datafabriken har skapats visas sidan **Datafabrik** med innehållet i datafabriken. Om du inte ser **data fabriks** sidan väljer du panelen för din data fabrik på instrument panelen.

    ![Bladet Datafabrik](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget skapar du två länkade tjänster. En tjänst länkar ditt Spark-kluster till din data fabrik och den andra tjänsten länkar din lagring till din data fabrik.

#### <a name="create-a-storage-linked-service"></a>Skapa en länkad lagringstjänst
I det här steget länkar du ditt lagringskonto till datafabriken. En data uppsättning som du skapar i ett steg senare i den här genom gången refererar till den här länkade tjänsten. Den länkade HDInsight-tjänsten som du definierar i nästa steg avser även den här länkade tjänsten.

1. På bladet **data fabrik** väljer du **författare och distribution**. Data Factory redigeraren visas.

1. Välj **Nytt datalager** och välj **Azure Storage**.

   ![Nytt data lager](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. JSON-skriptet som du använder för att skapa en länkad lagrings tjänst visas i redigeraren.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Ersätt **konto namnet** och **konto nyckeln** med namnet och åtkomst nyckeln för ditt lagrings konto. Information om hur du hämtar din lagrings åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).

1. Om du vill distribuera den länkade tjänsten väljer du **distribuera** i kommando fältet. Fönstret Draft-1 stängs när den länkade tjänsten har distribuerats. Du ser **AzureStorageLinkedService** i trädvyn till vänster.

#### <a name="create-an-hdinsight-linked-service"></a>Skapa en länkad HDInsight-tjänst
I det här steget skapar du en länkad HDInsight-tjänst för att länka ditt HDInsight Spark-kluster till data fabriken. HDInsight-klustret används för att köra Spark-programmet som anges i Spark-aktiviteten för pipelinen i det här exemplet.

1. I Data Factory redigeraren väljer du **mer** > **ny beräkning** > **HDInsight-kluster**.

    ![Skapa länkad HDInsight-tjänst](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. Utför följande steg i JSON-redigeraren:

    a. Ange URI: n för HDInsight Spark-klustret. Till exempel: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Ange namnet på den användare som har åtkomst till Spark-klustret.

    c. Ange lösen ordet för användaren.

    d. Ange den länkade lagrings tjänsten som är associerad med HDInsight Spark-klustret. I det här exemplet är det AzureStorageLinkedService.

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
    > - Spark-aktiviteten stöder bara befintliga (dina egna) HDInsight Spark-kluster. Den har inte stöd för en länkad HDInsight-tjänst på begäran.

    Mer information om den länkade HDInsight-tjänsten finns i [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Om du vill distribuera den länkade tjänsten väljer du **distribuera** i kommando fältet.

### <a name="create-the-output-dataset"></a>Skapa datauppsättningen för utdata
Data uppsättningen för utdata är det som driver schemat (varje timme, varje dag). Därför måste du ange en data uppsättning för Spark-aktiviteten i pipelinen även om aktiviteten inte genererar några utdata. Det är valfritt att ange en indata-datauppsättning för aktiviteten.

1. Välj **Mer** > **Ny datauppsättning** > **Azure Blob Storage** i Data Factory Editor.

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. JSON-kodfragmentet definierar en data uppsättning som kallas **OutputDataset**. Dessutom kan du ange att resultaten lagras i BLOB-behållaren med namnet **adfspark** och mappen med namnet **pyFiles/output**. Som tidigare nämnts är den här data uppsättningen en dummy-datauppsättning. Spark-programmet i det här exemplet genererar inga utdata. Avsnittet **tillgänglighet** anger att data uppsättningen för utdata skapas dagligen.

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
1. Om du vill distribuera data uppsättningen väljer du **distribuera** i kommando fältet.


### <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en HDInsightSpark-aktivitet. För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. Därför anges ingen indata-datamängd i det här exemplet.

1. Välj **Mer** > **Ny pipeline** i Data Factory Editor.

1. Ersätt skriptet i fönstret Draft-1 med följande skript:

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

    a. Egenskapen **Type** har angetts till **HDInsightSpark**.

    b. Egenskapen **rootPath** anges till **adfspark\\pyFiles** där adfspark är BLOB-behållaren och pyFiles är filmapp i den behållaren. I det här exemplet är Blob Storage det som är associerat med Spark-klustret. Du kan överföra filen till ett annat lagrings konto. Om du gör det skapar du en länkad lagrings tjänst för att länka lagrings kontot till data fabriken. Ange sedan namnet på den länkade tjänsten som ett värde för egenskapen **sparkJobLinkedService** . Mer information om den här egenskapen och andra egenskaper som stöds av Spark-aktiviteten finns i [Egenskaper för Spark-aktivitet](#spark-activity-properties).

    c. Egenskapen **entryFilePath** är inställd på **test.py**, vilket är python-filen.

    d. **GetDebugInfo** -egenskapen är inställd på **Always**, vilket innebär att loggfilerna alltid genereras (lyckade eller misslyckade).

    > [!IMPORTANT]
    > Vi rekommenderar att du inte anger den här egenskapen till `Always` i en produktions miljö om du inte felsöker ett problem.

    e. Avsnittet **utdata** har en data uppsättning för utdata. Du måste ange en data uppsättning för utdata även om Spark-programmet inte producerar några utdata. Den utgående data uppsättningen driver schemat för pipelinen (varje timme, varje dag).

    Mer information om vilka egenskaper som stöds av Spark-aktiviteten finns i avsnittet om [aktivitets egenskaper](#spark-activity-properties)för section Spark.

1. Om du vill distribuera pipelinen väljer du **distribuera** i kommando fältet.

### <a name="monitor-a-pipeline"></a>Övervaka en pipeline
1. På bladet **data fabrik** väljer du **övervaka & hantera** för att starta övervakningsprogrammet på en annan flik.

    ![Ikonen Övervaka och hantera](media/data-factory-spark/monitor-and-manage-tile.png)

1. Ändra **Start tids** filtret överst till **2/1/2017**och välj **Använd**.

1. Endast ett aktivitets fönster visas eftersom det bara finns en dag mellan start (2017-02-01) och slut tid (2017-02-02) för pipelinen. Bekräfta att data sektorn har statusen **klar** .

    ![Övervaka pipeline](media/data-factory-spark/monitor-and-manage-app.png)

1. In the **Activity windows** list, select an activity run to see details about it. If there is an error, you see details about it in the right pane.

### <a name="verify-the-results"></a>Verify the results

1. Start the Jupyter Notebook for your HDInsight Spark cluster by going to [this website](https://CLUSTERNAME.azurehdinsight.net/jupyter). You also can open a cluster dashboard for your HDInsight Spark cluster, and then start the Jupyter Notebook.

1. Select **New** > **PySpark** to start a new notebook.

    ![Jupyter new notebook](media/data-factory-spark/jupyter-new-book.png)

1. Run the following command by copying and pasting the text and pressing Shift+Enter at the end of the second statement:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Confirm that you see the data from the hvac table.

    ![Jupyter query results](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
For detailed instructions, see the section [Run a Spark SQL query](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Felsöka
Because you set getDebugInfo to **Always**, you see a log subfolder in the pyFiles folder in your blob container. The log file in the log folder provides additional information. This log file is especially useful when there is an error. In a production environment, you might want to set it to **Failure**.

For further troubleshooting, take the following steps:


1. Gå till `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![YARN UI application](media/data-factory-spark/yarnui-application.png)

1. Select **Logs** for one of the run attempts.

    ![Application page](media/data-factory-spark/yarn-applications.png)

1. You see the following additional error information in the log page:

    ![Log error](media/data-factory-spark/yarnui-application-error.png)

The following sections provide information about the data factory entities to use Spark cluster and Spark activity in your data factory.

## <a name="spark-activity-properties"></a>Spark activity properties
Here is the sample JSON definition of a pipeline with a Spark activity:

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
                    },
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

The following table describes the JSON properties used in the JSON definition.

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| namn | Name of the activity in the pipeline. | Ja |
| description | Text that describes what the activity does. | Inga |
| typ | This property must be set to HDInsightSpark. | Ja |
| linkedServiceName | Name of the HDInsight linked service on which the Spark program runs. | Ja |
| rootPath | The blob container and folder that contains the Spark file. The file name is case sensitive. | Ja |
| entryFilePath | Relative path to the root folder of the Spark code/package. | Ja |
| className | Application's Java/Spark main class. | Inga |
| arguments | A list of command-line arguments to the Spark program. | Inga |
| proxyUser | The user account to impersonate to execute the Spark program. | Inga |
| sparkConfig | Specify values for the Spark configuration properties listed in [Spark configuration: Application properties](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Inga |
| getDebugInfo | Specifies when the Spark log files are copied to the storage used by the HDInsight cluster (or) specified by sparkJobLinkedService. Allowed values are None, Always, or Failure. The default value is None. | Inga |
| sparkJobLinkedService | The Storage linked service that holds the Spark job file, dependencies, and logs. If you don't specify a value for this property, the storage associated with the HDInsight cluster is used. | Inga |

## <a name="folder-structure"></a>Folder structure
The Spark activity doesn't support an inline script as Pig and Hive activities do. Spark jobs are also more extensible than Pig/Hive jobs. For Spark jobs, you can provide multiple dependencies such as jar packages (placed in the java CLASSPATH), Python files (placed on the PYTHONPATH), and any other files.

Create the following folder structure in the blob storage referenced by the HDInsight linked service. Then, upload dependent files to the appropriate subfolders in the root folder represented by **entryFilePath**. For example, upload Python files to the pyFiles subfolder and jar files to the jars subfolder of the root folder. At runtime, the Data Factory service expects the following folder structure in the blob storage:

| Sökväg | Beskrivning | Krävs | Typ |
| ---- | ----------- | -------- | ---- |
| . | The root path of the Spark job in the storage linked service. | Ja | Mapp |
| &lt;user defined &gt; | The path that points to the entry file of the Spark job. | Ja | Fil |
| ./jars | All files under this folder are uploaded and placed on the Java classpath of the cluster. | Inga | Mapp |
| ./pyFiles | All files under this folder are uploaded and placed on the PYTHONPATH of the cluster. | Inga | Mapp |
| ./files | All files under this folder are uploaded and placed on the executor working directory. | Inga | Mapp |
| ./archives | All files under this folder are uncompressed. | Inga | Mapp |
| ./logs | The folder where logs from the Spark cluster are stored.| Inga | Mapp |

Here is an example for storage that contains two Spark job files in the blob storage referenced by the HDInsight linked service:

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
