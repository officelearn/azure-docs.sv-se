---
title: Branchning i Azure Data Factory-pipeline | Microsoft Docs
description: "Lär dig hur du styr flödet av data i Azure Data Factory genom branchning och kedjesammansättning av aktiviteter."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 3ffe45775a55f4bdb3103fea2075fcb83e0c9cf9
ms.contentlocale: sv-se
ms.lasthandoff: 09/28/2017

---

# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Branchning och kedjesammansättning av aktiviteter i en Data Factory-pipeline
Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda. 

I den här självstudiekursen skapar du en Data Factory-pipeline som visar några av funktionerna för att styra flödet. Den här pipelinen skapar en enkel kopia från en behållare i Azure Blob Storage till en annan behållare i samma lagringskonto. Om kopieringen lyckas vill du skicka information om den lyckade åtgärden (till exempel hur mycket data som har skrivits) i ett e-postmeddelande. Om kopieringen misslyckas vill du skicka information om att kopieringen misslyckades (till exempel ett felmeddelande) i ett e-postmeddelande. I självstudiekursen visas olika exempel på hur du skickar parametrar.

En översikt på hög nivå över scenariot: ![Översikt](media/tutorial-control-flow/overview.png)

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en länkad Azure Storage-tjänst.
> * Skapa en Azure Blob-datauppsättning
> * Skapa en pipeline som innehåller en kopieringsaktivitet och en webbaktivitet
> * Skicka utdata för aktiviteter till efterföljande aktiviteter
> * Skicka parametrar och använda systemvariabler
> * Starta en pipelinekörning
> * Övervaka pipelinen och aktivitetskörningar

