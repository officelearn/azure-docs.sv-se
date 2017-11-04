---
title: "Bearbeta stora datauppsättningar som använder Data Factory och Batch | Microsoft Docs"
description: "Beskriver hur du bearbetar stora mängder data i ett Azure Data Factory-pipelinen med hjälp av kapaciteten för parallell bearbetning av Azure Batch."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 2ceef65eaf195b605fada2f8dfe511fe33a5daa0
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="process-large-scale-datasets-using-data-factory-and-batch"></a>Bearbeta datauppsättningar i stor skala med Data Factory och Batch
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [anpassade aktiviteter i Data Factory version 2](../transform-data-using-dotnet-custom-activity.md).

Den här artikeln beskriver en arkitektur på en exempellösning som flyttar och bearbetar stora datauppsättningar på automatisk och schemalagda sätt. Det ger också en slutpunkt till slutpunkt-genomgång för att implementera lösningen med hjälp av Azure Data Factory och Azure Batch.

Den här artikeln är längre än våra vanliga artikeln eftersom den innehåller en genomgång av en lösning för hela exemplet. Om du är nybörjare på Batch och Data Factory, du kan lära dig om dessa tjänster och hur de fungerar tillsammans. Om du vet något om tjänsterna och designa/utforma en lösning, du kan fokusera endast på den [arkitektur avsnittet](#architecture-of-sample-solution) av artikeln och om du utvecklar en prototyp eller en lösning kan du även vill prova att använda instruktionerna i den [genomgången](#implementation-of-sample-solution). Vi erbjuder kommentarer om det här innehållet och hur du använder den.

Först ska vi titta på hur Data Factory och Batch-tjänster kan hjälpa med bearbetning av stora datamängder i molnet.     

## <a name="why-azure-batch"></a>Varför Azure Batch?
Med Azure Batch kan du effektivt köra storskalig parallella program och HPC-program (databehandling med höga prestanda) i molnet. Det är en plattformstjänst som schemalägger beräkningsintensivt arbete för körning i en hanterad samling virtuella datorer. Tjänsten kan automatiskt skala beräkningsresurser efter jobbens behov.

Med Batch-tjänsten definierar du Azure-beräkningsresurser att köra dina program parallellt och i stor skala. Du kan schemalägga jobb eller köra dem på begäran, och du behöver inte manuellt skapa, konfigurera och hantera ett HPC-kluster, enskilda virtuella datorer, virtuella nätverk eller en komplex infrastruktur för jobb- och aktivitetsschemaläggning.

Se följande artiklar om du inte är bekant med Azure Batch eftersom det hjälper med att förstå arkitektur/implementering av lösningen som beskrivs i den här artikeln.   

* [Grunderna i Azure Batch](../../batch/batch-technical-overview.md)
* [Översikt över funktionen för batch](../../batch/batch-api-basics.md)

(valfritt) Läs mer om Azure Batch i den [Utbildningsväg för Azure Batch](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Varför Azure Data Factory?
Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Med Data Factory-tjänsten kan du skapa hanterade data pipelines som flytta data från lokala och molnet datalager till en centraliserad datalagret (till exempel: Azure Blob Storage), och processen/Transformera data med hjälp av tjänster som Azure HDInsight och Azure Machine Learning. Du kan också schemalägga data pipelines att köra i en schemalagd tid (varje timme, varje dag, varje vecka, etc.) och övervaka och hantera dem snabbt identifiera problem och vidta åtgärder.

Se följande artiklar om du inte är bekant med Azure Data Factory eftersom det hjälper med att förstå arkitektur/implementering av lösningen som beskrivs i den här artikeln.  

* [Införandet av Azure Data Factory](data-factory-introduction.md)
* [Skapa din första pipeline för data](data-factory-build-your-first-pipeline.md)   

(valfritt) Läs mer om Azure Data Factory i den [för Azure Data Factory-Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Data Factory och Batch tillsammans
Data Factory innehåller inbyggda aktiviteter, till exempel Kopieringsaktiviteten att kopiera/flytta data från ett dataarkiv som ska användas som källa till ett dataarkiv som mål och Hive aktivitet för bearbetning av data med Hadoop-kluster (HDInsight) på Azure. Se [Data Transformation aktiviteter](data-factory-data-transformation-activities.md) en lista över aktiviteter för omvandling av stöds.

Du kan också skapa anpassade aktiviteter för .NET för att flytta eller bearbeta data med egna logik och köra dessa aktiviteter på Azure HDInsight-kluster eller ett Azure Batch-pool för virtuella datorer. När du använder Azure Batch, kan du konfigurera pool för att Autoskala (lägga till eller ta bort virtuella datorer baserat på arbetsbelastning) utifrån en formel som du anger.     

## <a name="architecture-of-sample-solution"></a>Arkitektur för exempellösning
Även om arkitekturen som beskrivs i den här artikeln är avsedd för en enkel lösning, är det relevanta för avancerade scenarier, till exempel risk modellering av finansiella tjänster, bild bearbetning och återgivning och genom analys.

Diagrammet visar 1) hur Data Factory samordnar dataförflyttning och bearbetning och 2) hur Azure Batch bearbetar data i en parallell sätt. Hämta och skriva ut diagram för enkel referens (11 x 17 i. eller A3-storlek): [HPC och data orchestration med hjälp av Azure Batch och Data Factory](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram för storskalig databearbetning](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

Följande lista innehåller de grundläggande steg i processen. Lösningen innehåller koden och beskrivningar för att skapa lösningen slutpunkt till slutpunkt.

1. **Konfigurera Azure Batch med en pool med compute-noder (VM)**. Du kan ange antalet noder och storleken på varje nod.
2. **Skapa en instans av Azure Data Factory** som har konfigurerats med entiteter som representerar Azure blob-lagring, beräkning Azure Batch-tjänsten, i/o-data och arbetsflöde/pipeline med aktiviteter som att flytta och transformera data.
3. **Skapa en anpassad .NET-aktivitet i Data Factory-pipelinen**. Aktiviteten är din användarkod som körs på Azure Batch-pool.
4. **Lagra stora mängder inkommande data som blobbar i Azure storage**. Data är uppdelat i logiska segment (vanligtvis genom tid).
5. **Data Factory kopierar data som bearbetas parallellt** till den sekundära platsen.
6. **Data Factory körs den anpassade aktiviteten använder allokerats av Batch**. Data Factory kan köra aktiviteter samtidigt. Varje aktivitet bearbetar ett segment av data. Resultaten lagras i Azure-lagring.
7. **Data Factory flyttar de slutliga resultaten till en tredje plats**, antingen för distribution via en app eller för ytterligare bearbetning av andra verktyg.

## <a name="implementation-of-sample-solution"></a>Implementering av exempellösning
Exempellösningen är avsiktligt enkel och är att visa dig hur du använder Data Factory och Batch för att bearbeta datauppsättningar. Lösningen är helt enkelt antalet förekomster av en sökterm (”Microsoft”) i indatafiler ordnade i en tidsserie. Det visar antalet utgående filer.

**Tid**: Om du känner till grunderna i Azure Data Factory och Batch och har slutfört de förutsättningar som anges nedan, beräkna den här lösningen tar 1 – 2 timmar att slutföra.

### <a name="prerequisites"></a>Krav
#### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har en Azure-prenumeration kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Se [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure Storage-konto
Du kan använda ett Azure storage-konto för att lagra data i den här kursen. Om du inte har ett Azure storage-konto, se [skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Exempellösningen använder blob-lagring.

#### <a name="azure-batch-account"></a>Azure Batch-kontot
Skapa ett Azure Batch-kontot med den [Azure-portalen](http://portal.azure.com/). Se [skapa och hantera Azure Batch-kontot](../../batch/batch-account-create-portal.md). Observera Azure Batch-kontot namn och åtkomstnyckel. Du kan också använda [ny AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) för att skapa ett Azure Batch-konto. Se [Kom igång med Azure Batch-PowerShell-cmdlets](../../batch/batch-powershell-cmdlets-get-started.md) detaljerade anvisningar om hur du använder denna cmdlet.

Exempellösningen använder Azure Batch (indirekt via ett Azure Data Factory-pipelinen) att bearbeta data på en parallell sätt i en pool med compute-noder (en hanterad samling med virtuella datorer).

#### <a name="azure-batch-pool-of-virtual-machines-vms"></a>Azure Batch-pool för virtuella datorer (VM)
Skapa en **Azure Batch-pool** med minst 2 compute-noder.

1. I den [Azure-portalen](https://portal.azure.com), klickar du på **Bläddra** i den vänstra menyn och klicka på **Batch-konton**.
2. Välj Azure Batch-konto för att öppna den **Batch-kontot** bladet.
3. Klicka på **pooler** panelen.
4. I den **pooler** bladet, klickar du på knappen Lägg till i verktygsfältet för att lägga till en pool.
   1. Ange ett ID för poolen (**programpoolens ID**). Observera den **ID för poolen**; du behöver den när du skapar Data Factory-lösning.
   2. Ange **Windows Server 2012 R2** för operativsystemsfamiljen inställningen.
   3. Välj en **nodprisnivå**.
   4. Ange **2** som värde för den **mål dedikerade** inställningen.
   5. Ange **2** som värde för den **maximalt antal uppgifter per nod** inställningen.
   6. Klicka på **OK** för att skapa poolen.

#### <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren
[Azure Storage Explorer 6 (Verktyg)](https://azurestorageexplorer.codeplex.com/) eller [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (från ClumsyLeaf programvara). Du kan använda dessa verktyg för att kontrollera och ändra data i Azure Storage projekt inklusive loggar molnbaserade program.

1. Skapa en behållare med namnet **minbehållare** med privat åtkomst (ingen anonym åtkomst)
2. Om du använder **CloudXplorer**, skapa mappar och undermappar med följande struktur:

   ![](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`och `outputfolder` är mappar på högsta nivå i `mycontainer`. Den `inputfolder` har undermappar med datum-/ tidsstämplar (åååå-MM-DD-HH).

   Om du använder **Azure Lagringsutforskaren**, i nästa steg, måste du överföra filer med namn: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt` och så vidare. Det här steget skapar automatiskt mappar.
3. Skapa en textfil **fil.txt** på datorn med innehåll som har nyckelordet **Microsoft**. Till exempel: ”test anpassad aktivitet Microsoft test anpassad aktivitet Microsoft”.
4. Överföra filen till följande inkommande mappar i Azure blob storage.

   ![](./media/data-factory-data-processing-using-batch/image4.png)

   Om du använder **Azure Lagringsutforskaren**, ladda upp filen **fil.txt** till **minbehållare**. Klicka på **kopiera** i verktygsfältet för att skapa en kopia av blob. I den **kopiera Blob** i dialogrutan den **blob målnamn** till `inputfolder/2015-11-16-00/file.txt`. Upprepa det här steget för att skapa `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt` och så vidare. Den här åtgärden skapar automatiskt mappar.
5. Skapa en annan behållare med namnet: `customactivitycontainer`. Du kan överföra anpassad aktivitet zip-filen till den här behållaren.

#### <a name="visual-studio"></a>Visual Studio
Installera Microsoft Visual Studio 2012 eller senare för att skapa den anpassade Batch-aktiviteten som ska användas i Data Factory-lösning.

### <a name="high-level-steps-to-create-the-solution"></a>Övergripande steg för att skapa lösningen
1. Skapa en anpassad aktivitet som innehåller logik för databearbetning.
2. Skapa ett Azure data factory som använder den anpassade aktiviteten:

### <a name="create-the-custom-activity"></a>Skapa den anpassade aktiviteten
Den anpassade Data Factory-aktiviteten är hjärtat i det här exemplet lösning. Exempellösningen använder Azure Batch för att köra den anpassade aktiviteten. Se [använda anpassade aktiviteter i ett Azure Data Factory-pipelinen](data-factory-use-custom-activities.md) grundläggande information att utveckla anpassade aktiviteter och använda dem i Azure Data Factory pipelines.

Om du vill skapa en anpassad .NET-aktivitet som du kan använda i ett Azure Data Factory-pipelinen, måste du skapa en **.NET-klassbibliotek** projekt med en klass som implementerar som **IDotNetActivity** gränssnitt. Det här gränssnittet har endast en metod: **kör**. Här är metodens signatur:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Metoden har några viktiga komponenter som behövs för att förstå.

* Metoden som använder fyra parametrar:

  1. **linkedServices**. En enumerable lista över länkade tjänster som länkar i/o-datakällor (till exempel: Azure Blob Storage) till datafabriken. I det här exemplet finns bara en länkad tjänst av typen Azure Storage som används för både inkommande och utgående.
  2. **datauppsättningar**. Detta är en enumerable lista över datauppsättningar. Du kan använda den här parametern för att få platser och scheman som definierats av inkommande och utgående datauppsättningar.
  3. **aktiviteten**. Den här parametern representerar den aktuella beräkning entiteten – i det här fallet Azure Batch-tjänsten.
  4. **loggaren**. Loggaren kan du skriva debug kommentarer som yta som ”användare” loggen för pipeline.
* Metoden returnerar en ordlista som kan användas för att kedja anpassade aktiviteter tillsammans i framtiden. Den här funktionen har inte implementerats ännu, så returneras en tom ordlista från metoden.

#### <a name="procedure-create-the-custom-activity"></a>Metod: Skapa den anpassade aktiviteten
1. Skapa ett .NET-klassbibliotek-projekt i Visual Studio.

   1. Starta **Visual Studio 2012**/**2013/2015**.
   2. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**.
   3. Expandera **mallar**, och välj **Visual C\#**. I den här genomgången ska du använda C\#, men du kan använda alla .NET-språk för att utveckla anpassade aktiviteten.
   4. Välj **klassbiblioteket** från listan över projekttyper till höger.
   5. Ange **MyDotNetActivity** för den **namn**.
   6. Välj **C:\\ADF** för den **plats**. Skapa mappen **ADF** om den inte finns.
   7. Klicka på **OK** för att skapa projektet.
2. Klicka på **Verktyg**, peka på **NuGet Package Manager** och klicka på **Package Manager Console**.
3. I den **Pakethanterarkonsolen**, kör följande kommando för att importera **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importera den **Azure Storage** NuGet-paketet i i projektet. Du behöver det här paketet eftersom du använder Blob storage API i det här exemplet.

    ```powershell
    Install-Package Azure.Storage
    ```
5. Lägg till följande **med** direktiven till källfilen i projektet.

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
6. Ändra namnet på den **namnområde** till **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Ändra namnet på klassen som **MyDotNetActivity** och härleds från den **IDotNetActivity** gränssnitt som visas nedan.

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementera (Lägg till) på **kör** metod för den **IDotNetActivity** gränssnitt till den **MyDotNetActivity** klassen och kopiera följande exempelkod till metoden. Finns det [Kör metod](#execute-method) avsnittet förklaring för den logik som används i den här metoden.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {
    
       // declare types for input and output data stores
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // using First method instead of Single since we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.
    
       // create storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
       // initialize the continuation token before using it in the do-while loop.
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
    
           // Calculate method returns the number of occurrences of
           // the search term (“Microsoft”) in each blob associated
           // with the data slice.
           //
           // definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
9. Lägg till följande helper-metoder i klassen. De här metoderna anropas av den **Execute** metod. Viktigast av allt den **Calculate** metoden isolerar den kod som går igenom varje blob.

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
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
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
    Den **GetFolderPath** metoden returnerar sökvägen till mappen som dataset pekar på och **GetFileName** metoden returnerar namnet på blob/fil som dataset pekar på.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Den **Calculate** metoden beräknar antalet instanser av nyckelordet **Microsoft** i indatafiler (blobbar i mappen). Sökordet (”Microsoft”) är hårdkodat i koden.

1. Kompilera projektet. Klicka på **skapa** i menyn och klickar på **skapa lösning**.
2. Starta **Windows Explorer**, och navigera till **bin\\felsöka** eller **bin\\släpper** beroende på vilken typ av version.
3. Skapa en zip-fil **MyDotNetActivity.zip** som innehåller alla binärfiler i den  **\\bin\\felsöka** mapp. Du kanske vill inkludera i MyDotNetActivity. **pdb** filen så att du får ytterligare information, till exempel radnumret i källkoden som har orsakat problemet när ett fel uppstår.

   ![](./media/data-factory-data-processing-using-batch/image5.png)
4. Överför **MyDotNetActivity.zip** som en blob till blob-behållare: `customactivitycontainer` i Azure blob-lagring som den **StorageLinkedService** länkade tjänsten i den **ADFTutorialDataFactory** använder. Skapa blob-behållaren `customactivitycontainer` om den inte redan finns.

#### <a name="execute-method"></a>Execute-metoden
Det här avsnittet innehåller mer information och kommentarer om koden i Execute-metoden.

1. Medlemmar för att söka igenom inkommande samling finns i den [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) namnområde. Söka igenom blob-samling kräver att du använder den **BlobContinuationToken** klass. I princip måste du använda en gör-samtidigt med token som metod för att avsluta slingan. Mer information finns i [använda Blob storage från .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). En grundläggande loop visas här:

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
   Finns i dokumentationen för den [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) metod för information.
2. Koden för att arbeta med en uppsättning blobbar logiskt kommer inom do-while-slinga. I den **kör** metod, do-medan loop skickar listan över blobbar till en metod med namnet **beräkna**. Metoden returnerar en variabel med namnet **utdata** som är resultatet av att ha hävdade via alla BLOB i segmentet.

   Returnerar antalet förekomster av sökordet (**Microsoft**) i blob som skickades till den **Calculate** metod.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. En gång i **Calculate** metod har utfört arbetet, det måste skrivas till en ny blob. Så att en ny blob kan skrivas med resultatet för varje uppsättning blobbar som bearbetas. Om du vill skriva till en ny blob att hitta datamängd för utdata.

    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. Koden anropar också en hjälpmetod: **GetFolderPath** att hämta sökvägen till mappen (storage behållarens namn).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Den **GetFolderPath** kastar DataSet-objekt till en AzureBlobDataSet som har en egenskap med namnet FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. Koden anropar den **GetFileName** metod för att hämta filnamnet (blob-namn). Koden liknar koden ovan för att hämta sökvägen till mappen.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Namnet på filen skrivs genom att skapa ett URI-objekt. URI-konstruktorn använder den **BlobEndpoint** egenskapen att returnera behållarens namn. Sökvägen och namnet på mappen läggs till konstruera utdata blob-URI.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. Namnet på filen har skrivits och nu kan du skriva utdata-strängen från den **Calculate** metod för att en ny blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Skapa datafabriken
I den [skapa den anpassade aktiviteten](#create-the-custom-activity) avsnittet du skapat en anpassad aktivitet och överfört zip-filen med binärfiler och PDB-filen till en Azure blob-behållare. I det här avsnittet skapar du en Azure **datafabriken** med en **pipeline** som använder den **anpassad aktivitet**.

Den inkommande datamängden för den anpassade aktiviteten representerar blobbar (filer) i mappen Inkommande (`mycontainer\\inputfolder`) i blob storage. Datamängd för utdata för aktiviteten representerar utdata-blobbar i den utgående mappen (`mycontainer\\outputfolder`) i blob storage.

Ta bort en eller flera filer i mapparna inkommande:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Till exempel släpp en fil (fil.txt) med följande innehåll i alla mappar.

```
test custom activity Microsoft test custom activity Microsoft
```

Varje inkommande mapp motsvarar en sektor i Azure Data Factory även om mappen har 2 eller flera filer. När varje segment bearbetas av pipelinen går anpassad aktivitet igenom alla blobbar i mappen inkommande för den sektorn.

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

En aktivitet skapas för varje aktivitet körs. I det här exemplet finns bara en aktivitet i pipelinen. När ett segment bearbetas av pipelinen körs den anpassade aktiviteten på Azure Batch att bearbeta sektorn. Eftersom det finns fem segment (varje segment kan ha flera blobbar eller -fil), det finns fem aktiviteter som skapats i Azure Batch. När en aktivitet körs på Batch är faktiskt anpassad aktivitet körs.

Den här genomgången innehåller ytterligare information.

#### <a name="step-1-create-the-data-factory"></a>Steg 1: Skapa datafabriken
1. När du loggar in till den [Azure-portalen](https://portal.azure.com/), gör du följande:

   1. Klicka på **NYTT** i den vänstra menyn.
   2. Klicka på **Data + analys** i den **ny** bladet.
   3. Klicka på **Data Factory** på bladet **Dataanalys**.
2. I den **nya datafabriken** bladet ange **CustomActivityFactory** för namnet. Namnet på Azure Data Factory måste vara globalt unikt. Om du får felmeddelandet: **datafabriksnamnet ”CustomActivityFactory” är inte tillgänglig**, byta namn på datafabriken (till exempel **yournameCustomActivityFactory**) och försöker skapa igen.
3. Klicka på **RESURSGRUPPENS namn**, och välj en befintlig resursgrupp eller skapa en resursgrupp.
4. Kontrollera att du använder rätt prenumeration och region där du vill att datafabriken ska skapas.
5. Klicka på **Skapa** på bladet **Ny datafabrik**.
6. Du ser datafabriken som skapas i den **instrumentpanelen** på Azure-portalen.
7. När datafabriken har skapats visas datafabrikssidan med innehållet i datafabriken.

   ![](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Steg 2: Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. I det här steget kan du länka din **Azure Storage** konto och **Azure Batch** till din data factory-kontot.

#### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
1. Klicka på den **författare och distribuera** panelen på den **DATAFABRIKEN** bladet för **CustomActivityFactory**. Du kan se Data Factory-redigeraren.
2. Klicka på **Nytt datalager** på kommandoraden och välj **Azure storage.** Du bör se JSON-skriptet för att skapa en länkad Azure-lagringstjänst i redigeraren.

   ![](./media/data-factory-data-processing-using-batch/image7.png)

3. Ersätt **kontonamn** med namnet på ditt Azure-lagringskonto och **kontonyckel** med åtkomstnyckeln för Azure-lagringskontot. Information om hur du hämtar din lagringsåtkomstnyckel finns i [Visa, kopiera och återskapa lagringsåtkomstnycklar](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Klicka på **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

   ![](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-azure-batch-linked-service"></a>Skapa Azure Batch länkad tjänst
I det här steget skapar du en länkad tjänst för din **Azure Batch** konto som används för att köra den anpassade Data Factory-aktiviteten.

1. Klicka på **nya beräkning** på kommandoraden och välj **Azure Batch.** Du bör se JSON-skript för att skapa en Azure Batch länkad tjänst i redigeraren.
2. I JSON-skript:

   1. Ersätt **kontonamn** med namnet på ditt Azure Batch-konto.
   2. Ersätt **åtkomstnyckeln** och snabbtangenten för Azure Batch-kontot.
   3. Ange ID för poolen för det **poolName** egenskapen**.** För den här egenskapen kan du ange antingen namnet på programpoolen eller poolens ID.
   4. Ange batch URI för den **batchUri** JSON-egenskapen.

      > [!IMPORTANT]
      > Den **URL** från den **Azure Batch-kontoblad** är i följande format: \<accountname\>.\< region\>. batch.azure.com. För den **batchUri** egenskap i JSON som du behöver **ta bort ”accountname”.** från URL-Adressen. Exempel: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![](./media/data-factory-data-processing-using-batch/image9.png)

      För den **poolName** egenskap, du kan också ange ID för poolen i stället för namnet på poolen.

      > [!NOTE]
      > Data Factory-tjänsten stöder inte ett alternativ på begäran för Azure Batch som för HDInsight. Du kan bara använda din egen Azure Batch-pool i ett Azure data factory.
      >
      >
   5. Ange **StorageLinkedService** för den **linkedServiceName** egenskapen. Du har skapat den här länkade tjänsten i föregående steg. Den här används som ett mellanlagringsområde för filer och loggar.
3. Klicka på **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

#### <a name="step-3-create-datasets"></a>Steg 3: Skapa datauppsättningar
I det här steget skapar du datauppsättningar som representerar indata och utdata.

#### <a name="create-input-dataset"></a>Skapa indatauppsättning
1. I den **Editor** Datafabriken, klickar du på **ny datamängd** knappen i verktygsfältet och på **Azure Blob storage** från den nedrullningsbara menyn.
2. Ersätt JSON i den högra rutan med följande kodavsnitt i JSON:

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

    Du skapar en pipeline senare i den här genomgången med starttid: 2015-11-16T00:00:00Z-och Sluttid: 2015-11-16T05:00:00Z. Den är schemalagd att generera data **varje timme**, så att det finns 5 i/o-segment (mellan **00**: 00:00 -\> **05**: 00:00).

    Den **frekvens** och **intervall** för inkommande datamängden har värdet **timme** och **1**, vilket innebär att inkommande segmentet finns varje timme.

    Här följer starttider för varje segment som representeras av **SliceStart** systemvariabel i ovanstående JSON-fragment.

    | **Sektorn** | **Starttid**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**: 00:00 |
    | 2         | 2015-11-16T**01**: 00:00 |
    | 3         | 2015-11-16T**02**: 00:00 |
    | 4         | 2015-11-16T**03**: 00:00 |
    | 5         | 2015-11-16T**04**: 00:00 |

    Den **folderPath** beräknas med hjälp av år, månad, dag och timme tillhör starttiden sektorn (**SliceStart**). Här är därför hur en inkommande mapp mappas till ett segment.

    | **Sektorn** | **Starttid**          | **Inkommande mapp**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**: 00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**: 00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**: 00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**: 00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**: 00:00 | 2015-11-16-**04** |

1. Klicka på **distribuera** i verktygsfältet för att skapa och distribuera den **InputDataset** tabell.

#### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
I det här steget skapar du en annan dataset av typen AzureBlob som representerar utdata.

1. I den **Editor** Datafabriken, klickar du på **ny datamängd** knappen i verktygsfältet och på **Azure Blob storage** från den nedrullningsbara menyn.
2. Ersätt JSON i den högra rutan med följande kodavsnitt i JSON:

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

    En blob/utdatafil genereras för varje inkommande segment. Här är hur en utdatafil heter för varje segment. Alla utdatafiler som skapas i en utdatamapp: `mycontainer\\outputfolder`.

    | **Sektorn** | **Starttid**          | **Utdatafil**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**: 00:00 | 2015-11-16 -**00. txt** |
    | 2         | 2015-11-16T**01**: 00:00 | 2015-11-16 -**01. txt** |
    | 3         | 2015-11-16T**02**: 00:00 | 2015-11-16 -**02. txt** |
    | 4         | 2015-11-16T**03**: 00:00 | 2015-11-16 -**03. txt** |
    | 5         | 2015-11-16T**04**: 00:00 | 2015-11-16 -**04. txt** |

    Tänk på att alla filer i en inkommande mapp (till exempel: 2015-11-16-00) är en del av ett segment med starttiden: 2015-11-16-00. När den här sektorn behandlas den anpassade aktiviteten söker igenom varje fil och ger en rad i filen med antalet förekomster av sökordet (”Microsoft”). Om det finns tre filer i mappen 2015-11-16-00, det finns tre rader i utdatafilen: 2015-11-16-00.txt.

1. Klicka på **distribuera** i verktygsfältet för att skapa och distribuera den **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-custom-activity"></a>Steg 4: Skapa och köra pipelinen med anpassad aktivitet
I det här steget skapar du en pipeline med en aktivitet, den anpassade aktiviteten som du skapade tidigare.

> [!IMPORTANT]
> Om du inte har överfört den **fil.txt** om du vill ange mappar i blob-behållaren göra det innan du skapar pipeline. Den **isPaused** egenskap är inställd på false i pipeline-JSON, så pipelinen körs omedelbart som den **starta** datum har passerats.
>
>

1. I den Data Factory-redigeraren, klicka på **ny pipeline** i kommandofältet. Om du inte ser kommandot klickar du på **... (Tre punkter)**  att se objektet.
2. Ersätt JSON i den högra rutan med följande JSON-skript:

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

   * Det finns bara en aktivitet i pipelinen och som är av typen: **DotNetActivity**.
   * **AssemblyName** är inställd på namnet på DLL: **MyDotNetActivity.dll**.
   * **EntryPoint** är inställd på **MyDotNetActivityNS.MyDotNetActivity**. Det är i princip \<namnområde\>.\< ClassName\> i koden.
   * **PackageLinkedService** är inställd på **StorageLinkedService** som pekar till blob-lagring som innehåller anpassad aktivitet zip-filen. Om du använder olika Azure Storage-konton för i/o-filer och anpassad aktivitet zip-filen, måste du skapa en annan länkad Azure Storage-tjänst. Den här artikeln förutsätter att du använder samma Azure Storage-konto.
   * **PackageFile** är inställd på **customactivitycontainer/MyDotNetActivity.zip**. Det är i formatet: \<containerforthezip\>/\<nameofthezip.zip\>.
   * Den anpassade aktiviteten tar **InputDataset** som indata och **OutputDataset** som utdata.
   * Den **linkedServiceName** -egenskapen för den anpassade aktiviteten pekar på den **AzureBatchLinkedService**, som talar om Azure Data Factory som den anpassade aktiviteten ska köras på Azure Batch.
   * Den **samtidighet** inställning är viktig. Om du använder standardvärdet, vilket är 1, även om du har 2 eller mer compute-noder i Azure Batch-pool, sektorerna bearbetas efter varandra. Därför kan tar du inte nytta av funktionen för parallell bearbetning av Azure Batch. Om du ställer in **samtidighet** till ett högre värde, säg 2 innebär det att två sektorer (motsvarar två aktiviteter i Azure Batch) kan bearbetas på samma gång, i vilket fall både virtuella datorer i Azure Batch adresspool används. Därför egenskapen samtidighet på lämpligt sätt.
   * Endast en aktivitet (segment) körs på en virtuell dator när som helst som standard. Anledningen är att, som standard den **maximalt uppgifter per VM** har värdet 1 för en Azure Batch-pool. Som en del av krav skapa en pool med denna egenskap angiven till 2, så att två Data Factory-segment kan köras på en virtuell dator på samma gång.

    -   **isPaused** egenskap är inställd på false som standard. Pipelinen körs direkt i det här exemplet eftersom sektorerna starta tidigare. Du kan ange egenskapen till true för att pausa pipeline och ställa tillbaka till false om du vill starta om.

    -   Den **starta** tid och **end** tider är fem timmar från varandra och segment produceras per timma, så att fem segment produceras av pipeline.

1. Klicka på **Distribuera** i kommandofältet för att distribuera pipelinen.

#### <a name="step-5-test-the-pipeline"></a>Steg 5: Testa pipeline
I det här steget kan testa du pipeline genom att släppa filer i de inkommande mapparna. Vi börjar med att testa pipeline med en fil per inkommande mappar.

1. I bladet Data Factory i Azure-portalen klickar du på **Diagram**.

   ![](./media/data-factory-data-processing-using-batch/image10.png)
2. Dubbelklicka på inkommande dataset i diagramvyn: **InputDataset**.

   ![](./media/data-factory-data-processing-using-batch/image11.png)
3. Du bör se den **InputDataset** bladet med alla fem sektorer redo. Observera den **sektorn starttid** och **sektorn SLUTTID** för varje segment.

   ![](./media/data-factory-data-processing-using-batch/image12.png)
4. I den **diagramvyn**, klicka på **OutputDataset**.
5. Du bör se att fem utdata-segment är i tillståndet redo om de redan har skapats.

   ![](./media/data-factory-data-processing-using-batch/image13.png)
6. Använda Azure-portalen för att visa den **uppgifter** som är associerade med den **segment** och se vilka VM varje segment som kördes på. Se [Data Factory och Batch-integrering](#data-factory-and-batch-integration) information.
7. Du bör se utdatafilerna i den `outputfolder` av `mycontainer` i din Azure blob storage.

   ![](./media/data-factory-data-processing-using-batch/image15.png)

   Du bör se fem utdatafiler, en för varje inkommande segment. Var och en av utdatafilen bör ha innehåll som liknar följande utdata:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Följande diagram illustrerar hur Data Factory-segment mappas till aktiviteter i Azure Batch. I det här exemplet har ett segment endast en körning.

   ![](./media/data-factory-data-processing-using-batch/image16.png)
8. Nu ska vi försöker med flera filer i en mapp. Skapa filer: **file2.txt**, **file3.txt**, **file4.txt**, och **file5.txt** med samma innehåll som fil.txt i mappen: **2015-11-06-01**.
9. I Utdatamappen **ta bort** utdatafilen: **2015-11-16-01.txt**.
10. Nu i den **OutputDataset** bladet högerklickar du på segmentet med **sektorn starttid** inställd på **2015-11/16 01:00:00 AM**, och klicka på **kör** till kör/återexport-process sektorn. Nu har sektorn fem filer i stället för en fil.

    ![](./media/data-factory-data-processing-using-batch/image17.png)
11. När sektorn körs och dess status är **klar**, kontrollera innehållet i filen för den här sektorn (**2015-11-16-01.txt**) i den `outputfolder` av `mycontainer` i blob storage. Det bör finnas en rad för varje fil av sektorn.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Om du inte togs bort den utdata filen 2015-11-16-01.txt innan du försöker med fem indatafiler, visas en rad från den sektor som kördes tidigare och fem rader från den aktuella segment-körningen. Som standard läggs innehållet för att skapa fil om den redan finns.
>
>

#### <a name="data-factory-and-batch-integration"></a>Data Factory och Batch-integrering
Data Factory-tjänsten skapar ett jobb med namnet i Azure Batch: `adf-poolname:job-xxx`.

![Azure Data Factory - batchjobb](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

En aktivitet i jobbet skapas för varje aktivitet kör för ett segment. Om det finns 10 segment som är redo att bearbetas, skapas 10 uppgifter i jobbet. Du kan ha fler än ett segment som körs parallellt om du har flera beräkningsnoder i poolen. Om högsta uppgifter per compute-nod har angetts till 1 > kan det finnas mer än ett segment som körs på samma beräkningar.

I det här exemplet finns fem segment, så fem aktiviteter i Azure Batch. Med den **samtidighet** inställd på **5** i pipeline-JSON i Azure Data Factory och **maximalt uppgifter per VM** inställd på **2** i Azure Batch-pool med **2** virtuella datorer kan de aktiviteter körs snabb (kontrollera start- och sluttider för aktiviteter).

Använda portalen för att visa Batch-jobbet och dess uppgifter som är associerade med den **segment** och se vilka VM varje segment som kördes på.

![Azure Data Factory - jobbet batchaktiviteter](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Felsöka pipeline
Felsökning består av några grundläggande metoder:

1. Om inkommande segmentet är inte **klar**, bekräfta att inkommande mappstrukturen är korrekt och fil.txt finns i mapparna inkommande.

   ![](./media/data-factory-data-processing-using-batch/image3.png)
2. I den **kör** metod för din anpassade aktivitet, Använd den **IActivityLogger** objekt att logga information som hjälper dig att felsöka problem. De loggade meddelandena visas i användaren\_0. loggfilen.

   I den **OutputDataset** bladet, klickar du på sektorn att se den **DATASEKTORN** bladet för den sektorn. Du ser **aktivitetskörningar** för den sektorn. Du bör se en aktivitet som körs för sektorn. Om du klickar på **kör** i kommandofältet börjar du en annan aktivitet som körs för samma segment.

   När du klickar på aktiviteten kör du ser den **kör AKTIVITETSINFORMATION** bladet med en lista över loggfilerna. Du ser loggade meddelanden i den **användare\_0. loggen** fil. När ett fel uppstår finns tre aktivitetskörningar eftersom antalet nya försök är inställd på 3 i pipeline/aktivitet JSON. När du klickar på kör aktiviteten finns i loggfilerna som du kan granska för att felsöka felet.

   ![](./media/data-factory-data-processing-using-batch/image18.png)

   I listan över loggfiler, klickar du på den **användare 0.log**. I den högra panelen är resultatet av att använda den **IActivityLogger.Write** metod.

   ![](./media/data-factory-data-processing-using-batch/image19.png)

   Kontrollera system-0.log för alla system felmeddelanden och undantag.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Inkludera den **PDB** filen i zip-filen så att felinformation har information som **anropsstacken** när ett fel inträffar.
4. Alla filer i zip-filen för den anpassade aktiviteten måste finnas på den **övre nivå** med inga undermappar.

   ![](./media/data-factory-data-processing-using-batch/image20.png)
5. Se till att den **assemblyName** (MyDotNetActivity.dll) **entryPoint** (MyDotNetActivityNS.MyDotNetActivity) **packageFile** (customactivitycontainer/MyDotNetActivity.zip) och **packageLinkedService** (ska peka på Azure blob storage som innehåller zip-filen) är inställda på rätt värden.
6. Om du har korrigerat ett fel och vill bearbeta sektorn på nytt, högerklickar du på sektorn i **OutputDataset**-bladet och klickar på **Kör**.

   ![](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Du ser en **behållare** i din Azure Blob storage med namnet: `adfjobs`. Den här behållaren tas inte bort automatiskt, men du kan ta bort när du är klar Testa lösningen. På liknande sätt kan Data Factory-lösning skapar ett Azure Batch **jobbet** med namnet: `adf-\<pool ID/name\>:job-0000000001`. Du kan ta bort det här jobbet när du har testat lösningen om du vill.
   >
   >
7. Den anpassade aktiviteten använder inte den **app.config** filen från paketet. Därför om koden läser eventuella anslutningssträngar i konfigurationsfilen, fungerar det inte vid körning. Bästa praxis och när du använder Azure Batch är att hålla alla hemligheter i en **Azure KeyVault**, använda en certifikatbaserad tjänstens huvudnamn för att skydda keyvault och distribuera certifikat till Azure Batch-pool. Den anpassade .NET-aktiviteten kan sedan komma åt hemligheter i KeyVault vid körning. Den här lösningen är en generisk och kan skalas till alla typer av hemlighet, inte bara anslutningssträngen.

    Det finns en enklare lösning (men inte rekommenderas): du kan skapa en **Azure SQL länkade tjänsten** skapa en datamängd som använder den länkade tjänsten med inställningar för sträng och kedja datamängden som en dummy inkommande datauppsättning för anpassad .NET-aktiviteten. Du kan sedan komma åt den länkade tjänsten anslutningssträngen i koden anpassad aktivitet och den ska fungera bra vid körning.  

#### <a name="extend-the-sample"></a>Utöka exemplet
Du kan utöka det här exemplet ska lära dig mer om Azure Data Factory och Azure Batch-funktioner. Till exempel för att bearbeta segment i ett annat tidsintervall, gör du följande steg:

1. Lägg till följande undermappar i den `inputfolder`: 2015-11-16-05 2015-11-16-06 201-11-16-07, 2011-11-16-08, 2015-11-16-09 och placera inkommande filer i mapparna. Ändra sluttiden för pipelinen från `2015-11-16T05:00:00Z` till `2015-11-16T10:00:00Z`. I den **diagramvyn**, dubbelklicka på den **InputDataset**, och bekräfta att de inkommande segment är klara. Dubbelklicka på **OuptutDataset** att se status för utdata segment. Om de är i tillståndet Ready Kontrollera utdatamapp för utdatafilerna.
2. Öka eller minska den **samtidighet** inställningen för att förstå hur den påverkar prestandan för din lösning, särskilt om bearbetningen som utförs på Azure Batch. (Se steg 4: skapa och köra pipelinen mer den **samtidighet** inställningen.)
3. Skapa en pool med högre/nedre **maximalt uppgifter per VM**. Uppdatera tjänsten Azure Batch länkade i Data Factory-lösningen för att använda den nya poolen som du skapade. (Se steg 4: skapa och köra pipelinen mer den **maximalt uppgifter per VM** inställningen.)
4. Skapa en Azure Batch-pool med **Autoskala** funktion. Skala automatiskt beräkningsnoder i en pool med Azure Batch är dynamisk justering av processorkraft som används av ditt program. 

    Exempel formeln här uppnår på följande: När poolen skapas, det börjar med 1 VM. $PendingTasks mått definierar antalet aktiviteter i körs + aktiv (i kö) tillstånd.  Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger därefter TargetDedicated. Det garanterar att TargetDedicated aldrig är mer omfattande än 25 virtuella datorer. Så när nya aktiviteter skickas pool växer automatiskt och som slutförda uppgifter, virtuella datorer blir ledigt i taget och autoskalning krymper dessa virtuella datorer. startingNumberOfVMs och maxNumberofVMs kan justeras efter dina behov.
 
    Autoskala formel:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Se [automatiskt skala compute-noder i en Azure Batch-pool](../../batch/batch-automatic-scaling.md) mer information.

   Om poolen använder standard [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), Batch-tjänsten kan ta 15 30 minuter att förbereda den virtuella datorn innan du kör den anpassade aktiviteten.  Om poolen använder en annan autoScaleEvaluationInterval, kan Batch-tjänsten ta autoScaleEvaluationInterval + 10 minuter.
5. I exempel-lösning i **Execute** metoden startar den **Calculate** metod som bearbetar ett indata-segment för att skapa ett utgående data segment. Du kan skriva en egen metod för att bearbeta inkommande data och Ersätt metodanropet Calculate i metoden Execute med ett anrop till metoden.

### <a name="next-steps-consume-the-data"></a>Nästa steg: använda data
När du bearbetar data kan du använda den med online verktyg som **Microsoft Power BI**. Här är länkar som hjälper dig att förstå Power BI och hur du använder den i Azure:

* [Utforska en datamängd i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Komma igång med Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Uppdatera data i Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure och Power BI - översikt](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referenser
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introduktion till Azure Data Factory-tjänsten](data-factory-introduction.md)
  * [Kom igång med Azure Data Factory](data-factory-build-your-first-pipeline.md)
  * [Använd anpassade aktiviteter i en Azure Data Factory-pipeline](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Grunderna i Azure Batch](../../batch/batch-technical-overview.md)
  * [Översikt över Azure Batch-funktioner](../../batch/batch-api-basics.md)
  * [Skapa och hantera Azure Batch-kontot i Azure-portalen](../../batch/batch-account-create-portal.md)
  * [Kom igång med Azure Batch-biblioteket .NET](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
