---
title: Bearbeta datauppsättningar i stor skala med hjälp av Data Factory och Batch | Microsoft Docs
description: Beskriver hur du bearbetar stora mängder data i en Azure Data Factory-pipeline med hjälp av parallella processkapacitet i Azure Batch.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: e95f167cf6dcfe90fff1c2be174ca197cb2aa004
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204024"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Bearbeta datauppsättningar i stor skala med hjälp av Data Factory och Batch
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig. Om du använder den aktuella versionen av Data Factory-tjänsten, se [anpassade aktiviteter i Data Factory](../transform-data-using-dotnet-custom-activity.md).

Den här artikeln beskrivs en arkitekturen för en exempel-lösning som flyttar och bearbeta datauppsättningar i stor skala på ett automatiskt och schemalagt sätt. Det ger också en slutpunkt till slutpunkt-genomgång för att implementera lösningen genom att använda Data Factory och Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här artikeln är längre än en typisk artikeln eftersom den innehåller en genomgång av en hel exempellösningen. Om du inte har använt Batch och Data Factory, du kan lära dig om de här tjänsterna och hur de fungerar tillsammans. Om du vet något om tjänsterna och designa/utforma en lösning, kan du fokusera på arkitekturen i artikeln. Om du utvecklar en prototyp eller en lösning, kan du prova att använda de stegvisa anvisningarna i den här genomgången. Vi bjuder in dina kommentarer om det här innehållet och hur du använder den.

Först ska vi titta på hur Data Factory och Batch-tjänster kan hjälpa dig att bearbeta stora datamängder i molnet.     


## <a name="why-azure-batch"></a>Varför Azure Batch?
 Du kan använda Batch för att effektivt köra storskaliga parallella och högpresterande datorbearbetning (HPC) program i molnet. Det är en plattformstjänst som schemalägger beräkningsintensiva arbeten att köras på en hanterad samling virtuella datorer (VM). Den kan automatiskt skala beräkningsresurser för att uppfylla behoven i dina jobb.

Med Batch-tjänsten definierar du Azure-beräkningsresurser att köra dina program parallellt och i stor skala. Du kan köra på begäran eller schemalagda jobb. Du behöver inte manuellt skapa, konfigurera och hantera ett HPC-kluster, enskilda virtuella datorer, virtuella nätverk eller en komplex jobbet och schemaläggning infrastruktur.

 Om du inte är bekant med Batch kan hjälpa dig förstå arkitekturen/implementeringen av lösningen som beskrivs i den här artikeln i följande artiklar:   

* [Grunderna i Batch](../../batch/batch-technical-overview.md)
* [Funktionsöversikt för batch](../../batch/batch-api-basics.md)

