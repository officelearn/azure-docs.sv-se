---
title: Kopiera flera tabeller stegvis med Azure Data Factory | Microsoft Docs
description: "I den här självstudiekursen kommer du att skapa en Azure Data Factory-pipeline som kopierar deltadata stegvis från flera tabeller i en lokal SQL Server-databas till en Azure SQL-databas. "
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: jingwang
ms.openlocfilehash: 5472c41af9b0c524b931ed3e6b149270e17a633a
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-azure-sql-database"></a>Läs in data stegvist från flera tabeller i SQL Server till Azure SQL-databas
I den här självstudiekursen kommer du att skapa en Azure-datafabrik med en pipeline som läser in deltadata från flera tabeller på en lokal SQL-server till en Azure SQL-databas.    

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Förbereda käll- och måldatalager.
> * Skapa en datafabrik.
> * Skapa Integration Runtime (IR) med egen värd
> * Installera Integration Runtime 
> * Skapa länkade tjänster. 
> * Skapa datauppsättningar för källa, mottagare, vattenstämpel.
> * Skapa, köra och övervaka en pipeline.
> * Granska resultatet
> * Lägga till eller uppdatera data i källtabeller
> * Köra och övervaka pipelinen igen
> * Granska slutresultatet 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Översikt
Här är några viktiga steg för att skapa den här lösningen: 

1. **Markera vattenstämpelkolumnen**.
    Markera en kolumn för varje tabell i källdatalagret som går att använda för att identifiera de nya eller uppdaterade posterna för varje körning. Vanligtvis ökar data i den markerade kolumnen (till exempel last_modify_time eller ID) när rader skapas eller uppdateras. Det maximala värdet i den här kolumnen används som vattenstämpel.
2. **Förbered datalagringen för att lagra värdet för vattenstämpeln**.   
    I den här självstudien lagrar du storleksgränsen i en Azure SQL-databas.
3. **Skapa en pipeline med följande aktiviteter:** 
    
    1. Skapa en **ForEach**-aktivitet som upprepas över en lista med namn på källtabeller och som skickas som en parameter till pipelinen. För varje källtabell anropas följande aktiviteter som utför deltainläsningen för tabellen. 
    2. Skapa två **lookupaktiviteter**. Använd den första lookup-aktiviteten för att hämta det sista vattenstämpelvärdet. Använd den andra lookup-aktiviteten för att hämta det nya vattenstämpelvärdet. Vattenstämpelvärdena skickas till kopieringsaktiviteten. 
    3. Skapa en **kopieringsaktivitet** som kopierar raderna från källdatalagringen med värdet för vattenstämpelkolumnen som är större än det gamla värdet och mindre än det nya. Sedan kopieras deltadata från källdatalagringen till en blog-lagring som en ny fil. 
    4. Skapa en **lagrad proceduraktivitet** som uppdaterar vattenstämpelvärdet för den pipeline som körs nästa gång. 

        Här är ett högnivådiagram för lösningen: 

        ![Läsa in data stegvis](media\tutorial-incremental-copy-multiple-tables-powershell\high-level-solution-diagram.png)


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav
* **SQL Server**. Du använder en lokal SQL Server-databas som **källdatalager** i den här självstudien. 
* **Azure SQL Database**. Du använder en Azure SQL-databas som **måldatalager**. Om du inte har någon Azure SQL-databas kan du läsa om hur du skapar en i [Skapa en Azure SQL-databas](../sql-database/sql-database-get-started-portal.md). 

### <a name="create-source-tables-in-your-sql-server-database"></a>Skapa källtabeller i din SQL Server-databas

1. Starta **SQL Server Management Studio** och anslut till din lokala SQL-server. 
2. I **Server Explorer** högerklickar du på databasen och väljer **Ny fråga**.
3. Kör följande SQL-kommando mot databasen för att skapa tabeller med namnen `customer_table` och `project_table`.

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

### <a name="create-destination-tables-in-your-azure-sql--database"></a>Skapa måltabeller i din Azure SQL-databas
1. Starta **SQL Server Management Studio** och anslut till din Azure SQL-server. 
2. I **Server Explorer** högerklickar du på **databasen** och väljer **Ny fråga**.
3. Kör följande SQL-kommando mot din Azure SQL-databas för att skapa tabeller med namnen `customer_table` och `project_table`.  
    
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

### <a name="create-another-table-in-azure-sql-database-to-store-the-high-watermark-value"></a>Skapa en annan tabell i Azure SQL-databasen för att lagra värdet för högvattenmärket
1. Kör följande SQL-kommando mot din Azure SQL-databas för att skapa en tabell med namnet `watermarktable` för att lagra värdet för högvattenmärket.  
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
3. Infoga inledande värden för högvattenmärket för båda källtabellerna i vattenmärkestabellen.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-azure-sql-database"></a>Skapa en lagrad procedur i Azure SQL Database 

