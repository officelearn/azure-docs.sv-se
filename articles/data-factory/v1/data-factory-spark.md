---
title: Anropa Spark-program från Azure Data Factory | Microsoft Docs
description: Lär dig mer om att anropa Spark-program från en Azure-datafabrik med hjälp av MapReduce-aktivitet.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 95c49eec6964984894f75ecd0a9e50c9c947683b
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015822"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Anropa Spark-program från Azure Data Factory-pipelines

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Piggningsåtgärd](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop Streaming activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning-batchkörningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-resursuppdatering aktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET-anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig. Om du använder den aktuella versionen av Data Factory-tjänsten, se [omvandla data med Apache Spark-aktiviteten i Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introduktion
Spark-aktivitet är en av de [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) stöds av Data Factory. Den här aktiviteten körs det angivna Spark-programmet på ditt Spark-kluster i Azure HDInsight. 

> [!IMPORTANT]
> - Spark-aktiviteten stöder inte HDInsight Spark-kluster som använder Azure Data Lake Store som primär lagring.
> - Spark-aktiviteten stöder endast befintliga (egna) HDInsight Spark-kluster. Det stöder inte en på begäran länkad HDInsight-tjänst.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Steg-för-steg-beskrivning: Skapa en pipeline med en Spark-aktivitet
Här följer vanliga stegen för att skapa en data factory-pipeline med en Spark-aktivitet: 

* Skapa en datafabrik.
* Skapa en länkad Azure Storage-tjänst för att länka ditt lagringsutrymme som är associerad med ditt HDInsight Spark-kluster till data factory.
* Skapa en länkad HDInsight-tjänst för att länka ditt Spark-kluster i HDInsight till data factory.
* Skapa en datauppsättning som refererar till den länkade Storage-tjänsten. För närvarande måste du ange en utdatauppsättning för en aktivitet, även om det finns inga utdata som genereras. 
* Skapa en pipeline med Spark-aktivitet som refererar till den länkade HDInsight-tjänst som du skapade. Aktiviteten har konfigurerats med den datauppsättning som du skapade i föregående steg som utdatauppsättning. Utdatauppsättningen är det utdatauppsättningen som skapar schemat (varje timme, varje dag). Därför måste du ange datauppsättningen för utdata, även om aktiviteten inte verkligen producera utdata.

### <a name="prerequisites"></a>Förutsättningar
1. Skapa ett allmänt lagringskonto genom att följa instruktionerna i [skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md).

1. Skapa ett Spark-kluster i HDInsight genom att följa anvisningarna i självstudien [skapa ett Spark-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Koppla lagringskonto som du skapade i steg 1 med det här klustret.

1. Ladda ned och granska Python-skriptfil **test.py** på [ https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py ](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

1. Ladda upp **test.py** till den **pyFiles** mapp i den **adfspark** behållare i blob storage. Skapa behållaren och mappen om de inte finns.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Gör så här för att skapa en datafabrik:

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **Nytt** > **Data och analys** > **Data Factory**.

1. På den **ny datafabrik** bladet under **namn**, ange **SparkDF**.

   > [!IMPORTANT]
   > Namnet på Azure Data Factory måste vara globalt unikt. Om du ser felet ”datafabriksnamnet SparkDF är inte tillgänglig” kan du ändra namnet på datafabriken. Till exempel använda yournameSparkDFdate och skapa datafabriken igen. Mer information om namngivningsregler finns [Data Factory: Namngivningsregler](data-factory-naming-rules.md).

1. Under **Prenumeration** väljer du den Azure-prenumeration där du vill att datafabriken ska skapas.

1. Välj en befintlig resursgrupp eller skapa en Azure-resursgrupp.

1. Markera kryssrutan **Fäst på instrumentpanelen**.

1. Välj **Skapa**.

   > [!IMPORTANT]
   > Om du vill skapa Data Factory-instanser måste du vara medlem i [rollen Deltagare för Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) på prenumerations-/resursgruppnivå.

1. Du ser att datafabriken när den skapas på instrumentpanelen i Azure-portalen.

1. När datafabriken har skapats visas sidan **Datafabrik** med innehållet i datafabriken. Om du inte ser den **datafabrik** väljer ikonen för din datafabrik på instrumentpanelen.

    ![Bladet Datafabrik](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget skapar du två länkade tjänster. En tjänst länkar ditt Spark-kluster till din data factory och den andra tjänsten länkar ditt lagringsutrymme till din datafabrik. 

#### <a name="create-a-storage-linked-service"></a>Skapa en länkad lagringstjänst
I det här steget länkar du ditt lagringskonto till datafabriken. En datauppsättning som du skapar i ett steg längre fram i den här genomgången refererar till den här länkade tjänsten. Länkad HDInsight-tjänst som du definierar i nästa steg refererar till den här länkade tjänsten för. 

1. På den **datafabrik** bladet väljer **författare och distribuera**. Data Factory-redigeraren visas.

1. Välj **Nytt datalager** och välj **Azure Storage**.

   ![Nytt datalager](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. JSON-skriptet som du använder för att skapa en lagrings-länkade tjänsten visas i redigeraren.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Ersätt **kontonamn** och **kontonyckel** med namn och åtkomstnyckel för ditt lagringskonto. Information om hur du hämtar lagringsåtkomstnyckeln finns i avsnitten om hur du visar, kopierar och återskapar lagringsåtkomstnycklar i [Manage your storage account](../../storage/common/storage-account-manage.md#access-keys) (Hantera ditt lagringskonto).

1. Om du vill distribuera den länkade tjänsten, Välj **distribuera** i kommandofältet. Fönstret Draft-1 stängs när den länkade tjänsten har distribuerats. Du ser **AzureStorageLinkedService** i trädvyn till vänster.

#### <a name="create-an-hdinsight-linked-service"></a>Skapa en länkad HDInsight-tjänst
I det här steget skapar du en länkad HDInsight-tjänst för att länka ditt HDInsight Spark-kluster till data factory. HDInsight-kluster används till att köra Spark-programmet som anges i Spark-aktiviteten i pipelinen i det här exemplet. 

1. I Data Factory Editor väljer **mer** > **ny beräkning** > **HDInsight-kluster**.

    ![Skapa länkad HDInsight-tjänst](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. I JSON-redigerare gör du följande:

    a. Ange URI för HDInsight Spark-klustret. Till exempel: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Ange namnet på den användare som har åtkomst till Spark-klustret.

    c. Ange lösenordet för användaren.

    d. Ange länkad Storage-tjänst som är associerad med HDInsight Spark-klustret. I det här exemplet är det AzureStorageLinkedService.

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
    > - Spark-aktiviteten stöder endast befintliga (egna) HDInsight Spark-kluster. Det stöder inte en på begäran länkad HDInsight-tjänst.

    Läs mer om tjänsten HDInsight länkad [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Om du vill distribuera den länkade tjänsten, Välj **distribuera** i kommandofältet. 

### <a name="create-the-output-dataset"></a>Skapa datauppsättningen för utdata
Utdatauppsättningen är det utdatauppsättningen som skapar schemat (varje timme, varje dag). Därför måste du ange en utdatauppsättning för Spark-aktiviteten i pipelinen även om aktiviteten inte genererar några utdata. Det är valfritt att ange en indatauppsättning för aktiviteten.

1. Välj **Mer** > **Ny datauppsättning** > **Azure Blob Storage** i Data Factory Editor.

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. JSON-kodfragmentet definierar en datauppsättning med namnet **OutputDataset**. Dessutom kan du ange att resultaten lagras i blobbehållaren **adfspark** och i mappen **pyFiles/output**. Som tidigare nämnts är är den här datauppsättningen en dummy datauppsättning. Spark-programmet i det här exemplet genererar inte några utdata. Den **tillgänglighet** avsnittet anges att utdatauppsättningen skapas varje dag. 

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
1. Om du vill distribuera datauppsättningen, Välj **distribuera** i kommandofältet.


### <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en HDInsightSpark aktivitet. För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. Därför anges ingen indatauppsättning i det här exemplet.

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

    a. Den **typ** är inställd på **HDInsightSpark**.

    b. Den **rootPath** är inställd på **adfspark\\pyFiles** där adfspark är blobbehållaren och pyFiles är filmapp i den behållaren. I det här exemplet är det som är associerad med Spark-kluster i blob-lagringen. Du kan överföra filen till ett annat lagringskonto. Om du gör det måste du skapa en länkad lagringstjänst för att länka det storage-kontot till datafabriken. Ange namnet på den länkade tjänsten som värde för den **sparkJobLinkedService** egenskapen. Mer information om den här egenskapen och andra egenskaper som stöds av Spark-aktiviteten finns i [Spark Aktivitetsegenskaper](#spark-activity-properties).

    c. Den **entryFilePath** är inställd på **test.py**, vilket är Python-filen.

    d. Den **getDebugInfo** är inställd på **alltid**, vilket innebär att loggfilerna är alltid genereras (lyckade eller misslyckade).

    > [!IMPORTANT]
    > Vi rekommenderar att du inte anger den här egenskapen `Always` i en produktionsmiljö såvida inte du felsöker ett problem.

    e. Den **matar ut** -avsnittet innehåller en datamängd för utdata. Du måste ange en utdatauppsättning även om Spark-programmet inte genererar några utdata. Utdatauppsättningen styr schemat för pipelinen (per timme, varje dag). 

    Mer information om egenskaper som stöds av Spark-aktiviteten finns i avsnittet [Spark Aktivitetsegenskaper](#spark-activity-properties).

1. För att distribuera pipelinen, Välj **distribuera** i kommandofältet.

### <a name="monitor-a-pipeline"></a>Övervaka en pipeline
1. På den **datafabrik** bladet väljer **övervaka och hantera** att starta övervakning programmet på en annan flik.

    ![Ikonen Övervaka och hantera](media/data-factory-spark/monitor-and-manage-tile.png)

1. Ändra den **starttid** filter längst upp till **2/1/2017**, och välj **tillämpa**.

1. Bara en aktivitet visas eftersom det är bara en gång mellan start (2017-02-01)- och sluttid (2017-02-02) i pipelinen. Kontrollera att datasektorn är i den **redo** tillstånd.

    ![Övervaka pipeline](media/data-factory-spark/monitor-and-manage-app.png)

1. I den **aktivitetsfönster** väljer du en aktivitet om du vill visa information om den. Om det finns ett fel, kan du se information om den i den högra rutan.

### <a name="verify-the-results"></a>Kontrollera resultatet

1. Starta en Jupyter-anteckningsboken för ditt HDInsight Spark-kluster genom att gå till [den här webbplatsen](https://CLUSTERNAME.azurehdinsight.net/jupyter). Du kan även öppna en klusterinstrumentpanel för ditt HDInsight Spark-kluster och starta sedan Jupyter-anteckningsboken.

1. Välj **New** > **PySpark** att starta en ny anteckningsbok.

    ![Ny Jupyter-anteckningsbok](media/data-factory-spark/jupyter-new-book.png)

1. Kör följande kommando genom att kopiera och klistra in texten och trycka på SKIFT + RETUR i slutet av den andra instruktionen:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Kontrollera att du ser data från hvac-tabell. 

    ![Jupyter-frågeresultat](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article --> Detaljerade anvisningar finns i avsnittet [kör en Spark SQL-fråga](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Felsökning
Eftersom du inställningen getDebugInfo **alltid**, visas en logg undermapp i mappen pyFiles i din blobbehållare. Loggfilen i loggmappen innehåller ytterligare information. Den här loggfilen är särskilt användbart när det finns ett fel. I en produktionsmiljö kan du ange den till **fel**.

Mer felsökningsinformation, gör du följande:


1. Gå till `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Användargränssnittet för YARN](media/data-factory-spark/yarnui-application.png)

1. Välj **loggar** inloggningen är för en av körningen.

    ![Programsidan](media/data-factory-spark/yarn-applications.png)

1. Du ser följande ytterligare information på sidan log:

    ![Logga fel](media/data-factory-spark/yarnui-application-error.png)

Följande avsnitt innehåller information om data factory-entiteter du använder Spark-kluster och Spark-aktivitet i din datafabrik.

## <a name="spark-activity-properties"></a>Egenskaper för Spark-aktivitet
Här är exempel JSON-definition för en pipeline med en Spark-aktivitet: 

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

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen.

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| namn | Namnet på aktiviteten i pipelinen. | Ja |
| beskrivning | Text som beskriver hur aktiviteten ska hantera. | Nej |
| typ | Den här egenskapen måste anges till HDInsightSpark. | Ja |
| linkedServiceName | Namnet på den länkade HDInsight-tjänst som Spark-programmet körs. | Ja |
| rootPath | Blobbehållaren och mappen som innehåller filen Spark. Filnamnet är skiftlägeskänsligt. | Ja |
| entryfilepath = | Relativa sökvägen till rotmappen för koden/paketet Spark. | Ja |
| Klassnamn | Programmets Java/Spark-huvudklass. | Nej |
| argument | En lista med kommandoradsargument till Spark-programmet. | Nej |
| proxyUser | Användarkontot att personifiera för att köra Spark-programmet. | Nej |
| sparkConfig | Ange värden för Spark-konfigurationsegenskaper som anges i [Spark-konfiguration: Programegenskaper](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nej |
| getDebugInfo | Anger när Spark filerna kopieras till den lagring som används av HDInsight-kluster (eller) anges av sparkJobLinkedService. Tillåtna värden är None, alltid eller misslyckats. Standardvärdet är ingen. | Nej |
| sparkJobLinkedService | Den länkade lagringstjänsten som innehåller Spark jobbfilen, beroenden och loggar. Om du inte anger ett värde för den här egenskapen används den lagring som är associerad med HDInsight-kluster. | Nej |

## <a name="folder-structure"></a>mappstruktur
Spark-aktiviteten har inte stöd för ett infogat skript som Pig och Hive aktiviteter gör. Spark-jobb är också mer omfattande än Pig/Hive-jobb. För Spark-jobb kan du ange flera beroenden som jar-paket (placeras i java KLASSÖKVÄGEN), Python-filer (placeras på PYTHONPATH) och andra filer.

Skapa följande mappstrukturen i blob-lagringen som refereras av den länkade HDInsight-tjänsten. Ladda sedan upp beroende filer till lämpliga undermappar i rotmappen som representeras av **entryFilePath**. Till exempel ladda upp Python-filer till undermappen pyFiles och jar-filer till undermappen JAR-filer i rotmappen. Vid körning förväntas i Data Factory-tjänsten följande mappstrukturen i blob storage: 

| Sökväg | Beskrivning | Krävs | Typ |
| ---- | ----------- | -------- | ---- |
| . | Rotsökvägen för Spark-jobb i länkade storage-tjänsten. | Ja | Mapp |
| &lt;användardefinierad &gt; | Den sökväg som pekar på Spark-jobbet startfil. | Ja | Fil |
| . / JAR: er | Alla filer under den här mappen överförs och placeras på Java-klassökvägen för klustret. | Nej | Mapp |
| . / pyFiles | Alla filer under den här mappen överförs och placeras på PYTHONPATH i klustret. | Nej | Mapp |
| . / filer | Alla filer under den här mappen överförs och placeras i arbetskatalogen executor. | Nej | Mapp |
| . / arkiverar | Alla filer under den här mappen är okomprimerade. | Nej | Mapp |
| . / loggar | Den mapp där loggar från Spark-klustret lagras.| Nej | Mapp |

Här är ett exempel för lagring som innehåller två Spark-jobbfiler i blob-lagringen som refereras av den länkade HDInsight-tjänsten:

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
