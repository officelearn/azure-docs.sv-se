---
title: Kopiera flera tabeller stegvis med Azure Data Factory | Microsoft Docs
description: I den här självstudiekursen kommer du att skapa en Azure Data Factory-pipeline som kopierar deltadata stegvis från flera tabeller i en lokal SQL Server-databas till en Azure SQL-databas.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 20a921a0248fdb8a3856e27ad5e2620f7adb70de
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52962146"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Läs in data stegvis från flera tabeller i SQL Server till en Azure SQL-databas
I den här självstudiekursen kommer du att skapa en Azure-datafabrik med en pipeline som läser in deltadata från flera tabeller på en lokal SQL-server till en Azure SQL-databas.    

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Förbereda käll- och måldatalager.
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Installera Integration Runtime. 
> * Skapa länkade tjänster. 
> * Skapa datauppsättningar för källa, mottagare och vattenstämpel.
> * Skapa, köra och övervaka en pipeline.
> * Granska resultaten.
> * Lägga till eller uppdatera data i källtabeller.
> * Köra och övervaka pipelinen igen.
> * Granska de slutliga resultaten.

## <a name="overview"></a>Översikt
Här är några viktiga steg för att skapa den här lösningen: 

1. **Markera vattenstämpelkolumnen**.
    Markera en kolumn för varje tabell i källdatalagret som går att använda för att identifiera de nya eller uppdaterade posterna för varje körning. Vanligtvis ökar data i den markerade kolumnen (till exempel last_modify_time elle ID) när rader skapas eller uppdateras. Det maximala värdet i den här kolumnen används som vattenstämpel.

1. **Förbered datalagringen för att lagra värdet för vattenstämpeln**.   
    I den här självstudien lagrar du storleksgränsen i en SQL-databas.

