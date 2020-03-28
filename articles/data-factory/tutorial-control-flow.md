---
title: Förgrening i Azure Data Factory-pipeline
description: Lär dig hur du styr flödet av data i Azure Data Factory genom branchning och kedjesammansättning av aktiviteter.
services: data-factory
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 7ba921656d0dad059b1d15f443bcefeff03ade50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977387"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Branchning och kedjesammansättning av aktiviteter i en Data Factory-pipeline

I den här självstudien skapar du en Pipeline för Data Factory som visar vissa kontrollflödesfunktioner. Den här pipelinen kopierar från en behållare i Azure Blob Storage till en annan behållare i samma lagringskonto. Om kopieringsaktiviteten lyckas skickar pipelinen information om den lyckade kopieringen i ett e-postmeddelande. Denna information kan omfatta mängden data som skrivs. Om kopieringsaktiviteten misslyckas skickas information om kopieringsfelet, till exempel felmeddelandet, i ett e-postmeddelande. I självstudiekursen visas olika exempel på hur du skickar parametrar.

Den här bilden ger en översikt över scenariot:

![Översikt](media/tutorial-control-flow/overview.png)

Den här självstudien visar hur du utför följande uppgifter:

> [!div class="checklist"]
> * Skapa en datafabrik
> * Skapa en länkad Azure Storage-tjänst
> * Skapa en Azure Blob-datauppsättning
> * Skapa en pipeline som innehåller en kopieringsaktivitet och en webbaktivitet
> * Skicka utdata för aktiviteter till efterföljande aktiviteter
> * Använd parameterpassning och systemvariabler
> * Starta en pipelinekörning
> * Övervaka pipelinen och aktivitetskörningar

I den här självstudiekursen används .NET SDK. Du kan använda andra mekanismer för att interagera med Azure Data Factory. Snabbstarter för Data Factory finns i [5-minuters snabbstarter](/azure/data-factory/quickstart-create-data-factory-portal).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* Azure Storage-konto. Du använder blob-lagring som ett källdatalager. Om du inte har ett Azure-lagringskonto läser du [Skapa ett lagringskonto](../storage/common/storage-account-create.md).
* Utforskaren för Azure Storage. Information om hur du installerar det här verktyget finns i [Azure Storage Explorer](https://storageexplorer.com/).
* Azure SQL Database. Du använder databasen som mottagare för datalagringen. Om du inte har en Azure SQL-databas läser du [Skapa en Azure SQL-databas](../sql-database/sql-database-get-started-portal.md).
* Visual Studio. I den här artikeln används Visual Studio 2019.
* Azure .NET SDK. Hämta och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/).

En lista över Azure-regioner där Data Factory är tillgängligt finns i Produkter som är [tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/). Datalager och beräkningar kan finnas i andra regioner. Butikerna inkluderar Azure Storage och Azure SQL Database. Beräkningarna inkluderar HDInsight, som Data Factory använder.

