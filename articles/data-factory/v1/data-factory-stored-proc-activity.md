---
title: SQLServer lagrade Proceduraktiviteten
description: Lär dig hur du kan använda lagrade Proceduraktiviteten för SQL Server för att anropa en lagrad procedur i en Azure SQL Database eller Azure SQL Data Warehouse från Data Factory-pipelinen.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 3a0d6998671df7f05d7077da6fffc20a5bf27e8a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622507"
---
# <a name="sql-server-stored-procedure-activity"></a>SQLServer lagrade Proceduraktiviteten
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
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Transformera data med hjälp av aktiviteten lagrad procedur i Data Factory version 2](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Översikt
Du kan använda data transformation aktiviteter i en Datafabrik [pipeline](data-factory-create-pipelines.md) att transformera och bearbeta rådata till förutsägelser och insikter. Den lagrade Proceduraktiviteten är en av omvandling av aktiviteter som har stöd för Data Factory. Den här artikeln bygger på den [data transformation aktiviteter](data-factory-data-transformation-activities.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter i Data Factory.

Du kan använda den lagrade Proceduraktiviteten för att anropa en lagrad procedur i någon av följande datalager i ditt företag eller på ett Azure-dator (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server-databas.  Om du använder SQL Server, installera Data Management Gateway på samma dator som värd för databasen eller på en separat dator som har åtkomst till databasen. Data Management Gateway är en komponent som ansluter datakällor på lokala/på virtuella Azure-datorn med molntjänster i en säker och hanterad sätt. Se [Data Management Gateway](data-factory-data-management-gateway.md) artikeln för information.

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server, kan du konfigurera den **SqlSink** i en Kopieringsaktivitet att anropa en lagrad procedur med hjälp av den **sqlWriterStoredProcedureName** egenskapen. Mer information finns i [anropa lagrade procedur från kopieringsaktiviteten](data-factory-invoke-stored-procedure-from-copy-activity.md). Mer information om egenskapen Se följande artiklar för koppling: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Anropa en lagrad procedur vid kopiering av data till en Azure SQL Data Warehouse med hjälp av en kopieringsaktiviteten stöds inte. Men du kan använda aktiviteten lagrad procedur för att anropa en lagrad procedur i en SQL Data Warehouse. 
>  
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure SQL Data Warehouse, kan du konfigurera **SqlSource** i en Kopieringsaktivitet att anropa en lagrad procedur för att läsa data från källdatabasen med hjälp av den  **sqlReaderStoredProcedureName** egenskapen. Mer information finns i följande artiklar för koppling: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          


Den här genomgången använder aktiviteten lagrad procedur i en pipeline för att anropa en lagrad procedur i en Azure SQL database. 

## <a name="walkthrough"></a>Genomgång
### <a name="sample-table-and-stored-procedure"></a>Exempel på en tabell och lagrade procedurer
1. Skapa följande **tabellen** i Azure SQL-databasen med hjälp av SQL Server Management Studio eller andra verktyg som du är nöjd med. Kolumnen datetimestamp är datum och tid när motsvarande ID har genererats.

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
    ID är unikt identifieras och datetimestamp kolumnen är datum och tid när motsvarande ID har genererats.
    
    ![Exempeldata](./media/data-factory-stored-proc-activity/sample-data.png)

    I det här exemplet är den lagrade proceduren i en Azure SQL Database. Om den lagrade proceduren finns i en Azure SQL Data Warehouse och SQL Server-databas, liknar tillvägagångssättet. SQL Server-databas måste du installera en [Data Management Gateway](data-factory-data-management-gateway.md).
2. Skapa följande **lagrade proceduren** som infogar data i den **sampletable**.

    ```SQL
    CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Namnet** och **skiftläge** för parametern (DateTime i det här exemplet) måste matcha parameter som anges i pipeline/aktivitet JSON. I den lagrade proceduren definitionen, kontrollerar du att **@** används som ett prefix för parametern.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **ny** på den vänstra menyn klickar du på **Intelligence + analys**, och klicka på **Data Factory**.

    ![Ny datafabrik](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. I den **nya datafabriken** bladet ange **SProcDF** för namnet. Azure Data Factory-namn är **globalt unika**. Du behöver Prefixnamn datafabriken med ditt namn, aktivera fabriken har skapats.

   ![Ny datafabrik](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. Välj din **Azure-prenumeration**.
5. Gör något av följande för **Resursgrupp**:
   1. Klicka på **Skapa nytt** och ange ett namn för resursgruppen.
   2. Klicka på **använda befintliga** och välj en befintlig resursgrupp.  
6. Välj **plats** för datafabriken.
7. Välj **fäst på instrumentpanelen** så att du kan se datafabriken på instrumentpanelen nästa gång du loggar in.
8. Klicka på **Skapa** på bladet **Ny datafabrik**.
9. Du ser datafabriken som skapas i den **instrumentpanelen** på Azure-portalen. När datafabriken har skapats visas datafabrikssidan med innehållet i datafabriken.

   ![Data Factory-startsida](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Skapa en Azure SQL-länkade tjänst
Efter att datafabriken kan du skapa en Azure SQL länkade tjänst som länkar din Azure SQL-databasen, som innehåller sampletable tabellen och sp_sample lagrade proceduren till din data factory.

1. Klicka på **författare och distribuera** på den **Datafabriken** bladet för **SProcDF** att starta Data Factory-redigeraren.
2. Klicka på **Nytt datalager** på kommandoraden och välj **Azure SQL Database**. Du bör se JSON-skript för att skapa en Azure SQL-länkade tjänst i redigeraren.

   ![Nytt datalager](media/data-factory-stored-proc-activity/new-data-store.png)
3. Gör följande ändringar i JSON-skript:

   1. Ersätt `<servername>` med namnet på din Azure SQL Database-server.
   2. Ersätt `<databasename>` med den databas som du skapade i tabell och den lagrade proceduren.
   3. Ersätt `<username@servername>` med det användarkonto som har åtkomst till databasen.
   4. Ersätt `<password>` med lösenordet för användarkontot.

      ![Nytt datalager](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Om du vill distribuera den länkade tjänsten klickar du på **distribuera** i kommandofältet. Bekräfta att du ser AzureSqlLinkedService i trädvyn till vänster.

    ![trädvy för länkad tjänst](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
Du måste ange en datamängd för utdata för en lagrad procedur aktivitet, även om den lagrade proceduren inte skapar några data. Det beror på att den är datamängd för utdata som enheter schemat för aktiviteten (hur ofta aktiviteten körs - varje timme, varje dag, etc.). Datamängd för utdata måste använda en **länkade tjänsten** som refererar till en Azure SQL Database eller ett Azure SQL Data Warehouse eller en SQL Server-databas som du vill använda den lagrade proceduren för att köra. Datamängd för utdata kan fungera som ett sätt att skicka resultatet av den lagrade proceduren för senare bearbetning av en annan aktivitet ([länkning aktiviteter](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) i pipelinen. Dock skriver Data Factory inte automatiskt utdata från en lagrad procedur denna DataSet. Det är den lagrade proceduren som skriver till en SQLtabell som datamängd för utdata som pekar på. I vissa fall datamängd för utdata kan vara en **dummy dataset** (en datamängd som pekar på en tabell som inte innehåller utdata från den lagrade proceduren verkligen). Den här dummy datauppsättningen används bara för att ange schemat för att köra aktiviteten lagrad procedur. 

1. Klicka på **... Flera** i verktygsfältet klickar du på **ny datamängd**, och klicka på **Azure SQL**. **Ny datamängd** i kommandofältet och välj **Azure SQL**.

    ![trädvy för länkad tjänst](media/data-factory-stored-proc-activity/new-dataset.png)
2. Kopiera och klistra in följande JSON-skript i JSON-redigeringsprogrammet.

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
3. Om du vill distribuera datauppsättningen klickar du på **distribuera** i kommandofältet. Bekräfta att du ser dataset i trädvyn.

    ![Trädvy med länkade tjänster](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Skapa en pipeline med SqlServerStoredProcedure aktivitet
Nu ska vi skapa en pipeline med en lagrad procedur-aktivitet. 

Observera följande egenskaper: 

- Den **typen** egenskap är inställd på **SqlServerStoredProcedure**. 
- Den **storedProcedureName** i typen egenskaper har angetts till **sp_sample** (namnet på den lagrade proceduren).
- Den **storedProcedureParameters** avsnittet innehåller en parameter med namnet **DateTime**. Namn och skiftläge för parametern i JSON måste matcha namnet och versaler och gemener i parametern i definitionen för lagrade proceduren. Om du behöver lägga till null för en parameter, använder du syntax: `"param1": null` (gemener).
 
1. Klicka på **... Flera** i kommandofältet och på **ny pipeline**.
2. Kopiera och klistra in följande kodutdrag JSON:   

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "sp_sample",
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
3. Om du vill distribuera pipeline, klickar du på **distribuera** i verktygsfältet.  

### <a name="monitor-the-pipeline"></a>Övervaka pipeline
1. Klicka på **X** för att stänga bladen i Data Factory-redigeraren och för att gå tillbaka till Data Factory-bladet och klicka på **Diagram**.

    ![Diagram över sida vid sida](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. I **diagramvyn** visas en översikt över pipelines och datauppsättningar som används i den här självstudien.

    ![Diagram över sida vid sida](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Dubbelklicka på datauppsättningen i diagramvyn `sprocsampleout`. Du ser segment i tillståndet Ready. Det bör finnas fem segment eftersom ett segment skapas för varje timme mellan start- och sluttid från JSON.

    ![Diagram över sida vid sida](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. När ett segment är i **klar** tillstånd, kör en `select * from sampletable` frågan mot Azure SQL-databasen för att verifiera att data har infogas i tabellen av den lagrade proceduren.

   ![Utdata](./media/data-factory-stored-proc-activity/output.png)

   Se [övervaka pipeline](data-factory-monitor-manage-pipelines.md) detaljerad information om hur du övervakar Azure Data Factory pipelines.  


## <a name="specify-an-input-dataset"></a>Ange en inkommande datauppsättning
I den här genomgången har lagrade proceduraktiviteten inte några inkommande datauppsättningar. Om du anger en inkommande datauppsättning körs inte aktiviteten lagrad procedur tills segment av inkommande dataset är tillgänglig (i tillståndet Ready). Dataset kan vara en externa datauppsättningen (som inte tillverkas av en annan aktivitet i samma rörledning) eller en intern datauppsättning som produceras av en överordnad aktivitet (den aktivitet som körs före den här aktiviteten). Du kan ange flera indatauppsättningar för aktiviteten lagrad procedur. Om du gör det körs lagrade proceduraktiviteten bara när alla inkommande dataset-segment (i tillståndet Ready). Inkommande dataset kan inte användas i den lagrade proceduren som en parameter. Den används endast för att kontrollera beroendet innan du startar aktiviteten lagrad procedur.

## <a name="chaining-with-other-activities"></a>Länkning med andra aktiviteter
Ange utdata från den överordnade aktiviteten som indata för den här aktiviteten om du vill att en överordnad aktivitet med den här aktiviteten. När du gör det fungerar inte aktiviteten lagrad procedur tills den överordnade aktiviteten har slutförts och datamängd för utdata för den överordnade aktiviteten är tillgänglig (i redo status). Du kan ange utdata-datauppsättningar på flera överordnade aktiviteter som indatauppsättningar för aktiviteten lagrad procedur. När du gör det körs lagrade proceduraktiviteten bara när alla inkommande dataset-segment är tillgängliga.  

I följande exempel utdata från kopieringsaktiviteten är: OutputDataset som utgör indata för aktiviteten lagrad procedur. Aktiviteten lagrad procedur körs därför inte förrän kopieringsaktiviteten har slutförts och OutputDataset sektorn är tillgänglig (i tillståndet Ready). Om du anger flera indatauppsättningar körs aktiviteten lagrad procedur inte förrän alla indata dataset-segment är tillgängliga (statusen Ready). Inkommande datauppsättningar kan inte användas direkt som parametrar för aktiviteten lagrad procedur. 

Mer information om länkning aktiviteter finns [flera aktiviteter i en pipeline](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

På liknande sätt att länka en aktivitet för store-procedur med **underordnade aktiviteter** (aktiviteter som körs när aktiviteten lagrad procedur har slutförts), ange datamängd för utdata för aktiviteten lagrad procedur som indata för den underordnad aktivitet i pipelinen.

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server, kan du konfigurera den **SqlSink** i en Kopieringsaktivitet att anropa en lagrad procedur med hjälp av den **sqlWriterStoredProcedureName** egenskapen. Mer information finns i [anropa lagrade procedur från kopieringsaktiviteten](data-factory-invoke-stored-procedure-from-copy-activity.md). Mer information om egenskapen finns i följande artiklar för koppling: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
>  
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure SQL Data Warehouse, kan du konfigurera **SqlSource** i en Kopieringsaktivitet att anropa en lagrad procedur för att läsa data från källdatabasen med hjälp av den  **sqlReaderStoredProcedureName** egenskapen. Mer information finns i följande artiklar för koppling: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          

## <a name="json-format"></a>JSON-format
Här är JSON-format för att definiera en lagrade Proceduraktiviteten:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs":  [ { "name": "inputtable"  } ],
    "outputs":  [ { "name": "outputtable" } ],
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

I följande tabell beskrivs egenskaperna JSON:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| namn | Namnet på aktiviteten |Ja |
| description |Text som beskriver aktiviteten är det som används för |Nej |
| typ | Måste anges till: **SqlServerStoredProcedure** | Ja |
| inmatningar | Valfri. Om du anger en inkommande datauppsättning, måste det vara tillgänglig (statusen ”klar”) för aktiviteten lagrad procedur att köra. Inkommande dataset kan inte användas i den lagrade proceduren som en parameter. Den används endast för att kontrollera beroendet innan du startar aktiviteten lagrad procedur. |Nej |
| utdata | Du måste ange en datamängd för utdata för en lagrad procedur-aktivitet. Utdatauppsättningen anger den **schema** för aktiviteten lagrad procedur (varje timme, varje vecka, månad, etc.). <br/><br/>Datamängd för utdata måste använda en **länkade tjänsten** som refererar till en Azure SQL Database eller ett Azure SQL Data Warehouse eller en SQL Server-databas som du vill använda den lagrade proceduren för att köra. <br/><br/>Datamängd för utdata kan fungera som ett sätt att skicka resultatet av den lagrade proceduren för senare bearbetning av en annan aktivitet ([länkning aktiviteter](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) i pipelinen. Dock skriver Data Factory inte automatiskt utdata från en lagrad procedur denna DataSet. Det är den lagrade proceduren som skriver till en SQLtabell som datamängd för utdata som pekar på. <br/><br/>I vissa fall datamängd för utdata kan vara en **dummy dataset**, som används bara för att ange schemat för att köra aktiviteten lagrad procedur. |Ja |
| storedProcedureName |Ange namnet på den lagrade proceduren i Azure SQL database eller Azure SQL Data Warehouse eller SQL Server-databas som representeras av den länkade tjänst som använder utdatatabellen. |Ja |
| storedProcedureParameters |Ange värden för parametrarna för lagrade procedurer. Om du måste överföra null för en parameter, använder du syntax: ”param1”: null (alla gemen). Se följande exempel mer information om hur du använder den här egenskapen. |Nej |

## <a name="passing-a-static-value"></a>Skicka ett statiskt värde
Nu ska vi Överväg att lägga till en annan kolumn med namnet 'Scenariot' i tabellen som innehåller ett statiskt värde med namnet 'Dokumentera exempel'.

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

**Lagrade proceduren:**

```SQL
CREATE PROCEDURE sp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Nu kan skicka den **scenariot** och värdet från aktiviteten lagrad procedur. Den **typeProperties** avsnitt i föregående exempel ser ut som följande utdrag:

```JSON
"typeProperties":
{
    "storedProcedureName": "sp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory datauppsättningen:**

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

**Data Factory-pipelinen**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample2",
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