I den här självstudiekursen används .NET SDK. Du kan använda andra metoder för att interagera med Azure Data Factory (se Snabbstarter i innehållsförteckningen).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* **Azure Storage-konto**. Du kan använda blob-lagringen som **källa** för datalagringen. Om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) .
* **Azure SQL Database**. Du använder databasen som **mottagare** för datalagringen. Om du inte har någon Azure SQL Database kan du läsa om hur du skapar en i [Skapa en Azure SQL Database](../sql-database/sql-database-get-started-portal.md).
* **Visual Studio** 2013, 2015 eller 2017. I den här artikeln används Visual Studio 2017.
* **Ladda ned och installera [Azure .NET SDK](http://azure.microsoft.com/downloads/)**.
* **Skapa en app i Azure Active Directory** med hjälp av [de här instruktionerna](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Observera följande värden som du kommer att använda i senare steg: **program-ID**, **autentiseringsnyckel** och **klient-ID**. Tilldela program till rollen **Deltagare** enligt instruktionerna i samma artikel.

### <a name="create-blob-table"></a>Skapa blob-tabell

1. Öppna Anteckningar. Kopiera följande text och spara den som en **input.txt**-fil på din disk.

    ```
    John|Doe
    Jane|Doe
    ```
2. Använd verktyg som till exempel [Azure Storage Explorer](http://storageexplorer.com/) till att skapa behållaren **adfv2branch** och för att ladda upp filen **input.txt** till behållaren.

## <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt

Skapa ett C# .NET-konsolprogram med hjälp av Visual Studio 2015/2017.

1. Starta **Visual Studio**.
2. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**. .NET version 4.5.2 eller senare krävs.
3. Välj **Visual C#** -> **Konsolprogram (.NET Framework)** i listan över projekttyper till höger.
4. Ange **ADFv2BranchTutorial** som namn.
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

2. Add these static variables to the **Program class**. Replace place-holders with your own values.

    ```csharp
        // Set variables
        static string tenantID = "<tenant ID>";
        static string applicationId = "<application ID>";
        static string authenticationKey = "<Authentication key for your application>";
        static string subscriptionId = "<Azure subscription ID>";
        static string resourceGroup = "<Azure resource group name>";

        // Note that the data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions.
        // Currently, only East US and East US 2 regions are supported. 
        static string region = "East US";
        static string dataFactoryName = "<Data factory name>";

        // Specify the source Azure Blob information
        static string storageAccount = "<Azure Storage account name>";
        static string storageKey = "<Azure Storage account key>";
        // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
        static string inputBlobPath = "adfv2branch/input";
        static string inputBlobName = "input.txt";
        static string outputBlobPath = "adfv2branch/output";
        static string emailReceiver = "<specify email address of the receiver>";

        static string storageLinkedServiceName = "AzureStorageLinkedService";
        static string blobSourceDatasetName = "SourceStorageDataset";
        static string blobSinkDatasetName = "SinkStorageDataset";
        static string pipelineName = "Adfv2TutorialBranchCopy";

        static string copyBlobActivity = "CopyBlobtoBlob";
        static string sendFailEmailActivity = "SendFailEmailActivity";
        static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
    
    ```

3. Lägg till följande kod till **Main**-metoden som skapar en instans av klassen **DataFactoryManagementClient**. Du använde det är objektet till att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Du kan också använda det här objektet för att övervaka information om pipelinekörning.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik
Skapa en ”CreateOrUpdateDataFactory”-funktion i filen Program.cs:

```csharp
static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating data factory " + dataFactoryName + "...");
    Factory resource = new Factory
    {
        Location = region
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

    Factory response;
    {
        response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
    }

    while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
    {
        System.Threading.Thread.Sleep(1000);
    }
    return response;
}
```



Lägg till följande kod som skapar en **datafabrik** till **Main**-metoden. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst
Skapa en ”StorageLinkedServiceDefinition”-funktion i filen Program.cs:

```csharp
static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
    AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
    LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
    return linkedService;
}
```
Lägg till följande kod i **Main**-metoden som skapar en **länkad Azure Storage-tjänst**. Mer information om vilka egenskaper som stöds finns i avsnittet om [länkade tjänstegenskaper för Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties).

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Skapa datauppsättningar

I det här avsnittet skapar du två datauppsättningar: en för källan och en för mottagaren. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Skapa en datauppsättning för Azure Blob-källan
Lägg till följande kod i **Main**-metoden som skapar en **Azure-blobdatauppsättning**. Mer information om vilka egenskaper som stöds finns i avsnittet om [egenskaper för Azure-blobdatauppsättningar](connector-azure-blob-storage.md#dataset-properties).

Du definierar en datauppsättning som representerar källdata i Azure Blob. Denna Blob-datauppsättning refererar till den Azure Storage-länkade tjänst som du skapar i föregående steg. Den beskriver:

- Platsen för den blob som du kopierar från: **FolderPath** och **FileName**;
- Observera användningen av parametrar för FolderPath. ”sourceBlobContainer” är namnet på parametern och uttrycket ersätts med de värden som skickas i pipelinekörningen. Syntaxen för att definiera parametrar är `@pipeline().parameters.<parameterName>`

Skapa en ”SourceBlobDatasetDefinition”-funktion i filen Program.cs

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Skapa en datauppsättning för Azure Blob-mottagaren

Skapa en ”SourceBlobDatasetDefinition”-funktion i filen Program.cs

```csharp
static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
    return dataset;
}
```

Lägg till följande kod i **Main**-metoden som skapar datauppsättningar för både Azure Blob-källan och -mottagaren. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Skapa en C#-klass: EmailRequest
Skapa en klass med namnet **EmailRequest** i C#-projektet. Detta definierar vilka egenskaper pipelinen skickar i brödtexten i begäran när du skickar ett e-postmeddelande. I den här självstudiekursen skickar pipelinen fyra egenskaper från pipelinen till e-postmeddelandet:

- **Meddelande**: e-postmeddelandets brödtext. Vid en lyckad kopiering innehåller egenskapen information om körningen (antal data som skrivits). Vid en misslyckad kopiering innehåller egenskapen information om felet.
- **Datafabriksnamn**: namnet på datafabriken
- **Pipelinenamn**: namnet på pipelinen
- **Mottagare**: parameter som skickas. Den här egenskapen anger mottagaren av e-postmeddelandet.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```
## <a name="create-email-workflow-endpoints"></a>Skapa slutpunkter för e-postarbetsflödet
För att utlösa utskicket av ett e-postmeddelande använder du [Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) för att definiera arbetsflödet. Mer information om hur du skapar ett Logic App-arbetsflöde finns i [Skapa en logikapp](../logic-apps/logic-apps-create-a-logic-app.md). 

### <a name="success-email-workflow"></a>Lyckat e-postarbetsflöde 
Skapa ett Logic App-arbetsflöde med namnet `CopySuccessEmail`. Definiera arbetsflödesutlösaren som `When an HTTP request is received`, och lägg till en `Office 365 Outlook – Send an email`-åtgärd.

![Lyckat e-postarbetsflöde](media/tutorial-control-flow/success-email-workflow.png)

För begärandeutlösaren fyller du i `Request Body JSON Schema` med följande JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```
Detta ligger i linje med klassen **EmailRequest** som du skapade i föregående avsnitt. 

Din begäran ska se ut så här i Logic App Designer:

![Logic App Designer – begäran](media/tutorial-control-flow/logic-app-designer-request.png)

För åtgärden **Skicka e-post** anger du hur du vill formatera e-postmeddelandet med hjälp av egenskaperna som skickas i begärandetexten i JSON-schemat. Här är ett exempel:

![Logic App Designer – åtgärd för att skicka e-post](media/tutorial-control-flow/send-email-action.png)

Notera URL:en för HTTP Post-begäran för arbetsflödet för e-postmeddelandet om lyckad kopiering:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Arbetsflöde för e-postmeddelande om misslyckad kopiering 
Klona **CopySuccessEmail** och skapa ett till Logic Apps-arbetsflöde för **CopyFailEmail**. `Request Body JSON schema` är samma i begärandeutlösaren. Ändra formatet för ditt e-postmeddelande som `Subject` för att skapa ett e-postmeddelande om att kopieringen misslyckats. Här är ett exempel:

![Logic App Designer – arbetsflöde för e-postmeddelande om misslyckad kopiering](media/tutorial-control-flow/fail-email-workflow.png)

Notera URL:en för HTTP Post-begäran för arbetsflödet för e-postmeddelandet om misslyckad kopiering:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Du bör nu ha två arbetsflödes-URL:er:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>Skapa en pipeline
Lägg till följande kod i Main-metoden som skapar en pipeline med en kopieringsaktivitet och egenskapen dependsOn. I den här självstudiekursen innehåller pipelinen en aktivitet: kopieringsaktiviteten, vilken tar Blob-datauppsättningen som en källa och en annan Blob-datauppsättning som mottagare. När kopieringsaktiviteten lyckas eller misslyckas anropas olika e-poståtgärder.

I denna pipeline kan du använda följande funktioner:

- Parametrar
- Webbaktivitet
- Aktivitetsberoende
- Använda utdata från en aktivitet som indata i efterföljande aktivitet

Lås oss titta på följande pipeline del för del:

```csharp

static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
        {
            Console.WriteLine("Creating pipeline " + pipelineName + "...");
            PipelineResource resource = new PipelineResource
            {
                Parameters = new Dictionary<string, ParameterSpecification>
                {
                    { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                },
                Activities = new List<Activity>
                {
                    new CopyActivity
                    {
                        Name = copyBlobActivity,
                        Inputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSourceDatasetName
                            }
                        },
                        Outputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSinkDatasetName
                            }
                        },
                        Source = new BlobSource { },
                        Sink = new BlobSink { }
                    },
                    new WebActivity
                    {
                        Name = sendSuccessEmailActivity,
                        Method = WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Succeeded" }
                            }
                        }
                    },
                    new WebActivity
                    {
                        Name = sendFailEmailActivity,
                        Method =WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Failed" }
                            }
                        }
                    }
                }
            };
            Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
            return resource;
        }
```
Lägg till följande kod för att skapa pipelinen i **Main**-metoden:

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>Parametrar
Den första delen av pipelinen definierar parametrar. 

- sourceBlobContainer – parameter i pipelinen som används av blob-datauppsättningen för källan.
- sinkBlobContainer – parameter i pipelinen som används av blob-datauppsättningen för mottagaren
- receiver – parameter i pipelinen som används av två webbaktiviteter och vars e-postadress mottar e-postmeddelandet


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>Webbaktivitet
Webbaktiviteten tillåter anrop till valfri REST-slutpunkt. Mer information om aktiviteten finns i [Webbaktivitet](control-flow-web-activity.md). Denna pipeline använder en webbaktivitet för att anropa Logic Apps arbetsflöde för e-post. Du skapar två webbaktiviteter: en som anropar **CopySuccessEmail**-arbetsflödet och en som anropar **CopyFailWorkFlow**.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```
I URL-egenskapen klistrar du in URL för begärandeslutpunkterna från ditt Logic Apps-arbetsflöde. I textegenskapen skickar du en instans av klassen EmailRequest. E-postbegäran innehåller följande egenskaper:

- Meddelande – skicka värdet `@{activity('CopyBlobtoBlob').output.dataWritten`. Hämtar en egenskap för den tidigare kopieringsaktiviteten och skickar värdet för dataWritten. Vid ett fel skickas felutdata i stället för `@{activity('CopyBlobtoBlob').error.message`.
- Datafabriksnamn – skicka värdet för `@{pipeline().DataFactory}`. Detta är en systemvariabel som gör att du kan komma åt motsvarande datafabriksnamn. En lista över systemvariabler finns i artikeln om [systemvariabler](control-flow-system-variables.md).
- Pipelinenamn – skicka värdet för `@{pipeline().Pipeline}`. Detta är också en systemvariabel som gör att du kan komma åt motsvarande pipelinenamn. 
- Mottagare – skicka värdet för "@pipeline().parameters.receiver"). Kommer åt pipelineparametrar.
 
Med den här koden skapas ett nytt aktivitetsberoende, beroende på den föregående kopieringsaktiviteten.

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning
Lägg till följande kod i **Main**-metoden för att **utlösa en pipelinekörning**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main-klass 
Din sista Main-metod bör se ut så här. Skapa och kör programmet för att utlösa en pipelinekörning!

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
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
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Kör koden
Skapa och starta programmet och kontrollera sedan pipelinekörningen.
Konsolen skriver ut förloppet för skapandet av datafabriken, den länkade tjänsten, datauppsättningar, pipeline och pipelinekörning. Sedan kontrolleras status för pipelinekörningen. Vänta tills du ser information om körningen av kopieringsaktiviteten med storlek för lästa/skrivna data. Använd sedan verktyg som Azure Storage Explorer för att kontrollera de blob(ar) som kopierats till outputBlobPath från inputBlobPath som du angav i variablerna.

**Exempel på utdata:**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du fått: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en länkad Azure Storage-tjänst.
> * Skapa en Azure Blob-datauppsättning
> * Skapa en pipeline som innehåller en kopieringsaktivitet och en webbaktivitet
> * Skicka utdata för aktiviteter till efterföljande aktiviteter
> * Skicka parametrar och använda systemvariabler
> * Starta en pipelinekörning
> * Övervaka pipelinen och aktivitetskörningar

Nu kan du gå vidare till begreppsavsnittet för mer information om Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines och aktiviteter](concepts-pipelines-activities.md)
