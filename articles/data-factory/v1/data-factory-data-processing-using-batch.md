---
title: Bearbeta storskaliga datauppsättningar med hjälp av Data Factory och Batch
description: Beskriver hur du bearbetar enorma mängder data i en Azure Data Factory-pipeline med hjälp av parallella bearbetningsfunktioner i Azure Batch.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: afc7a7406831568304c2ebd8d9a6c72b497e04e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972886"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Bearbeta storskaliga datauppsättningar med hjälp av Data Factory och Batch
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Anpassade aktiviteter i Data Factory](../transform-data-using-dotnet-custom-activity.md).

I den här artikeln beskrivs en arkitektur för en exempellösning som flyttar och bearbetar storskaliga datauppsättningar på ett automatiskt och schemalagt sätt. Det ger också en heltäckande genomgång för att implementera lösningen med hjälp av Data Factory och Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här artikeln är längre än en vanlig artikel eftersom den innehåller en genomgång av en hel exempellösning. Om du inte har något nytt för Batch- och Data Factory kan du läsa om dessa tjänster och hur de fungerar tillsammans. Om du vet något om tjänsterna och utformar/utformar/utformar en lösning kan du fokusera på arkitekturavsnittet i artikeln. Om du utvecklar en prototyp eller en lösning kanske du vill prova steg-för-steg-instruktioner i genomgången. Vi bjuder in dina kommentarer om detta innehåll och hur du använder det.

Låt oss först titta på hur Data Factory- och Batch-tjänster kan hjälpa dig att bearbeta stora datauppsättningar i molnet.     


## <a name="why-azure-batch"></a>Varför Azure Batch?
 Du kan använda Batch för att köra storskaliga parallella och högpresterande datorprogram (HPC) effektivt i molnet. Det är en plattformstjänst som schemalägger beräkningsintensivt arbete för att köras på en hanterad samling virtuella datorer (VMs). Det kan automatiskt skala beräkningsresurser för att uppfylla behoven för dina jobb.

Med Batch-tjänsten definierar du Azure-beräkningsresurser att köra dina program parallellt och i stor skala. Du kan köra jobb på begäran eller schemalagda jobb. Du behöver inte manuellt skapa, konfigurera och hantera ett HPC-kluster, enskilda virtuella datorer, virtuella nätverk eller en komplex jobb- och schemaläggningsinfrastruktur.

 Om du inte är bekant med Batch hjälper följande artiklar dig att förstå arkitekturen/implementeringen av den lösning som beskrivs i den här artikeln:   

* [Grunderna i batch](../../batch/batch-technical-overview.md)
* [Översikt över Batch-funktionen](../../batch/batch-api-basics.md)