Skapa ett program enligt beskrivningen i [Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Tilldela programmet till **rollen Deltagare** genom att följa instruktionerna i samma artikel. Du behöver flera värden för senare delar av den här självstudien, till exempel **Program -ID och** Katalog **-ID ((klient).**

### <a name="create-a-blob-table"></a>Skapa en blob-tabell

1. Öppna en textredigerare. Kopiera följande text och spara den lokalt som *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Öppna Azure Storage Explorer. Utöka ditt lagringskonto. Högerklicka på **Blob Containers** och välj **Skapa Blob Container**.
1. Ge den nya behållaren *namnet adfv2branch* och välj **Ladda upp** om du vill lägga till filen *input.txt* i behållaren.

## <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt<a name="create-visual-studio-project"></a>

Skapa ett C# .NET-konsolprogram:

1. Starta Visual Studio och välj **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt**väljer du Console App **(.NET Framework)** för C# och väljer **Nästa**.
1. Namnge projektet *ADFv2BranchTutorial*.
1. Välj **.NET version 4.5.2** eller senare och välj sedan **Skapa**.

### <a name="install-nuget-packages"></a>Installera NuGet-paket

1. Välj **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.
1. Kör följande kommandon i **Package Manager Console** för att installera paket. Mer information finns i [paketet Microsoft.Azure.Management.DataFactory nuget.](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Skapa en datafabriksklient

1. Öppna *Program.cs* och lägg till följande satser:

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

1. Lägg till dessa statiska variabler i `Program` klassen. Ersätt platshållarna med dina egna värden.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

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

1. Lägg till följande kod i metoden `Main`. Den här koden skapar `DataFactoryManagementClient` en förekomst av klass. Du kan sedan använda det här objektet för att skapa datafabrik, länkad tjänst, datauppsättningar och pipeline. Du kan också använda det här objektet för att övervaka information om pipelinekörning.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Lägg `CreateOrUpdateDataFactory` till en metod i *Program.cs-filen:*

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

1. Lägg till följande `Main` rad i metoden som skapar en datafabrik:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst

1. Lägg `StorageLinkedServiceDefinition` till en metod i *Program.cs-filen:*

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

1. Lägg till följande `Main` rad i metoden som skapar en Azure Storage-länkad tjänst:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Mer information om egenskaper och information som stöds finns i [Länkade tjänstegenskaper](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Skapa datauppsättningar

I det här avsnittet skapar du två datauppsättningar, en för källan och en för diskhon.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Skapa en datauppsättning för en azure-källblob

Lägg till en metod som skapar en *Azure blob-datauppsättning*. Mer information om egenskaper och information som stöds finns i [egenskaper för Azure Blob-datauppsättning](connector-azure-blob-storage.md#dataset-properties).

Lägg `SourceBlobDatasetDefinition` till en metod i *Program.cs-filen:*

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

Du definierar en datauppsättning som representerar källdata i Azure Blob. Den här Blob-datauppsättningen refererar till den Azure Storage-länkade tjänst som stöds i föregående steg. Blob-datauppsättningen beskriver platsen för blobben som ska kopieras från: *FolderPath* och *FileName*.

Observera användningen av parametrar för *FolderPath*. `sourceBlobContainer`är namnet på parametern och uttrycket ersätts med de värden som skickas i pipelinekörningen. Syntaxen för att definiera parametrar är `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Skapa en datauppsättning för en diskbänk Azure Blob

1. Lägg `SourceBlobDatasetDefinition` till en metod i *Program.cs-filen:*

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

1. Lägg till följande `Main` kod i metoden som skapar både Azure Blob-källa och sink-datauppsättningar.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Skapa en C#-klass: EmailRequest

Skapa en klass med namnet `EmailRequest`. Den här klassen definierar vilka egenskaper pipelinen skickar i brödtextbegäran när du skickar ett e-postmeddelande. I den här självstudiekursen skickar pipelinen fyra egenskaper från pipelinen till e-postmeddelandet:

* Meddelande. E-postmeddelandets brödtext. För en lyckad kopia innehåller den här egenskapen mängden data som skrivs. För en misslyckad kopia innehåller den här egenskapen information om felet.
* Datafabriksnamn. Datafabrikens namn.
* Pipeline-namn. Namnet på pipeline.
* Mottagare. Parameter som passerar. Den här egenskapen anger mottagaren av e-postmeddelandet.

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

För att utlösa utskicket av ett e-postmeddelande använder du [Logic Apps](../logic-apps/logic-apps-overview.md) för att definiera arbetsflödet. Mer information om hur du skapar ett Logic Apps-arbetsflöde finns i [Så här skapar du en Logic App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Lyckat e-postarbetsflöde

Skapa ett Logic Apps-arbetsflöde med namnet *CopySuccessEmail*i [Azure-portalen](https://portal.azure.com). Definiera arbetsflödesutlösaren som `When an HTTP request is received`. För begärandeutlösaren fyller du i `Request Body JSON Schema` med följande JSON:

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

Arbetsflödet ser ut ungefär som följande exempel:

![Lyckat e-postarbetsflöde](media/tutorial-control-flow/success-email-workflow-trigger.png)

Det här JSON-innehållet `EmailRequest` stämmer överens med den klass som du skapade i föregående avsnitt.

Lägg till `Office 365 Outlook – Send an email`en åtgärd i . För åtgärden **Skicka ett e-postmeddelande** kan du anpassa hur du vill formatera e-postmeddelandet med hjälp av egenskaperna som skickas i **body** JSON-schemat för begäran. Här är ett exempel:

![Logikappdesigner – skicka e-poståtgärd](media/tutorial-control-flow/customize-send-email-action.png)

När du har sparat arbetsflödet kopierar och sparar **HTTP POST-URL-värdet** från utlösaren.

## <a name="fail-email-workflow"></a>Arbetsflöde för e-postmeddelande om misslyckad kopiering

Klona **copySuccessEmail** som ett annat Logic Apps-arbetsflöde med namnet *CopyFailEmail*. `Request Body JSON schema` är samma i begärandeutlösaren. Ändra formatet för ditt e-postmeddelande som `Subject` för att skapa ett e-postmeddelande om att kopieringen misslyckats. Här är ett exempel:

![Logikappdesigner – misslyckas med e-postarbetsflöde](media/tutorial-control-flow/fail-email-workflow.png)

När du har sparat arbetsflödet kopierar och sparar **HTTP POST-URL-värdet** från utlösaren.

Du bör nu ha två arbetsflödesadresser, till exempel följande exempel:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Skapa en pipeline

Gå tillbaka till projektet i Visual Studio. Vi lägger nu till koden som skapar en `DependsOn` pipeline med en kopieringsaktivitet och egenskap. I den här självstudien innehåller pipelinen en aktivitet, en kopieringsaktivitet, som tar in Blob-datauppsättningen som källa och en annan Blob-datauppsättning som en mottagare. Om kopieringsaktiviteten lyckas eller misslyckas anropas olika e-postuppgifter.

I denna pipeline kan du använda följande funktioner:

* Parametrar
* Webbaktivitet
* Aktivitetsberoende
* Använda utdata från en aktivitet som indata till en annan aktivitet

1. Lägg till den här metoden i projektet. Följande avsnitt innehåller mer information.

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

1. Lägg till följande `Main` rad i metoden som skapar pipelinen:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parametrar

Den första delen av vår pipeline-kod definierar parametrar.

* `sourceBlobContainer`. Källblobbdatauppsättningen använder den här parametern i pipelinen.
* `sinkBlobContainer`. Sink-blob-datauppsättningen använder den här parametern i pipelinen.
* `receiver`. De två webbaktiviteterna i pipelinen som skickar lyckade eller misslyckade e-postmeddelanden till mottagaren använder den här parametern.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Webbaktivitet

Webbaktiviteten tillåter ett anrop till alla REST-slutpunkter. Mer information om aktiviteten finns [i Webbaktivitet i Azure Data Factory](control-flow-web-activity.md). Den här pipelinen använder en webbaktivitet för att anropa e-postarbetsflödet för Logic Apps. Du skapar två webbaktiviteter: en `CopySuccessEmail` som anropar `CopyFailWorkFlow`arbetsflödet och en som anropar .

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

I `Url` egenskapen klistrar du in **HTTP POST-URL-slutpunkterna** från logic apps-arbetsflödena. I `Body` egenskapen skickar du `EmailRequest` en instans av klassen. E-postbegäran innehåller följande egenskaper:

* Meddelande. Passerar värdet `@{activity('CopyBlobtoBlob').output.dataWritten`för . Öppnar en egenskap för föregående kopieringsaktivitet `dataWritten`och skickar värdet för . Vid ett fel skickas felutdata i stället för `@{activity('CopyBlobtoBlob').error.message`.
* Namn på datafabrik. Pass-värdet `@{pipeline().DataFactory}` för Den här systemvariabeln gör att du kan komma åt motsvarande datafabriksnamn. En lista över systemvariabler finns i [Systemvariabler](control-flow-system-variables.md).
* Pipeline-namn. Passerar värdet `@{pipeline().Pipeline}`för . Med den här systemvariabeln kan du komma åt motsvarande pipeline-namn.
* Mottagare. Passerar värdet `"@pipeline().parameters.receiver"`för . Öppnar pipelineparametrarna.

Den här koden skapar ett nytt aktivitetsberoende som beror på föregående kopieringsaktivitet.

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning

Lägg till följande `Main` kod i metoden som utlöser en pipeline-körning.

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

Din `Main` sista metod ska se ut så här.

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

Skapa och kör programmet för att utlösa en pipelinekörning!

## <a name="monitor-a-pipeline-run"></a>Övervaka en pipelinekörning

1. Lägg till följande kod i `Main`-metoden:

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

    Den här koden kontrollerar kontinuerligt körningens status tills den har kopierat data.

1. Lägg till följande `Main` kod i metoden som hämtar information om kopieringskörning, till exempel storleken på data som läs/200:

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

Programmet visar förloppet för att skapa datafabrik, länkad tjänst, datauppsättningar, pipeline och pipeline-körning. Sedan kontrolleras status för pipelinekörningen. Vänta tills du ser information om körningen av kopieringsaktiviteten med storlek för lästa/skrivna data. Använd sedan verktyg som Azure Storage Explorer för att kontrollera bloben kopierades till *outputBlobPath* från *inputBlobPath* som du angav i variabler.

Utdata bör likna följande exempel:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
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

Du gjorde följande uppgifter i den här självstudien:

> [!div class="checklist"]
> * Skapa en datafabrik
> * Skapa en länkad Azure Storage-tjänst
> * Skapa en Azure Blob-datauppsättning
> * Skapa en pipeline som innehåller en kopieringsaktivitet och en webbaktivitet
> * Skicka utdata för aktiviteter till efterföljande aktiviteter
> * Använd parameterpassning och systemvariabler
> * Starta en pipelinekörning
> * Övervaka pipelinen och aktivitetskörningar

Du kan nu fortsätta till avsnittet Begrepp för mer information om Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines och aktiviteter](concepts-pipelines-activities.md)
