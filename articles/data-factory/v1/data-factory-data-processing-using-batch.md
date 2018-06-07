---
title: Bearbeta stora datauppsättningar genom att använda Data Factory och Batch | Microsoft Docs
description: Beskriver hur du bearbetar stora mängder data i ett Azure Data Factory-pipelinen med hjälp av parallell bearbetning möjligheterna för Azure Batch.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: ad883248097fd84e2fa064515b6c7a5232aaa3d3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621946"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Processen stora datauppsättningar genom att använda Data Factory och Batch
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig. Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [anpassade aktiviteter i Data Factory version 2](../transform-data-using-dotnet-custom-activity.md).

Den här artikeln beskriver en arkitektur på en exempellösning som flyttar och bearbetar stora datauppsättningar på automatisk och schemalagda sätt. Det ger också en slutpunkt till slutpunkt-genomgång för att implementera lösningen genom att använda Data Factory och Azure Batch.

Den här artikeln är längre än en typisk artikel eftersom den innehåller en genomgång av en lösning för hela exemplet. Om du har använt Batch och Data Factory, du kan lära dig om dessa tjänster och hur de fungerar tillsammans. Om du vet något om tjänsterna och designa/utforma en lösning, kan du fokusera på den [arkitektur avsnittet](#architecture-of-sample-solution) av artikeln. Om du utvecklar en prototyp eller en lösning, kanske du vill prova att använda instruktionerna i den [genomgången](#implementation-of-sample-solution). Vi erbjuder kommentarer om det här innehållet och hur du använder den.

Först ska vi titta på hur Data Factory och Batch-tjänster kan hjälpa dig processen stora datamängder i molnet.     

## <a name="why-azure-batch"></a>Varför Azure Batch?
 Du kan använda Batch för att köra storskaliga parallella och högpresterande datorbearbetning (HPC) program effektivt i molnet. Det är en platform-tjänst som schemalägger beräkningsintensiva arbete ska köras på en hanterad samling av virtuella datorer (VM). Kan anpassas automatiskt beräkningsresurser för att möta behoven hos dina jobb.

Med Batch-tjänsten definierar du Azure-beräkningsresurser att köra dina program parallellt och i stor skala. Du kan köra på begäran eller schemalagda jobb. Behöver du inte manuellt skapa, konfigurera och hantera ett HPC-kluster, enskilda virtuella datorer, virtuella nätverk, eller en komplex jobb och schemaläggning infrastruktur.

 Om du inte är bekant med Batch hjälper dig att förstå arkitektur/implementering av lösningen som beskrivs i den här artikeln i följande artiklar:   

* [Grunderna i Batch](../../batch/batch-technical-overview.md)
* [Översikt över funktionen för batch](../../batch/batch-api-basics.md)

Du kan också finns mer information om Batch [Utbildningsväg för Batch](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Varför Azure Data Factory?
Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Du kan använda Data Factory för att skapa hanterade data pipelines som flytta data från lokala och molnet datalager till en centraliserad dataarkiv. Ett exempel är Azure Blob storage. Du kan använda Data Factory för att bearbeta/Transformera data med hjälp av tjänster som Azure HDInsight och Azure Machine Learning. Du kan också schemalägga data pipelines körs med schemalagda sätt (till exempel varje timme, varje dag, och varje vecka). Du kan övervaka och hantera pipelines snabbt identifiera problem och vidta åtgärder.

  Om du inte är bekant med Data Factory hjälper dig att förstå arkitektur/implementering av lösningen som beskrivs i den här artikeln i följande artiklar:  

* [Introduktion till Data Factory](data-factory-introduction.md)
* [Skapa din första pipeline för data](data-factory-build-your-first-pipeline.md)   

Du kan också finns mer information om Data Factory [för Data Factory-Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Data Factory och Batch tillsammans
Data Factory innehåller inbyggda aktiviteter. Aktiviteten kopiera används till exempel att kopiera/flytta data från ett dataarkiv som ska användas som källa till ett dataarkiv som mål. Aktiviteten Hive används för att bearbeta data med hjälp av Hadoop-kluster (HDInsight) på Azure. En lista över stöds omvandling aktiviteter, se [Data transformation aktiviteter](data-factory-data-transformation-activities.md).

Du kan också skapa anpassade aktiviteter för .NET för att flytta eller bearbeta data med egna logik. Du kan köra dessa aktiviteter på ett HDInsight-kluster eller på en Batch-pool för virtuella datorer. När du använder Batch, kan du konfigurera poolen Autoskala (lägga till eller ta bort virtuella datorer baserat på arbetsbelastning) utifrån en formel som du anger.     

## <a name="architecture-of-a-sample-solution"></a>Arkitekturen för en exempellösning
  Arkitekturen som beskrivs i den här artikeln är avsedd för en enkel lösning. Det är också relevant för avancerade scenarier, till exempel risk modellering av finansiella tjänster, bild bearbetning och återgivning och genom analys.

Diagrammet visar hur Data Factory samordnar dataförflyttning och bearbetning. Den visar även hur Batch bearbetar data i en parallell sätt. Hämta och skriva ut diagram för enkel referens (11 x 17 tum eller A3-storlek). Du hittar diagrammet så att du kan skriva ut den i [HPC och data orchestration med hjälp av Batch- och Data Factory](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram för storskalig databearbetning](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

Följande lista innehåller de grundläggande steg i processen. Lösningen innehåller koden och beskrivningar för att skapa lösningen slutpunkt till slutpunkt.

* **Konfigurera Batch med en pool med compute-noder (virtuella datorer).** Du kan ange antalet noder och storleken på varje nod.

* **Skapa en instans av Data Factory** som har konfigurerats med entiteter som representerar blob-lagring, beräkning Batch-tjänsten, i/o-data och ett arbetsflöde/pipeline med aktiviteter som att flytta och transformera data.

* **Skapa en anpassad .NET-aktivitet i Data Factory-pipelinen.** Aktiviteten är din användarkod som körs på Batch-pool.

* **Lagra stora mängder inkommande data som blobar i Azure Storage.** Data är uppdelat i logiska segment (vanligtvis genom tid).

* **Data Factory kopierar data som bearbetas parallellt** till den sekundära platsen.

* **Data Factory körs den anpassade aktiviteten med hjälp av poolen allokeras av Batch.** Data Factory kan köra aktiviteter samtidigt. Varje aktivitet bearbetar ett segment av data. Resultaten lagras i lagringen.

* **Data Factory flyttar de slutliga resultaten till en tredje plats** antingen för distribution via en app eller för ytterligare bearbetning av andra verktyg.

## <a name="implementation-of-the-sample-solution"></a>Implementering av
Exempellösningen är avsiktligt enkelt. Den har utformats för att visa dig hur du använder Data Factory och Batch tillsammans till processen datamängder. Lösningen är antalet förekomster av sökordet ”Microsoft” i inkommande filer som är ordnade i en tidsserie. Därefter visas det antal utgående filer.

**Tid:** om du känner till grunderna i Azure Data Factory och Batch och har följande förutsättningar, den här lösningen tar en till två timmar att slutföra.

### <a name="prerequisites"></a>Förutsättningar
#### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har en Azure-prenumeration kan du snabbt skapa ett kostnadsfritt utvärderingskonto. Mer information finns i [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure Storage-konto
Du kan använda ett lagringskonto för att lagra data i den här självstudiekursen. Om du inte har ett lagringskonto finns [skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Exempellösningen använder blob-lagring.

#### <a name="azure-batch-account"></a>Azure Batch-kontot
Skapa ett Batch-konto med hjälp av den [Azure-portalen](http://portal.azure.com/). Mer information finns i [skapa och hantera ett batchkonto](../../batch/batch-account-create-portal.md). Observera Batch-kontot namn och åtkomstnyckel. Du kan också använda den [ny AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) för att skapa ett Batch-konto. Anvisningar för hur du använder denna cmdlet finns [Kom igång med Batch-PowerShell-cmdlets](../../batch/batch-powershell-cmdlets-get-started.md).

Exempellösningen använder Batch (indirekt via en data factory-pipelinen) att bearbeta data på en parallell sätt i en pool med compute-noder (en hanterad samling med virtuella datorer).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch-pool för virtuella datorer
Skapa en Batch-pool med minst två compute-noder.

1. I den [Azure-portalen](https://portal.azure.com)väljer **Bläddra** i den vänstra menyn och välj **Batch-konton**.

2. Välj Batch-kontot för att öppna den **Batch-kontot** bladet.

3. Välj den **pooler** panelen.

4. På den **pooler** bladet väljer den **Lägg till** i verktygsfältet för att lägga till en pool.

   a. Ange ett ID för poolen (**programpoolens ID**). Observera ID för poolen. Du behöver den när du skapar data factory-lösning.

   b. Ange **Windows Server 2012 R2** för den **Operativsystemsfamilj** inställningen.

   c. Välj en **nodprisnivå**.

   d. Ange **2** som värde för den **mål dedikerade** inställningen.

   e. Ange **2** som värde för den **maximalt antal uppgifter per nod** inställningen.

   f. Välj **OK** skapa poolen.

#### <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren
Du använder [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) eller [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (från ClumsyLeaf programvara) att granska och ändra data i ditt projekt för lagring. Du kan också granska och ändra data i loggarna för din molnbaserade program.

1. Skapa en behållare med namnet **minbehållare** med privat åtkomst (ingen anonym åtkomst).

2. Om du använder CloudXplorer skapa mappar och undermappar med följande struktur:

   ![Mappen och undermapparna struktur](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` och `outputfolder` är mappar på högsta nivå i `mycontainer`. Den `inputfolder` mappen innehåller undermappar med datum-/ tidsstämplar (åååå-MM-DD-HH).

   Om du använder Lagringsutforskaren, i nästa steg kan du överföra filer med följande namn: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`och så vidare. Det här steget skapar automatiskt mappar.

3. Skapa en textfil **fil.txt** på datorn med innehåll som har nyckelordet **Microsoft**. Ett exempel är ”test anpassad aktivitet Microsoft test anpassad aktivitet Microsoft”.

4. Överföra filen till följande inkommande mappar i blob storage:

   ![Inkommande mappar](./media/data-factory-data-processing-using-batch/image4.png)

   Om du använder Lagringsutforskaren överför den **fil.txt** filen till **minbehållare**. Välj **kopiera** i verktygsfältet för att skapa en kopia av blob. I den **kopiera Blob** i dialogrutan den **blob målnamn** till `inputfolder/2015-11-16-00/file.txt`. Upprepa det här steget för att skapa `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`och så vidare. Den här åtgärden skapar automatiskt mappar.

5. Skapa en annan behållare med namnet `customactivitycontainer`. Överföra anpassad aktivitet zip-filen till den här behållaren.

#### <a name="visual-studio"></a>Visual Studio
Installera Visual Studio 2012 eller senare för att skapa den anpassade Batch-aktiviteten som ska användas i data factory-lösning.

### <a name="high-level-steps-to-create-the-solution"></a>Övergripande steg för att skapa lösningen
1. Skapa en anpassad aktivitet som innehåller logik för databearbetning.

2. Skapa en datafabrik som använder den anpassade aktiviteten.

### <a name="create-the-custom-activity"></a>Skapa den anpassade aktiviteten
Data factory anpassad aktivitet är hjärtat i det här exemplet lösning. Exempellösningen använder Batch för att köra den anpassade aktiviteten. Information om hur du utvecklar anpassade aktiviteter och använda dem i data factory pipelines finns [använda anpassade aktiviteter i en data factory-pipelinen](data-factory-use-custom-activities.md).

Om du vill skapa en anpassad .NET-aktivitet som du kan använda i en data factory-pipelinen, skapar du en .NET-klassbiblioteksprojektet med en klass som implementerar gränssnittet IDotNetActivity. Det här gränssnittet har endast en metod: köra. Här är metodens signatur:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Metoden har några viktiga komponenter som behövs för att förstå:

* Metoden som använder fyra parametrar:

  * **linkedServices**. Den här parametern är en enumerable lista över länkade tjänster som länkar i/o-datakällor (till exempel blobblagring) till datafabriken. I det här exemplet finns bara en länkad tjänst av typen Azure Storage som används för både inkommande och utgående.
  * **datauppsättningar**. Den här parametern är en enumerable lista över datauppsättningar. Du kan använda den här parametern för att få platser och scheman som definierats av inkommande och utgående datauppsättningar.
  * **aktiviteten**. Den här parametern representerar den aktuella beräknings-entiteten. I det här fallet är det en Batch-tjänsten.
  * **loggaren**. Du kan använda loggaren skriva debug kommentarer som yta som ”användare” loggen för pipeline.
* Metoden returnerar en ordlista som kan användas för att kedja anpassade aktiviteter tillsammans i framtiden. Den här funktionen har inte implementerats, returnera bara ett tomt Ordbok från metoden.

#### <a name="procedure-create-the-custom-activity"></a>Metod: Skapa den anpassade aktiviteten
1. Skapa en .NET-klassbiblioteksprojektet i Visual Studio.

   a. Starta Visual Studio 2012/2013/2015.

   b. Välj **Arkiv** > **Nytt** > **Projekt**.

   c. Expandera **mallar**, och välj **Visual C\#**. I den här genomgången ska du använda C\#, men du kan använda alla .NET-språk för att utveckla anpassade aktiviteten.

   d. Välj **klassbiblioteket** från listan över projekttyper till höger.

   e. Ange **MyDotNetActivity** för den **namn**.

   f. Välj **C:\\ADF** för den **plats**. Skapa mappen **ADF** om den inte finns.

   g. Välj **OK** att skapa projektet.

2. Välj **verktyg** > **NuGet Package Manager** > **Pakethanterarkonsolen**.

3. Kör följande kommando för att importera Microsoft.Azure.Management.DataFactories i Package Manager-konsolen:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importera den **Azure Storage** NuGet-paket i projektet. Du behöver det här paketet eftersom du använder Blob Storage-API i det här exemplet:

    ```powershell
    Install-Package Azure.Storage
    ```
5. Lägg till följande med hjälp av direktiven till källfilen i projektet:

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
6. Ändra namnet på namnområdet som **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Ändra namnet på klassen som **MyDotNetActivity**, och härleds från den **IDotNetActivity** gränssnitt som visas:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementera (Lägg till) på **kör** metod för den **IDotNetActivity** gränssnitt till den **MyDotNetActivity** klass. Kopiera följande exempelkod till metoden. En beskrivning av den logik som används i den här metoden finns i [Execute-metoden](#execute-method) avsnitt.

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
9. Lägg till följande helper-metoder i klassen. De här metoderna anropas av den **Execute** metod. De viktigaste den **Calculate** metoden isolerar den kod som går igenom varje blob.

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
    Metoden GetFolderPath returnerar sökvägen till mappen som dataset pekar på, och metoden GetFileName Returnerar namnet på blob/fil som dataset pekar på.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoden Calculate beräknar antalet instanser av nyckelordet ”Microsoft” i indatafiler (blobbar i mappen). Sökordet ”Microsoft” är hårdkodat i koden.

10. Kompilera projektet. Välj **skapa** -menyn och välj sedan **skapa lösning**.

11. Starta Utforskaren och gå till den **bin\\felsöka** eller **bin\\släpper** mapp. Valet av mappen beror på vilken typ av version.

12. Skapa en zip-fil **MyDotNetActivity.zip** som innehåller alla binärfiler i den  **\\bin\\felsöka** mapp. Du kanske vill inkludera i MyDotNetActivity. **pdb** filen så att du får ytterligare information, till exempel radnumret i källkoden som har orsakat problemet när ett fel uppstår.

   ![Listan över mappar bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

13. Överför **MyDotNetActivity.zip** som en blob till blob-behållare `customactivitycontainer` i blob-lagring som StorageLinkedService kopplad tjänst i ADFTutorialDataFactory använder. Skapa blob-behållaren `customactivitycontainer` om den inte redan finns.

#### <a name="execute-method"></a>Execute-metoden
Det här avsnittet innehåller mer information om koden i Execute-metoden.

1. Medlemmar för att söka igenom inkommande samling finns i den [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) namnområde. Om du vill söka igenom blob-samling, du måste använda den **BlobContinuationToken** klass. I princip måste du använda en gör-samtidigt med token som metod för att avsluta slingan. Mer information finns i [använda Blob storage från .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). En grundläggande loop visas här:

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
   Mer information finns i dokumentationen för den [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) metod.

2. Koden för att arbeta med en uppsättning blobbar logiskt kommer inom do-while-slinga. I den **kör** metod, do-medan loop skickar listan över blobbar till en metod med namnet **beräkna**. Metoden returnerar en variabel med namnet **utdata** som är resultatet av att ha hävdade via alla BLOB i segmentet.

   Returnerar antalet förekomster av sökordet ”Microsoft” i blob skickades till den **Calculate** metod.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Efter den **Calculate** metoden är klar, det måste skrivas till en ny blob. En ny blob kan skrivas med resultatet för varje uppsättning blobbar som bearbetas. Om du vill skriva till en ny blob att hitta datamängd för utdata.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. Koden anropar också metoden helper **GetFolderPath** att hämta sökvägen till mappen (storage behållarens namn).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Metoden GetFolderPath kastar DataSet-objekt till en AzureBlobDataSet som har en egenskap med namnet FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. Koden anropar den **GetFileName** metod för att hämta filnamnet (blob-namn). Koden liknar föregående kod som användes för att hämta sökvägen till mappen.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Namnet på filen skrivs genom att skapa ett URI-objekt. URI-konstruktorn använder den **BlobEndpoint** egenskapen att returnera behållarens namn. Sökvägen och namnet på mappen läggs till konstruera utdata blob-URI.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. När namnet på filen skrivs, du kan skriva utdata-strängen från den **Calculate** metod för att en ny blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Skapa datafabriken
I den [skapa den anpassade aktiviteten](#create-the-custom-activity) avsnittet du skapat en anpassad aktivitet och överfört zip-filen med binärfiler och PDB-filen till en blobbbehållare. I det här avsnittet skapar du en datafabrik med en pipeline som använder den anpassade aktiviteten.

Den inkommande datamängden för den anpassade aktiviteten representerar blobbar (filer) i mappen Inkommande (`mycontainer\\inputfolder`) i blob storage. Datamängd för utdata för aktiviteten representerar utdata-blobbar i den utgående mappen (`mycontainer\\outputfolder`) i blob storage.

Ta bort en eller flera filer i de inkommande mapparna:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Släpp till exempel en fil (fil.txt) med följande innehåll i alla mappar:

```
test custom activity Microsoft test custom activity Microsoft
```

Varje inkommande mapp motsvarar en sektor i datafabriken även om mappen har två eller flera filer. När varje segment bearbetas av pipelinen går anpassad aktivitet igenom alla blobbar i mappen inkommande för den sektorn.

Du ser fem filer med samma innehåll. Utdatafilen från att bearbeta filen i mappen 2015-11-16-00 har till exempel följande innehåll:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Om du släpper flera filer (fil.txt, file2.txt, file3.txt) med samma innehåll till mappen Inkommande finns i följande innehåll i utdatafilen. Varje mapp (2015-11-16-00, etc.) motsvarar en sektor i det här exemplet, även om mappen har flera inkommande filer.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Utdatafilen innehåller tre rader nu, en för varje indatafilen (blob) i mappen som är associerade med sektorn (2015-11-16-00).

En aktivitet skapas för varje aktivitet körs. I det här exemplet finns bara en aktivitet i pipelinen. När ett segment bearbetas av pipelinen körs den anpassade aktiviteten på Batch för att bearbeta sektorn. Eftersom det finns fem segment (varje segment kan ha flera blobbar eller -fil), skapas fem uppgifter i batchen. När en aktivitet körs på Batch, är den anpassade aktiviteten körs.

Den här genomgången innehåller ytterligare information.

#### <a name="step-1-create-the-data-factory"></a>Steg 1: Skapa datafabriken
1. När du loggar in på den [Azure-portalen](https://portal.azure.com/), gör du följande:

   a. Välj **ny** på den vänstra menyn.

   b. Välj **Data + analys** på den **ny** bladet.

   c. Välj **Datafabriken** på den **dataanalys** bladet.

2. På den **nya datafabriken** bladet ange **CustomActivityFactory** för namnet. Namnet på datafabriken måste vara globalt unikt. Om du får felet ”datafabriksnamnet CustomActivityFactory är inte tillgänglig”, byta namn på datafabriken. Till exempel använda yournameCustomActivityFactory och skapa datafabriken igen.

3. Välj **RESURSGRUPPENS namn**, och välj en befintlig resursgrupp eller skapa en resursgrupp.

4. Kontrollera att den prenumeration och region där du vill att data factory skapas är korrekta.

5. Välj **skapa** på den **nya data factory** bladet.

6. Datafabriken har skapats i instrumentpanelen i portalen.

7. När datafabriken har skapats visas den **datafabriken** sidan som visar innehållet i datafabriken.

   ![Data factory-sida](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Steg 2: Skapa länkade tjänster
Länkade tjänster länka datalager eller beräkna en datafabrik-tjänster. I det här steget kan länka du ditt lagringskonto och Batch-kontot till din data factory.

#### <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst
1. Välj den **författare och distribuera** panelen på den **datafabriken** bladet för **CustomActivityFactory**. Data Factory-redigeraren visas.

2. Välj **Nytt datalager** i kommandofältet, och välj **Azure storage.** JSON-skript som du använder för att skapa en länkad tjänst i redigeraren visas lagring.

   ![Nytt datalager](./media/data-factory-data-processing-using-batch/image7.png)

3. Ersätt **account name** med namnet på ditt lagringskonto. Ersätt **account key** med åtkomstnyckeln för lagringskontot. Information om hur du hämtar din lagringsåtkomstnyckel finns [visa, kopiera och generera lagring åtkomstnycklar](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Välj **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

   ![Distribuera](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Skapa en Azure Batch länkad tjänst
I det här steget skapar du en länkad tjänst för Batch-kontot som används för att köra data factory anpassad aktivitet.

1. Välj **nya beräkning** i kommandofältet, och välj **Azure Batch.** JSON-skript som du använder för att skapa en länkad tjänst i redigeraren visas Batch.

2. I JSON-skript:

   a. Ersätt **kontonamn** med namnet på Batch-kontot.

   b. Ersätt **åtkomstnyckeln** med åtkomstnyckeln för Batch-kontot.

   c. Ange ID för poolen för det **poolName** egenskapen. För den här egenskapen kan du ange namn på pool eller pool-ID

   d. Ange batch URI för den **batchUri** JSON-egenskapen.

      > [!IMPORTANT]
      > URL: en från den **Batch-kontot** bladet är i följande format: \<accountname\>.\< region\>. batch.azure.com. För den **batchUri** egenskap i JSON-skript som du vill ta bort a88 ”accountname”. ** från URL-Adressen. Ett exempel är `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Bladet för batch-konto](./media/data-factory-data-processing-using-batch/image9.png)

      För den **poolName** egenskap, du kan också ange ID för poolen i stället för namnet på poolen.

      > [!NOTE]
      > Data Factory-tjänsten stöder inte ett alternativ på begäran för Batch som för HDInsight. Du kan använda endast en egen Batch-pool i en data factory.
      >
      >
   
   e. Ange **StorageLinkedService** för den **linkedServiceName** egenskapen. Du har skapat den här länkade tjänsten i föregående steg. Den här används som ett mellanlagringsområde för filer och loggar.

3. Välj **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

#### <a name="step-3-create-datasets"></a>Steg 3: Skapa datauppsättningar
I det här steget skapar du datauppsättningar som representerar indata och utdata.

#### <a name="create-the-input-dataset"></a>Skapa indatauppsättningen
1. I den Data Factory-redigeraren, Välj den **ny datamängd** i verktygsfältet. Välj **Azure Blob storage** från den nedrullningsbara listan.

2. Ersätt JSON-skript i den högra rutan med följande kodavsnitt i JSON:

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

    Du skapar en pipeline senare i den här genomgången med starttiden 2015-11-16T00:00:00Z och slutar tid 2015-11-16T05:00:00Z. Den har schemalagts för att generera data varje timma, så att det finns fem i/o-segment (mellan **00**: 00:00 -\> **05**: 00:00).

    Den **frekvens** och **intervall** för inkommande datamängden är inställda på att **timme** och **1**, vilket innebär att inkommande segmentet finns varje timme.

    Starttiden för varje segment representeras av den **SliceStart** systemvariabel i föregående JSON-fragment. Här följer starttider för varje segment.

    | **Sektorn** | **Starttid**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**: 00:00 |
    | 2         | 2015-11-16T**01**: 00:00 |
    | 3         | 2015-11-16T**02**: 00:00 |
    | 4         | 2015-11-16T**03**: 00:00 |
    | 5         | 2015-11-16T**04**: 00:00 |

    Den **folderPath** beräknas med hjälp av år, månad, dag och timme tillhör starttiden sektorn (**SliceStart**). Här är hur en inkommande mapp mappas till ett segment.

    | **Sektorn** | **Starttid**          | **Inkommande mapp**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**: 00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**: 00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**: 00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**: 00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**: 00:00 | 2015-11-16-**04** |

3. Välj **distribuera** i verktygsfältet för att skapa och distribuera den **InputDataset** tabell.

#### <a name="create-the-output-dataset"></a>Skapa datauppsättningen för utdata
I det här steget skapar du en annan dataset av typen AzureBlob som representerar utdata.

1. I den Data Factory-redigeraren, Välj den **ny datamängd** i verktygsfältet. Välj **Azure Blob storage** från den nedrullningsbara listan.

2. Ersätt JSON-skript i den högra rutan med följande kodavsnitt i JSON:

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

    En blob/utdatafil genereras för varje inkommande segment. Här är hur en utdatafil heter för varje segment. Alla utdatafiler som skapas i en utdatamapp `mycontainer\\outputfolder`.

    | **Sektorn** | **Starttid**          | **Utdatafil**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**: 00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**: 00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**: 00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**: 00:00 | 2015-11-16 -**03. txt** |
    | 5         | 2015-11-16T**04**: 00:00 | 2015-11-16-**04.txt** |

    Kom ihåg att alla filer i en inkommande mapp (till exempel 2015-11-16-00) är en del av ett segment med starttiden 2015-11-16-00. När den här sektorn behandlas den anpassade aktiviteten söker igenom varje fil och ger en rad i filen med antalet förekomster av sökordet ”Microsoft”. Om det finns tre filer i mappen 2015-11-16-00, finns det tre rader i den utgående filen 2015-11-16-00.txt.

3. Välj **distribuera** i verktygsfältet för att skapa och distribuera den **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Steg 4: Skapa och köra pipelinen med en anpassad aktivitet
I det här steget skapar du en pipeline med en aktivitet, den anpassade aktiviteten som du skapade tidigare.

> [!IMPORTANT]
> Om du inte har överfört **fil.txt** göra det innan du skapar pipeline för om du vill ange mappar i blob-behållaren. Den **isPaused** egenskap är inställd på false i pipeline-JSON, så pipelinen körs direkt eftersom den **starta** datum har passerats.
>
>

1. I den Data Factory-redigeraren, Välj **ny pipeline** i kommandofältet. Om du inte ser kommandot, Välj symbolen knappen ska visas.

2. Ersätt JSON-skript i den högra rutan med följande kodavsnitt i JSON:

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

   * Endast en aktivitet är i pipeline och det är av typen **DotNetActivity**.
   * **AssemblyName** är inställd på namnet på den **MyDotNetActivity.dll**.
   * **EntryPoint** är inställd på **MyDotNetActivityNS.MyDotNetActivity**. Det är i princip \<namnområde\>.\< ClassName\> i koden.
   * **PackageLinkedService** är inställd på **StorageLinkedService**, som pekar till blob-lagring som innehåller anpassad aktivitet zip-filen. Om du använder olika lagringskonton för i/o-filer och anpassad aktivitet zip-filen, måste du skapa en annan länkade lagringstjänsten. Den här artikeln förutsätter att du använder samma lagringskonto.
   * **PackageFile** är inställd på **customactivitycontainer/MyDotNetActivity.zip**. Det är i formatet \<containerforthezip\>/\<nameofthezip.zip\>.
   * Den anpassade aktiviteten tar **InputDataset** som indata och **OutputDataset** som utdata.
   * Den **linkedServiceName** -egenskapen för den anpassade aktiviteten pekar på **AzureBatchLinkedService**, som visar Data Factory som den anpassade aktiviteten ska köras på Batch.
   * Den **samtidighet** inställning är viktig. Om du använder standardvärdet, vilket är 1, även om du har två eller flera compute-noder i Batch-pool, sektorerna bearbetas efter varandra. Därför du inte genom att dra nytta av parallell bearbetning möjligheterna för Batch. Om du ställer in **samtidighet** till ett högre värde, säg 2 innebär det att två sektorer (motsvarar två aktiviteter i Batch) kan bearbetas samtidigt. I det här fallet används båda de virtuella datorerna i Batch-pool. Egenskapen samtidighet på lämpligt sätt.
   * Endast en aktivitet (segment) körs på en virtuell dator när som helst som standard. Som standard **maximalt uppgifter per VM** har värdet 1 för en Batch-pool. Som en del av kraven skapa en pool med denna egenskap angiven till 2. Därför kan två data factory segment köras på en virtuell dator på samma gång.
    - Den **isPaused** egenskap är inställd på false som standard. Pipelinen körs direkt i det här exemplet eftersom sektorerna starta tidigare. Du kan ange egenskapen **SANT** att pausa pipeline- och set tillbaka till **FALSKT** startas om.
    -   Den **starta** och **end** tider är fem timmar från varandra. Segment produceras varje timma, så att fem segment produceras av pipeline.

3. Välj **Distribuera** i kommandofältet för att distribuera pipelinen.

#### <a name="step-5-test-the-pipeline"></a>Steg 5: Testa pipeline
I det här steget kan testa du pipeline genom att släppa filer i de inkommande mapparna. Börja med att testa pipeline med en fil för varje inkommande mapp.

1. På den **datafabriken** bladet i Azure portal, Välj **Diagram**.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

2. I den **Diagram** dubbelklickar du på inkommande datauppsättningen **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

3. Den **InputDataset** bladet visas med alla fem segment redo. Observera den **sektorn starttid** och **sektorn SLUTTID** för varje segment.

   ![Ange sektorn start- och sluttider](./media/data-factory-data-processing-using-batch/image12.png)

4. I den **Diagram** väljer **OutputDataset**.

5. Fem utdata sektorer som visas i den **klar** tillstånd om de skapas.

   ![Utdata sektorn start- och sluttider](./media/data-factory-data-processing-using-batch/image13.png)

6. Använda portalen för att visa de uppgifter som är kopplade till sektorerna och se vilka VM varje segment som kördes på. Mer information finns i [Data Factory och Batch-integrering](#data-factory-and-batch-integration) avsnitt.

7. Utdatafilerna visas under `mycontainer` i `outputfolder` i blob storage.

   ![Utdatafilerna i lagring](./media/data-factory-data-processing-using-batch/image15.png)

   Fem utdatafilerna visas, en för varje inkommande sektorn. Varje utdatafilerna har innehåll som liknar följande utdata:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Följande diagram illustrerar hur data factory segment mappas till aktiviteter i en Batch. I det här exemplet har ett segment endast en körning.

   ![Sektorn mappning diagram](./media/data-factory-data-processing-using-batch/image16.png)

8. Försök med flera filer i en mapp. Skapa filer **file2.txt**, **file3.txt**, **file4.txt**, och **file5.txt** med samma innehåll som fil.txt i mappen **2015-11-06-01**.

9. Ta bort utdatafilen i Utdatamappen **2015-11-16-01.txt**.

10. På den **OutputDataset** bladet högerklickar du på segmentet med **sektorn starttid** inställd på **2015-11/16 01:00:00 AM**. Välj **kör** till kör/bearbeta segmentet på nytt. Sektorn har nu fem filer i stället för en fil.

    ![Kör](./media/data-factory-data-processing-using-batch/image17.png)

11. När sektorn körs och dess status är **klar**, kontrollera innehållet i filen för den här sektorn (**2015-11-16-01.txt**). Utdatafilen visas under `mycontainer` i `outputfolder` i blob storage. Det bör finnas en rad för varje fil av sektorn.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Om du inte tar bort den utdata filen 2015-11-16-01.txt innan du försökte med fem indatafiler finns en rad från den sektor som kördes tidigare och fem rader från den aktuella segment-körningen. Som standard läggs innehållet till utdatafilen om den redan finns.
>
>

#### <a name="data-factory-and-batch-integration"></a>Data Factory och Batch-integrering
Data Factory-tjänsten skapar ett jobb i Batch med namnet `adf-poolname:job-xxx`.

![Batchjobb](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

En aktivitet i jobbet skapas för varje aktivitet kör för ett segment. Om 10 segment är redo att bearbetas, skapas 10 uppgifter i jobbet. Du kan ha fler än ett segment som körs parallellt om du har flera beräkningsnoder i poolen. Om det maximala antalet aktiviteter per compute-nod är större än ett, kan flera segment köra på samma beräkningar.

I det här exemplet finns fem segment så att det finns fem uppgifter i batchen. Med **samtidighet** inställd på **5** i pipeline-JSON i data factory och **maximalt uppgifter per VM** inställd på **2** i Batch-pool med **2** virtuella datorer, aktiviteterna köra snabbt. (Kontrollera start- och sluttider för aktiviteter.)

Använda portalen för att visa Batch-jobbet och dess uppgifter som är kopplade till sektorerna och se vilka VM varje segment som kördes på.

![Jobbet batchaktiviteter](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Felsöka pipeline
Felsökning består av några grundläggande tekniker.

1. Om inkommande segmentet inte är inställd på **klar**, bekräfta att inkommande mappstrukturen är rätt och att fil.txt finns i mapparna inkommande.

   ![Inkommande mappstruktur](./media/data-factory-data-processing-using-batch/image3.png)

2. I den **kör** metod för din anpassade aktivitet, Använd den **IActivityLogger** objekt att logga information som hjälper dig att felsöka problem. De loggade meddelandena visas i användaren\_0. loggfilen.

   På den **OutputDataset** bladet välj sektorn att se den **datasektorn** bladet för den sektorn. Under **aktiviteten körs**, visas en aktivitet som körs för sektorn. Om du väljer **kör** i kommandofältet börjar du en annan aktivitet som körs för samma segment.

   När du väljer aktiviteten kör du ser den **aktivitet köras information** bladet med en lista över loggfilerna. Du ser loggade meddelanden i användaren\_0. loggfilen. När ett fel uppstår finns tre aktivitetskörningar eftersom antalet nya försök är inställd på 3 i pipeline/aktivitet JSON. När du väljer aktiviteten kör finns i loggfilerna som du kan granska för att felsöka felet.

   ![OutputDataset och Data sektorn blad](./media/data-factory-data-processing-using-batch/image18.png)

   Välj i listan över loggfiler, **användare 0.log**. I den högra panelen, resultatet av att använda den **IActivityLogger.Write** metoden visas.

   ![Aktiviteten kör informationsbladet](./media/data-factory-data-processing-using-batch/image19.png)

   Kontrollera system-0.log för alla system felmeddelanden och undantag.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Inkludera den **PDB** filen i zip-filen så att felinformation har information till exempel anropsstacken när ett fel uppstår.

4. Alla filer i zip-filen för den anpassade aktiviteten måste vara på den översta nivån med inga undermappar.

   ![Anpassad aktivitet zip-fil i listan](./media/data-factory-data-processing-using-batch/image20.png)

5. Se till att **assemblyName** (MyDotNetActivity.dll) **entryPoint** (MyDotNetActivityNS.MyDotNetActivity) **packageFile** (customactivitycontainer / MyDotNetActivity.zip) och **packageLinkedService** (ska peka till blob-lagring som innehåller zip-filen) är inställda på rätt värden.

6. Om du har löst ett fel och om du vill bearbeta sektorn Högerklicka sektor i den **OutputDataset** och välj **kör**.

   ![OutputDataset bladet kör alternativet](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > En behållare som är i ditt blob storage med namnet `adfjobs`. Den här behållaren tas bort inte automatiskt, men kan du ta bort den när du har slutfört testningen lösningen. På liknande sätt kan data factory lösning skapar ett Batch-jobb med namnet `adf-\<pool ID/name\>:job-0000000001`. Du kan ta bort det här jobbet när du har testat lösningen om du vill.
   >
   >
7. Den anpassade aktiviteten använder inte den **app.config** filen från paketet. Därför om koden läser eventuella anslutningssträngar i konfigurationsfilen, fungerar det inte vid körning. Det är bra när du använder Batch att hålla alla hemligheter i Azure Key Vault. Använd sedan ett huvudnamn för tjänsten certifikatbaserad att skydda nyckelvalvet och distribuera certifikat till Batch-pool. Den anpassade aktiviteten .NET kan komma åt hemligheter från nyckelvalvet vid körning. Allmän lösningen kan skalas till alla typer av hemlighet, inte bara en anslutningssträng.

    Det finns en enklare lösning, men det är inte bästa praxis. Du kan skapa en SQL database länkad tjänst med anslutning sträng inställningar. Du kan sedan skapa en datamängd som använder den länkade tjänsten och kedja datamängden som en dummy inkommande datauppsättning för anpassad .NET-aktiviteten. Du kan sedan komma åt den länkade tjänsten anslutningssträngen i koden anpassad aktivitet. Det bör fungera bra vid körning.  

#### <a name="extend-the-sample"></a>Utöka exemplet
Du kan utöka det här exemplet ska lära dig mer om funktioner som Data Factory och Batch. Till exempel för att bearbeta segment i ett annat tidsintervall, gör du följande:

1. Lägg till följande undermappar i `inputfolder`: 2015-11-16-05, 2015-11-16-06 201-11-16-07 och 2011-11-16-08 2015-11-16-09. Placera indatafiler i mapparna. Ändra sluttiden för pipelinen från `2015-11-16T05:00:00Z` till `2015-11-16T10:00:00Z`. I den **Diagram** dubbelklickar du på **InputDataset** och bekräfta att de inkommande segment är klara. Dubbelklicka på **OutputDataset** att se status för utdata-segment. Om de inte finns i den **klar** tillstånd, kontrollera utdatamapp för utdatafilerna.

2. Öka eller minska den **samtidighet** inställningen för att förstå hur den påverkar prestandan för din lösning, särskilt om bearbetningen som utförs på Batch. Mer information om den **samtidighet** inställningen finns i ”steg 4: skapa och köra pipelinen med en anpassad aktivitet”.

3. Skapa en pool med högre/nedre **maximalt uppgifter per VM**. Uppdatera länkade batchtjänsten i data factory-lösningen för att använda den nya poolen som du skapade. Mer information om den **maximalt uppgifter per VM** inställningen finns i ”steg 4: skapa och köra pipelinen med en anpassad aktivitet”.

4. Skapa en Batch-pool med den **Autoskala** funktion. Skala automatiskt beräkningsnoder i poolen Batch är dynamisk justering av processorkraft som används av ditt program. 

    Exempel formeln här uppnår följande beteende. När poolen skapas, börjar det med en virtuell dator. Måttet $PendingTasks definierar antalet aktiviteter som körs och aktiv (i kö). Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger därefter TargetDedicated. Det garanterar att TargetDedicated aldrig är mer omfattande än 25 virtuella datorer. När nya aktiviteter skickas växer poolen automatiskt. Som slutförda uppgifter, virtuella datorer blir ledigt i taget och autoskalning krymper dessa virtuella datorer. Du kan justera startingNumberOfVMs och maxNumberofVMs efter dina behov.
 
    Autoskala formel:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Mer information finns i [automatiskt skala compute-noder i en Batch-pool](../../batch/batch-automatic-scaling.md).

   Om poolen använder [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), Batch-tjänsten kan ta 15 till 30 minuter att förbereda den virtuella datorn innan du kör den anpassade aktiviteten. Om poolen använder en annan autoScaleEvaluationInterval, kan Batch-tjänsten ta autoScaleEvaluationInterval plus 10 minuter.

5. I exempel-lösning i **Execute** metoden startar den **Calculate** metod som bearbetar ett indata-segment för att skapa ett utgående data segment. Du kan skriva en egen metod för att bearbeta inkommande data och Ersätt den **Calculate** anrop den **Execute** metod med ett anrop till metoden.

### <a name="next-steps-consume-the-data"></a>Nästa steg: använda data
När du bearbetar data kan du använda den med online-verktyg som Power BI. Här är länkar som hjälper dig att förstå Power BI och hur du använder den i Azure:

* [Utforska en datamängd i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Kom igång med Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Uppdatera data i Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure och Power BI: översikt](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referenser
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introduktion till Data Factory-tjänsten](data-factory-introduction.md)
  * [Kom igång med Data Factory](data-factory-build-your-first-pipeline.md)
  * [Använda anpassade aktiviteter i en Data Factory-pipelinen](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Grunderna i Batch](../../batch/batch-technical-overview.md)
  * [Översikt över Batch-funktioner](../../batch/batch-api-basics.md)
  * [Skapa och hantera ett Batch-konto i Azure-portalen](../../batch/batch-account-create-portal.md)
  * [Kom igång med Batch-klientbiblioteket för .NET](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
