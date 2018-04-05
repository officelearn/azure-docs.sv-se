---
title: Skapa en Azure-datafabrik med .NET | Microsoft Docs
description: Skapa en Azure-datafabrik för att kopiera data från en plats i Azure Blob Storage till en annan plats.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: c5b7af290a5e5c45d3f64ccb50586db0811dd592
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Skapa en datafabrik och pipeline med .NET SDK
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 – förhandsversion](quickstart-create-data-factory-dot-net.md)

Den här snabbstarten beskriver hur du använder .NET SDK till att skapa en Azure-datafabrik. Den pipeline du skapar i den här datafabriken **kopierar** data från en mapp till en annan mapp i Azure Blob Storage. Om du vill se en självstudie som visar hur du **omvandlar** data med Azure Data Factory går du till [Tutorial: Transform data using Spark](transform-data-using-spark.md) (Självstudie: Omvandla data med Spark). 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Läs [get started with Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (kom igång med Data Factory version 1) om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig.
>
> Den här artikeln ger inte någon detaljerad introduktion till Azure Data Factory-tjänsten. En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen. I Azure-portalen klickar du på ditt **användarnamn** i det övre högra hörnet och väljer **Behörigheter** för att visa de behörigheter du har i prenumerationen. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Azure Storage-konto
I den här snabbstarten använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för både **källa** och **destination**. Om du inte har något allmänt Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account). 

#### <a name="get-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för Azure Storage-kontot i den här snabbstarten. Följande procedur innehåller steg för att få fram namnet och nyckeln för ditt lagringskonto. 

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com). Logga in med ditt Azure-användarnamn och lösenord. 
2. Klicka på **Fler tjänster >** i den vänstra menyn, filtrera på nyckelordet **Lagring** och välj **Lagringskonton**.

    ![Sök efter lagringskontot](media/quickstart-create-data-factory-dot-net/search-storage-account.png)
3. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton och välj sedan **ditt lagringskonto**. 
4. Gå till sidan **Lagringskonto** väljer du **Åtkomstnycklar** i menyn.

    ![Hämta lagringskontots namn och nyckel](media/quickstart-create-data-factory-dot-net/storage-account-name-key.png)
5. Kopiera värdena från fälten med **lagringskontots namn** och **nyckel 1** till Urklipp. Klistra in dem i Anteckningar eller något annat redigeringsprogram och spara.  

#### <a name="create-input-folder-and-files"></a>Skapa indatamapp och filer
Det här avsnittet förutsätter att du har en blobbehållare med namnet **adftutorial** i Azure Blob Storage. Skapa en mapp med namnet **input** i behållaren och ladda upp en exempelfil i indatamappen. 

1. På sidan **Lagringskonto** växlar du till **Översikt** och klickar på **Blobbar**. 

    ![Alternativet Välj blobar](media/quickstart-create-data-factory-dot-net/select-blobs.png)
2. På sidan **Blob Service** klickar du på **+ Behållare** i verktygsfältet. 

    ![Lägga till behållarknapp](media/quickstart-create-data-factory-dot-net/add-container-button.png)    
3. I dialogrutan **Ny behållare** anger du **adftutorial** som namn och klickar på **OK**. 

    ![Ange namn på behållare](media/quickstart-create-data-factory-dot-net/new-container-dialog.png)
4. Klicka på **adftutorial** i listan över behållare. 

    ![Välja behållaren](media/quickstart-create-data-factory-dot-net/select-adftutorial-container.png)
1. På sidan **Behållare** klickar du på **Ladda upp** i verktygsfältet.  

    ![Knappen för överföring](media/quickstart-create-data-factory-dot-net/upload-toolbar-button.png)
6. På sidan **Ladda upp blob** klickar du på **Avancerat**.

    ![Klicka på länken Avancerat](media/quickstart-create-data-factory-dot-net/upload-blob-advanced.png)
7. Öppna **Anteckningar** och skapa en fil med namnet **emp.txt** med följande innehåll: Spara den i mappen **c:\ADFv2QuickStartPSH**: Skapa mappen **ADFv2QuickStartPSH** om den inte redan finns.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. På sidan **Ladda upp blob** i Azure-portalen bläddrar du och väljer filen **emp.txt** för fältet **Filer**. 
9. Ange **input** som värdet som **Ladda upp till mapp** arkiverade. 

    ![Ladda upp blobinställningar](media/quickstart-create-data-factory-dot-net/upload-blob-settings.png)    
10. Kontrollera att mappen är **input** och att filen är **emp.txt** och klicka på **Ladda upp**.
11. Du bör se filen **emp.txt** och uppladdningens status i listan. 
12. Stäng sidan **Ladda upp blob** genom att klicka på **X** i hörnet. 

    ![Stänga sidan Ladda upp blob](media/quickstart-create-data-factory-dot-net/close-upload-blob.png)
1. Låt **behållarsidan** vara öppen. Du kommer att använda den för att bekräfta utdata i slutet av snabbstarten.

### <a name="visual-studio"></a>Visual Studio
I den här artikeln används Visual Studio 2017. Du kan också använda Visual Studio 2013 eller 2015.

