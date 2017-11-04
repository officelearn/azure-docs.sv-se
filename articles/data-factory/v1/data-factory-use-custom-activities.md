---
title: "Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)"
description: "Lär dig hur du skapar anpassade aktiviteter och använda dem i ett Azure Data Factory-pipelinen."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 4264af14370557c55050b0c4951812090d33d7a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-use-custom-activities.md)
> * [Version 2 – förhandsversion](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [anpassade aktiviteter i V2](../transform-data-using-dotnet-custom-activity.md).

Det finns två typer av aktiviteter som du kan använda i ett Azure Data Factory-pipelinen.

- [Data Movement aktiviteter](data-factory-data-movement-activities.md) att flytta data mellan [stöds källa och mottagare datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Data Transformation aktiviteter](data-factory-data-transformation-activities.md) för att omvandla data med hjälp av compute-tjänster som Azure HDInsight, Azure Batch och Azure Machine Learning. 

Om du vill flytta data till/från ett dataarkiv som Data Factory inte har stöd för, skapa en **anpassad aktivitet** med dina egna data movement logik och Använd aktiviteten i en pipeline. På samma sätt för att transformera/bearbeta data på ett sätt som inte stöds av Data Factory, skapa en anpassad aktivitet med dina egna data transformation logik och använda aktiviteten i en pipeline. 

Du kan konfigurera en anpassad aktivitet ska köras på en **Azure Batch** poolen med virtuella datorer eller en Windows-baserad **Azure HDInsight** klustret. När du använder Azure Batch kan använda du endast en befintlig Azure Batch-pool. När du använder HDInsight kan du använda ett befintligt HDInsight-kluster eller ett kluster som skapas automatiskt för du på-begäran vid körning.  

Den här genomgången innehåller stegvisa instruktioner för att skapa en anpassad .NET-aktivitet och använda den anpassade aktiviteten i en pipeline. Den här genomgången använder en **Azure Batch** länkade tjänsten. Att använda en Azure HDInsight länkade tjänsten i stället skapar du en länkad tjänst av typen **HDInsight** (egna HDInsight-kluster) eller **HDInsightOnDemand** (Data Factory skapar ett HDInsight-kluster på begäran). Konfigurera sedan anpassad aktivitet om du vill använda HDInsight länkade tjänsten. Se [Använd Azure HDInsight länkade tjänster](#use-hdinsight-compute-service) information om hur du använder Azure HDInsight för att köra den anpassade aktiviteten.

> [!IMPORTANT]
> - Anpassad .NET-aktiviteter körs bara på Windows-baserade HDInsight-kluster. En lösning för den här begränsningen är att använda aktiviteten mappa minska för att köra anpassad Java-kod på en Linux-baserade HDInsight-kluster. Ett annat alternativ är att använda en Azure Batch-pool för virtuella datorer för att köra anpassade aktiviteter i stället för ett HDInsight-kluster.
> - Det går inte att använda en Data Management Gateway från en anpassad aktivitet för att få åtkomst till lokala datakällor. För närvarande [Data Management Gateway](data-factory-data-management-gateway.md) stöder endast kopieringsaktiviteten och aktiviteten lagrad procedur i Data Factory.   

## <a name="walkthrough-create-a-custom-activity"></a>Genomgång: skapa en anpassad aktivitet
### <a name="prerequisites"></a>Krav
* Visual Studio 2012/2013/2015
* Ladda ned och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Krav för Azure Batch
I den här genomgången kan du köra dina anpassade .NET-aktiviteter med hjälp av Azure Batch som en resurs för beräkning. **Azure Batch** är en plattform som tjänsten för att köra storskaliga parallellt och högpresterande datorbearbetning (HPC) program effektivt i molnet. Azure Batch schemalägger beräkningsintensiva arbete ska köras på en hanterad **samling av virtuella datorer**, och kan automatiskt skala beräkningsresurser för att möta behoven hos dina jobb. Se [grunderna i Azure Batch] [ batch-technical-overview] artikel en detaljerad översikt över Azure Batch-tjänsten.

Skapa ett Azure Batch-konto med en pool för virtuella datorer för självstudierna. Här är stegen:

1. Skapa en **Azure Batch-kontot** med hjälp av den [Azure-portalen](http://portal.azure.com). Se [skapa och hantera Azure Batch-kontot] [ batch-create-account] artikel anvisningar.
2. Notera Azure Batch-kontonamn, kontonyckel, URI: N och namnet på programpoolen. Du behöver dem för att skapa en Azure Batch länkad tjänst.
    1. På startsidan för Azure Batch-kontot som du ser en **URL** i följande format: `https://myaccount.westus.batch.azure.com`. I det här exemplet **MITTKONTO** är namnet på Azure Batch-kontot. URI som du använder i länkade tjänstdefinitionen är URL: en utan att namnet på kontot. Till exempel: `https://<region>.batch.azure.com`.
    2. Klicka på **nycklar** på den vänstra menyn och kopiera den **primära ÅTKOMSTNYCKELN**.
    3. Om du vill använda en befintlig adresspool **pooler** på menyn och Skriv ner den **ID** för poolen. Om du inte har en befintlig adresspool går vidare till nästa steg.     
2. Skapa en **Azure Batch-pool**.

   1. I den [Azure-portalen](https://portal.azure.com), klickar du på **Bläddra** i den vänstra menyn och klicka på **Batch-konton**.
   2. Välj Azure Batch-konto för att öppna den **Batch-kontot** bladet.
   3. Klicka på **pooler** panelen.
   4. I den **pooler** bladet, klickar du på knappen Lägg till i verktygsfältet för att lägga till en pool.
      1. Ange ett ID för poolen (Pool-ID). Observera den **ID för poolen**; du behöver den när du skapar Data Factory-lösning.
      2. Ange **Windows Server 2012 R2** för operativsystemsfamiljen inställningen.
      3. Välj en **nodprisnivå**.
      4. Ange **2** som värde för den **mål dedikerade** inställningen.
      5. Ange **2** som värde för den **maximalt antal uppgifter per nod** inställningen.
   5. Klicka på **OK** för att skapa poolen.
   6. Notera den **ID** för poolen. 



### <a name="high-level-steps"></a>Anvisningar
Här är de två övergripande steg du utför som en del av den här genomgången: 

1. Skapa en anpassad aktivitet som innehåller enkla data transformation/standardbearbetningslogiken.
2. Skapa ett Azure data factory med en pipeline som använder den anpassade aktiviteten.

### <a name="create-a-custom-activity"></a>Skapa en anpassad aktivitet
Om du vill skapa en anpassad aktivitet för .NET, skapa en **.NET-klassbibliotek** projekt med en klass som implementerar som **IDotNetActivity** gränssnitt. Det här gränssnittet har endast en metod: [kör](https://msdn.microsoft.com/library/azure/mt603945.aspx) och signaturen är:

```csharp
public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
```


Metoden som använder fyra parametrar:

- **linkedServices**. Den här egenskapen är en enumerable lista över datalager som är länkade tjänster som refereras av in-/ utdata-datauppsättningar för aktiviteten.   
- **datauppsättningar**. Den här egenskapen är en enumerable lista över datauppsättningar in-/ utdata för aktiviteten. Du kan använda den här parametern för att få platser och scheman som definierats av inkommande och utgående datauppsättningar.
- **aktiviteten**. Denna egenskap representerar den aktuella aktiviteten. Den kan användas för att få åtkomst till utökade egenskaper som är associerade med den anpassade aktiviteten. Se [åtkomst utökade egenskaper](#access-extended-properties) mer information.
- **loggaren**. Det här objektet kan du skriva debug kommentarer som yta i användarinloggning för pipeline.

Metoden returnerar en ordlista som kan användas för att kedja anpassade aktiviteter tillsammans i framtiden. Den här funktionen har inte implementerats ännu, så returneras en tom ordlista från metoden.  

### <a name="procedure"></a>Procedur
1. Skapa en **.NET-klassbibliotek** projekt.
   <ol type="a">
     <li>Starta <b>Visual Studio 2017</b> eller <b>Visual Studio 2015</b> eller <b>Visual Studio 2013</b> eller <b>Visual Studio 2012</b>.</li>
     <li>Klicka på <b>Arkiv</b>, peka på <b>Nytt</b> och klicka på <b>Projekt</b>.</li>
     <li>Expandera <b>Mallar</b> och välj <b>Visual C#</b>. I den här genomgången ska du använda C#, men du kan använda alla .NET-språk för att utveckla anpassade aktiviteten.</li>
     <li>Välj <b>klassbiblioteket</b> från listan över projekttyper till höger. Välj i VS 2017 <b>Class Library (.NET Framework)</b> </li>
     <li>Ange <b>MyDotNetActivity</b> för den <b>namn</b>.</li>
     <li>Välj <b>C:\ADFGetStarted</b> för den <b>plats</b>.</li>
     <li>Klicka på <b>OK</b> för att skapa projektet.</li>
   </ol>
2.Klicka på **verktyg**, peka på **NuGet Package Manager**, och klicka på **Pakethanterarkonsolen**.
3. I Package Manager-konsolen kör du följande kommando för att importera **Microsoft.Azure.Management.DataFactories**.

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importera den **Azure Storage** NuGet-paketet i i projektet.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Startprogram för data Factory-tjänsten kräver 4.3 version av WindowsAzure.Storage. Om du lägger till en referens till en senare version av Azure Storage sammansättning i projektet anpassad aktivitet finns ett fel när aktiviteten körs. Du kan lösa problemet, se [Appdomain isolering](#appdomain-isolation) avsnitt. 
5. Lägg till följande **med** instruktioner till källfilen i projektet.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Ändra namnet på den **namnområde** till **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Ändra namnet på klassen som **MyDotNetActivity** och härleds från den **IDotNetActivity** gränssnitt som visas i följande kodavsnitt:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementera (Lägg till) på **kör** metod för den **IDotNetActivity** gränssnitt till den **MyDotNetActivity** klassen och kopiera följande exempelkod till metoden.

    I följande exempel räknar antal förekomster av sökordet (”Microsoft”) i varje blob som är associerade med en datasektorn.

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
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];
    
        // to log information, use the logger object
        // log all extended properties            
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }
    
        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;
        
        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset 
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);
    
        // get the first Azure Storate linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;
    
        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;
    
        // get the folder path from the input dataset definition
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
               // with the data slice. definition of the method is shown in the next step.
    
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
        } while (continuationToken != null);
    
        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path   
        logger.Write("Writing blob to the folder: {0}", folderPath);
    
        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
        // log the output file name
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
9. Lägg till följande helper-metoder: 

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

        // get type properties of the dataset   
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the folder path found in the type properties
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
    
        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the blob/file name in the type properties
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

    Metoden GetFolderPath returnerar sökvägen till mappen som dataset pekar på, och metoden GetFileName Returnerar namnet på blob/fil som dataset pekar på. Om du havefolderPath definierar använda variabler, till exempel {Year}, {Month}, {Day} osv., metoden returnerar strängen eftersom den är utan att ersätta dem med runtime-värden. Se [åtkomst utökade egenskaper](#access-extended-properties) information om att komma åt SliceStart, SliceEnd osv.    

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoden Calculate beräknar antalet instanser av nyckelordet Microsoft i indatafiler (blobbar i mappen). Sökordet (”Microsoft”) är hårdkodat i koden.
10. Kompilera projektet. Klicka på **skapa** i menyn och klickar på **skapa lösning**.

    > [!IMPORTANT]
    > Ange 4.5.2 version av .NET Framework som målramverk för ditt projekt: Högerklicka på projektet och klicka på **egenskaper** ange Målversionen av framework. Data Factory stöder inte anpassade aktiviteter kompileras mot .NET Framework-versioner senare än 4.5.2.

11. Starta **Windows Explorer**, och navigera till **bin\debug** eller **bin\release** beroende på vilken typ av version.
12. Skapa en zip-fil **MyDotNetActivity.zip** som innehåller alla binärfiler i den <project folder>\bin\Debug mapp. Inkludera den **MyDotNetActivity.pdb** filen så att du får ytterligare information, till exempel radnumret i källkoden som har orsakat problemet om ett fel uppstod. 

    > [!IMPORTANT]
    > Alla filer i zip-filen för den anpassade aktiviteten måste vara på den **översta nivån** utan undermappar.

    ![Binära filer](./media/data-factory-use-custom-activities/Binaries.png)
14. Skapa en blobbbehållare med namnet **customactivitycontainer** om den inte redan finns. 
15. Ladda upp MyDotNetActivity.zip som en blobb till customactivitycontainer i en **allmänna** Azure blob-lagring (hot inte/lågfrekvent Blob-lagring) som refereras av AzureStorageLinkedService.  

> [!IMPORTANT]
> Om du lägger till det här projektet för .NET-aktiviteten en lösning i Visual Studio som innehåller ett projekt som Data Factory och lägga till en referens till projektet för .NET-aktiviteten från Data Factory-programprojekt, behöver du inte utföra de två sista stegen för att manuellt skapa zip-filen och överföra den till allmänna Azure blob storage. När du publicerar Data Factory-enheter med hjälp av Visual Studio, utförs automatiskt de här stegen av publiceringsprocessen. Mer information finns i [Data Factory-projekt i Visual Studio](#data-factory-project-in-visual-studio) avsnitt.

## <a name="create-a-pipeline-with-custom-activity"></a>Skapa en pipeline med anpassad aktivitet
Du har skapat en anpassad aktivitet och upp zip-filen med binärfiler till en blobbbehållare i en **allmänna** Azure Storage-konto. I det här avsnittet skapar du ett Azure data factory med en pipeline som använder den anpassade aktiviteten.

Den inkommande datamängden för den anpassade aktiviteten representerar blobbar (filer) i mappen customactivityinput för adftutorial behållare i blob storage. Datamängd för utdata för aktiviteten representerar utdata blobbar i mappen customactivityoutput för adftutorial behållare i blob storage.

Skapa **fil.txt** filen med följande innehåll och överföra den till **customactivityinput** mappen för den **adftutorial** behållare. Skapa adftutorial behållaren om den inte redan finns. 

```
test custom activity Microsoft test custom activity Microsoft
```

Mappen inkommande motsvarar en sektor i Azure Data Factory även om mappen har två eller flera filer. När varje segment bearbetas av pipelinen går anpassad aktivitet igenom alla blobbar i mappen inkommande för den sektorn.

Du ser en utdatafil med i mappen adftutorial\customactivityoutput med en eller flera rader (samma som antal blobbar i mappen inkommande):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Här är vad du behöver göra i det här avsnittet:

1. Skapa en **datafabriken**.
2. Skapa **länkade tjänster** för Azure Batch-pool för virtuella datorer som den anpassade aktiviteten körs och Azure-lagring som innehåller in-/ utdata-BLOB.
3. Skapa ingående och utgående **datauppsättningar** som representerar indata och utdata för den anpassade aktiviteten.
4. Skapa en **pipeline** som använder den anpassade aktiviteten.

> [!NOTE]
> Skapa den **fil.txt** och överföra den till en blob-behållare, om du inte redan gjort det.. Se anvisningarna i föregående avsnitt.   

### <a name="step-1-create-the-data-factory"></a>Steg 1: Skapa datafabriken
1. När du loggar in på Azure-portalen, gör du följande steg:
   1. Klicka på **NYTT** i den vänstra menyn.
   2. Klicka på **Data + analys** i den **ny** bladet.
   3. Klicka på **Data Factory** på bladet **Dataanalys**.
   
    ![Nya Azure Data Factory-menyn](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. I den **nya datafabriken** bladet ange **CustomActivityFactory** för namnet. Namnet på Azure Data Factory måste vara globalt unikt. Om du får felmeddelandet: **datafabriksnamnet ”CustomActivityFactory” är inte tillgänglig**, byta namn på datafabriken (till exempel **yournameCustomActivityFactory**) och försöker skapa igen.

    ![Nya Azure Data Factory-bladet](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Klicka på **RESURSGRUPPENS namn**, och välj en befintlig resursgrupp eller skapa en resursgrupp.
4. Kontrollera att du använder rätt **prenumeration** och **region** där du vill att datafabriken ska skapas.
5. Klicka på **Skapa** på bladet **Ny datafabrik**.
6. Du ser datafabriken som skapas i den **instrumentpanelen** på Azure-portalen.
7. När datafabriken har skapats, kan du se Data Factory-bladet, som visar innehållet i datafabriken.
    
    ![Bladet Datafabrik](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Steg 2: Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. I det här steget kan länka du ditt Azure Storage-konto och Azure Batch-konto till din data factory.

#### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
1. Klicka på den **författare och distribuera** panelen på den **DATAFABRIKEN** bladet för **CustomActivityFactory**. Du kan se Data Factory-redigeraren.
2. Klicka på **Nytt datalager** på kommandoraden och välj **Azure storage**. Du bör se JSON-skriptet för att skapa en länkad Azure-lagringstjänst i redigeraren.
    
    ![Nytt datalager - Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Ersätt `<accountname>` med namnet på ditt Azure storage-konto och `<accountkey>` med åtkomstnyckeln för Azure storage-konto. Information om hur du hämtar din lagringsåtkomstnyckel finns i [Visa, kopiera och återskapa lagringsåtkomstnycklar](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

    ![Azure Storage tyckte om tjänsten](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Klicka på **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

#### <a name="create-azure-batch-linked-service"></a>Skapa Azure Batch länkad tjänst
1. I den Data Factory-redigeraren, klicka på **... Flera** i kommandofältet klickar du på **nya beräkning**, och välj sedan **Azure Batch** på menyn.

    ![Nya beräknings - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Gör följande ändringar i JSON-skriptet:

   1. Ange Azure Batch-kontonamnet för den **accountName** egenskapen. Den **URL** från den **Azure Batch-kontoblad** är i följande format: `http://accountname.region.batch.azure.com`. För den **batchUri** egenskap i JSON som du behöver ta bort `accountname.` från URL: en och använder den `accountname` för den `accountName` JSON-egenskapen.
   2. Ange nyckel för Azure Batch-kontot för den **accessKey** egenskapen.
   3. Ange namnet på poolen som du har skapat som en del av krav för den **poolName** egenskapen. Du kan också ange ID för poolen i stället för namnet på poolen.
   4. Ange Azure Batch-URI för den **batchUri** egenskapen. Exempel: `https://westus.batch.azure.com`.  
   5. Ange den **AzureStorageLinkedService** för den **linkedServiceName** egenskapen.

        ```json
        {
         "name": "AzureBatchLinkedService",
         "properties": {
           "type": "AzureBatch",
           "typeProperties": {
             "accountName": "myazurebatchaccount",
             "batchUri": "https://westus.batch.azure.com",
             "accessKey": "<yourbatchaccountkey>",
             "poolName": "myazurebatchpool",
             "linkedServiceName": "AzureStorageLinkedService"
           }
         }
        }
        ```

       För den **poolName** egenskap, du kan också ange ID för poolen i stället för namnet på poolen.

      > [!IMPORTANT]
      > Data Factory-tjänsten stöder inte ett alternativ på begäran för Azure Batch som för HDInsight. Du kan bara använda din egen Azure Batch-pool i ett Azure data factory.   
    

### <a name="step-3-create-datasets"></a>Steg 3: Skapa datauppsättningar
I det här steget skapar du datauppsättningar som representerar indata och utdata.

#### <a name="create-input-dataset"></a>Skapa indatauppsättning
1. I **redigeringsprogrammet** för Data Factory klickar du på **... Flera** i kommandofältet klickar du på **ny datamängd**, och välj sedan **Azure Blob storage** från den nedrullningsbara menyn.
2. Ersätt JSON i den högra rutan med följande kodavsnitt i JSON:

    ```json
    {
     "name": "InputDataset",
     "properties": {
         "type": "AzureBlob",
         "linkedServiceName": "AzureStorageLinkedService",
         "typeProperties": {
             "folderPath": "adftutorial/customactivityinput/",
             "format": {
                 "type": "TextFormat"
             }
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

   Du skapar en pipeline senare i den här genomgången med starttid: 2016-11-16T00:00:00Z-och Sluttid: 2016-11-16T05:00:00Z. Den är schemalagd att generera data varje timma, så att det finns fem i/o-segment (mellan **00**: 00:00 -> **05**: 00:00).

   Den **frekvens** och **intervall** för inkommande datamängden har värdet **timme** och **1**, vilket innebär att inkommande segmentet finns varje timme. I det här exemplet är det samma fil (fil.txt) på intputfolder.

   Här följer starttider för varje segment som representeras av SliceStart systemvariabel i ovanstående JSON-fragment.
3. Klicka på **distribuera** i verktygsfältet för att skapa och distribuera den **InputDataset**. Kontrollera att du ser meddelandet **TABELLEN HAR SKAPATS** i namnlisten i redigeraren.

#### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
1. I den **Data Factory-redigeraren**, klickar du på **... Flera** i kommandofältet klickar du på **ny datamängd**, och välj sedan **Azure Blob storage**.
2. Ersätt JSON-skript i den högra rutan med följande JSON-skript:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
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

     Platsen är **adftutorial/customactivityoutput/** och utdata-filnamn är åååå-MM-dd-HH.txt där åååå-MM-dd-HH är år, månad, datum och timme av sektorn som skapas. Se [för utvecklare] [ adf-developer-reference] mer information.

    En blob/utdatafil genereras för varje inkommande segment. Här är hur en utdatafil heter för varje segment. Alla utdatafiler som skapas i en utdatamapp: **adftutorial\customactivityoutput**.

   | Sektorn | Starttid | Utdatafil |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Kom ihåg att alla filer i en inkommande mapp är en del av ett segment med starttiderna som nämns ovan. När den här sektorn behandlas den anpassade aktiviteten söker igenom varje fil och ger en rad i filen med antalet förekomster av sökordet (”Microsoft”). Om det finns tre filer i inputfolder, det finns tre rader i filen för varje timme segment: 2016-11-16-00.txt 2016-11-16:01:00:00.txt, etc.
3. Att distribuera den **OutputDataset**, klickar du på **distribuera** i kommandofältet.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Skapa och köra en pipeline som använder den anpassade aktiviteten
1. I den Data Factory-redigeraren, klicka på **... Flera**, och välj sedan **ny pipeline** i kommandofältet. 
2. Ersätt JSON i den högra rutan med följande JSON-skript:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Observera följande punkter:

   * **Concurrency** är inställd på **2** så att två segment bearbetas parallellt med 2 virtuella datorer i Azure Batch-pool.
   * Det finns en aktivitet i avsnittet aktiviteter och är av typen: **DotNetActivity**.
   * **AssemblyName** är inställd på namnet på DLL: **MyDotnetActivity.dll**.
   * **EntryPoint** är inställd på **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** är inställd på **AzureStorageLinkedService** som pekar till blob-lagring som innehåller anpassad aktivitet zip-filen. Om du använder olika Azure Storage-konton för i/o-filer och anpassad aktivitet zip-filen kan skapa du en annan länkad Azure Storage-tjänst. Den här artikeln förutsätter att du använder samma Azure Storage-konto.
   * **PackageFile** är inställd på **customactivitycontainer/MyDotNetActivity.zip**. Det är i formatet: containerforthezip/nameofthezip.zip.
   * Den anpassade aktiviteten tar **InputDataset** som indata och **OutputDataset** som utdata.
   * LinkedServiceName-egenskapen för den anpassade aktiviteten pekar på den **AzureBatchLinkedService**, som talar om Azure Data Factory som den anpassade aktiviteten ska köras på virtuella datorer i Azure Batch.
   * **isPaused** egenskap är inställd på **FALSKT** som standard. Pipelinen körs direkt i det här exemplet eftersom sektorerna starta tidigare. Du kan ange egenskapen till true för att pausa pipeline och ställa tillbaka till false om du vill starta om.
   * Den **starta** tid och **end** tider är **fem** timmar från varandra och segment produceras varje timma, så att fem segment produceras av pipeline.
3. Om du vill distribuera pipeline, klickar du på **distribuera** i kommandofältet.

### <a name="monitor-the-pipeline"></a>Övervaka pipeline
1. I bladet Data Factory i Azure-portalen klickar du på **Diagram**.

    ![Ikonen Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Klicka på OutputDataset i diagramvyn.

    ![Diagramvy](./media/data-factory-use-custom-activities/diagram.png)
3. Du bör se att fem utdata-segment är i tillståndet redo. Om de inte är i tillståndet klar inte har de producerats ännu. 

   ![Utdata segment](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Kontrollera att utdatafilerna genereras i blobblagring i den **adftutorial** behållare.

   ![utdata från anpassad aktivitet][image-data-factory-ouput-from-custom-activity]
5. Om du öppnar filen bör du se utdata som liknar följande utdata:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Använd den [Azure-portalen] [ azure-preview-portal] eller Azure PowerShell-cmdletar för att övervaka din data factory, rörledningar och datauppsättningar. Du kan ta emot meddelanden från den **ActivityLogger** i koden för den anpassade aktiviteten i loggarna (särskilt användar-0.log) som du kan hämta från portalen eller med cmdlet: ar.

   ![Hämta loggar från anpassad aktivitet][image-data-factory-download-logs-from-custom-activity]

Se [övervaka och hantera Pipelines](data-factory-monitor-manage-pipelines.md) detaljerade anvisningar för att övervaka datamängd och pipelinor.      

## <a name="data-factory-project-in-visual-studio"></a>Data Factory-projekt i Visual Studio  
Du kan skapa och publicera Data Factory-enheter med hjälp av Visual Studio istället för att använda Azure-portalen. För detaljerad information om hur du skapar och publicerar Data Factory-enheter med hjälp av Visual Studio finns [skapa din första pipeline med Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) och [kopiera data från Azure Blob till Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) artiklar.

Gör följande om du skapar Data Factory-projekt i Visual Studio:
 
1. Lägga till Data Factory-projekt i Visual Studio-lösning som innehåller anpassad aktivitet projektet. 
2. Lägg till en referens till projektet .NET aktivitet från Data Factory-projekt. Högerklicka på Data Factory-projektet, peka på **Lägg till**, och klicka sedan på **referens**. 
3. I den **Lägg till referens** dialogrutan markerar du den **MyDotNetActivity** projektet och klicka på **OK**.
4. Skapa och publicera lösningen.

    > [!IMPORTANT]
    > När du publicerar Data Factory-entiteter, en zip-fil skapas automatiskt för dig och överförs till blob-behållaren: customactivitycontainer. Om blob-behållaren inte finns, skapas den automatiskt för.  


## <a name="data-factory-and-batch-integration"></a>Data Factory och Batch-integrering
Data Factory-tjänsten skapar ett jobb med namnet i Azure Batch: **poolname adf: jobb-xxx**. Klicka på **jobb** i den vänstra menyn. 

![Azure Data Factory - batchjobb](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

En aktivitet skapas för varje aktivitet kör för ett segment. Om det finns fem segment som är redo att bearbetas, skapas fem uppgifter i jobbet. Om det finns flera compute-noder i Batch-pool, kan två eller flera segment köras parallellt. Du kan också ha fler än ett segment som körs på samma beräkningar om de maximala uppgifterna per compute-nod är > 1.

![Azure Data Factory - jobbet batchaktiviteter](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Följande diagram illustrerar förhållandet mellan Azure Data Factory och Batch uppgifter.

![Data Factory & Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Felsöka fel
Felsökning består av några grundläggande tekniker:

1. Om du ser följande fel kan kanske du använder en aktiv/lågfrekvent blob-lagring i stället för med ett allmänt Azure blob storage. Ladda upp zip-filen till en **allmänna Azure Storage-konto**. 
 
    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ``` 
2. Om du ser följande fel, bekräfta att namnet på klassen i filen CS matchar det namn du angav för den **EntryPoint** egenskap i pipeline-JSON. I den här genomgången är namnet på klassen: MyDotNetActivity och EntryPoint i JSON: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Om namnen stämmer överens, bekräfta att alla binärfiler i **rotmappen** i zip-filen. När du öppnar zip-filen som är bör du se alla filer i rotmappen, inte i alla undermappar.   
3. Om inkommande segmentet är inte **klar**, bekräfta att inkommande mappstrukturen är korrekt och **fil.txt** finns i mapparna inkommande.
3. I den **kör** metod för din anpassade aktivitet, Använd den **IActivityLogger** objekt att logga information som hjälper dig att felsöka problem. De loggade meddelandena visas i loggfilerna för användare (en eller flera filer med namnet: användaren 0.log, användaren 1.log, användaren 2.log osv.).

   I den **OutputDataset** bladet, klickar du på sektorn att se den **DATASEKTORN** bladet för den sektorn. Du ser **aktivitetskörningar** för den sektorn. Du bör se en aktivitet som körs för sektorn. Om du klickar på Kör i kommandofältet kan du starta en annan aktivitet som körs för samma segment.

   När du klickar på aktiviteten kör du ser den **kör AKTIVITETSINFORMATION** bladet med en lista över loggfilerna. Du ser loggade meddelanden i filen user_0.log. När ett fel uppstår finns tre aktivitetskörningar eftersom antalet nya försök är inställd på 3 i pipeline/aktivitet JSON. När du klickar på kör aktiviteten finns i loggfilerna som du kan granska för att felsöka felet.

   I listan över loggfiler, klickar du på den **användare 0.log**. I den högra panelen är resultatet av att använda den **IActivityLogger.Write** metod. Om du inte ser alla meddelanden, kontrollera om du har flera loggfiler med namnet: user_1.log, user_2.log osv. Koden kan annars kan ha misslyckats efter senaste loggat meddelandet.

   Du kan också kontrollera **system 0.log** för alla system felmeddelanden och undantag.
4. Inkludera den **PDB** filen i zip-filen så att felinformation har information som **anropsstacken** när ett fel inträffar.
5. Alla filer i zip-filen för den anpassade aktiviteten måste vara på den **översta nivån** utan undermappar.
6. Se till att den **assemblyName** (MyDotNetActivity.dll) **entryPoint**(MyDotNetActivityNS.MyDotNetActivity) **packageFile** (customactivitycontainer/MyDotNetActivity.zip) och **packageLinkedService** (måste peka på den **allmänna**Azure blob-lagring som innehåller zip-filen) är inställda på rätt värden.
7. Om du har korrigerat ett fel och vill bearbeta sektorn på nytt, högerklickar du på sektorn i **OutputDataset**-bladet och klickar på **Kör**.
8. Om du ser följande fel använder du Azure Storage-paketet med version > 4.3.0. Startprogram för data Factory-tjänsten kräver 4.3 version av WindowsAzure.Storage. Se [Appdomain isolering](#appdomain-isolation) avsnittet för en arbete runt om du måste använda den senare versionen av Azure Storage-sammansättningen. 

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 
    ```

    Om du kan använda 4.3.0 versionen av Azure Storage, ta bort befintliga referensen till Azure Storage-paketet med version > 4.3.0. Kör sedan följande kommando från NuGet Package Manager-konsolen. 

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Bygga projektet. Ta bort Azure.Storage sammansättningen av version > 4.3.0 från mappen bin\Debug. Skapa en zip-fil med binärfiler och PDB-filen. Ersätt den gamla zipfilen med den här i blob-behållaren (customactivitycontainer). Köra sektorerna misslyckades (Högerklicka segment och klicka på Kör).   
8. Den anpassade aktiviteten använder inte den **app.config** filen från paketet. Därför om koden läser eventuella anslutningssträngar i konfigurationsfilen, fungerar det inte vid körning. Bästa praxis och när du använder Azure Batch är att hålla alla hemligheter i en **Azure KeyVault**, använda en certifikatbaserad tjänstens huvudnamn för att skydda den **keyvault**, och distribuera certifikat till Azure Batch-pool. Den anpassade .NET-aktiviteten kan sedan komma åt hemligheter i KeyVault vid körning. Den här lösningen är en allmän lösning och kan skalas till alla typer av hemlighet, inte bara anslutningssträngen.

   Det finns en enklare lösning (men inte rekommenderas): du kan skapa en **Azure SQL länkade tjänsten** skapa en datamängd som använder den länkade tjänsten med inställningar för sträng och kedja datamängden som en dummy inkommande datauppsättning för anpassad .NET-aktiviteten. Du kan sedan komma åt den länkade tjänsten anslutningssträngen i koden anpassad aktivitet.  

## <a name="update-custom-activity"></a>Uppdatera anpassad aktivitet
Om du uppdaterar koden för den anpassade aktiviteten, skapa den och ladda upp zip-filen som innehåller nya binärfiler till blob storage.

## <a name="appdomain-isolation"></a>AppDomain isolering
Se [mellan AppDomain-exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) som visar hur du skapar en anpassad aktivitet som inte är begränsad till sammansättningen versioner som används av Data Factory-programstart (exempel: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x osv.).

## <a name="access-extended-properties"></a>Åtkomst till utökade egenskaper
Du kan deklarera utökade egenskaper i aktivitets-JSON som visas i följande exempel:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```


Det här exemplet finns det två utökade egenskaper: **SliceStart** och **DataFactoryName**. Värdet för SliceStart baseras på variabeln SliceStart system. Se [systemvariabler](data-factory-functions-variables.md) för en lista över variabler system som stöds. Värdet för DataFactoryName är hårdkodat till CustomActivityFactory.

Åtkomst till dessa utökade egenskaper i den **Execute** metod, Använd koden liknar följande kod:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties                               
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Automatisk skalning av Azure Batch
Du kan också skapa en Azure Batch-pool med **Autoskala** funktion. Du kan till exempel skapa en azure batch-pool med 0 dedikerade virtuella datorer och en Autoskala formel baserat på antalet väntande åtgärder. 

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

## <a name="use-hdinsight-compute-service"></a>Använda HDInsight beräknings-tjänst
I den här genomgången används Azure Batch beräkning för att köra den anpassade aktiviteten. Du kan också använda egna Windows-baserade HDInsight-kluster eller har Data Factory skapar på-begäran-Windows-baserade HDInsight-kluster och har den anpassade aktiviteten körs på HDInsight-klustret. Här följer anvisningar för att använda ett HDInsight-kluster.

> [!IMPORTANT]
> Anpassad .NET-aktiviteter körs bara på Windows-baserade HDInsight-kluster. En lösning för den här begränsningen är att använda aktiviteten mappa minska för att köra anpassad Java-kod på en Linux-baserade HDInsight-kluster. Ett annat alternativ är att använda en Azure Batch-pool för virtuella datorer för att köra anpassade aktiviteter i stället för ett HDInsight-kluster.
 

1. Skapa en Azure HDInsight länkad tjänst.   
2. Använd HDInsight länkade tjänsten i stället för **AzureBatchLinkedService** i pipeline-JSON.

Om du vill testa den med den här genomgången, ändra **starta** och **end** tidsgränsen för pipeline så att du kan testa scenariot med tjänsten Azure HDInsight.

#### <a name="create-azure-hdinsight-linked-service"></a>Skapa en Azure HDInsight-länkad tjänst
Azure Data Factory-tjänsten stöder skapandet av ett kluster på begäran och använda den för att behandla indata för att gav inga utdata. Du kan också använda ditt eget kluster för att utföra samma. När du använder HDInsight-kluster på begäran, skapas ett kluster för varje segment. Om du använder egna HDInsight-kluster i klustret är redo att utföra sektorn omedelbart. Därför när du använder kluster på begäran måste kanske du inte visas utdata så snabbt som när du använder ditt eget kluster.

> [!NOTE]
> Vid körning körs en instans av en .NET-aktiviteten bara på en worker-nod i HDInsight-klustret; den kan inte skalas för att köras på flera noder. Flera instanser av .NET-aktiviteten kan köras parallellt på olika noder i HDInsight-klustret.
>
>

##### <a name="to-use-an-on-demand-hdinsight-cluster"></a>Att använda ett HDInsight-kluster på begäran
1. I den **Azure-portalen**, klickar du på **författare och distribution** i Data Factory-startsidan.
2. I den Data Factory-redigeraren, klicka på **nya beräkning** kommandofältet och välj **på begäran HDInsight-kluster** på menyn.
3. Gör följande ändringar i JSON-skriptet:

   1. För den **clusterSize** -egenskapen anger storleken på HDInsight-klustret.
   2. För den **timeToLive** -egenskapen anger hur länge kunden kan vara inaktiv innan den tas bort.
   3. För den **version** egenskap, ange HDInsight-version som du vill använda. Om du utesluter den här egenskapen används den senaste versionen.  
   4. För den **linkedServiceName**, ange **AzureStorageLinkedService**.

        ```JSON
        {
           "name": "HDInsightOnDemandLinkedService",
           "properties": {
               "type": "HDInsightOnDemand",
               "typeProperties": {
                   "clusterSize": 4,
                   "timeToLive": "00:05:00",
                   "osType": "Windows",
                   "linkedServiceName": "AzureStorageLinkedService",
               }
           }
        }
        ```

    > [!IMPORTANT]
    > Anpassad .NET-aktiviteter körs bara på Windows-baserade HDInsight-kluster. En lösning för den här begränsningen är att använda aktiviteten mappa minska för att köra anpassad Java-kod på en Linux-baserade HDInsight-kluster. Ett annat alternativ är att använda en Azure Batch-pool för virtuella datorer för att köra anpassade aktiviteter i stället för ett HDInsight-kluster.

4. Klicka på **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

##### <a name="to-use-your-own-hdinsight-cluster"></a>Använda din egen HDInsight-kluster:
1. I den **Azure-portalen**, klickar du på **författare och distribution** i Data Factory-startsidan.
2. I den **Data Factory-redigeraren**, klickar du på **nya beräkning** kommandofältet och välj **HDInsight-kluster** på menyn.
3. Gör följande ändringar i JSON-skriptet:

   1. För den **clusterUri** egenskap, ange URL för din HDInsight. Till exempel: https://<clustername>.azurehdinsight.net/     
   2. För den **användarnamn** egenskap, ange namnet på användaren som har åtkomst till HDInsight-klustret.
   3. För den **lösenord** egenskap, ange lösenordet för användaren.
   4. För den **LinkedServiceName** egenskap, ange **AzureStorageLinkedService**.
4. Klicka på **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

Se [Compute länkade tjänster](data-factory-compute-linked-services.md) mer information.

I den **pipeline-JSON**, använda HDInsight (på begäran eller egna) länkade tjänsten:

```JSON
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "HDInsightOnDemandLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00Z",
    "end": "2016-11-16T05:00:00Z",
    "isPaused": false
  }
}
```

## <a name="create-a-custom-activity-by-using-net-sdk"></a>Skapa en anpassad aktivitet med hjälp av .NET SDK
I den här genomgången i den här artikeln skapa en datafabrik med en pipeline som använder den anpassade aktiviteten med hjälp av Azure portal. Följande kod visar hur du skapar datafabriken med hjälp av .NET SDK i stället. Du hittar mer information om hur du använder SDK för att skapa programmässigt pipelines i den [skapar en pipeline med kopieringsaktiviteten med hjälp av .NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md) artikel. 

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Felsöka anpassad aktivitet i Visual Studio
Den [Azure Data Factory - lokal miljö](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) prov på GitHub innehåller ett verktyg som hjälper dig att felsöka anpassade .NET-aktiviteter i Visual Studio.  


## <a name="sample-custom-activities-on-github"></a>Exempel anpassade aktiviteter på GitHub
| Exempel | Vilka anpassade aktiviteten har |
| --- | --- |
| [HTTP-Data Installationshämtaren](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Hämtar data från en HTTP-slutpunkt till Azure Blob Storage med hjälp av anpassade C# aktiviteten i Data Factory. |
| [Twitter-Sentiment Analysis-exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Anropar en Azure ML-modell och vill sentiment analys, bedömningen, förutsägelse osv. |
| [Kör R-skriptet](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Anropar R-skriptet genom att köra RScript.exe på ditt HDInsight-kluster som redan har R installerat på den. |
| [Mellan AppDomain .NET-aktiviteten](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Använder olika sammansättningen versioner från de som används av Data Factory-starta |
| [Bearbeta en modell i Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Ombearbeta en modell i Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
