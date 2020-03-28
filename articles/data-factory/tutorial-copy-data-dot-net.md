---
title: Kopiera data från Azure Blob Storage till Azure SQL Database
description: Den här självstudien innehåller stegvisa instruktioner för att kopiera data från Azure Blob Storage till Azure SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: 93c4f71c762cff3e3f5a01f0e2595f3498f9d38d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977321"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Kopiera data från en Azure-blob till Azure SQL Database med Data Factory

I de här självstudierna skapar du en datafabrikpipeline som kopierar Azure Blob Storage till Azure SQL Database. Konfigurationsmönstret i den här självstudien gäller kopiering av ett filbaserat datalager till ett relationsdatalager. En lista över datalager som stöds som källor och sänkor finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa länkade Azure Storage- och Azure SQL Database-tjänster.
> * Skapa Azure Blob- och Azure SQL Database-datauppsättningar.
> * Skapa en pipeline som innehåller en kopieringsaktivitet.
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.

I den här självstudiekursen används .NET SDK. Du kan använda andra mekanismer för att interagera med Azure Data Factory. se exempel under **Snabbstarter**.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

* *Azure Storage-konto*. Du kan använda blob-lagringen som *källa* för datalagringen. Om du inte har ett Azure-lagringskonto läser du [Skapa ett allmänt lagringskonto](../storage/common/storage-account-create.md).
* *Azure SQL-databas*. Du använder databasen som *mottagare* för datalagringen. Om du inte har en Azure SQL-databas läser du [Skapa en Azure SQL-databas](../sql-database/sql-database-single-database-get-started.md).
* *Visual Studio*. Genomgången i den här artikeln används Visual Studio 2019.
* *[Azure SDK för .NET](/dotnet/azure/dotnet-tools)*.
* *Azure Active Directory-program*. Om du inte har ett Azure Active Directory-program läser du avsnittet [Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) i Så [här: Använd portalen för att skapa ett Azure AD-program](../active-directory/develop/howto-create-service-principal-portal.md). Kopiera följande värden för användning i senare steg: **Program-ID ,** **autentiseringsnyckel**och **katalog-ID (katalog (klient).** Tilldela programmet till **rollen Deltagare** genom att följa instruktionerna i samma artikel.

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered nu din Azure Blob och Azure SQL Database för självstudien genom att skapa en källblogg och en sink SQL-tabell.

#### <a name="create-a-source-blob"></a>Skapa en källblob

Skapa först en källblobb genom att skapa en behållare och överföra en indatatextfil till den:

1. Öppna Anteckningar. Kopiera följande text och spara den lokalt i en fil med namnet *inputEmp.txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Använd ett verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att skapa *adfv2tutorial-behållaren* och för att överföra *filen inputEmp.txt* till behållaren.

#### <a name="create-a-sink-sql-table"></a>Skapa en SQL-mottagartabell

Skapa sedan en sink SQL-tabell:

1. Använd följande SQL-skript för att skapa tabellen *dbo.emp* i Azure SQL Database.

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

2. Ge Azure-tjänster åtkomst till SQL-servern. Se till att du tillåter åtkomst till Azure-tjänster i din Azure SQL-server så att Data Factory-tjänsten kan skriva data till din Azure SQL-server. Gör så här för att kontrollera och aktivera den här inställningen:

    1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera din SQL-server. Sök efter och välj **SQL-servrar**.

    2. Välj server.

    3. Under sql-servermenyns **säkerhetsrubrik** väljer du **Brandväggar och virtuella nätverk**.

    4. På sidan **Brandvägg och virtuella nätverk** under Tillåt **Azure-tjänster och resurser för åtkomst till den här servern**väljer du **PÅ**.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

Skapa ett C# .NET-konsolprogram med Visual Studio.

1. Öppna Visual Studio.
2. Välj Skapa ett **nytt projekt**i **startfönstret** .
3. I fönstret **Skapa ett nytt projekt** väljer du C#-versionen av Console App **(.NET Framework)** i listan över projekttyper. Välj sedan **Nästa**.
4. Ange ett **projektnamn** för *ADFv2Tutorial i*fönstret **Konfigurera det nya projektet** . För **Plats**bläddrar du till och/eller skapar katalogen som projektet ska sparas i. Välj sedan **Skapa**. Det nya projektet visas i Visual Studio IDE.

## <a name="install-nuget-packages"></a>Installera NuGet-paket

Installera sedan de nödvändiga bibliotekspaketen med NuGet-pakethanteraren.

1. Välj **Verktyg** > **NuGet Package Manager** > Package Manager Console i menyraden .**Package Manager Console**
2. I fönstret **Package Manager Console** kör du följande kommandon för att installera paket. Information om Azure Data Factory NuGet-paketet finns i [Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Skapa en datafabriksklient

Följ dessa steg för att skapa en datafabriksklient.

1. Öppna *Program.cs*och skriv sedan över `using` befintliga satser med följande kod för att lägga till referenser till namnområden.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Lägg till följande `Main` kod i metoden som anger variabler. Ersätt de 14 platshållarna med dina egna värden.

    Information om hur du ser listan över Azure-regioner där Data Factory är tillgängligt finns i Produkter som är [tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/). Under listrutan **Produkter** väljer du **Bläddra bland** > **Analytics** > Data**Factory**. Välj sedan de regioner som intresserar dig i listrutan **Regioner.** Ett rutnät visas med tillgänglighetsstatus för Data Factory-produkter för dina valda regioner.

    > [!NOTE]
    > Datalager, till exempel Azure Storage och Azure SQL Database, och beräkningar, till exempel HDInsight, som Data Factory använder kan finnas i andra regioner än vad du väljer för Data Factory.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Lägg till följande `Main` kod i metoden som `DataFactoryManagementClient` skapar en förekomst av klass. Du använde det är objektet till att skapa en datafabrik, länkade tjänster, datauppsättningar och en pipeline. Du kan också använda det här objektet för att övervaka information om pipelinekörning.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Lägg till följande `Main` kod i metoden som skapar en *datafabrik*.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Skapa länkade tjänster

I den här självstudien skapar du två länkade tjänster för käll- och diskbänken.

### <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst

Lägg till följande `Main` kod i metoden som skapar en *Azure Storage-länkad tjänst*. Information om egenskaper och information som stöds finns i [Azure Blob-länkade tjänstegenskaper](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst

Lägg till följande `Main` kod i metoden som skapar en *Azure SQL Database-länkad tjänst*. Information om egenskaper och information som stöds finns i [Azure SQL Database-länkade tjänstegenskaper](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Skapa datauppsättningar

I det här avsnittet skapar du två datauppsättningar: en för källan, den andra för diskhon.

### <a name="create-a-dataset-for-source-azure-blob"></a>Skapa en datauppsättning för Azure Blob-källan

Lägg till följande `Main` kod i metoden som skapar en *Azure blob-datauppsättning*. Information om egenskaper och information som stöds finns i [egenskaper för Azure Blob-datauppsättning](connector-azure-blob-storage.md#dataset-properties).

Du definierar en datauppsättning som representerar källdata i Azure Blob. Denna Blob-datauppsättning refererar till den Azure Storage-länkade tjänst som du skapar i föregående steg. Den beskriver:

- Platsen för blobben att `FolderPath` kopiera från: och`FileName`
- Blob-formatet som anger hur innehållet `TextFormat` ska tolkas: och dess inställningar, till exempel kolumnavgränsare
- Datastrukturen, inklusive kolumnnamn och datatyper, som i det här exemplet mappas till sink-SQL-tabellen

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Skapa en datauppsättning för Azure SQL Database-mottagaren

Lägg till följande `Main` kod i metoden som skapar en *Azure SQL Database-datauppsättning*. Information om egenskaper och information som stöds finns i [egenskaper för Azure SQL Database-datauppsättning](connector-azure-sql-database.md#dataset-properties).

Du definierar en datauppsättning som representerar mottagardata i Azure SQL Database. Den här datauppsättningen refererar till den azure SQL Database-länkade tjänst som du skapade i föregående steg. Den anger också vilken SQL-tabell som innehåller de kopierade data.

```csharp
// Create an Azure SQL Database dataset
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

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Skapa en pipeline

Lägg till följande `Main` kod i metoden som skapar en *pipeline med en kopieringsaktivitet*. I den här självstudien innehåller `CopyActivity`den här pipelinen en aktivitet: , som tar in Blob-datauppsättningen som källa och SQL-datauppsättningen som mottagare. Information om information om kopieringsaktivitet finns [i Kopiera aktivitet i Azure Data Factory](copy-activity-overview.md).

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
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning

Lägg till följande `Main` kod i metoden som *utlöser en pipeline-körning*.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Övervaka en pipelinekörning

Nu infoga koden för att kontrollera pipeline kör tillstånd och för att få information om kopieringen aktivitet köra.

1. Lägg till följande `Main` kod i metoden för att kontinuerligt kontrollera status för pipelinekörningen tills data har kopierats.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Lägg till följande `Main` kod i metoden som hämtar information om kopieringsaktivitetskörning, till exempel storleken på de data som lästes eller skrevs.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Kör koden

Skapa programmet genom att välja > **Byggversionslösning**. **Build** Starta sedan programmet genom att välja **Felsökning** > **av startfelsökning**och verifiera pipelinekörningen.

Konsolen skriver ut förloppet för skapandet av en datafabrik, den länkade tjänsten, datauppsättningar, pipeline och pipelinekörning. Sedan kontrolleras status för pipelinekörningen. Vänta tills du ser information om kopieringsaktivitetskörning med dataläs/skriftlig storlek. Med hjälp av verktyg som SQL Server Management Studio (SSMS) eller Visual Studio kan du sedan ansluta till ditt mål Azure SQL Database och kontrollera om måltabellen du angav innehåller de kopierade data.

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
  "usedDataIntegrationUnits": 2,
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