### <a name="azure-net-sdk"></a>SDK för Azure .NET
Ladda ned och installera [Azure .NET SDK](http://azure.microsoft.com/downloads/) på datorn.

### <a name="create-an-application-in-azure-active-directory"></a>Skapa ett program i Azure Active Directory
Utför följande uppgifter genom att följa anvisningarna i avsnitten i [den här artikeln](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application): 

1. **Skapa ett program i Azure Active Directory**. Skapa ett program i Azure Active Directory som representerar .NET-programmet du skapar i den här självstudien. För inloggnings-URL kan du ange en låtsas-URL enligt artikeln (`https://contoso.org/exampleapp`).
2. Hämta **program-ID:t** och **autentiseringsnyckeln**, och skriv ned dessa värden, vilka du kommer att använda senare i den här kursen. 
3. Hämta **klientorganisations-ID:t** och skriv ned det här värdet, vilket du kommer att använda senare i den här kursen.
4. Tilldela programmet till rollen **Deltagare** på prenumerationsnivå så programmet kan skapa datafabriker i prenumerationen.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

Skapa ett C# .NET-konsolprogram med hjälp av Visual Studio 2013/2015/2017.

1. Starta **Visual Studio**.
2. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**.
3. Välj **Visual C#** -> **Konsolprogram (.NET Framework)** i listan över projekttyper till höger. .NET version 4.5.2 eller senare krävs.
4. Ange **ADFv2QuickStart** som namn.
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
    ```

2. Lägg till följande kod till **Main**-metoden som anger variablerna. Ersätt platshållarna med dina egna värden. För närvarande kan du endast skapa datafabriker i Data Factory V2 i regionerna USA, östra; USA; östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
    ```

3. Lägg till följande kod till **Main**-metoden som skapar en instans av klassen **DataFactoryManagementClient**. Du kan använda det här objektet för att skapa en datafabrik, en länkad tjänst, datauppsättningar och en pipeline. Du kan också använda det här objektet för att övervaka information om pipelinekörning.

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
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
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

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst

Lägg till följande kod i **Main**-metoden som skapar en **länkad Azure Storage-tjänst**.

Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här snabbstarten behöver du bara skapa en Azure Storage-länkad tjänst som både kopia på källa och mottagarlagring, med namnet "AzureStorageLinkedService" i exemplet.

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

## <a name="create-a-dataset"></a>Skapa en datamängd

Lägg till följande kod till **Main**-metoden som skapar en **Azure-blobdatauppsättning**.

Du definierar en datauppsättning som representerar data som ska kopieras från en källa till en mottagare. I det här exemplet refererar denna blob-datauppsättning till den Azure Storage-länkade tjänst som du skapade i föregående steg. Datauppsättningen tar en parameter vars värde anges i en aktivitet som förbrukar datauppsättningen. Parametern används för att konstruera "folderPath" som pekar mot där data finns/lagras.

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
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Skapa en pipeline

Lägg till följande kod som skapar och **aktiverar en pipeline** till **Main**-metoden.

I det här exemplet innehåller denna pipeline en aktivitet och tar två parametrar – sökvägen till indata-blob och sökvägen till utdata-blob. Värdena för dessa parametrar anges när pipeline utlöses/körs. Kopieringsaktiviteten refererar till samma blobdatauppsättning som skapats i föregående steg som indata och utdata. När datauppsättningen används som indatauppsättning anges indatasökvägen. Och när datauppsättningen används som utdatauppsättning anges utdatasökvägen. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning

Lägg till följande kod i **Main**-metoden för att **utlösa en pipelinekörning**.

Med den här koden anger du också värden för parametrarna **inputPath** och **outputPath** som anges i pipeline med de faktiska värdena för sökvägarna för käll- och mottagar-blob.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Övervaka en pipelinekörning

1. Lägg till följande kod i **Main**-metoden för att kontinuerligt kontrollera status tills den har slutat att kopiera data.

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
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Kör koden

Skapa och starta programmet och kontrollera sedan pipelinekörningen.

Konsolen skriver ut förloppet för skapandet av datafabriken, den länkade tjänsten, datauppsättningar, pipeline och pipelinekörning. Sedan kontrolleras status för pipelinekörningen. Vänta tills du ser information om körningen av kopieringsaktiviteten med storlek för lästa/skrivna data. Använd sedan verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att kontrollera de blob(ar) som kopierats till outputBlobPath från inputBlobPath som du angav i variablerna.

### <a name="sample-output"></a>Exempel på utdata: 
```json
Creating data factory SPv2Factory0907...
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
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verifiera utdata
Pipelinen skapar automatiskt utdatamappen i blobbehållaren adftutorial. Filen emp.txt kopieras från indatamappen till utdatamappen. 

1. På behållarsidan **adftutorial** i Azure-portalen klickar du på **Uppdatera** för att se utdatamappen. 
    
    ![Uppdatera](media/quickstart-create-data-factory-dot-net/output-refresh.png)
2. Klicka på **output** i mapplistan. 
2. Kontrollera att **emp.txt** har kopierats till utdatamappen. 

    ![Uppdatera](media/quickstart-create-data-factory-dot-net/output-file.png)

## <a name="clean-up-resources"></a>Rensa resurser
För att programmässigt ta bort datafabriken lägger du till följande rader med kod till programmet: 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