Kör följande kommando för att skapa en lagrad procedur i din Azure SQL-databas. Den här lagrade proceduren uppdaterar vattenmärkesvärdet efter varje pipelinekörning. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures"></a>Skapa datatyper och ytterligare lagrade procedurer
Skapa två lagrade procedurer och två datatyper i din Azure SQL-databas genom att köra följande fråga: de används till att sammanfoga data från källtabellerna i måltabellerna.

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

[!INCLUDE [data-factory-quickstart-prerequisites-2](../../includes/data-factory-quickstart-prerequisites-2.md)]

## <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Till exempel: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen
2. Definiera en variabel för datafabrikens plats: 

    ```powershell
    $location = "East US"
    ```
3. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen. 
3. Definiera en variabel för datafabrikens namn. 

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn måste vara unikt globalt. Till exempel ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Kör följande cmdlet av typen **Set-AzureRmDataFactoryV2** för att skapa en datafabrik: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.
* För närvarande kan du endast skapa datafabriker i Data Factory version 2 i regionerna USA, östra, USA östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]



## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I det här avsnittet får du skapa länkade tjänster till din lokala SQL Server-databas och din Azure SQL-databas. 

### <a name="create-sql-server-linked-service"></a>Skapa länkad tjänst till SQL Server.
I det här steget länkar du din lokala SQL Server till datafabriken.