1. **Skapa en pipeline med följande aktiviteter**: 
    
    a. Skapa en ForEach-aktivitet som upprepas över en lista med namn på källtabeller och som skickas som en parameter till pipelinen. För varje källtabell anropas följande aktiviteter som utför deltainläsningen för tabellen.

    b. Skapa två sökningsaktiviteter. Använd den första sökningsaktiviteten för att hämta det sista vattenstämpelvärdet. Använd den andra sökningsaktiviteten för att hämta det nya vattenstämpelvärdet. Vattenstämpelvärdena skickas till kopieringsaktiviteten.

    c. Skapa en kopieringsaktivitet som kopierar raderna från källdatalagringen med värdet för vattenstämpelkolumnen som är större än det gamla värdet och mindre än det nya. Sedan kopieras deltadata från källdatalagringen till Azure Blob-lagring som en ny fil.

    d. Skapa en StoredProcedure-aktivitet som uppdaterar vattenstämpelvärdet för den pipeline som körs nästa gång. 

    Här är det avancerade diagrammet: 

    ![Läsa in data stegvis](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
* **SQL Server**. Du använder en lokal SQL Server-databas som källdatalager i den här självstudien. 
* **Azure SQL Database**. Du använder en SQL-databas som måldatalager. Om du inte har någon SQL Database kan du läsa om hur du skapar en i [Skapa en Azure SQL Database](../sql-database/sql-database-get-started-portal.md). 

### <a name="create-source-tables-in-your-sql-server-database"></a>Skapa källtabeller i din SQL Server-databas

1. Öppna SQL Server Management Studio och anslut till din lokala SQL-serverdatabas.

1. I **Server Explorer** högerklickar du på databasen och väljer **Ny fråga**.

1. Kör följande SQL-kommando mot databasen för att skapa tabeller med namnen `customer_table` och `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Skapa måltabeller i Azure SQL-databasen
1. Öppna SQL Server Management Studio och anslut till din SQL Serverdatabas.

1. I **Server Explorer** högerklickar du på databasen och väljer **Ny fråga**.

1. Kör följande SQL-kommando mot SQL-databasen för att skapa tabeller med namnen `customer_table` och `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Skapa en annan tabell i Azure SQL-databasen för att lagra värdet för högvattenmärket
1. Kör följande SQL-kommando mot din SQL-databas för att skapa en tabell med namnet `watermarktable` för att lagra värdet för högvattenmärket: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Infoga inledande värden för högvattenmärket för båda källtabellerna i vattenmärkestabellen.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Skapa en lagrad procedur i Azure SQL-databasen 

Kör följande kommando för att skapa en lagrad procedur i din SQL-databas. Den här lagrade proceduren uppdaterar vattenmärkets värde efter varje pipelinekörning. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-the-azure-sql-database"></a>Skapa datatyper och ytterligare lagrade procedurer i Azure SQL-databasen
Kör följande fråga för att skapa två lagrade procedurer och två datatyper i SQL-databasen. De används för att slå samman data från källtabellerna till måltabellerna.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Ett exempel är `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen.

1. Definiera en variabel för datafabrikens plats. 

    ```powershell
    $location = "East US"
    ```
1. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen.

1. Definiera en variabel för datafabrikens namn. 

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn för att göra det unikt globalt. Ett exempel är ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
1. Kör följande cmdlet av typen **Set-AzureRmDataFactoryV2** för att skapa en datafabrik: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Observera följande punkter:

* Namnet på datafabriken måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen:

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna deltagare eller ägare, eller vara administratör för Azure-prenumerationen.
* Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren ( Azure Storage, Azure SQL Database o.s.v.) och beräkningarna (Azure HDInsight o.s.v.) som används i datafabriken kan finnas i andra regioner.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]



## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I det här avsnittet får du skapa länkade tjänster till din lokala SQL Server-databas och din SQL-databas. 

### <a name="create-the-sql-server-linked-service"></a>Skapa länkad tjänst till SQL Server
I det här steget länkar du din lokala SQL Serverdatabas till datafabriken.

1. Skapa en JSON-fil med namnet SqlServerLinkedService.json i mappen C:\ADFTutorials\IncCopyMultiTableTutorial med följande innehåll. Välj rätt avsnitt baserat på vilken autentisering du använder när du ansluter till SQL Server. Skapa de lokala mapparna om de inte redan finns. 

    > [!IMPORTANT]
    > Välj rätt avsnitt baserat på vilken autentisering du använder när du ansluter till SQL Server.

    Om du använder SQL-autentisering kopierar du följande JSON-definition:

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    
    Om du använder Windows-autentisering kopierar du följande JSON-definition:

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```
    > [!IMPORTANT]
    > - Välj rätt avsnitt baserat på vilken autentisering du använder när du ansluter till SQL Server.
    > - Ersätt &lt;integration runtime-namn > med namnet på din Integration Runtime.
    > - Ersätt &lt;servername>&lt;, databasename>, &lt;username>&lt; och password> med värdena för din SQL Serverdatabas innan du sparar filen.
    > - Om du behöver använda ett snedstreck (`\`) i ditt användarkonto eller användarnamn använder du escape-tecknet (`\`). Ett exempel är `mydomain\\myuser`.

1. I PowerShell växlar du till mappen C:\ADFTutorials\IncCopyMultiTableTutorial.

1. Kör cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten AzureStorageLinkedService. I följande exempel skickar du värden för parametrarna *ResourceGroupName* och *DataFactoryName*: 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Här är exempel på utdata:

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Skapa länkad SQL-databastjänst
1. Skapa en JSON-fil med namnet AzureSQLDatabaseLinkedService.json i mappen C:\ADFTutorials\IncCopyMultiTableTutorial med följande innehåll. (Skapa mappen ADF om den inte redan finns.) Ersätt &lt;server&gt;, &lt;database name&gt;, &lt;user id&gt; och &lt;password&gt; med namnen för din SQL-serverdatabas, databas, ditt användar-ID och lösenord innan du sparar filen. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
1. I PowerShell kör du cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten: AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Här är exempel på utdata:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du datauppsättningar som representerar datakällan, datamålet och platsen för vattenstämpeln.

### <a name="create-a-source-dataset"></a>Skapa en källdatauppsättning

1. Skapa en JSON-fil med namnet SourceDataset.json i samma mapp med följande innehåll: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dummyName"
            },
            "linkedServiceName": {
                "referenceName": "SqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```

    Tabellnamnet är ett dummynamn. Kopieringsaktivitet i pipelinen använder en SQL-fråga till att läsa in data, snarare än att läsa in hela tabellen.

1. Kör cmdleten **Set-AzureRmDataFactoryV2Dataset** för att skapa datauppsättningen SourceDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Här är exempel på utdata från cmdleten:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Skapa en källdatauppsättning

1. Skapa en JSON-fil med namnet SinkDataset.json i samma mapp med följande innehåll. Elementet tableName har angetts av pipelinen dynamiskt vid körning. ForEach-aktiviteten i pipelinen upprepas över en lista med tabellnamn och skickar tabellnamnet till datamängden i varje iteration. 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().SinkTableName}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "SinkTableName": {
                    "type": "String"
                }
            }
        }
    }
    ```

1. Kör cmdleten **Set-AzureRmDataFactoryV2Dataset** för att skapa datauppsättningen SinkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Här är exempel på utdata från cmdleten:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Skapa en datauppsättning för en vattenstämpel
I det här steget skapar du en datauppsättning för att lagra ett värde för ett högvattenmärke. 

1. Skapa en JSON-fil med namnet WatermarkDataset.json i samma mapp med följande innehåll: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
1. Kör cmdleten **Set-AzureRmDataFactoryV2Dataset** för att skapa datauppsättningen WatermarkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Här är exempel på utdata från cmdleten:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : <data factory name>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Skapa en pipeline
Den här pipelinen tar en lista med tabellnamn som en parameter. ForEach-aktiviteten upprepas över listan med tabellnamn och utför följande åtgärder: 

1. Använd sökningsaktiviteten till att hämta det gamla vattenmärkesvärdet (startvärdet eller det som användes i den senaste iterationen).

1. Använd sökningsaktiviteten för att hämta det nya högvattenmärket (högsta värdet i kolumnen vattenmärke i källtabellen).

1. Använd kopieringsaktiviteten till att kopiera data mellan de två vattenmärkesvärdena från källdatabasen till måldatabasen.

1. Använd StoredProcedure-aktiviteten för att uppdatera det gamla vattenmärket som ska användas i det första steget i nästa iteration. 

### <a name="create-the-pipeline"></a>Skapa pipelinen
1. Skapa en JSON-fil med namnet IncrementalCopyPipeline.json i samma mapp med följande innehåll: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [{
    
                "name": "IterateSQLTables",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "items": {
                        "value": "@pipeline().parameters.tableList",
                        "type": "Expression"
                    },
    
                    "activities": [
                        {
                            "name": "LookupOldWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'"
                                },
    
                                "dataset": {
                                    "referenceName": "WatermarkDataset",
                                    "type": "DatasetReference"
                                }
                            }
                        },
                        {
                            "name": "LookupNewWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}"
                                },
    
                                "dataset": {
                                    "referenceName": "SourceDataset",
                                    "type": "DatasetReference"
                                }
                            }
                        },
    
                        {
                            "name": "IncrementalCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                                },
                                "sink": {
                                    "type": "SqlSink",
                                    "SqlWriterTableType": "@{item().TableType}",
                                    "SqlWriterStoredProcedureName": "@{item().StoredProcedureNameForMergeOperation}"
                                }
                            },
                            "dependsOn": [{
                                    "activity": "LookupNewWaterMarkActivity",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                },
                                {
                                    "activity": "LookupOldWaterMarkActivity",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
    
                            "inputs": [{
                                "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                            }],
                            "outputs": [{
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference",
                                "parameters": {
                                    "SinkTableName": "@{item().TableType}"
                                }
                            }]
                        },
    
                        {
                            "name": "StoredProceduretoWriteWatermarkActivity",
                            "type": "SqlServerStoredProcedure",
                            "typeProperties": {
    
                                "storedProcedureName": "sp_write_watermark",
                                "storedProcedureParameters": {
                                    "LastModifiedtime": {
                                        "value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                        "type": "datetime"
                                    },
                                    "TableName": {
                                        "value": "@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                        "type": "String"
                                    }
                                }
                            },
    
                            "linkedServiceName": {
                                "referenceName": "AzureSQLDatabaseLinkedService",
                                "type": "LinkedServiceReference"
                            },
    
                            "dependsOn": [{
                                "activity": "IncrementalCopyActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }]
                        }
    
                    ]
    
                }
            }],
    
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```
1. Kör cmdleten **Set-AzureRmDataFactoryV2Pipeline** för att skapa pipelinen IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Här är exempel på utdata: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Köra en pipeline

