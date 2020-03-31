---
title: Anropa Spark-program från Azure Data Factory
description: Lär dig hur du anropar Spark-program från en Azure-datafabrik med hjälp av MapReduce-aktiviteten.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969232"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Anropa Spark-program från Azure Data Factory-pipelines

> [!div class="op_single_selector" title1="Omvandlingsaktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Grisaktivitet](data-factory-pig-activity.md)
> * [MapReduce aktivitet](data-factory-map-reduce.md)
> * [Hadoop Streaming aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Körning av maskininlärningsbatch](data-factory-azure-ml-batch-execution-activity.md)
> * [Resursaktivitet för uppdatering av maskininlärning](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [U-SQL-aktivitet för DataSjöanalys](data-factory-usql-activity.md)
> * [.NET-anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Omforma data med hjälp av Apache Spark-aktiviteten i Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introduktion
Spark-aktiviteten är en av de [dataomvandlingsaktiviteter](data-factory-data-transformation-activities.md) som stöds av Data Factory. Den här aktiviteten kör det angivna Spark-programmet på Spark-klustret i Azure HDInsight.

> [!IMPORTANT]
> - Spark-aktiviteten stöder inte HDInsight Spark-kluster som använder Azure Data Lake Store som primär lagring.
> - Spark-aktiviteten stöder endast befintliga (dina egna) HDInsight Spark-kluster. Det stöder inte en hdinsight-länkad tjänst på begäran.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Genomgång: Skapa en pipeline med en Spark-aktivitet
Här är de vanligaste stegen för att skapa en pipeline för datafabriker med en Spark-aktivitet:

* Skapa en datafabrik.
* Skapa en Azure Storage-länkad tjänst för att länka ditt lagringsutrymme som är associerat med ditt HDInsight Spark-kluster till datafabriken.
* Skapa en HDInsight-länkad tjänst för att länka Spark-klustret i HDInsight till datafabriken.
* Skapa en datauppsättning som refererar till den lagringslänkade tjänsten. För närvarande måste du ange en utdatauppsättning för en aktivitet även om det inte produceras någon utdata.
* Skapa en pipeline med Spark-aktivitet som refererar till den HDInsight-länkade tjänst som du skapade. Aktiviteten konfigureras med den datauppsättning som du skapade i föregående steg som en utdatauppsättning. Utdatauppsättningen är det som driver schemat (varje timme, dagligen). Därför måste du ange utdatauppsättningen även om aktiviteten inte ger någon utdata.

### <a name="prerequisites"></a>Krav
1. Skapa ett lagringskonto för allmänt bruk genom att följa instruktionerna i [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).

1. Skapa ett Spark-kluster i HDInsight genom att följa instruktionerna i självstudien [Skapa ett Spark-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Associera lagringskontot som du skapade i steg 1 med det här klustret.

1. Ladda ned och granska **test.py** Python-skriptfilen [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py)test.py som finns på .

1. Ladda **upp test.py** till **pyFiles-mappen** i **adfspark-behållaren** i blob-lagringen. Skapa behållaren och mappen om de inte finns.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Gör så här för att skapa en datafabrik:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **Ny** > data +**analysdatafabrik****Data + Analytics** > .

1. Ange **SparkDF**under **Namn**på bladet **Ny datafabrik** .

   > [!IMPORTANT]
   > Namnet på Azure Data Factory måste vara globalt unikt. Om felet "Data fabriksnamn SparkDF inte är tillgängligt" ändrar du namnet på datafabriken. Använd till exempel dittnamnSparkDFdate och skapa datafabriken igen. Mer information om namngivningsregler finns i [Data Factory: namngivningsregler](data-factory-naming-rules.md).

1. Under **Prenumeration** väljer du den Azure-prenumeration där du vill att datafabriken ska skapas.

1. Välj en befintlig resursgrupp eller skapa en Azure-resursgrupp.

1. Markera kryssrutan **Fäst på instrumentpanelen**.

1. Välj **Skapa**.

   > [!IMPORTANT]
   > Om du vill skapa Data Factory-instanser måste du vara medlem i rollen [Data Factory-deltagare](../../role-based-access-control/built-in-roles.md#data-factory-contributor) på prenumerations-/resursgruppsnivå.

1. Du ser datafabriken när den skapas i instrumentpanelen i Azure-portalen.

1. När datafabriken har skapats visas sidan **Datafabrik** med innehållet i datafabriken. Om du inte ser sidan **Datafabrik** väljer du panelen för datafabriken på instrumentpanelen.

    ![Bladet Datafabrik](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget skapar du två länkade tjänster. En tjänst länkar Spark-klustret till din datafabrik och den andra tjänsten länkar lagringen till din datafabrik.

#### <a name="create-a-storage-linked-service"></a>Skapa en länkad lagringstjänst
I det här steget länkar du ditt lagringskonto till datafabriken. En datauppsättning som du skapar i ett steg senare i den här genomgången refererar till den länkade tjänsten. Den HDInsight-länkade tjänst som du definierar i nästa steg refererar också till den länkade tjänsten.

1. På **bladet Datafabrik** väljer du **Författare och distribuerar**. Data Factory Editor visas.

1. Välj **Nytt datalager** och välj **Azure Storage**.

   ![Nytt datalager](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Det JSON-skript som du använder för att skapa en lagringslänkade tjänst visas i redigeraren.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Ersätt **kontonamn** och **kontonyckel** med namn och åtkomstnyckel för ditt lagringskonto. Mer information om hur du hämtar åtkomstnyckeln för lagring finns i [Hantera åtkomstnycklar för lagringskonto](../../storage/common/storage-account-keys-manage.md).

1. Om du vill distribuera den länkade tjänsten väljer du **Distribuera** i kommandofältet. Fönstret Draft-1 stängs när den länkade tjänsten har distribuerats. Du ser **AzureStorageLinkedService** i trädvyn till vänster.

#### <a name="create-an-hdinsight-linked-service"></a>Skapa en länkad HDInsight-tjänst
I det här steget skapar du en HDInsight-länkad tjänst för att länka ditt HDInsight Spark-kluster till datafabriken. HDInsight-klustret används för att köra Spark-programmet som anges i Spark-aktiviteten för pipelinen i det här exemplet.

1. I Data Factory Editor väljer du **Mer** > nytt**beräknings-HDInsight-kluster**.**New compute** > 

    ![Skapa länkad HDInsight-tjänst](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. I JSON-redigeraren gör du följande:

    a. Ange URI för HDInsight Spark-klustret. Till exempel: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Ange namnet på den användare som har åtkomst till Spark-klustret.

    c. Ange lösenordet för användaren.

    d. Ange den lagringslänkade tjänst som är associerad med HDInsight Spark-klustret. I det här exemplet är det AzureStorageLinkedService.

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
    > - Spark-aktiviteten stöder endast befintliga (dina egna) HDInsight Spark-kluster. Det stöder inte en hdinsight-länkad tjänst på begäran.

    Mer information om den HDInsight-länkade tjänsten finns i [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Om du vill distribuera den länkade tjänsten väljer du **Distribuera** i kommandofältet.

### <a name="create-the-output-dataset"></a>Skapa datauppsättningen för utdata
Utdatauppsättningen är det som driver schemat (varje timme, dagligen). Därför måste du ange en utdatauppsättning för Spark-aktiviteten i pipelinen även om aktiviteten inte producerar någon utdata. Det är valfritt att ange en indatauppsättning för aktiviteten.

1. I Data Factory Editor väljer du **Mer** > **ny datauppsättning** > Azure**Blob storage**.

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. JSON-kodavsnittet definierar en datauppsättning som kallas **OutputDataset**. Dessutom anger du att resultaten ska lagras i blob-behållaren **adfspark** och mappen **pyFiles/output**. Som tidigare nämnts är denna datauppsättning en dummy datauppsättning. Spark-programmet i det här exemplet ger inga utdata. Tillgänglighetsavsnittet anger att utdatauppsättningen produceras dagligen. **availability**

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
1. Om du vill distribuera datauppsättningen väljer du **Distribuera** i kommandofältet.


### <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en HDInsightSpark-aktivitet. För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. Därför anges ingen indatauppsättning i det här exemplet.

1. Välj **Fler** > **nya pipeline**i Data Factory Editor .

1. Ersätt skriptet i draft-1-fönstret med följande skript:

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

    a. Egenskapen **Type** är inställd på **HDInsightSpark**.

    b. Egenskapen **rootPath** är inställd på **adfspark\\pyFiles** där adfspark är blob-behållaren och pyFiles är en filmapp i den behållaren. I det här exemplet är blob-lagringen den som är associerad med Spark-klustret. Du kan ladda upp filen till ett annat lagringskonto. Om du gör det skapar du en lagringslänkade tjänst för att länka lagringskontot till datafabriken. Ange sedan namnet på den länkade tjänsten som ett värde för egenskapen **sparkJobLinkedService.** Mer information om den här egenskapen och andra egenskaper som stöds av Spark-aktiviteten finns i [Spark-aktivitetsegenskaper](#spark-activity-properties).

    c. Egenskapen **entryFilePath** är inställd **på test.py**, som är Python-filen.

    d. Egenskapen **getDebugInfo** är inställd **på Alltid**, vilket innebär att loggfilerna alltid genereras (framgång eller misslyckande).

    > [!IMPORTANT]
    > Vi rekommenderar att du inte `Always` ställer in den här egenskapen på i en produktionsmiljö om du inte felsöker ett problem.

    e. **Utdataavsnittet** har en utdatauppsättning. Du måste ange en utdatauppsättning även om Spark-programmet inte ger utdata. Utdatauppsättningen styr schemat för pipelinen (varje timme, dagligen).

    Mer information om egenskaperna som stöds av Spark-aktiviteten finns i avsnittet [Spark-aktivitetsegenskaper](#spark-activity-properties).

1. Om du vill distribuera pipelinen väljer du **Distribuera** i kommandofältet.

### <a name="monitor-a-pipeline"></a>Övervaka en pipeline
1. På **bladet Datafabrik** väljer du **Övervaka & Hantera** för att starta övervakningsprogrammet på en annan flik.

    ![Ikonen Övervaka och hantera](media/data-factory-spark/monitor-and-manage-tile.png)

1. Ändra **starttidsfiltret** högst upp till **2017-02-01**och välj **Använd**.

1. Endast ett aktivitetsfönster visas eftersom det bara finns en dag mellan start (2017-02-01) och sluttider (2017-02-02) av pipelinen. Bekräfta att datasegmentet är i läget **Klar.**

    ![Övervaka pipeline](media/data-factory-spark/monitor-and-manage-app.png)

1. I listan **Aktivitetsfönster** väljer du en aktivitetskörning för att se information om den. Om det finns ett fel visas information om det i den högra rutan.

### <a name="verify-the-results"></a>Verifiera resultaten

1. Starta Jupyter Notebook för din HDInsight Spark kluster genom att gå till [denna webbplats](https://CLUSTERNAME.azurehdinsight.net/jupyter). Du kan också öppna en klusterinstrumentpanel för ditt HDInsight Spark-kluster och sedan starta Jupyter Notebook.

1. Välj **Ny** > **PySpark** om du vill starta en ny anteckningsbok.

    ![Jupyter ny anteckningsbok](media/data-factory-spark/jupyter-new-book.png)

1. Kör följande kommando genom att kopiera och klistra in texten och trycka på Skift+Retur i slutet av den andra satsen:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Bekräfta att du ser data från vvs-tabellen.

    ![Jupyter frågeresultat](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Detaljerade instruktioner finns i avsnittet [Kör en Spark SQL-fråga](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Felsökning
Eftersom du anger getDebugInfo till **Alltid**visas en loggundermapp i pyFiles-mappen i blob-behållaren. Loggfilen i loggmappen innehåller ytterligare information. Den här loggfilen är särskilt användbar när det finns ett fel. I en produktionsmiljö kanske du vill ställa in den på **Fel**.

För ytterligare felsökning:


1. Gå till `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![YARN UI ansökan](media/data-factory-spark/yarnui-application.png)

1. Välj **Loggar** för ett av körningsförsöken.

    ![Sidan Program](media/data-factory-spark/yarn-applications.png)

1. Följande ytterligare felinformation visas på loggsidan:

    ![Loggfel](media/data-factory-spark/yarnui-application-error.png)

Följande avsnitt innehåller information om datafabrikentiteterna för att använda Spark-kluster och Spark-aktivitet i datafabriken.

## <a name="spark-activity-properties"></a>Egenskaper för sparkaktivitet
Här är exempel JSON-definitionen av en pipeline med en Spark-aktivitet:

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

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen.

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| namn | Namnet på aktiviteten på pipelinen. | Ja |
| description | Text som beskriver vad aktiviteten gör. | Inga |
| typ | Den här egenskapen måste vara inställd på HDInsightSpark. | Ja |
| linkedServiceName | Namn på den HDInsight-länkade tjänst som Spark-programmet körs på. | Ja |
| rootPath (rotPath) | Blob-behållaren och mappen som innehåller Spark-filen. Filnamnet är skiftlägeskänsligt. | Ja |
| entryFilePath | Relativ sökväg till rotmappen för Spark-koden/-paketet. | Ja |
| Classname | Programmets huvudklass Java/Spark. | Inga |
| Argument | En lista med kommandoradsargument till Spark-programmet. | Inga |
| proxyAnvändare | Användarkontot som personifieras för att köra Spark-programmet. | Inga |
| sparkConfig (på andra sätt) | Ange värden för de spark-konfigurationsegenskaper som anges i [Spark-konfiguration: Programegenskaper](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Inga |
| getDebugInfo | Anger när Spark-loggfilerna kopieras till det lagringsutrymme som används av HDInsight-klustret (eller) som anges av sparkJobLinkedService. Tillåtna värden är Ingen, Alltid eller Fel. Standardvärdet är Ingen. | Inga |
| sparkJobLinkedService | Den lagringslänkade tjänsten som innehåller Spark-jobbfilen, beroenden och loggar. Om du inte anger något värde för den här egenskapen används lagringen som är associerad med HDInsight-klustret. | Inga |

## <a name="folder-structure"></a>Mappstrukturen
Spark-aktiviteten stöder inte ett infogat skript som Pig- och Hive-aktiviteter gör. Spark jobb är också mer utökningsbara än Pig / Hive jobb. För Spark-jobb kan du ange flera beroenden, till exempel jar-paket (placerade i java CLASSPATH), Python-filer (placerade på PYTHONPATH) och andra filer.

Skapa följande mappstruktur i blob-lagringen som refereras av den HDInsight-länkade tjänsten. Ladda sedan upp beroende filer till lämpliga undermappar i rotmappen som representeras av **entryFilePath**. Ladda till exempel upp Python-filer till undermappen pyFiles och jar-filerna till undermappen burkar i rotmappen. Vid körning förväntar sig tjänsten Data Factory följande mappstruktur i blob-lagringen:

| Sökväg | Beskrivning | Krävs | Typ |
| ---- | ----------- | -------- | ---- |
| . | Rotsökvägen för Spark-jobbet i den lagringslänkade tjänsten. | Ja | Mapp |
| &lt;användardefinierad&gt; | Sökvägen som pekar på inmatningsfilen för Spark-jobbet. | Ja | Fil |
| ./burkar | Alla filer under den här mappen laddas upp och placeras på Java-klassökvägen i klustret. | Inga | Mapp |
| ./pyFiles | Alla filer under den här mappen överförs och placeras på PYTHONPATH i klustret. | Inga | Mapp |
| ./filer | Alla filer under den här mappen laddas upp och placeras på den verkställande arbetskatalogen. | Inga | Mapp |
| ./arkiv | Alla filer under den här mappen är okomprimerade. | Inga | Mapp |
| ./loggar | Mappen där loggar från Spark-klustret lagras.| Inga | Mapp |

Här är ett exempel för lagring som innehåller två Spark-jobbfiler i blob-lagringen som refereras av den HDInsight-länkade tjänsten:

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
