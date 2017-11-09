---
title: "Kopiera data från Azure Blob Storage till SQL Database | Microsoft Docs"
description: "Den här självstudien innehåller stegvisa instruktioner för att kopiera data från Azure Blob Storage till Azure SQL Database."
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
ms.date: 10/06/2017
ms.author: jingwang
ms.openlocfilehash: 63a59dbfa9c9adab1354c89f35fe649213edf3bc
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Kopiera data från en Azure-blob till Azure SQL Database med Data Factory

[!INCLUDE [data-factory-what-is-include-md](../../includes/data-factory-what-is-include.md)]

#### <a name="this-tutorial"></a>Den här självstudien

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

I de här självstudierna skapar du en datafabrikpipeline som kopierar Azure Blob Storage till Azure SQL Database. Konfigurationsmönstret i den här självstudien gäller kopiering av ett filbaserat datalager till ett relationsdatalager. En lista över datakällor som stöds som källor och mottagare finns i tabellen över [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

I de här självstudierna går du igenom följande steg:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa länkade Azure Storage- och Azure SQL Database-tjänster.
> * Skapa Azure Blob- och Azure SQL Database-datauppsättningar.
> * Skapa en pipeline som innehåller en kopieringsaktivitet.
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningar.

I den här självstudiekursen används .NET SDK. Du kan använda andra metoder för att interagera med Azure Data Factory. Exempel finns i ”Snabbstarter”.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* **Azure Storage-konto**. Du kan använda blob-lagringen som **källa** för datalagringen. Om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) .
* **Azure SQL Database**. Du använder databasen som **mottagare** för datalagringen. Om du inte har någon Azure SQL Database läser du [Skapa en Azure SQL Database](../sql-database/sql-database-get-started-portal.md) för att lära dig hur du skapar en.
* **Visual Studio** 2015 eller 2017. I den här genomgången används Visual Studio 2017.
* **Ladda ned och installera [Azure .NET SDK](http://azure.microsoft.com/downloads/)**.
* **Skapa en app i Azure Active Directory** med hjälp av [den här instruktionen](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Notera följande värden som du använder i senare steg: **program-ID**, **autentiseringsnyckel** och **klient-ID**. Tilldela program till rollen **Deltagare** enligt instruktionerna i samma artikel.

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered nu Azure-blobblagringen och Azure SQL-databasen för den här självstudien genom att utföra följande steg:

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Öppna Anteckningar. Kopiera följande text och spara den som en **inputEmp.txt**-fil på din disk.

    ```
    John|Doe
    Jane|Doe
    ```

2. Använd verktyg som t.ex. [Azure Lagringsutforskaren](http://storageexplorer.com/) till att skapa behållaren **adfv2tutorial** och för att ladda upp filen **inputEmp.txt** till behållaren.

#### <a name="create-a-sink-sql-table"></a>Skapa en SQL-mottagartabell

1. Använd följande SQL-skript för att skapa tabellen **dbo.emp** i din Azure SQL Database.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Ge Azure-tjänster åtkomst till SQL-servern. Kontrollera att inställningen **Tillåt åtkomst till Azure-tjänster** är **PÅ** för din Azure SQL-server så att tjänsten Data Factory kan skriva data till din Azure SQL-server. Gör så här för att kontrollera och aktivera den här inställningen:

    1. Klicka på hubben **Fler tjänster** till vänster och klicka på **SQL-servrar**.
    2. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
    3. På sidan **Brandväggsinställningar** väljer du **På** för **Tillåt åtkomst till Azure-tjänster**.


## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

Skapa ett C# .NET-konsolprogram med hjälp av Visual Studio 2015/2017.

1. Starta **Visual Studio**.
2. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**.
3. Välj **Visual C#** -> **Konsolprogram (.NET Framework)** i listan över projekttyper till höger. .NET version 4.5.2 eller senare krävs.
4. Ange **ADFv2Tutorial** som namn.
5. Klicka på **OK** för att skapa projektet.

## <a name="install-nuget-packages"></a>Installera NuGet-paket

1. Klicka på **Verktyg** -> **NuGet Package Manager** -> **Package Manager Console**.
2. Kör följande kommandon i **Package Manager Console** för att installera paket:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Skapa en datafabriksklient

1. Öppna **Program.cs**, lägg till följande instruktioner för att lägga till referenser till namnområden.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;

2. Add the following code to the **Main** method that sets variables. Replace place-holders with your own values. Currently, Data Factory V2 allows you to create data factories only in the East US, East US2, and West Europe regions. The data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Lägg till följande kod till **Main**-metoden som skapar en instans av klassen **DataFactoryManagementClient**. Du använde det är objektet till att skapa en datafabrik, länkade tjänster, datauppsättningar och en pipeline. Du kan också använda det här objektet för att övervaka information om pipelinekörning.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Lägg till följande kod som skapar en **datafabrik** till **Main**-metoden.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Skapa länkade tjänster

I den här självstudien skapar du två länkade tjänster för källa respektive mottagare:

### <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst

Lägg till följande kod som skapar en **länkad Azure Storage-tjänst** till **Main**-metoden. Mer information om vilka egenskaper som stöds finns i avsnittet om [länkade tjänstegenskaper för Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst

Lägg till följande kod i **Main**-metoden som skapar en **länkad Azure SQL Database-tjänst**. Mer information om vilka egenskaper som stöds finns i avsnittet om [länkade tjänstegenskaper för Azure SQL Database](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Skapa datauppsättningar

I det här avsnittet skapar du två datauppsättningar: en för källan och en för mottagaren. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Skapa en datauppsättning för Azure Blob-källan

Lägg till följande kod i **Main**-metoden som skapar en **Azure-blobdatauppsättning**. Mer information om vilka egenskaper som stöds finns i avsnittet om [egenskaper för Azure-blobdatauppsättningar](connector-azure-blob-storage.md#dataset-properties).

Du definierar en datauppsättning som representerar källdata i Azure Blob. Denna Blob-datauppsättning refererar till den Azure Storage-länkade tjänst som du skapar i föregående steg. Den beskriver:

- Platsen för den blob som du kopierar från: **FolderPath** och **FileName**;
- Blobformatet indikerar hur innehållet ska parsas: **TextFormat** och dess inställningar (till exempel kolumnavgränsare).
- Datastrukturen, inklusive kolumnnamn och datatyper som i det här fallet mappar till mottagar-SQL-tabellen.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Skapa en datauppsättning för Azure SQL Database-mottagaren

Lägg till följande kod i **Main**-metoden som skapar en **Azure SQL Database-datauppsättning**. Mer information om vilka egenskaper som stöds finns i avsnittet om [egenskaper för Azure SQL Database-datauppsättningar](connector-azure-sql-database.md#dataset-properties).

Du definierar en datauppsättning som representerar mottagardata i Azure SQL Database. Denna datauppsättning refererar till den Azure SQL Database-länkade tjänst som du skapar i föregående steg. Den anger också vilken SQL-tabell som innehåller de kopierade data. 

```csharp
// Create a Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Skapa en pipeline

Lägg till följande kod som skapar och **aktiverar en pipeline** till **Main**-metoden. I den här självstudiekursen innehåller pipelinen en aktivitet: kopieringsaktiviteten, vilken tar Blob-datauppsättningen som en källa och SQL-datauppsättningen som mottagare. Läs mer i [Översikt över kopieringsaktivitet](copy-activity-overview.md) om kopiering.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning

Lägg till följande kod i **Main**-metoden som **utlöser en pipelinekörning**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Övervaka en pipelinekörning

1. Lägg till följande kod i **Main**-metoden för att kontinuerligt kontrollera status för pipelinekörningen tills den har slutat att kopiera data.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Lägg till följande kod i **Main**-metoden för att ta emot information om körningen av kopieringsaktiviteten, till exempel storleken på lästa/skrivna data.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Kör koden

Skapa och starta programmet och kontrollera sedan pipelinekörningen.

Konsolen skriver ut förloppet för skapandet av en datafabrik, den länkade tjänsten, datauppsättningar, pipeline och pipelinekörning. Sedan kontrolleras status för pipelinekörningen. Vänta tills du ser information om körningen av kopieringsaktiviteten med storlek för lästa/skrivna data. Använd sedan verktyg som SSMS (SQL Server Management Studio) eller Visual Studio för att ansluta till ditt mål för Azure SQL Database och kontrollera om data har kopierats till den angivna tabellen.

### <a name="sample-output"></a>Exempel på utdata

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedCloudDataMovementUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa länkade Azure Storage- och Azure SQL Database-tjänster.
> * Skapa Azure Blob- och Azure SQL Database-datauppsättningar.
> * Skapa en pipeline som innehåller en kopieringsaktivitet.
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.


Fortsätt till följande självstudie och lär dig att kopiera data från en lokal plats till molnet: 

> [!div class="nextstepaction"]
>[Kopiera data från en lokal plats till molnet](tutorial-hybrid-copy-powershell.md)