1. Skapa en JSON-fil med namnet **SqlServerLinkedService.json** i mappen **C:\ADFTutorials\IncCopyMultiTableTutorial** med följande innehåll: välj rätt avsnitt baserat på vilken **autentisering** du använder när du ansluter till SQL Server. Skapa de lokala mapparna om de inte redan finns. 

    > [!IMPORTANT]
    > Välj rätt avsnitt baserat på vilken **autentisering** du använder när du ansluter till SQL Server.

    **Om du använder SQL-autentisering (sa) kopierar du följande JSON-definition:**

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
    **Om du använder Windows-autentisering kopierar du följande JSON-definition:**

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
    > - Välj rätt avsnitt baserat på vilken **autentisering** du använder när du ansluter till SQL Server.
    > - Ersätt **&lt;integration** **runtime** **name>** med namnet på din Integration Runtime.
    > - Ersätt **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, och **&lt;password>** med värdena för din SQL Server innan du sparar filen.
    > - Om du behöver använda ett snedstreck (`\`) i ditt användarkonto eller användarnamn använder du escape-tecknet (`\`). Till exempel `mydomain\\myuser`.

2. I **Azure PowerShell** växlar du till mappen **C:\ADFTutorials\IncCopyMultiTableTutorial**.
3. Kör cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureStorageLinkedService**. I följande exempel skickar du värden för parametrarna **ResourceGroupName** och **DataFactoryName**. 

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

### <a name="create-azure-sql-database-linked-service"></a>Skapa länkad Azure SQL Database-tjänst.
1. Skapa en JSON-fil med namnet **AzureSQLDatabaseLinkedService.json** i mappen **C:\ADFTutorials\IncCopyMultiTableTutorial** med följande innehåll: (skapa mappen ADF om den inte redan finns). Ersätt **&lt;server&gt;, &lt;database&gt;, &lt;user id&gt; och &lt;password&gt;** med namnen för din Azure SQL-server, databas, ditt användar-ID och lösenord innan du sparar filen. 

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
2. I **Azure PowerShell** kör du cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureSQLDatabaseLinkedService**. 

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
I det här steget skapar du datamängder för att representera datakälla, datamål och plats för att lagra högvattenmärket.

### <a name="create-a-source-dataset"></a>Skapa en källdatauppsättning

1. Skapa en JSON-fil med namnet **SourceDataset.json** i samma mapp med följande innehåll: 

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
1.  Kör cmdleten Set-AzureRmDataFactoryV2Dataset för att skapa datauppsättningen: SourceDataset
    
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

1. Skapa en JSON-fil med namnet **SinkDataset.json** i samma mapp med följande innehåll: tableName anges dynamiskt av pipelinen vid körning. ForEach-aktiviteten i pipelinen upprepas över en lista med tabellnamn och skickar tabellnamnet till datamängden i varje iteration. 

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

2.  Kör cmdleten Set-AzureRmDataFactoryV2Dataset för att skapa datauppsättningen: SinkDataset
    
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

### <a name="create-a-dataset-for-watermark"></a>Skapa en datauppsättning för vattenstämpel
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
2.  Kör cmdleten Set-AzureRmDataFactoryV2Dataset för att skapa datauppsättningen: WatermarkDataset
    
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
Den här pipelinen tar en lista med tabellnamn som en parameter. **ForEach-aktiviteten** upprepas över listan med tabellnamn och utför följande åtgärder: 

1. Använd **lookup-aktiviteten** till att hämta det gamla högvattenmärket (startvärdet eller det som användes i den senaste iterationen).
2. Använd **lookup-aktiviteten** till att hämta det nya högvattenmärket (högsta värdet i kolumnen watermark i källtabellen).
3. Använd **copy-aktiviteten** till att kopiera data mellan de två vattenmärkesvärdena från källdatabasen till måldatabasen. 
4. Använd **stored procedure-aktiviteten** till att uppdatera det gamla högvattenmärket som ska användas i det första steget i nästa iteration. 

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
                                    "SinkTableName": "@{item().TABLE_NAME}"
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
2. Kör cmdleten Set-AzureRmDataFactoryV2Pipeline för att skapa pipelinen: IncrementalCopyPipeline.
    
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
 
## <a name="run-the-pipeline"></a>Kör pipelinen

1. Skapa en parameterfil med namnet **Parameters.json** i samma mapp med följande innehåll:

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
2. Kör pipelinen: **IncrementalCopyPipeline** med cmdleten **Invoke-AzureRmDataFactoryV2Pipeline**. Ersätt platshållarna med din egen grupp och datafabrikens namn.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Övervaka pipelinen

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Fler tjänster**, sök med nyckelordet `data factories` och välj **Datafabriker**. 

    ![Menyn Datafabriker](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-data-factories-menu-1.png)
3. Sök efter **din datafabrik** i listan med datafabriker och välj den så att du öppnar sidan Datafabrik. 

    ![Sök efter din datafabrik](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-search-data-factory-2.png)
4. På sidan Datafabrik klickar du på panelen **Övervaka och hantera**. 

    ![Ikonen Övervaka och hantera](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-monitor-manage-tile-3.png)    
5. Programmet **Data Integration** öppnas i en separat flik. Du kan se alla **pipelinekörningar** och deras status. Lägg i följande exempel märke till att statusen för pipelinekörningen är **Lyckades**. Du kan kontrollera parametrarna som skickats till pipelinen genom att klicka på länken i kolumnen **Parametrar**. Om det uppstod ett fel ser du en länk i kolumnen **Fel**. Klicka på länken i kolumnen **Åtgärder**. 

    ![Pipelinekörningar](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-4.png)    
6. När du klickar på länken i kolumnen **Åtgärder** ser du följande sida, som visar alla **aktivitetskörningar** för pipelinen. 

    ![Aktivitetskörningar](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-activity-runs-5.png)
7. Om du vill växla tillbaka till vyn **Pipelinekörningar** klickar du på **Pipelines** enligt bilden. 

## <a name="review-the-results"></a>Granska resultatet
Kör följande frågor mot Azure SQL-måldatabasen i SQL Server Management Studio för att verifiera att data har kopierats från källtabellerna till måltabellerna. 

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

**Fråga:**

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

Observera att värdena för högvattenmärket har uppdaterats för båda tabellerna. 

## <a name="add-more-data-to-the-source-tables"></a>Lägga till mer data i källtabellerna

Kör följande fråga mot SQL Server-källdatabasen för att uppdatera en befintlig rad i customer_table och infoga en ny rad i project_table. 

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
2. Övervaka pipelinekörningarna genom att följa anvisningarna i avsnittet [Övervaka pipelinen](#monitor-the-pipeline). När pipelinens status är **Pågår** ser du en annan åtgärdslänk under **Åtgärder** för att avbryta pipelinekörningen. 

    ![Pipelinekörningar](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-6.png)    
3. Klicka på **Uppdatera** om du vill uppdatera listan tills pipelinekörningen lyckas. 

    ![Pipelinekörningar](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-succeded-7.png)
4. (valfritt) Klicka på länken **Visa aktivitetskörningar** (ikon) under Åtgärder om du vill se alla aktivitetskörningar som är associerade med den här pipelinekörningen. 

## <a name="review-final-results"></a>Granska slutresultatet
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

Lägg märke till de nya värdena för Name och LastModifytime för PersonID: 3. 

**Fråga:**

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

Observera att posten NewProject har lagts till i project_table. 

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

Observera att värdena för högvattenmärket har uppdaterats för båda tabellerna.
     
## <a name="next-steps"></a>Nästa steg
I den här självstudien har du fått: 

> [!div class="checklist"]
> * Förbereda käll- och måldatalager.
> * Skapa en datafabrik.
> * Skapa Integration Runtime (IR) med egen värd
> * Installera Integration Runtime 
> * Skapa länkade tjänster. 
> * Skapa datauppsättningar för källa, mottagare, vattenstämpel.
> * Skapa, köra och övervaka en pipeline.
> * Granska resultatet
> * Lägga till eller uppdatera data i källtabeller
> * Köra och övervaka pipelinen igen
> * Granska slutresultatet 

Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Läs in data stegvis från Azure SQL Database till Azure Blob Storage med ändringsspårningsteknik](tutorial-incremental-copy-multiple-tables-powershell.md)


