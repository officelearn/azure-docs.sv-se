---
title: SQLServer-lagrad Proceduraktivitet
description: Lär dig hur du kan använda lagrade Proceduraktiviteten för SQL Server för att anropa en lagrad procedur i en Azure SQL Database eller Azure SQL Data Warehouse från Data Factory-pipeline.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 77842b60108629168f423f25eb03b01079cf55e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61256061"
---
# <a name="sql-server-stored-procedure-activity"></a>SQLServer-lagrad Proceduraktivitet
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Piggningsåtgärd](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET-anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Transformera data med aktivitet för lagrad procedur i Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Översikt
Du använder datatransformeringsaktiviteter i en Data Factory [pipeline](data-factory-create-pipelines.md) att transformera och bearbeta rådata till förutsägelser och insikter. Den lagrade Proceduraktiviteten är en av transformeringsaktiviteter som har stöd för Data Factory. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter i Data Factory.

Du kan använda den lagrade Proceduraktiviteten för att anropa en lagrad procedur i någon av följande datalager i ditt företag eller på en Azure-dator (VM):

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server-databas. Om du använder SQL Server kan du installera Data Management Gateway på samma dator som är värd för databasen eller på en separat dator som har åtkomst till databasen. Data Management Gateway är en komponent som ansluter data datakällor på plats/på virtuella Azure-datorer med molntjänster i ett säkert och hanterat sätt. Se [Data Management Gateway](data-factory-data-management-gateway.md) nedan för information.

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server, kan du konfigurera den **SqlSink** i kopieringsaktiviteten att anropa en lagrad procedur med hjälp av den **sqlWriterStoredProcedureName** egenskapen. Mer information finns i [anropa lagrade procedur från kopieringsaktiviteten](data-factory-invoke-stored-procedure-from-copy-activity.md). Information om egenskapen hittar du i följande artiklar för anslutningen: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQLServer](data-factory-sqlserver-connector.md#copy-activity-properties). Anropa en lagrad procedur när du kopierar data till en Azure SQL Data Warehouse med hjälp av en Kopieringsaktivitet stöds inte. Men du kan använda aktiviteten lagrad procedur för att anropa en lagrad procedur i ett SQL Data Warehouse.
>
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure SQL Data Warehouse, kan du konfigurera **SqlSource** i kopieringsaktiviteten att anropa en lagrad procedur för att läsa data från källdatabasen med hjälp av den  **sqlReaderStoredProcedureName** egenskapen. Mer information finns i följande artiklar för anslutningen: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQLServer](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

Den här genomgången använder den lagrade Proceduraktiviteten i en pipeline för att anropa en lagrad procedur i en Azure SQL database.

## <a name="walkthrough"></a>Genomgång
### <a name="sample-table-and-stored-procedure"></a>Exempeltabell och lagrad procedur
1. Skapa följande **tabell** i Azure SQL Database med hjälp av SQL Server Management Studio eller något annat verktyg som du är nöjd med. Kolumnen datumtidsstämpel är datum och tid när motsvarande ID genereras.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    ID: t är det unika identifieras och kolumnen datumtidsstämpel är datum och tid när motsvarande ID genereras.
    
    ![Exempeldata](./media/data-factory-stored-proc-activity/sample-data.png)

    I det här exemplet är den lagrade proceduren i en Azure SQL Database. Om den lagrade proceduren finns i en Azure SQL Data Warehouse och SQL Server-databas, liknar metoden. För en SQL Server-databas, måste du installera en [Data Management Gateway](data-factory-data-management-gateway.md).
2. Skapa följande **lagrade proceduren** som infogar data i den **sampletable**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Namnet** och **gemener och versaler** för parametern (DateTime i det här exemplet) måste matcha parameter som anges i pipeline/JSON-aktiviteten. Se till att i definitionen av lagrade proceduren **\@** används som ett prefix för parametern.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **NEW** på den vänstra menyn klickar du på **information + analys**, och klicka på **Data Factory**.

    ![Ny datafabrik](media/data-factory-stored-proc-activity/new-data-factory.png)
3. I den **ny datafabrik** bladet ange **SProcDF** för namnet. Azure Data Factory-namn är **globalt unikt**. Du behöver som prefix i namnet på datafabriken med ditt namn, att det har skapandet på fabriken.

   ![Ny datafabrik](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Välj din **Azure-prenumeration**.
5. Gör något av följande för **Resursgrupp**:
   1. Klicka på **Skapa nytt** och ange ett namn för resursgruppen.
   2. Klicka på **Använd befintlig** och välj en befintlig resursgrupp.
6. Välj **plats** för datafabriken.
7. Välj **fäst på instrumentpanelen** så att du kan se data factory på instrumentpanelen nästa gång du loggar in.
8. Klicka på **Skapa** på bladet **Ny datafabrik**.
9. Du ser att datafabriken skapas på den **instrumentpanelen** i Azure Portal. När datafabriken har skapats visas datafabrikssidan med innehållet i datafabriken.

   ![Datafabrikens startsida](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Skapa en länkad Azure SQL-tjänst
Efter att data factory kan du skapa en Azure SQL länkade tjänst som länkar din Azure SQL-databas, som innehåller sampletable tabellen och usp_sample lagrade proceduren till din datafabrik.

1. Klicka på **författare och distribuera** på den **Data Factory** bladet för **SProcDF** att starta Data Factory-redigeraren.
2. Klicka på **Nytt datalager** på kommandoraden och välj **Azure SQL Database**. Du bör se JSON-skriptet för att skapa en länkad Azure SQL-tjänst i redigeraren.

   ![Nytt datalager](media/data-factory-stored-proc-activity/new-data-store.png)
3. Gör följande ändringar i JSON-skriptet:

   1. Ersätt `<servername>` med namnet på din Azure SQL Database-server.
   2. Ersätt `<databasename>` med den databas som du skapade tabellen och den lagrade proceduren.
   3. Ersätt `<username@servername>` med det användarkonto som har åtkomst till databasen.
   4. Ersätt `<password>` med lösenordet för användarkontot.

      ![Nytt datalager](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Om du vill distribuera den länkade tjänsten, klickar du på **distribuera** i kommandofältet. Kontrollera att du ser AzureSqlLinkedService i trädvyn till vänster.

    ![trädvy med länkade tjänsten](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
Du måste ange en utdatauppsättning för en lagrad procedur-aktivitet, även om den lagrade proceduren inte producerar några data. Det beror på att det är den utdatauppsättningen som styr schemat för aktiviteten (hur ofta aktiviteten körs - per timme, varje dag osv.). Datauppsättningen för utdata måste använda en **länkad tjänst** som refererar till en Azure SQL Database eller en Azure SQL Data Warehouse eller en SQL Server-databas som du vill att den lagrade proceduren för att köra. Datauppsättningen för utdata kan fungera som ett sätt att skicka resultatet av den lagrade proceduren för efterföljande bearbetning av en annan aktivitet ([länkning av aktiviteter](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) i pipelinen. Data Factory skriva inte dock automatiskt utdata från en lagrad procedur till den här datauppsättningen. Det är den lagrade proceduren som skriver till en SQLtabell som utdata-datauppsättningen pekar på. I vissa fall kan datauppsättningen för utdata kan vara en **dummy datauppsättning** (en datauppsättning som pekar på en tabell som inte riktigt innehåller utdata från den lagrade proceduren). Den här dummy datauppsättningen används bara för att ange schemat för att köra aktiviteten lagrad procedur.

1. Klicka på **... Mer** i verktygsfältet klickar du på **ny datauppsättning**, och klicka på **Azure SQL**. **Ny datauppsättning** i kommandofältet och väljer **Azure SQL**.

    ![trädvy med länkade tjänsten](media/data-factory-stored-proc-activity/new-dataset.png)
2. Kopiera och klistra in följande JSON-skript i till JSON-redigerare.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Om du vill distribuera datauppsättningen, klickar du på **distribuera** i kommandofältet. Kontrollera att du ser datauppsättningen i trädvyn.

    ![trädvy med länkade tjänster](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Skapa en pipeline med SqlServerStoredProcedure aktivitet
Nu ska vi skapa en pipeline med en aktivitet för lagrad procedur.

Lägg märke till följande egenskaper:

- Den **typ** är inställd på **SqlServerStoredProcedure**.
- Den **storedProcedureName** i typen egenskaper anges till **usp_sample** (namn på den lagrade proceduren).
- Den **storedProcedureParameters** avsnittet innehåller en parameter med namnet **DateTime**. Namn och versaler och gemener i parametern i JSON måste matcha namnet och versaler och gemener i parametern i definitionen för lagrad procedur. Om du behöver skicka null för en parameter, använder du syntax: `"param1": null` (endast gemener).

1. Klicka på **... Mer** till kommandofältet och klicka på **ny pipeline**.
2. Kopiera och klistra in följande JSON-kodfragment:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Om du vill distribuera pipelinen klickar du på **distribuera** i verktygsfältet.

### <a name="monitor-the-pipeline"></a>Övervaka pipeline
1. Klicka på **X** för att stänga bladen i Data Factory-redigeraren och för att gå tillbaka till Data Factory-bladet och klicka på **Diagram**.

    ![Ikonen diagram](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. I **diagramvyn** visas en översikt över pipelines och datauppsättningar som används i den här självstudien.

    ![Ikonen diagram](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. I diagramvyn dubbelklickar du på datauppsättningen `sprocsampleout`. Du se sektorer statusen klar. Det bör finnas fem sektorer eftersom en sektor produceras för varje timme mellan starttid och sluttid från JSON-filen.

    ![Ikonen diagram](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. När sektorn har **redo** tillstånd, kör en `select * from sampletable` fråga mot Azure SQL-databasen för att verifiera att data har infogas i tabellen av den lagrade proceduren.

   ![Utdata](./media/data-factory-stored-proc-activity/output.png)

   Se [övervaka pipelinen](data-factory-monitor-manage-pipelines.md) detaljerad information om hur du övervakar Azure Data Factory-pipelines.

## <a name="specify-an-input-dataset"></a>Ange en indatauppsättning
I den här genomgången har lagrad proceduraktivitet inte någon indatauppsättningar. Om du anger en indatauppsättning körs inte aktiviteten lagrad procedur tills sektor av indatauppsättningen är tillgänglig (statusen Ready). Datauppsättningen kan vara en extern datauppsättning (som inte tillverkas av en annan aktivitet i samma pipelinen) eller en intern datauppsättningen som produceras av en överordnad aktivitet (den aktivitet som körs före den här aktiviteten). Du kan ange flera indatauppsättningar för aktiviteten lagrad procedur. Om du gör det, körs lagrad procedur-aktivitet bara när alla sektorer för datauppsättningen för indata är tillgängliga (statusen Ready). Datauppsättningen för indata kan inte användas i den lagrade proceduren som en parameter. Det är bara används för att kontrollera beroendet innan du startar aktivitet för lagrad procedur.

## <a name="chaining-with-other-activities"></a>Länkning med andra aktiviteter
Om du vill länka en överordnad aktivitet med den här aktiviteten anger du utdata från den överordnade aktiviteten som indata för den här aktiviteten. När du gör det körs inte aktiviteten lagrad procedur förrän den överordnade aktiviteten har slutförts och datamängd för utdata för den överordnade aktiviteten är tillgänglig (i status redo). Du kan ange utdata datauppsättningar på flera överordnade aktiviteter som indatauppsättningar för aktiviteten lagrad procedur. När du gör det, körs lagrad proceduraktivitet bara när alla sektorer för datauppsättningen för indata är tillgängliga.

I exemplet nedan är utdata för kopieringsaktiviteten: OutputDataset utgör indata för aktiviteten lagrad procedur. Därför körs inte aktiviteten lagrad procedur tills kopieringen har slutförts och OutputDataset sektorn är tillgänglig (statusen Ready). Om du anger flera indatauppsättningar körs inte aktiviteten lagrad procedur förrän alla sektorer för datauppsättningen för indata är tillgängliga (statusen Ready). Datauppsättningarna som indata kan inte användas direkt som parametrar för lagrad procedur-aktivitet.

Mer information om länkning av aktiviteter finns i [flera aktiviteter i en pipeline](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

På samma sätt att länka store proceduraktivitet med **nedströms aktiviteter** (de aktiviteter som körs när den lagrade proceduraktiviteten har slutförts), ange datauppsättningen för utdata för aktiviteten lagrad procedur som indata för den underordnad aktivitet i pipelinen.

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server, kan du konfigurera den **SqlSink** i kopieringsaktiviteten att anropa en lagrad procedur med hjälp av den **sqlWriterStoredProcedureName** egenskapen. Mer information finns i [anropa lagrade procedur från kopieringsaktiviteten](data-factory-invoke-stored-procedure-from-copy-activity.md). Mer information om egenskapen finns i följande artiklar för anslutningen: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQLServer](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure SQL Data Warehouse, kan du konfigurera **SqlSource** i kopieringsaktiviteten att anropa en lagrad procedur för att läsa data från källdatabasen med hjälp av den  **sqlReaderStoredProcedureName** egenskapen. Mer information finns i följande artiklar för anslutningen: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQLServer](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>JSON-format
Här är JSON-format för att definiera en lagrade Proceduraktiviteten:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

I följande tabell beskrivs de här JSON-egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| namn | Namn på aktiviteten |Ja |
| description |Text som beskriver vad aktiviteten används till |Nej |
| typ | Måste anges till: **SqlServerStoredProcedure** | Ja |
| inmatningar | Valfri. Om du anger en indatauppsättning, måste den vara tillgänglig (statusen ”klar”) för aktiviteten lagrad procedur att köra. Datauppsättningen för indata kan inte användas i den lagrade proceduren som en parameter. Det är bara används för att kontrollera beroendet innan du startar aktivitet för lagrad procedur. |Nej |
| utdata | Du måste ange en utdatauppsättning för en lagrad procedur-aktivitet. Datauppsättningen för utdata anger den **schema** för aktiviteten lagrad procedur (varje timme, varje vecka, månadsvis, osv.). <br/><br/>Datauppsättningen för utdata måste använda en **länkad tjänst** som refererar till en Azure SQL Database eller en Azure SQL Data Warehouse eller en SQL Server-databas som du vill att den lagrade proceduren för att köra. <br/><br/>Datauppsättningen för utdata kan fungera som ett sätt att skicka resultatet av den lagrade proceduren för efterföljande bearbetning av en annan aktivitet ([länkning av aktiviteter](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) i pipelinen. Data Factory skriva inte dock automatiskt utdata från en lagrad procedur till den här datauppsättningen. Det är den lagrade proceduren som skriver till en SQLtabell som utdata-datauppsättningen pekar på. <br/><br/>I vissa fall kan datauppsättningen för utdata kan vara en **dummy datauppsättning**, vilket används endast för att ange schemat för att köra aktiviteten lagrad procedur. |Ja |
| storedProcedureName |Ange namnet på den lagrade proceduren i Azure SQL database eller Azure SQL Data Warehouse eller SQL Server-databasen som representeras av den länkade tjänst som använder utdatatabellen. |Ja |
| storedProcedureParameters |Ange värden för parametrarna för lagrad procedur. Om du behöver skicka null för en parameter, använder du syntax: ”param1”: null (gemener). Se följande exempel för att lära dig om hur du använder den här egenskapen. |Nej |

## <a name="passing-a-static-value"></a>Skicka ett statiskt värde
Nu kan anta att du lägger till en annan kolumn med namnet ”scenariot' i tabellen som innehåller ett statiskt värde med namnet” dokumentera sample ”.

![Exempeldata 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabell:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Lagrad procedur:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Nu kan skicka den **scenariot** parametern och värdet från aktiviteten lagrad procedur. Den **typeProperties** avsnitt i det föregående exemplet ser ut som följande kodavsnitt:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory-datauppsättningen:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Data Factory-pipeline**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
