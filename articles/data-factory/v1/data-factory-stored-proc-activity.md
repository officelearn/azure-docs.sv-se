---
title: Sql Server-lagrad proceduraktivitet
description: Lär dig hur du kan använda SQL Server Stored Procedure Activity för att anropa en lagrad procedur i en Azure SQL-databas eller Azure SQL Data Warehouse från en Data Factory-pipeline.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 45aa49de51f42b26c653b15e79c865e3f5647c39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931634"
---
# <a name="sql-server-stored-procedure-activity"></a>Sql Server-lagrad proceduraktivitet
> [!div class="op_single_selector" title1="Omvandlingsaktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Grisaktivitet](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop streaming aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [transformera data med hjälp av lagrad proceduraktivitet i Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Översikt
Du använder dataomvandlingsaktiviteter i en Data [Factory-pipeline](data-factory-create-pipelines.md) för att omvandla och bearbeta rådata till förutsägelser och insikter. Den lagrade proceduraktiviteten är en av de omvandlingsaktiviteter som Data Factory stöder. Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](data-factory-data-transformation-activities.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds i Data Factory.

Du kan använda aktiviteten Lagrad procedur för att anropa en lagrad procedur i något av följande datalager i företaget eller på en virtuell Azure-dator (VM):

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server-databas. Om du använder SQL Server installerar du Data Management Gateway på samma dator som är värd för databasen eller på en separat dator som har åtkomst till databasen. Data Management Gateway är en komponent som ansluter datakällor lokalt/på Azure VM med molntjänster på ett säkert och hanterat sätt. Mer information finns i artikeln [Data Management Gateway.](data-factory-data-management-gateway.md)

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server kan du konfigurera **SqlSink** i kopieringsaktivitet för att anropa en lagrad procedur med hjälp av egenskapen **sqlWriterStoredProcedureName.** Mer information finns i [Anropa lagrad procedur från kopieringsaktivitet](data-factory-invoke-stored-procedure-from-copy-activity.md). Mer information om egenskapen finns i följande anslutningsartiklar: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties). Det går inte att anropa en lagrad procedur när du kopierar data till ett Azure SQL Data Warehouse med hjälp av en kopieringsaktivitet. Men du kan använda den lagrade proceduraktiviteten för att anropa en lagrad procedur i ett SQL Data Warehouse.
>
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure SQL Data Warehouse kan du konfigurera **SqlSource** i kopieringsaktivitet för att anropa en lagrad procedur för att läsa data från källdatabasen med hjälp av egenskapen **sqlReaderStoredProcedureName.** Mer information finns i följande anslutningsartiklar: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties), Azure SQL [Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

I följande genomgång används den lagrade proceduraktiviteten i en pipeline för att anropa en lagrad procedur i en Azure SQL-databas.

## <a name="walkthrough"></a>Genomgång
### <a name="sample-table-and-stored-procedure"></a>Exempeltabell och lagrad procedur
1. Skapa följande **tabell** i Din Azure SQL Database med SQL Server Management Studio eller något annat verktyg som du är bekväm med. Datetimestampkolumnen är det datum och den tid då motsvarande ID genereras.

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
    Id är den unika identifierade och datetimestamp kolumnen är datum och tid då motsvarande ID genereras.
    
    ![Exempeldata](./media/data-factory-stored-proc-activity/sample-data.png)

    I det här exemplet finns den lagrade proceduren i en Azure SQL-databas. Om den lagrade proceduren finns i en Azure SQL Data Warehouse och SQL Server Database är metoden liknande. För en SQL Server-databas måste du installera en [Data Management Gateway](data-factory-data-management-gateway.md).
2. Skapa följande **lagrade procedur** som infogar data i **exempeltabellen**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Namn** och **hölje** för parametern (DateTime i det här exemplet) måste matcha parametern som anges i pipelinen/aktiviteten JSON. Kontrollera att den **\@** används som prefix för parametern i definitionen av lagrade procedurer.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **NYTT** på den vänstra menyn, klicka på **Intelligens + Analys**och klicka på Data **Factory**.

    ![Ny datafabrik](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Ange **SProcDF** för namnet i bladet **Nytt datafabrik.** Azure Data Factory-namn är **globalt unika**. Du måste prefix namnet på datafabriken med ditt namn, för att möjliggöra ett framgångsrikt skapandet av fabriken.

   ![Ny datafabrik](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Välj din **Azure-prenumeration**.
5. För **Resursgrupp**gör du något av följande:
   1. Klicka på **Skapa nytt** och ange ett namn för resursgruppen.
   2. Klicka på **Använd befintlig** och välj en befintlig resursgrupp.
6. Välj **plats** för datafabriken.
7. Välj **Fäst på instrumentpanelen** så att du kan se datafabriken på instrumentpanelen nästa gång du loggar in.
8. Klicka på **Skapa** på bladet **Ny datafabrik**.
9. Du ser datafabriken som skapas i **instrumentpanelen** i Azure-portalen. När datafabriken har skapats visas datafabrikssidan med innehållet i datafabriken.

   ![Startsida för Data Factory](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Skapa en Azure SQL-länkad tjänst
När du har skapat datafabriken skapar du en Azure SQL-länkad tjänst som länkar din Azure SQL-databas, som innehåller exempeltabelltabellen och usp_sample lagrad procedur, till din datafabrik.

1. Klicka på **Författare och distribuera** på **datafabriksbladet** för **SProcDF** för att starta Data Factory Editor.
2. Klicka på **Nytt datalager** i kommandofältet och välj **Azure SQL Database**. Du bör se JSON-skriptet för att skapa en Azure SQL-länkad tjänst i redigeraren.

   ![Nytt datalager](media/data-factory-stored-proc-activity/new-data-store.png)
3. Gör följande ändringar i JSON-skriptet:

   1. Ersätt `<servername>` med namnet på din Azure SQL Database-server.
   2. Ersätt `<databasename>` med databasen där du skapade tabellen och den lagrade proceduren.
   3. Ersätt `<username@servername>` med användarkontot som har åtkomst till databasen.
   4. Ersätt `<password>` med lösenordet för användarkontot.

      ![Nytt datalager](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Om du vill distribuera den länkade tjänsten klickar du på **Distribuera** i kommandofältet. Bekräfta att du ser AzureSqlLinkedService i trädvyn till vänster.

    ![trädvy med länkad tjänst](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
Du måste ange en utdatauppsättning för en lagrad proceduraktivitet även om den lagrade proceduren inte producerar några data. Det beror på att det är utdatauppsättningen som driver schemat för aktiviteten (hur ofta aktiviteten körs - varje timme, dagligen osv.). Utdatauppsättningen måste använda en **länkad tjänst** som refererar till en Azure SQL-databas eller en Azure SQL Data Warehouse eller en SQL Server-databas där du vill att den lagrade proceduren ska köras. Utdatauppsättningen kan fungera som ett sätt att överföra resultatet av det lagrade förfarandet för efterföljande bearbetning av en annan aktivitet[(kedjeaktiviteter](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) på gång. Data Factory skriver dock inte automatiskt utdata från en lagrad procedur till den här datauppsättningen. Det är den lagrade proceduren som skrivs till en SQL-tabell som utdatauppsättningen pekar på. I vissa fall kan utdatauppsättningen vara en **dummy-datauppsättning** (en datauppsättning som pekar på en tabell som inte riktigt innehåller utdata från den lagrade proceduren). Den här dummy-datauppsättningen används bara för att ange schemat för att köra den lagrade proceduraktiviteten.

1. Klicka på **... Mer** i **verktygsfältet**klickar du på Ny datauppsättning och klickar på **Azure SQL**. **Ny datauppsättning** i kommandofältet och välj **Azure SQL**.

    ![trädvy med länkad tjänst](media/data-factory-stored-proc-activity/new-dataset.png)
2. Kopiera/klistra in följande JSON-skript i till JSON-redigeraren.

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
3. Om du vill distribuera datauppsättningen klickar du på **Distribuera** i kommandofältet. Bekräfta att du ser datauppsättningen i trädvyn.

    ![trädvy med länkade tjänster](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Skapa en pipeline med sqlserverStoredProcedure-aktivitet
Nu ska vi skapa en pipeline med en lagrad proceduraktivitet.

Observera följande egenskaper:

- **Egenskapen Type** är inställd på **SqlServerStoredProcedure**.
- Egenskaperna **storedProcedureName** i typ är inställt **på usp_sample** (namnet på den lagrade proceduren).
- Avsnittet **storedProcedureParameters** innehåller en parameter med namnet **DateTime**. Parameterns namn och hölje i JSON måste matcha parameterns namn och hölje i den lagrade procedurdefinitionen. Om du behöver skicka null för en `"param1": null` parameter använder du syntaxen: (alla gemener).

1. Klicka på **... Mer** om kommandofältet och klicka på **Ny pipeline**.
2. Kopiera/klistra in följande JSON-kodavsnitt:

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
3. Om du vill distribuera pipelinen klickar du på **Distribuera** i verktygsfältet.

### <a name="monitor-the-pipeline"></a>Övervaka pipeline
1. Klicka på **X** för att stänga bladen i Data Factory-redigeraren och för att gå tillbaka till Data Factory-bladet och klicka på **Diagram**.

    ![diagrampanel](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. I **diagramvyn**visas en översikt över de pipelines och datauppsättningar som används i den här självstudien.

    ![diagrampanel](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Dubbelklicka på datauppsättningen `sprocsampleout`i diagramvyn . Du ser segmenten i klarläge. Det bör finnas fem segment eftersom ett segment produceras för varje timme mellan starttiden och sluttiden från JSON.

    ![diagrampanel](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. När ett segment är i `select * from sampletable` läget **Klar** kör du en fråga mot Azure SQL-databasen för att kontrollera att data har infogats i tabellen av den lagrade proceduren.

   ![Utdata](./media/data-factory-stored-proc-activity/output.png)

   Se [Övervaka pipelinen](data-factory-monitor-manage-pipelines.md) för detaljerad information om hur du övervakar Azure Data Factory-pipelines.

## <a name="specify-an-input-dataset"></a>Ange en indatauppsättning
I genomgången har lagrad proceduraktivitet inga indatauppsättningar. Om du anger en indatauppsättning körs inte den lagrade proceduraktiviteten förrän segmentet med indatauppsättning är tillgängligt (i klart tillstånd). Datauppsättningen kan vara en extern datauppsättning (som inte produceras av en annan aktivitet i samma pipeline) eller en intern datauppsättning som produceras av en aktivitet uppströms (aktiviteten som körs före den här aktiviteten). Du kan ange flera indatauppsättningar för den lagrade proceduraktiviteten. Om du gör det körs den lagrade proceduraktiviteten endast när alla indatadatasegment är tillgängliga (i klart läge). Indatauppsättningen kan inte förbrukas i den lagrade proceduren som en parameter. Det används bara för att kontrollera beroendet innan du startar den lagrade proceduraktiviteten.

## <a name="chaining-with-other-activities"></a>Kedja med andra aktiviteter
Om du vill kedja en aktivitet uppströms med den här aktiviteten anger du utdata för aktiviteten uppströms som en indata för den här aktiviteten. När du gör det körs inte den lagrade proceduraktiviteten förrän aktiviteten i föregående led har slutförts och utdatauppsättningen för aktiviteten uppströms är tillgänglig (i klar status). Du kan ange utdatauppsättningar för flera aktiviteter i tidigare led som indatauppsättningar för den lagrade proceduraktiviteten. När du gör det körs den lagrade proceduraktiviteten endast när alla indatadatauppsättningssegment är tillgängliga.

I följande exempel är utdata för kopieringsaktiviteten: OutputDataset, som är en indata för den lagrade proceduraktiviteten. Därför körs inte den lagrade proceduraktiviteten förrän kopieringsaktiviteten är klar och segmentet OutputDataset är tillgängligt (i klart läge). Om du anger flera indatauppsättningar körs inte den lagrade proceduren förrän alla indatadatauppsättningssegment är tillgängliga (i klart läge). Indatauppsättningarna kan inte användas direkt som parametrar för den lagrade proceduraktiviteten.

Mer information om kedjeaktiviteter finns [i flera aktiviteter i en pipeline](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

Om du vill koppla butiksproceduraktiviteten till **aktiviteter i senare led** (de aktiviteter som körs efter att den lagrade proceduren har slutförts) anger du utdatauppsättningen för den lagrade proceduraktiviteten som en indata för aktiviteten i efterföljande led i rörledningen.

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server kan du konfigurera **SqlSink** i kopieringsaktivitet för att anropa en lagrad procedur med hjälp av egenskapen **sqlWriterStoredProcedureName.** Mer information finns i [Anropa lagrad procedur från kopieringsaktivitet](data-factory-invoke-stored-procedure-from-copy-activity.md). Mer information om egenskapen finns i följande anslutningsartiklar: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure SQL Data Warehouse kan du konfigurera **SqlSource** i kopieringsaktivitet för att anropa en lagrad procedur för att läsa data från källdatabasen med hjälp av egenskapen **sqlReaderStoredProcedureName.** Mer information finns i följande anslutningsartiklar: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties), Azure SQL [Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>JSON-format
Här är JSON-formatet för att definiera en lagrad proceduraktivitet:

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

I följande tabell beskrivs dessa JSON-egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| namn | Aktivitetens namn |Ja |
| description |Text som beskriver vad aktiviteten används för |Inga |
| typ | Måste ställas in på: **SqlServerStoredProcedure** | Ja |
| Ingångar | Valfri. Om du anger en indatauppsättning måste den vara tillgänglig (i "Klar"-status) för att den lagrade proceduraktiviteten ska kunna köras. Indatauppsättningen kan inte förbrukas i den lagrade proceduren som en parameter. Det används bara för att kontrollera beroendet innan du startar den lagrade proceduraktiviteten. |Inga |
| Utgångar | Du måste ange en utdatauppsättning för en lagrad proceduraktivitet. Utdatauppsättningen anger **schemat** för den lagrade proceduraktiviteten (timme, vecka, månad osv.). <br/><br/>Utdatauppsättningen måste använda en **länkad tjänst** som refererar till en Azure SQL-databas eller en Azure SQL Data Warehouse eller en SQL Server-databas där du vill att den lagrade proceduren ska köras. <br/><br/>Utdatauppsättningen kan fungera som ett sätt att överföra resultatet av det lagrade förfarandet för efterföljande bearbetning av en annan aktivitet[(kedjeaktiviteter](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) på gång. Data Factory skriver dock inte automatiskt utdata från en lagrad procedur till den här datauppsättningen. Det är den lagrade proceduren som skrivs till en SQL-tabell som utdatauppsättningen pekar på. <br/><br/>I vissa fall kan utdatauppsättningen vara en **dummy-datauppsättning**, som endast används för att ange schemat för att köra den lagrade proceduraktiviteten. |Ja |
| storedProcedureName |Ange namnet på den lagrade proceduren i Azure SQL-databasen eller Azure SQL Data Warehouse eller SQL Server-databasen som representeras av den länkade tjänst som utdatatabellen använder. |Ja |
| storedProcedureParameters |Ange värden för parametrar för lagrade procedurer. Om du behöver skicka null för en parameter använder du syntaxen: "param1": null (alla gemener). Läs följande exempel om du vill veta mer om hur du använder den här egenskapen. |Inga |

## <a name="passing-a-static-value"></a>Skicka ett statiskt värde
Nu ska vi överväga att lägga till en annan kolumn med namnet "Scenario" i tabellen som innehåller ett statiskt värde som kallas "Dokumentexempel".

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

Nu, skicka **scenario** parametern och värdet från den lagrade proceduraktiviteten. Avsnittet **typeProperties** i föregående exempel ser ut som följande utdrag:

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

**Data Factory datauppsättning:**

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

**Pipeline för datafabrik**

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