1. Skapa en parameterfil med namnet Parameters.json i samma mapp med följande innehåll:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
            }
        ]
    }
    ```
1. Kör pipelinen IncrementalCopyPipeline med cmdleten **Invoke-AzureRmDataFactoryV2Pipeline**. Ersätt platshållarna med din egen grupp och datafabrikens namn.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla tjänster**, sök med nyckelordet *Datafabriker* och välj **Datafabriker**. 

    ![Menyn Datafabriker](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-data-factories-menu-1.png)

1. Sök efter din datafabrik i listan med datafabriker och välj den så att du öppnar sidan **Datafabrik**. 

    ![Sök efter din datafabrik](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-search-data-factory-2.png)

1. På dsidan **Datafabrik** väljer du **Övervaka och hantera**. 

    ![Ikonen Övervaka och hantera](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-monitor-manage-tile-3.png)

1. Programmet **Data Integration** öppnas i en separat flik. Du kan se alla pipelinekörningar och deras status. Lägg i följande exempel märke till att statusen för pipelinekörningen är **Lyckades**. Du kan kontrollera parametrarna som skickats till pipelinen genom att klicka på länken i kolumnen **Parametrar**. Om det uppstod ett fel ser du en länk i kolumnen **Fel**. Välj länken i kolumnen **Åtgärder**. 

    ![Pipelinekörningar](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
1. När du väljer länken i kolumnen **Åtgärder** ser du följande sida som visar alla aktivitetskörningar för pipelinen: 

    ![Aktivitetskörningar](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-activity-runs-5.png)

1. Om du vill gå tillbaka till vyn **Pipelinekörningar** väljer du **Pipelines** enligt bilden. 

## <a name="review-the-results"></a>Granska resultaten
Kör följande frågor mot SQL-måldatabasen i SQL Server Management Studio för att verifiera att data har kopierats från källtabellerna till måltabellerna: 

**Fråga** 
```sql
select * from customer_table
```

**Resultat**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Fråga**

```sql
select * from project_table
```

**Resultat**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Fråga**

```sql
select * from watermarktable
```

**Resultat**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Observera att vattenstämpelvärdena för båda tabellerna har uppdaterats. 

## <a name="add-more-data-to-the-source-tables"></a>Lägga till mer data i källtabellerna

Kör följande fråga mot SQL Servers källdatabas för att uppdatera en befintlig rad i customer_table. Infoga en ny rad i project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Kör pipelinen igen

1. Kör nu pipelinen igen genom att köra följande PowerShell-kommando:

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
1. Övervaka pipelinekörningarna genom att följa anvisningarna i avsnittet [Övervaka pipelinen](#monitor-the-pipeline). När pipelinens status är **Pågår** ser du en annan åtgärdslänk under **Åtgärder** för att avbryta pipelinekörningen. 

    ![Pågående pipelinekörningar](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-6.png)

1. Välj **Uppdatera** om du vill uppdatera listan tills pipelinekörningen lyckas. 

    ![Uppdatera pipelinekörningar](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-succeded-7.png)

1. Om du vill kan du välja **Visa aktivitetskörningar** under **Åtgärder** om du vill se alla aktivitetskörningar som är associerade med den här pipelinekörningen. 

## <a name="review-the-final-results"></a>Granska de slutliga resultaten
Kör följande frågor mot måldatabasen i SQL Server Management Studio för att verifiera att nya/uppdaterade data har kopierats från källtabellerna till måltabellerna. 

**Fråga** 
```sql
select * from customer_table
```

**Resultat**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Lägg märke till de nya värdena för **Name** och **LastModifytime** för **PersonID** för nummer 3. 

**Fråga**

```sql
select * from project_table
```

**Resultat**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Observera att posten **NewProject** har lagts till i project_table. 

**Fråga**

```sql
select * from watermarktable
```

**Resultat**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Observera att vattenstämpelvärdena för båda tabellerna har uppdaterats.
     
## <a name="next-steps"></a>Nästa steg
I den här självstudien har du fått: 

> [!div class="checklist"]
> * Förbereda käll- och måldatalager.
> * Skapa en datafabrik.
> * Skapa Integration Runtime (IR) med egen värd.
> * Installera Integration Runtime.
> * Skapa länkade tjänster. 
> * Skapa datauppsättningar för källa, mottagare och vattenstämpel.
> * Skapa, köra och övervaka en pipeline.
> * Granska resultaten.
> * Lägga till eller uppdatera data i källtabeller.
> * Köra och övervaka pipelinen igen.
> * Granska de slutliga resultaten.

Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Läs in data stegvis från Azure SQL Database till Azure Blob Storage med ändringsspårningsteknik](tutorial-incremental-copy-change-tracking-feature-powershell.md)