Du kan också mer information om Batch finns [dokumentation om Batch](https://docs.microsoft.com/azure/batch/).

## <a name="why-azure-data-factory"></a>Varför Azure Data Factory?
Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Du kan använda Data Factory för att skapa hanterade datapipelines som flyttar data från lokala och molnbaserade datalager till ett centraliserat datalager. Ett exempel är Azure Blob storage. Du kan använda Data Factory för att bearbeta/omvandla data med hjälp av tjänster som Azure HDInsight och Azure Machine Learning. Du kan även schemalägga datapipelines för att köra på ett schemalagt sätt (till exempel varje timme, varje dag, och varje vecka). Du kan övervaka och hantera pipelines blixtsnabbt och identifiera problem och vidta åtgärder.

  Om du inte är bekant med Data Factory i följande artiklar hjälper dig att förstå arkitekturen/implementeringen av lösningen som beskrivs i den här artikeln:  

* [Introduktion till Data Factory](data-factory-introduction.md)
* [Skapa din första datapipeline](data-factory-build-your-first-pipeline.md)   

Du kan också mer information om Data Factory finns [Data Factory-dokumentation](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Data Factory och Batch tillsammans
Data Factory innehåller inbyggda aktiviteter. Exempelvis används kopieringsaktiviteten för att kopiera/flytta data från ett källdatalager till ett måldatalager. Hive-aktivitet används för att bearbeta data med hjälp av Hadoop-kluster (HDInsight) på Azure. En lista över transformeringsaktiviteter som stöds finns i [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md).

Du kan också skapa anpassade .NET-aktiviteter för att flytta eller bearbeta data med egen logik. Du kan köra dessa aktiviteter i ett HDInsight-kluster eller i en Batch-pool med virtuella datorer. När du använder Batch kan du konfigurera poolen för automatisk skalning (lägga till eller ta bort virtuella datorer baserat på arbetsbelastning) baserat på en formel som du anger.     

## <a name="architecture-of-a-sample-solution"></a>Arkitekturen för en exempellösning
  Den arkitektur som beskrivs i den här artikeln är en enkel lösning. Det är också relevant för komplexa scenarier, till exempel riskmodellering genom finansiella tjänster, bildbearbetning och rendering och stora uppsättningar genomiska analys.

Diagrammet visar hur samordnar Data Factory dataförflyttning och bearbetning. Den visar även hur Batch bearbetar data i en parallell sätt. Hämta och skriva ut det till hands (11 x 17 tum eller A3-storlek). Du hittar diagrammet så att du kan skriva ut den i [HPC och datadirigering med hjälp av Batch och Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Bearbetning av storskaliga diagram](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Följande lista innehåller de grundläggande stegen för processen. Lösningen innehåller koden och förklaringar för att skapa slutpunkt till slutpunkt-lösningen.

* **Konfigurera Batch med en pool med beräkningsnoder (virtuella datorer).** Du kan ange antalet noder och storleken på varje nod.

* **Skapa en Data Factory-instans** som har konfigurerats med entiteter som representerar blob-lagring, beräkning Batch-tjänsten, indata/utdata och en arbetsflöde/pipeline med aktiviteter som att flytta och omvandla data.

* **Skapa en anpassad .NET-aktivitet i Data Factory-pipeline.** Aktiviteten är din användarkod som kör på Batch-pool.

* **Store stora mängder inkommande data som blobar i Azure Storage.** Data är uppdelad i logiska segment (vanligtvis genom tid).

* **Data Factory kopierar alla data som bearbetas parallellt** till den sekundära platsen.

* **Data Factory körs den anpassade aktiviteten med hjälp av poolen allokeras av Batch.** Aktiviteter kan köras samtidigt av data Factory. Varje aktivitet bearbetar en sektor av data. Resultaten lagras i lagring.

* **Data Factory flyttas de slutliga resultaten till en tredje plats** antingen för distribution via en app eller för vidare bearbetning av andra verktyg.

## <a name="implementation-of-the-sample-solution"></a>Implementering av exempellösningen
Exempellösningen är avsiktligt enkelt. Det är utformat för att visa dig hur du använder Data Factory och Batch tillsammans att processen datauppsättningar. Lösningen räknar antalet förekomster av söktermen ”Microsoft” i inkommande filer som är ordnade i en tidsserie. Antal till utdatafilerna matar sedan ut.

**Tid:** Om du är bekant med grunderna för Azure Data Factory och Batch och har följande förutsättningar, tar den här lösningen en till två timmar att slutföra.

### <a name="prerequisites"></a>Nödvändiga komponenter
#### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har en Azure-prenumeration kan du snabbt skapa ett kostnadsfritt konto. Mer information finns i [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure Storage-konto
Du kan använda ett lagringskonto för att lagra data i den här självstudien. Om du inte har ett lagringskonto kan du läsa [skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md). Exempellösningen använder blob storage.

#### <a name="azure-batch-account"></a>Azure Batch-konto
Skapa ett Batch-konto med hjälp av den [Azure-portalen](https://portal.azure.com/). Mer information finns i [skapa och hantera ett Batch-konto](../../batch/batch-account-create-portal.md). Observera Batch-konto och kontonyckeln. Du kan också använda den [New AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) cmdlet för att skapa ett Batch-konto. Anvisningar för hur du använder den här cmdleten finns i [Kom igång med Batch PowerShell-cmdlets](../../batch/batch-powershell-cmdlets-get-started.md).

Exempellösningen använder Batch (indirekt via en data factory-pipeline) att bearbeta data i en parallell sätt i en pool med beräkningsnoder (en hanterad samling virtuella datorer).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch-pool med virtuella datorer
Skapa en Batch-pool med minst två beräkningsnoder.

1. I den [Azure-portalen](https://portal.azure.com)väljer **Bläddra** i den vänstra menyn och välj **Batch-konton**.

1. Välj ditt Batch-konto för att öppna den **Batch-kontot** bladet.

1. Välj den **pooler** panelen.

1. På den **pooler** bladet och välja den **Lägg till** knapp i verktygsfältet för att lägga till en pool.

   a. Ange ett ID för poolen (**Pool-ID**). Observera ID: T för poolen. Du behöver den när du skapar data factory-lösning.

   b. Ange **Windows Server 2012 R2** för den **Operating System Family** inställningen.

   c. Välj en **nodprisnivå**.

   d. Ange **2** som värde för den **Target dedikerade** inställningen.

   e. Ange **2** som värde för den **högsta antal aktiviteter per nod** inställningen.

   f. Välj **OK** skapa poolen.

#### <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren
Du använder [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) eller [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (från ClumsyLeaf programvara) att granska och ändra data i ditt Storage-projekt. Du kan också granska och ändra data i loggarna för ditt program i molnet.

1. Skapa en behållare med namnet **mycontainer** med privat åtkomst (ingen anonym åtkomst).

1. Om du använder CloudXplorer kan du skapa mappar och undermappar med följande struktur:

   ![Mappen och undermappen struktur](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` och `outputfolder` är översta mappar i `mycontainer`. Den `inputfolder` mappen innehåller undermappar med datum / tid-stämplar (åååå-MM-DD-HH).

   Om du använder Storage Explorer i nästa steg kan du överföra filer med följande namn: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`och så vidare. Det här steget skapar automatiskt mapparna.

1. Skapa en textfil **fil.txt** på din dator med innehåll som har nyckelordet **Microsoft**. Ett exempel är ”testa anpassad aktivitet Microsoft test anpassad aktivitet Microsoft”.

1. Ladda upp filen till följande indatamapparna i blob storage:

   ![Indatamapparna](./media/data-factory-data-processing-using-batch/image4.png)

   Om du använder Storage Explorer kan du ladda upp den **fil.txt** filen till **mycontainer**. Välj **kopia** i verktygsfältet för att skapa en kopia av blobben. I den **kopiering av Blob** i dialogrutan den **blob målnamn** till `inputfolder/2015-11-16-00/file.txt`. Upprepa steget för att skapa `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`och så vidare. Den här åtgärden skapar automatiskt mapparna.

1. Skapa en annan behållare med namnet `customactivitycontainer`. Ladda upp den anpassade aktivitet zip-filen till den här behållaren.

#### <a name="visual-studio"></a>Visual Studio
Installera Visual Studio 2012 eller senare för att skapa den anpassade Batch-aktiviteten som ska användas i data factory-lösning.

### <a name="high-level-steps-to-create-the-solution"></a>Övergripande steg för att skapa lösningen
1. Skapa en anpassad aktivitet som innehåller data för bearbetning av dataströmmar.

1. Skapa en datafabrik som använder den anpassade aktiviteten.

### <a name="create-the-custom-activity"></a>Skapa den anpassade aktiviteten
Anpassad aktivitet för data factory är hjärtat i den här exempellösningen. Exempellösningen använder Batch för att köra den anpassade aktiviteten. Information om hur du utvecklar anpassade aktiviteter och använda dem i data factory-pipelines finns i [Använd anpassade aktiviteter i en data factory-pipeline](data-factory-use-custom-activities.md).

Om du vill skapa en anpassad .NET-aktivitet som du kan använda i en data factory-pipeline, skapar du en .NET-klassbiblioteksprojektet med en klass som implementerar IDotNetActivity-gränssnittet. Det här gränssnittet har bara en av metoderna: Köra. Här är signaturen för metoden:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Metoden har några viktiga komponenter som du behöver för att förstå:

* Metoden tar fyra parametrar:

  * **linkedServices**. Den här parametern är en uppräkningsbara lista över länkade tjänster som länkar indata/utdata-källor (till exempel blobblagring) till data factory. I det här exemplet har endast en länkad tjänst av typen Azure Storage används för både inkommande och utgående.
  * **datauppsättningar**. Den här parametern är en uppräkningsbara lista över datauppsättningar. Du kan använda den här parametern för att få de platser och scheman som definieras av in- och utdatauppsättningar.
  * **aktiviteten**. Den här parametern representerar den aktuella compute-entiteten. I det här fallet är det en Batch-tjänsten.
  * **logger**. Du kan använda loggaren skriva debug kommentarer som yta som ”användare”-loggen för pipelinen.
* Metoden returnerar en ordlista som kan användas för att länka anpassade aktiviteter tillsammans i framtiden. Den här funktionen är inte implementerats ännu, så här kommer bara returnerar en tom ordlista från metoden.

#### <a name="procedure-create-the-custom-activity"></a>Proceduren: Skapa den anpassade aktiviteten
1. Skapa en .NET-klassbiblioteksprojektet i Visual Studio.

   a. Starta Visual Studio 2012/2013/2015.

   b. Välj **Arkiv** > **Nytt** > **Projekt**.

   c. Expandera **mallar**, och välj **Visual C\#**. I den här genomgången ska du använda C\#, men du kan använda valfritt .NET-språk för att utveckla den anpassade aktiviteten.

   d. Välj **klassbiblioteket** i listan över projekttyper till höger.

   e. Ange **MyDotNetActivity** för den **namn**.

   f. Välj **C:\\ADF** för den **plats**. Skapa mappen **ADF** om den inte finns.

   g. Klicka på **OK** för att skapa projektet.

1. Välj **verktyg** > **NuGet-Pakethanteraren** > **Pakethanterarkonsolen**.

1. Kör följande kommando för att importera Microsoft.Azure.Management.DataFactories i Package Manager-konsolen:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importera den **Azure Storage** NuGet-paketet i projektet. Du behöver det här paketet eftersom du använder Blob Storage-API i det här exemplet:

    ```powershell
    Install-Package Az.Storage
    ```
1. Lägg till följande med hjälp av direktiv till källfilen i projektet:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Ändra namnet på namnområdet som ska **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Ändra namnet på klassen för att **MyDotNetActivity**, och få det från den **IDotNetActivity** gränssnitt som visas:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementera (Lägg till) det **kör** -metoden för den **IDotNetActivity** gränssnitt till den **MyDotNetActivity** klass. Kopiera följande exempelkod till metoden. En förklaring av den logik som används i den här metoden finns i den [Körningsmetod](#execute-method) avsnittet.

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {
    
       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.
    
       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
       // Initialize the continuation token before using it in the do-while loop.
       BlobContinuationToken continuationToken = null;
       do
       {   // get the list of input blobs from the input storage client object.
           BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                    true,
                                    BlobListingDetails.Metadata,
                                    null,
                                    continuationToken,
                                    null,
                                    null);
    
           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Lägg till följande helper-metoder i klassen. De här metoderna anropas av den **kör** metod. Viktigaste, den **Calculate** metoden isolerar den kod som upprepas varje blob.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    private static string GetFolderPath(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FolderPath;
    }
    
    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>
    
    private static string GetFileName(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>
    
    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
       string output = string.Empty;
       logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
       foreach (IListBlobItem listBlobItem in Bresult.Results)
       {
           CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
           if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
           {
               string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
               logger.Write("input blob text: {0}", blobText);
               string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
               var matchQuery = from word in source
                                where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                select word;
               int wordCount = matchQuery.Count();
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    Metoden GetFolderPath returnerar sökvägen till den mapp som den här datauppsättningen pekar på och metoden GetFileName Returnerar namnet på blob/fil som datauppsättningen pekar på.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoden Calculate beräknar antalet instanser av nyckelordet ”Microsoft” i indatafilerna (blobbar i mappen). Söktermen ”Microsoft” är hårdkodad i koden.

1. Kompilera projektet. Välj **skapa** från menyn och välj sedan **skapa lösning**.

1. Starta Windows Explorer och gå till den **bin\\felsöka** eller **bin\\viktig** mapp. Mappen valet beror på vilken typ av build.

1. Skapa en zip-fil **MyDotNetActivity.zip** som innehåller alla binärfiler i den  **\\bin\\felsöka** mapp. Du kanske vill inkludera i MyDotNetActivity. **pdb** filen så att du får ytterligare information, till exempel radnumret i källkoden som har orsakat problemet när ett fel uppstår.

   ![Mapplistan bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Ladda upp **MyDotNetActivity.zip** som en blob till blob-behållaren `customactivitycontainer` i blob-lagringen som StorageLinkedService länkad tjänst i ADFTutorialDataFactory använder. Skapa blob-behållare `customactivitycontainer` om den inte redan finns.

#### <a name="execute-method"></a>Execute-metoden
Det här avsnittet innehåller mer information om koden i Execute-metoden.

1. Medlemmar för att söka igenom inkommande samling finns i den [Microsoft.WindowsAzure.Storage.Blob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) namnområde. Om du vill gå igenom samlingen blob, så måste du använda den **BlobContinuationToken** klass. I princip måste du använda en gör-vid-loop med token som mekanism för att avsluta slingan. Mer information finns i [använda Blob storage från .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). En grundläggande loop visas här:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    
                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.
    
     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
    } while (continuationToken != null);

    ```
   Mer information finns i dokumentationen för den [ListBlobsSegmented](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobssegmented) metod.

1. Koden för att arbeta igenom uppsättning blobar logiskt hamnar inom do-samtidigt loop. I den **kör** metod, do-medan loop skickar listan över blobar till en metod med namnet **Calculate**. Metoden returnerar en variabel med namnet **utdata** som är resultatet av att ha itereras via alla blobbar i segmentet.

   Returnerar antalet förekomster av den sökterm som ”Microsoft” i blobben skickas till den **Calculate** metod.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Efter den **Calculate** metoden har slutförts, den måste skrivas till en ny blob. En ny blob kan skrivas med resultaten för varje uppsättning blobbar som bearbetas. Om du vill skriva till en ny blob först hitta datauppsättningen för utdata.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. I koden anropas också hjälpmetoden **GetFolderPath** att hämta sökvägen till mappen (namnet på lagringsbehållaren).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Metoden GetFolderPath kastar datamängdsobjektet till en AzureBlobDataSet som har en egenskap med namnet FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
1. Koden anropar den **GetFileName** metod för att hämta namnet på filen (blobnamnet). Koden liknar föregående kod som användes för att hämta sökvägen till mappen.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
1. Namnet på filen skrivs genom att skapa ett URI-objekt. Konstruktorn URI: N använder den **BlobEndpoint** egenskapen att returnera behållarens namn. Sökvägen och namnet på mappen läggs för att konstruera utdatablob-URI.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. När namnet på filen skrivs, du kan skriva Utdatasträngen från den **Calculate** metod för att en ny blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Skapa data factory
I den [skapa den anpassade aktiviteten](#create-the-custom-activity) avsnittet du skapat en anpassad aktivitet och överfört zip-filen med binärfilerna och PDB-filen till en blob-behållare. I det här avsnittet skapar du en datafabrik med en pipeline som använder den anpassade aktiviteten.

Datauppsättningen för indata för den anpassade aktiviteten representerar BLOB-objekt (filer) i Indatamappen (`mycontainer\\inputfolder`) i blob storage. Datauppsättningen för utdata för aktiviteten representerar utdata blobarna i den utgående mappen (`mycontainer\\outputfolder`) i blob storage.

Ta bort en eller flera filer i indatamapparna:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Till exempel släpp en fil (fil.txt) med följande innehåll i alla mappar:

```
test custom activity Microsoft test custom activity Microsoft
```

Varje Indatamappen motsvarar en sektor i data factoryn även om mappen har två eller flera filer. När varje sektor bearbetas av pipelinen, upprepas den anpassade aktiviteten över alla blobbar i Indatamappen för den sektorn.

Du ser fem utdatafilerna till samma innehåll. Utdatafil från filen i mappen 2015-11-16-00 bearbetas har exempelvis följande innehåll:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Om du släpper flera filer (fil.txt, file2.txt, file3.txt) med samma innehåll i Indatamappen ser du följande innehåll i utdatafilen. Varje mapp (2015-11-16-00 osv) motsvarar en sektor i det här exemplet, även om mappen har flera indatafiler.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Utdatafilen innehåller tre rader nu, en för varje indatafil (blob) i mappen som är associerade med sektorn (2015-11-16-00).

En uppgift skapas för varje aktivitet som körs. I det här exemplet finns bara en aktivitet i pipelinen. När en sektor bearbetas av pipelinen, körs den anpassade aktiviteten på Batch för att bearbeta sektorn. Eftersom det finns fem sektorer (varje segment kan ha flera blobar eller fil), skapas fem aktiviteter i Batch. När en aktivitet körs på Batch, är den anpassade aktiviteten som körs.

Den här genomgången innehåller ytterligare information.

#### <a name="step-1-create-the-data-factory"></a>Steg 1: Skapa data factory
1. När du loggar in på den [Azure-portalen](https://portal.azure.com/), gör följande:

   a. Välj **NEW** på den vänstra menyn.

   b. Välj **Data och analys** på den **New** bladet.

   c. Välj **Data Factory** på den **dataanalys** bladet.

1. På den **ny datafabrik** bladet ange **CustomActivityFactory** för namnet. Namnet på datafabriken måste vara globalt unikt. Om du får felet ”datafabriksnamnet CustomActivityFactory är inte tillgängligt” ändrar du namnet på datafabriken. Till exempel använda yournameCustomActivityFactory och skapa datafabriken igen.

1. Välj **RESURSGRUPPENS namn**, och välj en befintlig resursgrupp eller skapa en resursgrupp.

1. Kontrollera att den prenumeration och region där du vill att datafabriken ska skapas är korrekta.

1. Välj **skapa** på den **ny datafabrik** bladet.

1. Data factory skapas på instrumentpanelen i portalen.

1. När datafabriken har skapats visas den **datafabrik** sidan som visar innehållet i datafabriken.

   ![Data factory-sida](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Steg 2: Skapa länkade tjänster
Länkade tjänster länkar datalager eller Beräkningstjänster till en data factory. I det här steget länkar du ditt lagringskonto och Batch-konto till datafabriken.

#### <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst
1. Välj den **författare och distribuera** panel på den **datafabrik** bladet för **CustomActivityFactory**. Data Factory-redigeraren visas.

1. Välj **Nytt datalager** på kommandofältet och välj **Azure storage.** JSON-skriptet som du använder för att skapa en lagrings-länkade tjänsten i redigeraren visas.

   ![Nytt datalager](./media/data-factory-data-processing-using-batch/image7.png)

1. Ersätt **account name** med namnet på ditt lagringskonto. Ersätt **account key** med åtkomstnyckeln för lagringskontot. Information om hur du hämtar din lagringsåtkomstnyckel finns [visa, kopiera, och återskapa åtkomstnycklar](../../storage/common/storage-account-manage.md#access-keys).

1. Välj **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

   ![Distribuera](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Skapa en Azure Batch-länkad tjänst
I det här steget skapar du en länkad tjänst för ditt Batch-konto som används för att köra anpassad aktivitet för data factory.

1. Välj **ny beräkning** på kommandofältet och välj **Azure Batch.** JSON-skriptet som du använder för att skapa en Batch länkade tjänsten i redigeraren visas.

1. I JSON-skriptet:

   a. Ersätt **kontonamn** med namnet på ditt Batch-konto.

   b. Ersätt **åtkomstnyckel** med åtkomstnyckeln för Batch-kontot.

   c. Ange ID för poolen för det **poolName** egenskapen. För den här egenskapen, kan du ange poolnamnet eller pool-ID.

   d. Ange batch URI för den **batchUri** JSON-egenskap.

      > [!IMPORTANT]
      > URL: en från den **Batch-kontot** bladet är i följande format: \<accountname\>.\< region\>. batch.azure.com. För den **batchUri** egenskap i JSON-skriptet som du vill ta bort a88 ”accountname”. ** från URL: en. Ett exempel är `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Bladet för batch-kontot](./media/data-factory-data-processing-using-batch/image9.png)

      För den **poolName** egenskapen, du kan också ange ID för poolen i stället för namnet på poolen.

      > [!NOTE]
      > Data Factory-tjänsten stöder inte ett alternativ för på begäran för Batch som för HDInsight. Du kan använda bara dina egna Batch-pool i en datafabrik.
      >
      >
   
   e. Ange **StorageLinkedService** för den **linkedServiceName** egenskapen. Du har skapat den här länkade tjänsten i föregående steg. Den här lagringen används som ett mellanlagringsområde för filer och loggar.

1. Välj **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

#### <a name="step-3-create-datasets"></a>Steg 3: Skapa datauppsättningar
I det här steget skapar du datauppsättningar som representerar indata och utdata.

#### <a name="create-the-input-dataset"></a>Skapa indatauppsättningen
1. I Data Factory Editor väljer den **ny datauppsättning** i verktygsfältet. Välj **Azure Blob storage** från den nedrullningsbara listan.

1. Ersätt JSON-skriptet i den högra rutan med följande JSON-kodfragment:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    Skapar du en pipeline senare i den här genomgången med starttid 2015-11-16T00:00:00Z och den tid 2015-11-16T05:00:00Z. Den är schemalagd för att producera data per timme, så att det finns fem indata/utdata-segment (mellan **00**: 00:00 -\> **05**: 00:00).

    Den **frekvens** och **intervall** för datauppsättningen för indata är inställda på **timme** och **1**, vilket innebär att indatasektorn är tillgänglig per timme.

    Starttiden för varje sektor representeras av den **SliceStart** systemvariabeln i föregående JSON-kodfragmentet. Här följer starttider för varje segment.

    | **Sektorn** | **Starttid**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    Den **folderPath** beräknas med hjälp av den år, månad, dag och timme delen av Starttid för sektor (**SliceStart**). Här är hur en Indatamappen mappas till ett segment.

    | **Sektorn** | **Starttid**          | **Indatamappen**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Välj **distribuera** i verktygsfältet för att skapa och distribuera den **InputDataset** tabell.

#### <a name="create-the-output-dataset"></a>Skapa datauppsättningen för utdata
I det här steget skapar du en annan datauppsättning av typen AzureBlob att representera utdata.

1. I Data Factory Editor väljer den **ny datauppsättning** i verktygsfältet. Välj **Azure Blob storage** från den nedrullningsbara listan.

1. Ersätt JSON-skriptet i den högra rutan med följande JSON-kodfragment:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
               "partitionedBy": [
                   {
                       "name": "slice",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy-MM-dd-HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           }
       }
    }
    ```

    En blob/utdatafil genereras för varje indatasektorn. Här är hur en utdatafil har namnet för varje sektor. Alla utdatafiler som genereras i en Utdatamappen `mycontainer\\outputfolder`.

    | **Sektorn** | **Starttid**          | **Utdatafil**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Kom ihåg att alla filer i en Indatamappen (t.ex, 2015-11-16-00) är en del av ett segment med starttid 2015-11-16-00. När den här sektor bearbetas, den anpassade aktiviteten söker igenom varje fil och skapar en rad i filen med antalet förekomster av söktermen ”Microsoft”. Om det finns tre filer i mappen 2015-11-16-00, finns det tre raderna i den utdata filen 2015-11-16-00.txt.

1. Välj **distribuera** i verktygsfältet för att skapa och distribuera den **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Steg 4: Skapa och köra en pipeline med en anpassad aktivitet
I det här steget skapar du en pipeline med en aktivitet, den anpassade aktiviteten som du skapade tidigare.

> [!IMPORTANT]
> Om du inte har laddat upp **fil.txt** göra det innan du skapar pipelinen för inmatning mappar i blob-behållaren. Den **isPaused** egenskap är inställd på false i pipelinens JSON, så att pipelinen körs omedelbart eftersom den **starta** datum är i förflutna.
>
>

1. I Data Factory Editor väljer **ny pipeline** i kommandofältet. Om du inte ser kommandot, Välj symbolen med tre punkter att visa den.

1. Ersätt JSON-skriptet i den högra rutan med följande JSON-kodfragment:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Observera följande punkter:

   * Bara en aktivitet finns i pipelinen och är av typen **DotNetActivity**.
   * **AssemblyName** anges till namnet på den **MyDotNetActivity.dll**.
   * **EntryPoint** är inställd på **MyDotNetActivityNS.MyDotNetActivity**. Det är i princip \<namnområde\>.\< ClassName\> i din kod.
   * **PackageLinkedService** är inställd på **StorageLinkedService**, som pekar mot blob-lagringen som innehåller anpassad aktivitet zip-filen. Om du använder olika lagringskonton för indata/utdata-filer och anpassad aktivitet zip-filen som du behöver skapa en annan länkad lagringstjänst. Den här artikeln förutsätter att du använder samma lagringskonto.
   * **PackageFile** är inställd på **customactivitycontainer/MyDotNetActivity.zip**. Det är i formatet \<containerforthezip\>/\<nameofthezip.zip\>.
   * Den anpassade aktiviteten tar **InputDataset** som indata och **OutputDataset** som utdata.
   * Den **linkedServiceName** egenskapen för den anpassade aktiviteten pekar **AzureBatchLinkedService**, som anger att Data Factory, som den anpassade aktiviteten ska köras på Batch.
   * Den **samtidighet** inställningen är viktig. Om du använder standardvärdet, vilket är 1, även om du har två eller flera beräkningsnoder i Batch-pool, segment bearbetas efter varandra. Därför inte du dra nytta av parallell bearbetningskapacitet i Batch. Om du ställer in **samtidighet** till ett högre värde, exempelvis 2 innebär det att två skär (motsvarar två aktiviteter i Batch) kan bearbetas samtidigt. I det här fallet används båda de virtuella datorerna i Batch-pool. Ange egenskapen samtidighet på rätt sätt.
   * Endast en aktivitet (segment) körs på en virtuell dator när som helst som standard. Som standard **maximalt uppgifter per virtuell dator** har angetts till 1 för en Batch-pool. Som en del av förutsättningarna skapade du en pool med den här egenskapen angiven till 2. Därför kan två data factory sektorer köras på en virtuell dator på samma gång.
     - Den **isPaused** egenskap är inställd på false som standard. Pipelinen körs direkt i det här exemplet eftersom sektorer starta tidigare. Du kan ange egenskapen **SANT** att pausa pipeline och set tillbaka till **FALSKT** startas om.
     -   Den **starta** och **slutet** tiderna är fem timmar från varandra. Sektorer produceras per timme, så att fem sektorer som produceras av pipelinen.

1. Välj **Distribuera** i kommandofältet för att distribuera pipelinen.

#### <a name="step-5-test-the-pipeline"></a>Steg 5: Testa pipeline
I det här steget ska testa du pipelinen genom att släppa filer till indatamapparna. Starta genom att testa pipelinen med en fil för varje Indatamappen.

1. På den **datafabrik** -bladet i Azure portal, Välj **Diagram**.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

1. I den **Diagram** dubbelklickar du på datauppsättningen för indata **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. Den **InputDataset** bladet visas med alla fem sektorer redo. Observera den **sektorn starttid** och **SLUTTID för sektor** för varje sektor.

   ![Ange sektorn start- och sluttider](./media/data-factory-data-processing-using-batch/image12.png)

1. I den **Diagram** väljer **OutputDataset**.

1. Fem utdatasegment visas i den **redo** tillstånd om de skapas.

   ![Utdata sektorn start- och sluttider](./media/data-factory-data-processing-using-batch/image13.png)

1. Använda portalen för att visa de uppgifter som är associerade med segment och se vilka virtuella datorer som varje sektor som kördes på. Mer information finns i den [Data Factory och Batch-integrering](#data-factory-and-batch-integration) avsnittet.

1. Utdatafilerna visas under `mycontainer` i `outputfolder` i blob storage.

   ![Utdatafilerna i storage](./media/data-factory-data-processing-using-batch/image15.png)

   Fem utdatafilerna visas, en för varje indatasektorn. Var och en av utdatafilerna har innehåll som liknar följande utdata:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Följande diagram illustrerar hur data factory sektorer mappas till aktiviteter i Batch. I det här exemplet har en sektor endast körs.

   ![Sektorn mappning diagram](./media/data-factory-data-processing-using-batch/image16.png)

1. Prova nu med flera filer i en mapp. Skapa filerna **file2.txt**, **file3.txt**, **file4.txt**, och **file5.txt** med samma innehåll som fil.txt i mappen **2015-11-06-01**.

1. Ta bort utdatafilen i den utgående mappen **2015-11-16-01.txt**.

1. På den **OutputDataset** bladet högerklickar du på sektorn med **sektorn starttid** inställd **11/16/2015 01:00:00 AM**. Välj **kör** till kör/bearbeta sektorn på nytt. Sektorn har nu fem filer i stället för en fil.

    ![Kör](./media/data-factory-data-processing-using-batch/image17.png)

1. När sektorn körs och dess status **redo**, kontrollera innehållet i filen för den här sektor (**2015-11-16-01.txt**). Utdatafilen visas under `mycontainer` i `outputfolder` i blob storage. Det bör finnas en rad för varje fil för sektorn.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Om du inte tar bort den utdata filen 2015-11-16-01.txt innan du testat med fem indatafilerna, visas en rad från den tidigare körningen av sektorn och fem rader från den aktuella sektor-körningen. Som standard läggs innehållet till utdatafilen om den redan finns.
>
>

#### <a name="data-factory-and-batch-integration"></a>Data Factory och Batch-integrering
Data Factory-tjänsten skapar ett jobb i Batch med namnet `adf-poolname:job-xxx`.

![Batch-jobb](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

En uppgift i jobbet har skapats för varje aktivitet kör för en sektor. Om 10 segment är redo att bearbetas, skapas 10 uppgifter i jobbet. Du kan ha fler än en sektor som körs parallellt om du har flera beräkningsnoder i poolen. Om det maximala antalet aktiviteter per compute-nod har angetts till större än ett kan mer än ett segment köras på samma beräkning.

I det här exemplet finns fem sektorer, så att det finns fem aktiviteter i Batch. Med **samtidighet** inställd **5** i pipeline-JSON i data factoryn och **maximalt uppgifter per virtuell dator** inställd **2** i Batch-pool med **2** virtuella datorer, aktiviteterna körs snabbt. (Kontrollera start- och sluttider för uppgifter.)

Använda portalen för att visa Batch-jobb och tillhörande aktiviteter som är associerade med segment och se vilka virtuella datorer som varje sektor som kördes på.

![Batch-aktiviteterna för jobbet](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Felsöka pipeline
Felsökning består av några grundläggande metoder.

1. Om indatasektorn inte är inställd på **redo**, bekräfta att mappstrukturen för indata är rätt och att fil.txt finns i indatamapparna.

   ![Mappstrukturen för indata](./media/data-factory-data-processing-using-batch/image3.png)

1. I den **kör** -metoden för din anpassade aktivitets den **IActivityLogger** objekt att logga information som hjälper dig att felsöka problem. De loggade meddelandena som visas i användaren\_0. loggfilen.

   På den **OutputDataset** bladet välj sektorn om du vill se den **datasektor** bladet för den sektorn. Under **aktivitetskörningar**, visas en aktivitetskörning för sektorn. Om du väljer **kör** du kan starta en annan aktivitet som kör för samma segment i kommandofältet.

   När du väljer den aktivitet som körs kan du se den **aktivitetskörningsinformation** blad med en lista med loggfiler. Du ser loggade meddelanden i användaren\_0. loggfilen. När ett fel inträffar, se tre aktivitetskörningar eftersom antalet återförsök är inställd på 3 i pipeline/JSON-aktiviteten. När du väljer kör aktiviteten, visas de loggfiler som du kan granska för att felsöka problemet.

   ![Blad för OutputDataset och Data sektor](./media/data-factory-data-processing-using-batch/image18.png)

   Välj i listan över loggfiler, **user-0.log**. I den högra panelen, resultatet av att använda den **IActivityLogger.Write** metod visas.

   ![Aktivitetskörning informationsblad](./media/data-factory-data-processing-using-batch/image19.png)

   Kontrollera system-0.log för alla system felmeddelanden och undantag.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Inkludera den **PDB** filen i zip-filen så att felinformationen har information, till exempel anropsstacken när ett fel uppstår.

1. Alla filer i zip-filen för den anpassade aktiviteten måste vara på den översta nivån med utan undermappar.

   ![Anpassad aktivitet zip-fillista](./media/data-factory-data-processing-using-batch/image20.png)

1. Se till att **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip) och **packageLinkedService** (bör peka på blob-lagringen som innehåller zip-filen) är inställda på rätt värden.

1. Om du har korrigerat ett fel och vill bearbeta sektorn på nytt, högerklickar du på sektorn i **OutputDataset** bladet och välj **kör**.

   ![OutputDataset bladet kör alternativet](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > En behållare är i blob storage med namnet `adfjobs`. Den här behållaren tas bort inte automatiskt, men du kan ta bort den när du har slutfört testningen lösningen. På samma sätt kan data factory-lösning skapar ett Batch-jobb med namnet `adf-\<pool ID/name\>:job-0000000001`. Du kan ta bort det här jobbet när du har testat lösningen om du vill.
   >
   >
1. Den anpassade aktiviteten använder inte den **app.config** filen från paketet. Därför, om din kod läser några anslutningssträngar från konfigurationsfilen, den fungerar inte vid körning. Den bästa metoden när du använder Batch är att förvara eventuella hemligheter i Azure Key Vault. Använd certifikatbaserad tjänstens huvudnamn för att skydda nyckelvalvet och distribuera certifikat till Batch-pool. .NET-anpassad aktivitet kan komma åt hemligheter från nyckelvalvet vid körning. Den här allmän lösning kan skalas till alla typer av hemligheter, inte bara en anslutningssträng.

    Det finns en enklare lösning, men den är inte bästa praxis. Du kan skapa en SQL-databas som länkad tjänst med anslutning inställningar för anslutningssträngen. Du kan sedan skapa en datauppsättning som använder den länkade tjänsten och länka datauppsättningen som en dummy datauppsättningen för indata till anpassad .NET-aktivitet. Du kan sedan komma åt den länkade tjänsten anslutningssträng i koden anpassad aktivitet. Det bör fungera bra vid körning.  

#### <a name="extend-the-sample"></a>Utöka exemplet
Du kan utöka det här exemplet vill veta mer om Data Factory och Batch-funktioner. Till exempel för att bearbeta sektorer i ett annat tidsintervall, gör du följande:

1. Lägg till följande undermappar i `inputfolder`: 2015-11-16-05, 2015-11-16-06 201-11-16-07-2011-11-16-08 och 2015-11-16-09. Placera indatafilerna i dessa mappar. Ändra sluttid för pipelinen från `2015-11-16T05:00:00Z` till `2015-11-16T10:00:00Z`. I den **Diagram** dubbelklickar du på **InputDataset** och bekräfta att indatasektorerna är klara. Dubbelklicka på **OutputDataset** att se status för utdatasegment. Om de finns i den **redo** tillstånd, kontrollera Utdatamappen för utdatafilerna.

1. Öka eller minska den **samtidighet** inställningen för att förstå hur den påverkar prestandan för din lösning, särskilt bearbetningen som utförs på Batch. Mer information om den **samtidighet** inställningen finns i ”steg 4: Skapa och köra en pipeline med en anpassad aktivitet ”.

1. Skapa en pool med högre/lägre **maximalt uppgifter per virtuell dator**. Uppdatera länkade batchtjänsten i data factory-lösning för att använda den nya poolen som du skapade. Mer information om den **maximalt uppgifter per virtuell dator** inställningen finns i ”steg 4: Skapa och köra en pipeline med en anpassad aktivitet ”.

1. Skapa en Batch-pool med den **Autoskala** funktionen. Automatiskt skalning av compute-noder i en Batch-pool är att dynamiskt justera av processorkraft som används av ditt program. 

    Exemplet formeln här ger följande beteende. När poolen skapas, börjar det med en virtuell dator. Måttet $PendingTasks definierar antalet uppgifter körs och aktiv (köad). Formeln hittar det genomsnittliga antalet väntande aktiviteter de senaste 180 sekunderna och anger TargetDedicated därefter. Det innebär att TargetDedicated aldrig är mer omfattande än 25 virtuella datorer. När nya aktiviteter skickas växer automatiskt i poolen. Som aktiviteterna slutförs kan virtuella datorer blir kostnadsfria en i taget och autoskalning minskar storleken på de virtuella datorerna. Du kan justera startingNumberOfVMs och maxNumberofVMs efter dina behov.
 
    Formel för automatisk skalning:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Mer information finns i [skala beräkningsnoder automatiskt i en Batch-pool](../../batch/batch-automatic-scaling.md).

   Om poolen använder [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), Batch-tjänsten kan ta 15 till 30 minuter att förbereda den virtuella datorn innan du kör den anpassade aktiviteten. Om poolen använder en annan autoScaleEvaluationInterval, ta Batch-tjänsten autoScaleEvaluationInterval plus 10 minuter.

1. I exempellösningen är den **kör** metoden anropar den **Calculate** metod som bearbetar ett indata-segment för att skapa en utdatasektor. Du kan skriva en egen metod för att bearbeta indata och Ersätt den **Calculate** anrop den **kör** metoden med ett anrop till metoden.

### <a name="next-steps-consume-the-data"></a>Nästa steg: Använda data
När du bearbetar data kan du använda den med online-verktyg, till exempel Power BI. Här följer länkar som hjälper dig att förstå Power BI och hur du använder den i Azure:

* [Utforska en datauppsättning i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Kom igång med Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Uppdatera data i Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure och Powerbi: Grundläggande översikt](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referenser
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introduktion till Data Factory-tjänsten](data-factory-introduction.md)
  * [Kom igång med Data Factory](data-factory-build-your-first-pipeline.md)
  * [Använd anpassade aktiviteter i Data Factory-pipeline](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Grunderna i Batch](../../batch/batch-technical-overview.md)
  * [Översikt över Batch-funktioner](../../batch/batch-api-basics.md)
  * [Skapa och hantera ett Batch-konto i Azure portal](../../batch/batch-account-create-portal.md)
  * [Kom igång med Batch-klientbiblioteket för .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