Du kan också läsa [batchdokumentationen](https://docs.microsoft.com/azure/batch/)om du vill veta mer om Batch.

## <a name="why-azure-data-factory"></a>Varför Azure Data Factory?
Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Du kan använda Data Factory för att skapa hanterade datapipelpipelsar som flyttar data från lokala datalager och molnlager till ett centraliserat datalager. Ett exempel är Azure Blob storage. Du kan använda Data Factory för att bearbeta/omvandla data med hjälp av tjänster som Azure HDInsight och Azure Machine Learning. Du kan också schemalägga datapipellines så att de körs på ett schemalagt sätt (till exempel varje timme, dag och varje vecka). Du kan övervaka och hantera pipelines på ett ögonblick för att identifiera problem och vidta åtgärder.

  Om du inte är bekant med Data Factory hjälper följande artiklar dig att förstå arkitekturen/implementeringen av den lösning som beskrivs i den här artikeln:  

* [Introduktion till Data Factory](data-factory-introduction.md)
* [Skapa din första datapipeline](data-factory-build-your-first-pipeline.md)   

Du kan också läsa [dokumentationen för Data Factory.](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory)

## <a name="data-factory-and-batch-together"></a>Data Factory och Batch tillsammans
Data Factory innehåller inbyggda aktiviteter. Kopiera aktiviteten används till exempel för att kopiera/flytta data från ett källdatalager till ett måldatalager. Hive-aktiviteten används för att bearbeta data med hjälp av Hadoop-kluster (HDInsight) på Azure. En lista över omvandlingsaktiviteter som stöds finns i [Dataomvandlingsaktiviteter](data-factory-data-transformation-activities.md).

Du kan också skapa anpassade .NET-aktiviteter för att flytta eller bearbeta data med din egen logik. Du kan köra dessa aktiviteter i ett HDInsight-kluster eller på en batchpool med virtuella datorer. När du använder Batch kan du konfigurera poolen så att den skalas automatiskt (lägga till eller ta bort virtuella datorer baserat på arbetsbelastningen) baserat på en formel som du tillhandahåller.     

## <a name="architecture-of-a-sample-solution"></a>Arkitektur för en exempellösning
  Arkitekturen som beskrivs i den här artikeln är för en enkel lösning. Det är också relevant för komplexa scenarier, till exempel riskmodellering av finansiella tjänster, bildbehandling och rendering samt genomisk analys.

Diagrammet illustrerar hur Data Factory dirigerar datarörelser och databearbetning. Det visar också hur Batch bearbetar data på ett parallellt sätt. Ladda ned och skriv ut diagrammet för enkel referens (11 x 17 tum eller A3-storlek). Information om hur du öppnar diagrammet så att du kan skriva ut det finns i [HPC och dataorkestrering med hjälp av Batch och Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Storskaligt databehandlingsdiagram](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Följande lista innehåller de grundläggande stegen i processen. Lösningen innehåller kod och förklaringar för att bygga end-to-end-lösningen.

* **Konfigurera batch med en pool av beräkningsnoder (virtuella datorer).** Du kan ange antalet noder och storleken på varje nod.

* **Skapa en Data Factory-instans** som är konfigurerad med entiteter som representerar blob-lagring, batchberäkningstjänsten, indata-/utdata och ett arbetsflöde/pipeline med aktiviteter som flyttar och omvandlar data.

* **Skapa en anpassad .NET-aktivitet i datafabrikspipelinen.** Aktiviteten är din användarkod som körs på batchpoolen.

* **Lagra stora mängder indata som blobbar i Azure Storage.** Data delas in i logiska segment (vanligtvis efter tid).

* **Data Factory kopierar data som bearbetas parallellt med** den sekundära platsen.

* **Data Factory kör den anpassade aktiviteten med hjälp av poolen som allokerats av Batch.** Data Factory kan köra aktiviteter samtidigt. Varje aktivitet bearbetar en datasegment. Resultaten lagras i lager.

* **Data Factory flyttar slutresultatet till en tredje plats,** antingen för distribution via en app eller för vidare bearbetning av andra verktyg.

## <a name="implementation-of-the-sample-solution"></a>Implementering av provlösningen
Provlösningen är avsiktligt enkel. Den är utformad för att visa dig hur du använder Data Factory och Batch tillsammans för att bearbeta datauppsättningar. Lösningen räknar antalet förekomster av söktermen "Microsoft" i indatafiler som är ordnade i en tidsserie. Det matar sedan ut antalet till utdatafiler.

**Tid:** Om du är bekant med grunderna i Azure, Data Factory och Batch och har slutfört följande förutsättningar tar den här lösningen en till två timmar att slutföra.

### <a name="prerequisites"></a>Krav
#### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt utvärderingskonto snabbt. Mer information finns i [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure Storage-konto
Du använder ett lagringskonto för att lagra data i den här självstudien. Om du inte har ett lagringskonto läser du [Skapa ett lagringskonto](../../storage/common/storage-account-create.md). Exempellösningen använder blob-lagring.

#### <a name="azure-batch-account"></a>Azure Batch-konto
Skapa ett batchkonto med hjälp av [Azure-portalen](https://portal.azure.com/). Mer information finns i [Skapa och hantera ett batchkonto](../../batch/batch-account-create-portal.md). Notera kontonamnet och kontonyckeln för batch. Du kan också använda cmdleten [New-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) för att skapa ett batchkonto. Instruktioner om hur du använder den här cmdleten finns i [Komma igång med Batch PowerShell-cmdlets](../../batch/batch-powershell-cmdlets-get-started.md).

Exempellösningen använder Batch (indirekt via en datafabrikspipeline) för att bearbeta data på ett parallellt sätt på en pool av beräkningsnoder (en hanterad samling virtuella datorer).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch-pool med virtuella datorer
Skapa en batchpool med minst två beräkningsnoder.

1. I [Azure-portalen](https://portal.azure.com)väljer du **Bläddra** i den vänstra menyn och väljer **Batchkonton**.

1. Välj ditt batchkonto för att öppna bladet **Batchkonto.**

1. Välj panelen **Pooler.**

1. På **bladet Pooler** väljer du knappen **Lägg till** i verktygsfältet för att lägga till en pool.

   a. Ange ett ID för poolen (**Pool ID**). Observera poolens ID. Du behöver det när du skapar lösningen för datafabriken.

   b. Ange **Windows Server 2012 R2** för inställningen **Operativsystemfamilj.**

   c. Välj en **nodprisnivå**.

   d. Ange **2** som värde för inställningen **Måldedikerad.**

   e. Ange **2** som värde för inställningen **Max aktiviteter per nod.**

   f. Välj **OK** om du vill skapa poolen.

#### <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren
Du använder [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) eller [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (från ClumsyLeaf Software) för att granska och ändra data i dina lagringsprojekt. Du kan också granska och ändra data i loggarna för dina molnbaserade program.

1. Skapa en behållare med namnet **mycontainer** med privat åtkomst (ingen anonym åtkomst).

1. Om du använder CloudXplorer skapar du mappar och undermappar med följande struktur:

   ![Mapp- och undermappsstruktur](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`och `outputfolder` är mappar på `mycontainer`den översta nivån i . Mappen `inputfolder` har undermappar med datum-tidsstämplar (YYYY-MM-DD-HH).

   Om du använder Storage Explorer laddar du upp filer med `inputfolder/2015-11-16-00/file.txt` `inputfolder/2015-11-16-01/file.txt`följande namn i nästa steg: , och så vidare. Det här steget skapar mapparna automatiskt.

1. Skapa en **textfilfil.txt** på datorn med innehåll som har nyckelordet **Microsoft**. Ett exempel är "testa anpassad aktivitet Microsoft testa anpassad aktivitet Microsoft."

1. Ladda upp filen till följande indatamappar i blob storage:

   ![Inmatningsmappar](./media/data-factory-data-processing-using-batch/image4.png)

   Om du använder Storage Explorer laddar du upp **filen file.txt** till **mycontainer**. Välj **Kopiera** i verktygsfältet om du vill skapa en kopia av blobben. Ändra **målblobbnamnet** till i dialogrutan Kopiera `inputfolder/2015-11-16-00/file.txt` **blob** till . Upprepa det här `inputfolder/2015-11-16-01/file.txt` `inputfolder/2015-11-16-02/file.txt`steget `inputfolder/2015-11-16-03/file.txt` `inputfolder/2015-11-16-04/file.txt`för att skapa , , , och så vidare. Den här åtgärden skapar mapparna automatiskt.

1. Skapa en `customactivitycontainer`annan behållare med namnet . Ladda upp zip-filen för anpassad aktivitet till den här behållaren.

#### <a name="visual-studio"></a>Visual Studio
Installera Visual Studio 2012 eller senare för att skapa den anpassade batchaktivitet som ska användas i datafabrikslösningen.

### <a name="high-level-steps-to-create-the-solution"></a>Steg på hög nivå för att skapa lösningen
1. Skapa en anpassad aktivitet som innehåller databehandlingslogiken.

1. Skapa en datafabrik som använder den anpassade aktiviteten.

### <a name="create-the-custom-activity"></a>Skapa den anpassade aktiviteten
Den anpassade aktiviteten för datafabriken är hjärtat i den här exempellösningen. Exempellösningen använder Batch för att köra den anpassade aktiviteten. Information om hur du utvecklar anpassade aktiviteter och använder dem i datafabrikspipelor finns [i Använda anpassade aktiviteter i en pipeline för datafabrik](data-factory-use-custom-activities.md).

Om du vill skapa en .NET-anpassad aktivitet som du kan använda i en datafabrikspipeline skapar du ett .NET-klassbiblioteksprojekt med en klass som implementerar IDotNetActivity-gränssnittet. Det här gränssnittet har bara en metod: Kör. Här är signaturen av metoden:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Metoden har några viktiga komponenter som du behöver förstå:

* Metoden tar fyra parametrar:

  * **linkedServices**. Den här parametern är en uppräkningsbar lista över länkade tjänster som länkar indata-/utdatakällor (till exempel blob storage) till datafabriken. I det här exemplet finns det bara en länkad tjänst av typen Azure Storage som används för både indata och utdata.
  * **datauppsättningar**. Den här parametern är en uppräkningsbar lista över datauppsättningar. Du kan använda den här parametern för att hämta de platser och scheman som definieras av indata- och utdatauppsättningar.
  * **aktivitet**. Den här parametern representerar den aktuella beräkningsentiteten. I det här fallet är det en Batch-tjänst.
  * **logger**. Du kan använda loggern för att skriva felsökningskommentarer som visas som "Användare"-loggen för pipelinen.
* Metoden returnerar en ordlista som kan användas för att kedja ihop anpassade aktiviteter i framtiden. Den här funktionen har inte implementerats ännu, så returnera bara en tom ordlista från metoden.

#### <a name="procedure-create-the-custom-activity"></a>Procedur: Skapa den anpassade aktiviteten
1. Skapa ett .NET-klassbiblioteksprojekt i Visual Studio.

   a. Starta Visual Studio 2012/2013/2015.

   b. Välj **Arkiv** > **Nytt** > **projekt**.

   c. Expandera **mallar**och välj **Visuell C\#**. I den här genomgången använder\#du C , men du kan använda vilket .NET-språk som helst för att utveckla den anpassade aktiviteten.

   d. Välj **Klassbibliotek** i listan över projekttyper till höger.

   e. Ange **MyDotNetActivity** för **namnet**.

   f. Välj **C:\\ADF** för **platsen**. Skapa mappen **ADF** om den inte finns.

   g. Klicka på **OK** för att skapa projektet.

1. Välj **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.

1. I Package Manager-konsolen kör du följande kommando för att importera Microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importera **Azure Storage** NuGet-paketet till projektet. Du behöver det här paketet eftersom du använder API:et för Blob Storage i det här exemplet:

    ```powershell
    Install-Package Az.Storage
    ```
1. Lägg till följande med hjälp av direktiv i källfilen i projektet:

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
1. Ändra namnet på namnområdet till **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Ändra namnet på klassen till **MyDotNetActivity**och härleda den från **gränssnittet IDotNetActivity** enligt bilden:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementera (lägg till) **kör-metoden** för **IDotNetActivity-gränssnittet** i klassen **MyDotNetActivity.** Kopiera följande exempelkod till metoden. En förklaring av logiken som används i den här metoden finns i avsnittet [Kör metod.](#execute-method)

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
1. Lägg till följande hjälpmetoder i klassen. Dessa metoder anropas med metoden **Kör.** Viktigast av allt, **metoden Beräkna** isolerar koden som itererar genom varje blob.

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
    GetFolderPath-metoden returnerar sökvägen till mappen som datauppsättningen pekar på och metoden GetFileName returnerar namnet på blob/filen som datauppsättningen pekar på.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoden Beräkna beräknar antalet instanser av nyckelordet "Microsoft" i indatafilerna (blobbar i mappen). Söktermen "Microsoft" är hårdkodad i koden.

1. Kompilera projektet. Välj **Bygg** på menyn och välj sedan **Bygg lösning**.

1. Starta Utforskaren och gå till mappen **bin\\debug** eller **\\bin release.** Mappvalet beror på typen av version.

1. Skapa en zip-fil **MyDotNetActivity.zip** som innehåller alla binärfiler i ** \\mappen Bin\\Debug.** Du kanske vill inkludera MyDotNetActivity. **pdb-filen** så att du får ytterligare information, till exempel radnumret i källkoden som orsakade problemet när ett fel inträffar.

   ![Listan bin\Felsökningsmapp](./media/data-factory-data-processing-using-batch/image5.png)

1. Ladda upp **MyDotNetActivity.zip** som en blob till blob-behållaren `customactivitycontainer` i blob-lagringen som den StorageLinkedService-länkade tjänsten i ADFTutorialDataFactory använder. Skapa blob-behållaren `customactivitycontainer` om den inte redan finns.

#### <a name="execute-method"></a>Kör metod
Det här avsnittet innehåller mer information om koden i metoden Kör.

1. Medlemmarna för att iterera via indatasamlingen finns i namnområdet [Microsoft.WindowsAzure.Storage.Blob.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) För att iterera genom blob-samlingen måste du använda klassen **BlobContinuationToken.** I huvudsak måste du använda en do-while-loop med token som mekanism för att avsluta slingan. Mer information finns i [Använda Blob-lagring från .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). En grundläggande slinga visas här:

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
   Mer information finns i dokumentationen för [metoden ListBlobsSegmented.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobssegmented)

1. Koden för att arbeta igenom uppsättningen blobbar går logiskt inom do-while-loopen. I metoden **Kör** skickar do-while-loopen listan över blobbar till en metod med namnet **Beräkna**. Metoden returnerar en strängvariabel med namnet **utdata** som är resultatet av att ha itererat genom alla blobbar i segmentet.

   Antalet förekomster av sökordet "Microsoft" i bloben som skickas till metoden **Beräkna** returneras.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. När metoden **Beräkna** är klar måste den skrivas till en ny blob. För varje uppsättning blobbar som bearbetas kan en ny blob skrivas med resultaten. Om du vill skriva till en ny blob hittar du först utdatauppsättningen.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. Koden anropar också hjälpmetoden **GetFolderPath** för att hämta mappsökvägen (lagringsbehållarens namn).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   GetFolderPath-metoden castar DataSet-objektet till en AzureBlobDataSet, som har en egenskap med namnet FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. Koden anropar **metoden GetFileName** för att hämta filnamnet (blob-namnet). Koden liknar den tidigare koden som användes för att hämta mappsökvägen.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. Namnet på filen skrivs genom att skapa ett URI-objekt. URI-konstruktorn använder egenskapen **BlobEndpoint** för att returnera behållarnamnet. Mappsökvägen och filnamnet läggs till för att konstruera utdatablobben URI.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. När namnet på filen har skrivits kan du skriva utdatasträngen från metoden **Beräkna** till en ny blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Skapa datafabriken
I avsnittet [Skapa anpassad aktivitet](#create-the-custom-activity) skapade du en anpassad aktivitet och laddade upp zip-filen med binärfiler och PDB-filen till en blob-behållare. I det här avsnittet skapar du en datafabrik med en pipeline som använder den anpassade aktiviteten.

Indatauppsättningen för den anpassade aktiviteten representerar blobbar (filer) i indatamappen (`mycontainer\\inputfolder`) i blob-lagring. Utdatauppsättningen för aktiviteten representerar utdatablobar i`mycontainer\\outputfolder`utdatamappen ( ) i blob-lagring.

Släpp en eller flera filer i indatamapparna:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Släpp till exempel en fil (file.txt) med följande innehåll i var och en av mapparna:

```
test custom activity Microsoft test custom activity Microsoft
```

Varje indatamapp motsvarar ett segment i datafabriken även om mappen har två eller flera filer. När varje segment bearbetas av pipelinen itererar den anpassade aktiviteten igenom alla blobbar i indatamappen för det segmentet.

Du ser fem utdatafiler med samma innehåll. Utdatafilen från bearbetningen av filen i mappen 2015-11-16-00 har till exempel följande innehåll:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Om du släpper flera filer (file.txt, file2.txt, file3.txt) med samma innehåll i indatamappen visas följande innehåll i utdatafilen. Varje mapp (2015-11-16-00, etc.) motsvarar ett segment i det här exemplet även om mappen har flera indatafiler.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Utdatafilen har tre rader nu, en för varje indatafil (blob) i mappen som är associerad med segmentet (2015-11-16-00).

En aktivitet skapas för varje aktivitetskörning. I det här exemplet finns det bara en aktivitet på gång. När ett segment bearbetas av pipelinen körs den anpassade aktiviteten på Batch för att bearbeta segmentet. Eftersom det finns fem segment (varje segment kan ha flera blobbar eller fil) skapas fem aktiviteter i Batch. När en aktivitet körs på Batch är det den anpassade aktiviteten som körs.

Följande genomgång innehåller ytterligare information.

#### <a name="step-1-create-the-data-factory"></a>Steg 1: Skapa datafabriken
1. När du har loggat in på [Azure-portalen](https://portal.azure.com/)gör du följande:

   a. Välj **NYTT** på den vänstra menyn.

   b. Välj **Data + Analytics** på det **nya** bladet.

   c. Välj **Data Factory** på **dataanalysbladet.**

1. På bladet **Nytt datafabrik** anger du **CustomActivityFactory** för namnet. Namnet på datafabriken måste vara globalt unikt. Om felet "Data fabriksnamn CustomActivityFactory inte är tillgängligt" ändrar du namnet på datafabriken. Använd till exempel ditt namnCustomActivityFactory och skapa datafabriken igen.

1. Välj **RESURSGRUPPNAMN**och välj en befintlig resursgrupp eller skapa en resursgrupp.

1. Kontrollera att prenumerationen och regionen där du vill att datafabriken ska skapas är korrekta.

1. Välj **Skapa** på det **nya datafabriksbladet.**

1. Datafabriken skapas i portalens instrumentpanel.

1. När datafabriken har skapats visas sidan **Datafabrik,** som visar innehållet i datafabriken.

   ![Sidan Datafabrik](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Steg 2: Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en datafabrik. I det här steget länkar du ditt lagringskonto och batchkonto till din datafabrik.

#### <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst
1. Välj panelen **Författare och distribuera** på **datafabriksbladet** för **CustomActivityFactory**. Data Factory Editor visas.

1. Välj **Nytt datalager** i kommandofältet och välj **Azure-lagring.** Det JSON-skript som du använder för att skapa en lagringslänkade tjänst i redigeraren visas.

   ![Nytt datalager](./media/data-factory-data-processing-using-batch/image7.png)

1. Ersätt **account name** med namnet på ditt lagringskonto. Ersätt **account key** med åtkomstnyckeln för lagringskontot. Mer information om hur du hämtar åtkomstnyckeln för lagring finns i [Hantera åtkomstnycklar för lagringskonto](../../storage/common/storage-account-keys-manage.md).

1. Välj **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

   ![Distribuera](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Skapa en Azure Batch-länkad tjänst
I det här steget skapar du en länkad tjänst för ditt Batch-konto som används för att köra den anpassade aktiviteten för datafabriken.

1. Välj **Ny beräkning** i kommandofältet och välj Azure **Batch.** Det JSON-skript som du använder för att skapa en batchlänkd tjänst i redigeraren visas.

1. I JSON-skriptet:

   a. Ersätt **kontonamn** med namnet på ditt Batch-konto.

   b. Ersätt **åtkomstnyckeln** med åtkomstnyckeln för batchkontot.

   c. Ange ID för poolen för egenskapen **poolName.** För den här egenskapen kan du ange antingen poolnamnet eller pool-ID:t.

   d. Ange batch-URI för egenskapen **batchUri** JSON.

      > [!IMPORTANT]
      > URL:en från bladet **Batch-konto** \<är\>i följande format: kontonamn . \<regionen\>.batch.azure.com. För **egenskapen batchUri** i JSON-skriptet måste du ta bort a88"accountname". ** från webbadressen. Ett exempel är `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Bladet batchkonto](./media/data-factory-data-processing-using-batch/image9.png)

      För egenskapen **poolName** kan du också ange ID för poolen i stället för namnet på poolen.

      > [!NOTE]
      > Tjänsten Data Factory stöder inte ett alternativ på begäran för Batch som det gör för HDInsight. Du kan bara använda din egen batchpool i en datafabrik.
      >
      >

   e. Ange **StorageLinkedService** för egenskapen **linkedServiceName.** Du skapade den länkade tjänsten i föregående steg. Den här lagringen används som mellanlagringsområde för filer och loggar.

1. Välj **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

#### <a name="step-3-create-datasets"></a>Steg 3: Skapa datauppsättningar
I det här steget skapar du datauppsättningar som representerar in- och utdata.

#### <a name="create-the-input-dataset"></a>Skapa indatauppsättningen
1. Välj knappen Ny datauppsättning i **verktygsfältet** i Data Factory Editor. Välj **Azure Blob-lagring** i listrutan.

1. Ersätt JSON-skriptet i den högra rutan med följande JSON-kodavsnitt:

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

    Du skapar en pipeline senare i den här genomgången med starttiden 2015-11-16T00:00:00Z och sluttiden 2015-11-16T05:00:00Z. Det är planerat att producera data per timme, så det finns fem indata/ utdatasegment\> (mellan 00:00:00 - **00** **05:00:00).**

    **Frekvensen** och **intervallet** för indatauppsättningen är inställt på **Timme** och **1**, vilket innebär att indatasegmentet är tillgängligt varje timme.

    Starttiden för varje segment representeras av systemvariabeln **SegmentStart** i föregående JSON-utdrag. Här är starttiderna för varje segment.

    | **Skiva** | **Starttid**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    **folderPath** beräknas med hjälp av år, månad, dag och timme del av segmentet starttid (**SliceStart**). Så här mappas en indatamapp till ett segment.

    | **Skiva** | **Starttid**          | **Inmatningsmapp**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Välj **Distribuera** i verktygsfältet om du vill skapa och distribuera tabellen **InputDataset.**

#### <a name="create-the-output-dataset"></a>Skapa datauppsättningen för utdata
I det här steget skapar du en annan datauppsättning av typen AzureBlob för att representera utdata.

1. Välj knappen Ny datauppsättning i **verktygsfältet** i Data Factory Editor. Välj **Azure Blob-lagring** i listrutan.

1. Ersätt JSON-skriptet i den högra rutan med följande JSON-kodavsnitt:

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

    En utdatablob/fil genereras för varje indatasegment. Så här namnges en utdatafil för varje segment. Alla utdatafiler genereras i en `mycontainer\\outputfolder`utdatamapp.

    | **Skiva** | **Starttid**          | **Utdatafil**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Kom ihåg att alla filer i en indatamapp (till exempel 2015-11-16-00) ingår i en skiva med starttiden 2015-11-16-00. När det här segmentet bearbetas söker den anpassade aktiviteten igenom varje fil och skapar en rad i utdatafilen med antalet förekomster av sökordet "Microsoft". Om det finns tre filer i mappen 2015-11-16-00 finns det tre rader i utdatafilen 2015-11-16-00.txt.

1. Välj **Distribuera** i verktygsfältet om du vill skapa och distribuera **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Steg 4: Skapa och kör pipelinen med en anpassad aktivitet
I det här steget skapar du en pipeline med en aktivitet, den anpassade aktivitet som du skapade tidigare.

> [!IMPORTANT]
> Om du inte har laddat upp **file.txt** till indatamappar i blob-behållaren gör du det innan du skapar pipelinen. Egenskapen **isPaused** är inställd på false i pipeline-JSON, så pipelinen körs omedelbart eftersom **startdatumet** är tidigare.
>
>

1. Välj **Ny pipeline** i kommandofältet i Data Factory Editor. Om kommandot inte visas väljer du ellipssymbolen för att visa det.

1. Ersätt JSON-skriptet i den högra rutan med följande JSON-kodavsnitt:

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

   * Endast en aktivitet är på gång och den är av typen **DotNetActivity**.
   * **AssemblyName** är inställt på namnet på DLL **MyDotNetActivity.dll**.
   * **EntryPoint** är inställt på **MyDotNetActivityNS.MyDotNetActivity**. Det är i \<grunden\>namnområde . \<klassnamn\> i koden.
   * **PackageLinkedService** är inställt på **StorageLinkedService**, som pekar på blob-lagring som innehåller den anpassade zip-filen för aktivitet. Om du använder olika lagringskonton för indata-/utdatafiler och zip-filen för anpassad aktivitet måste du skapa en annan lagringslänkad tjänst. Den här artikeln förutsätter att du använder samma lagringskonto.
   * **PackageFile** är inställd på **customactivitycontainer/MyDotNetActivity.zip**. Det är i \<formatet containerforthezip\>/\<nameofthezip.zip\>.
   * Den anpassade aktiviteten tar **InputDataset** som input och **OutputDataset** som utdata.
   * Egenskapen **linkedServiceName** för den anpassade aktiviteten pekar på **AzureBatchLinkedService**, som talar om för Data Factory att den anpassade aktiviteten måste köras på Batch.
   * Inställningen **för samtidighet** är viktig. Om du använder standardvärdet, som är 1, även om du har två eller flera beräkningsnoder i batch-poolen, bearbetas segmenten en efter en. Därför utnyttjar du inte den parallella bearbetningskapaciteten för Batch. Om du anger **samtidighet** till ett högre värde, säg 2, betyder det att två segment (motsvarar två aktiviteter i Batch) kan bearbetas samtidigt. I det här fallet används båda virtuella datorerna i batchpoolen. Ange egenskapen samtidighet på rätt sätt.
   * Endast en aktivitet (segment) körs på en virtuell dator som standard som standard. Som standard är **högsta aktiviteter per virtuell dator** inställd på 1 för en batchpool. Som en del av förutsättningarna skapade du en pool med den här egenskapen inställd på 2. Därför kan två datafabrikssegment köras på en virtuell dator samtidigt.
     - Egenskapen **isPaused** är inställd på false som standard. Pipelinen körs omedelbart i det här exemplet eftersom segmenten börjar tidigare. Du kan ange den här egenskapen så att den **är true** så att den pausas och att den ska **startas** om igen.
     -   **Start-** och **sluttiderna** är fem timmars mellanrum. Segment produceras varje timme, så fem segment produceras av rörledningen.

1. Välj **Distribuera** i kommandofältet för att distribuera pipelinen.

#### <a name="step-5-test-the-pipeline"></a>Steg 5: Testa rörledningen
I det här steget testar du pipelinen genom att släppa filer i indatamapparna. Börja med att testa pipelinen med en fil för varje indatamapp.

1. Välj **Diagram**på **datafabriksbladet** i Azure-portalen .

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

1. Dubbelklicka på indatauppsättningen **InputDataset**i **diagramvyn** .

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. **Bladet InputDataset** visas med alla fem segmenten klara. Lägg märke till **SKIVA STARTTID** OCH **SKIVA SLUTTID** FÖR varje segment.

   ![Start- och sluttider för inmatningssegment](./media/data-factory-data-processing-using-batch/image12.png)

1. Välj **OutputDataset**i **diagramvyn** .

1. De fem utdatasegmenten visas i läget **Klar** om de har producerats.

   ![Start- och sluttider för utmatningssegment](./media/data-factory-data-processing-using-batch/image13.png)

1. Använd portalen för att visa de uppgifter som är associerade med segmenten och se vilken virtuell dator varje segment kördes på. Mer information finns i avsnittet [Data factory och batch-integrering.](#data-factory-and-batch-integration)

1. Utdatafilerna `mycontainer` visas `outputfolder` under i din blob-lagring.

   ![Utdatafiler i lagring](./media/data-factory-data-processing-using-batch/image15.png)

   Fem utdatafiler visas, en för varje indatasegment. Var och en av utdatafilerna har innehåll som liknar följande utdata:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Följande diagram visar hur datafabriken segmentar mappa till uppgifter i Batch. I det här exemplet har ett segment bara en körning.

   ![Diagram över segmentmappning](./media/data-factory-data-processing-using-batch/image16.png)

1. Försök nu med flera filer i en mapp. Skapa filerna **file2.txt**, **file3.txt**, **file4.txt**och **file5.txt** med samma innehåll som i file.txt i mappen **2015-11-06-01**.

1. I utdatamappen tar du bort utdatafilen **2015-11-16-01.txt**.

1. Högerklicka på segmentet med **SKIVA STARTTID** på **2015-11-16 på bladet** **OutputDataset** . Välj **Kör** om du vill köra om/bearbeta om segmentet. Segmentet har nu fem filer i stället för en fil.

    ![Kör](./media/data-factory-data-processing-using-batch/image17.png)

1. När segmentet har körts och dess status är **Klar**kontrollerar du innehållet i utdatafilen för det här segmentet (**2015-11-16-01.txt**). Utdatafilen visas `mycontainer` `outputfolder` under i blob-lagringen. Det bör finnas en rad för varje fil i segmentet.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Om du inte tog bort utdatafilen 2015-11-16-01.txt innan du försökte med fem indatafiler visas en rad från föregående segmentkörning och fem rader från den aktuella segmentkörningen. Som standard läggs innehållet till i utdatafilen om det redan finns.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integrering av datafabrik och batch
Tjänsten Data Factory skapar ett jobb i `adf-poolname:job-xxx`Batch med namnet .

![Batch-jobb](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

En aktivitet i jobbet skapas för varje aktivitetskörning i ett segment. Om 10 segment är klara att bearbetas skapas 10 aktiviteter i jobbet. Du kan ha mer än ett segment som körs parallellt om du har flera beräkningsnoder i poolen. Om det maximala antalet aktiviteter per beräkningsnod är inställt på större än en, kan mer än ett segment köras på samma beräkning.

I det här exemplet finns det fem segment, så det finns fem aktiviteter i Batch. Med **samtidighet** inställd på **5** i pipeline JSON i datafabriken och **Högsta uppgifter per virtuell dator** inställd på **2** i batch-poolen med **2** virtuella datorer, körs aktiviteterna snabbt. (Kontrollera start- och sluttider för aktiviteter.)

Använd portalen för att visa batch-jobbet och dess uppgifter som är associerade med segmenten och se vilken virtuell dator varje segment kördes på.

![Batchjobbuppgifter](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Felsöka pipeline
Felsökning består av några grundläggande tekniker.

1. Om indatasegmentet inte är inställt **på Klar**erbeter du att indatamappstrukturen är korrekt och att filen.txt finns i indatamapparna.

   ![Struktur för indatamapp](./media/data-factory-data-processing-using-batch/image3.png)

1. Använd **IActivityLogger-objektet** i metoden **Kör** för att logga information som hjälper dig att felsöka problem. De loggade meddelandena visas\_i användarfilen 0.log.

   På **bladet OutputDataset** markerar du segmentet för att se **datasegmentbladet** för det segmentet. Under **Aktivitetskörning**ser du en aktivitet som körs för segmentet. Om du väljer **Kör** i kommandofältet kan du starta en annan aktivitetskörning för samma segment.

   När du väljer aktivitetskörning visas bladet **Aktivitetskörningsinformation** med en lista med loggfiler. Du ser loggade meddelanden\_i användarfilen 0.log. När ett fel inträffar visas tre aktivitetskörningar eftersom antalet försök på nytt är inställt på 3 i pipeline/aktivitets-JSON. När du väljer aktivitetskörningen visas loggfilerna som du kan granska för att felsöka felet.

   ![Bladen OutputDataset och Data-segment](./media/data-factory-data-processing-using-batch/image18.png)

   Välj **användare-0.log**i listan över loggfiler . På den högra panelen visas resultatet av att använda metoden **IActivityLogger.Write.**

   ![Informationsblad för aktivitetskörning](./media/data-factory-data-processing-using-batch/image19.png)

   Kontrollera system-0.log för alla systemfelmeddelanden och undantag.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Inkludera **PDB-filen** i zip-filen så att felinformationen har information som anropsstack när ett fel inträffar.

1. Alla filer i zip-filen för den anpassade aktiviteten måste vara på den översta nivån utan undermappar.

   ![Lista över zip-filer för anpassad aktivitet](./media/data-factory-data-processing-using-batch/image20.png)

1. Kontrollera att **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) och **packageLinkedService** (bör peka på blob-lagringen som innehåller zip-filen) är inställda på rätt värden.

1. Om du åtgärdade ett fel och vill bearbeta segmentet igen högerklickar du på segmentet i **bladet OutputDataset** och väljer **Kör**.

   ![Alternativet OutputDataset blad Run](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > En behållare finns i blob-lagringen med namnet `adfjobs`. Den här behållaren tas inte bort automatiskt, men du kan ta bort den på ett säkert sätt när du har testat lösningen. På samma sätt skapar datafabrikslösningen `adf-\<pool ID/name\>:job-0000000001`ett batchjobb med namnet . Du kan ta bort det här jobbet när du har testat lösningen om du vill.
   >
   >
1. Den anpassade aktiviteten använder inte **filen app.config** från paketet. Om koden läser några anslutningssträngar från konfigurationsfilen fungerar den därför inte vid körning. Det bästa sättet när du använder Batch är att hålla hemligheter i Azure Key Vault. Använd sedan ett certifikatbaserat tjänsthuvudnamn för att skydda nyckelvalvet och distribuera certifikatet till batchpoolen. Den anpassade aktiviteten .NET kan komma åt hemligheter från nyckelvalvet vid körning. Denna generiska lösning kan skalas till alla typer av hemlighet, inte bara en anslutningssträng.

    Det finns en enklare lösning, men det är inte en bra idé. Du kan skapa en SQL-databaslänkd tjänst med anslutningsstränginställningar. Sedan kan du skapa en datauppsättning som använder den länkade tjänsten och kedja datauppsättningen som en dummy indatauppsättning till den anpassade .NET-aktiviteten. Du kan sedan komma åt den länkade tjänstens anslutningssträng i den anpassade aktivitetskoden. Det ska fungera bra vid körning.  

#### <a name="extend-the-sample"></a>Utöka provet
Du kan utöka det här exemplet om du vill veta mer om datafabriks- och batchfunktioner. Om du till exempel vill bearbeta segment i ett annat tidsintervall gör du följande:

1. Lägg till följande undermappar i `inputfolder`: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 och 2015-11-16-09. Placera indatafiler i mapparna. Ändra sluttiden för pipelinen från `2015-11-16T05:00:00Z` till `2015-11-16T10:00:00Z`. Dubbelklicka på **InputDataset** i **diagramvyn** och bekräfta att indatasegmenten är klara. Dubbelklicka på **OutputDataset** om du vill visa läget för utdatasegmenten. Om de är i **läget Klar** kontrollerar du utdatamappen för utdatafilerna.

1. Öka eller minska **samtidighetsinställningen** för att förstå hur det påverkar lösningens prestanda, särskilt bearbetningen som sker på Batch. Mer information om **samtidighetsinställningen** finns i "Steg 4: Skapa och kör pipelinen med en anpassad aktivitet".

1. Skapa en pool med högre/lägre **högsta uppgifter per virtuell dator**. Om du vill använda den nya poolen som du skapade uppdaterar du den batchlänkade tjänsten i datafabrikslösningen. Mer information om inställningen **Maximal aktivitet per virtuell dator** finns i "Steg 4: Skapa och kör pipelinen med en anpassad aktivitet".

1. Skapa en batchpool med funktionen **för automatisk skalning.** Att automatiskt skala beräkningsnoder i en batchpool är den dynamiska justeringen av processorkraft som används av ditt program.

    Exempelformeln här uppnår följande beteende. När poolen skapas börjar den med en virtuell dator. Måttet $PendingTasks definierar antalet aktiviteter i de löp- och aktiva (köade) tillstånden. Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger TargetDedicated därefter. Det säkerställer att TargetDedicated aldrig går längre än 25 virtuella datorer. När nya uppgifter skickas växer poolen automatiskt. När uppgifterna slutförs blir virtuella datorer fria en efter en och den automatiska skalningen krymper dessa virtuella datorer. Du kan justera startnumberofVM och maxNumberofVMs till dina behov.

    Formel för automatisk skalning:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Mer information finns i [Skala automatiskt beräkningsnoder i en batchpool](../../batch/batch-automatic-scaling.md).

   Om poolen använder standardvärdet [för automatisk skalningInvärdering](https://msdn.microsoft.com/library/azure/dn820173.aspx)kan batch-tjänsten ta 15 till 30 minuter att förbereda den virtuella datorn innan den anpassade aktiviteten körs. Om poolen använder en annan automatisk skalningSutvärderingInterval kan batchtjänsten ta autoScaleEvaluationInterval plus 10 minuter.

1. I exempellösningen anropar **metoden Kör** metoden **metoden Calculate** som bearbetar ett indatasegment för att producera ett utdatasegment. Du kan skriva din egen metod för att bearbeta indata och ersätta metoden **Beräkna** i metoden **Kör** med ett anrop till din metod.

### <a name="next-steps-consume-the-data"></a>Nästa steg: Förbruka data
När du har bearbetat data kan du använda dem med onlineverktyg som Power BI. Här är länkar som hjälper dig att förstå Power BI och hur du använder det i Azure:

* [Utforska en datauppsättning i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Komma igång med Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Uppdatera data i Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure och Power BI: Grundläggande översikt](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referenser
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introduktion till datafabrikstjänsten](data-factory-introduction.md)
  * [Komma igång med Data Factory](data-factory-build-your-first-pipeline.md)
  * [Använda anpassade aktiviteter i en Pipeline för datafabrik](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Grunderna i batch](../../batch/batch-technical-overview.md)
  * [Översikt över batchfunktioner](../../batch/batch-api-basics.md)
  * [Skapa och hantera ett batchkonto i Azure-portalen](../../batch/batch-account-create-portal.md)
  * [Komma igång med batchklientbiblioteket för .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
