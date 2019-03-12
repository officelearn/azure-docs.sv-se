---
title: Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)
description: Lär dig hur du skapar anpassade aktiviteter och använda dem i ett Azure Data Factory-pipelinen.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 6d7aeef415fcc53fda2f5d66d748dd38f6d644fb
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576743"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-use-custom-activities.md)
> * [Version 2 (aktuell version)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [anpassade aktiviteter i V2](../transform-data-using-dotnet-custom-activity.md).

Det finns två typer av aktiviteter som du kan använda i en Azure Data Factory-pipeline.

- [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) att flytta data mellan [datalager för källa och mottagare som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) för att omvandla data med Beräkningstjänster som Azure HDInsight, Azure Batch och Azure Machine Learning.

Om du vill flytta data till/från ett datalager som Data Factory inte stöder kan skapa en **anpassad aktivitet** med dina egna data movement logik och Använd aktiviteten i en pipeline. På samma sätt för att transformera/bearbeta data på ett sätt som inte stöds av Data Factory, skapa en anpassad aktivitet med egen logik för omvandling av data och använda aktiviteten i en pipeline.

Du kan konfigurera en anpassad aktivitet ska köras på en **Azure Batch** pool med virtuella datorer. När du använder Azure Batch kan använda du endast en befintlig Azure Batch-pool.

Den här genomgången innehåller stegvisa instruktioner för att skapa en anpassad .NET-aktivitet och använda den anpassade aktiviteten i en pipeline. Den här genomgången använder en **Azure Batch** länkad tjänst.

> [!IMPORTANT]
> - Det går inte att använda en Gateway för datahantering från en anpassad aktivitet för att komma åt lokala datakällor. För närvarande [Data Management Gateway](data-factory-data-management-gateway.md) stöder endast Kopieringsaktivitet och lagrad proceduraktivitet i Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Genomgång: skapa en anpassad aktivitet
### <a name="prerequisites"></a>Förutsättningar
* Visual Studio 2012/2013/2015
* Ladda ned och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Krav för Azure Batch
I den här genomgången kan du köra dina anpassade .NET-aktiviteter med hjälp av Azure Batch som en beräkningsresurs. **Azure Batch** är en plattform som tjänst för att köra storskaliga parallella och högpresterande databehandlingsprogram (HPC) effektivt i molnet. Azure Batch schemalägger beräkningsintensiva arbeten att köras på en hanterad **samling virtuella datorer**, och kan automatiskt skala beräkningsresurser för att uppfylla behoven i dina jobb. Se [grunderna om Azure Batch] [ batch-technical-overview] artikeln en detaljerad översikt över Azure Batch-tjänsten.

Skapa ett Batch-konto med en pool med virtuella datorer för den här självstudien. Här är stegen:

1. Skapa en **Azure Batch-konto** med hjälp av den [Azure-portalen](https://portal.azure.com). Se [skapa och hantera ett Azure Batch-konto] [ batch-create-account] artikeln anvisningar.
2. Skriv ned Azure Batch-kontonamn, nyckel, URI: N och namn på programpool. Du behöver dem för att skapa ett Azure Batch-länkad tjänst.
    1. På startsidan för Azure Batch-konto, visas en **URL** i följande format: `https://myaccount.westus.batch.azure.com`. I det här exemplet **myaccount** är namnet på Azure Batch-kontot. URI: N som du använder i länkade tjänstedefinition är URL: en utan att namnet på kontot. Till exempel: `https://<region>.batch.azure.com`.
    2. Klicka på **nycklar** på den vänstra menyn och kopiera den **primära ÅTKOMSTNYCKEL**.
    3. Om du vill använda en befintlig pool, klickar du på **pooler** på menyn och Skriv ned den **ID** för poolen. Om du inte har en befintlig pool kan du flytta till nästa steg.
2. Skapa en **Azure Batch-pool**.

   1. I den [Azure-portalen](https://portal.azure.com), klickar du på **Bläddra** i den vänstra menyn och på **Batch-konton**.
   2. Välj din Azure Batch-konto för att öppna den **Batch-kontot** bladet.
   3. Klicka på **pooler** panelen.
   4. I den **pooler** bladet klickar du på knappen Lägg till i verktygsfältet för att lägga till en pool.
      1. Ange ett ID för poolen (Pool-ID). Obs den **ID: T för poolen**; du behöver den när du skapar Data Factory-lösning.
      2. Ange **Windows Server 2012 R2** för operativsystemets familj inställningen.
      3. Välj en **nodprisnivå**.
      4. Ange **2** som värde för den **Target dedikerade** inställningen.
      5. Ange **2** som värde för den **högsta antal aktiviteter per nod** inställningen.
   5. Klicka på **OK** för att skapa poolen.
   6. Anteckna den **ID** för poolen.

### <a name="high-level-steps"></a>Steg på hög nivå
Här är de två övergripande steg du utför som en del av den här genomgången:

1. Skapa en anpassad aktivitet som innehåller enkla transformering/bearbetning av dataströmmar.
2. Skapa en Azure-datafabrik med en pipeline som använder den anpassade aktiviteten.

### <a name="create-a-custom-activity"></a>Skapa en anpassad aktivitet
Om du vill skapa en anpassad .NET-aktivitet, skapa en **.NET-klassbiblioteket** projekt med en klass som implementerar som **IDotNetActivity** gränssnitt. Det här gränssnittet har bara en av metoderna: [Köra](https://msdn.microsoft.com/library/azure/mt603945.aspx) och signaturen är:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

Metoden tar fyra parametrar:

- **linkedServices**. Den här egenskapen är en uppräkningsbara lista över Data Store länkade tjänster som refereras av datauppsättningar för indata/utdata för aktiviteten.
- **datauppsättningar**. Den här egenskapen är en uppräkningsbara lista över datauppsättningar för indata/utdata för aktiviteten. Du kan använda den här parametern för att få de platser och scheman som definieras av in- och utdatauppsättningar.
- **aktiviteten**. Denna egenskap representerar den aktuella aktiviteten. Den kan användas för att få åtkomst till utökade egenskaper som är associerade med den anpassade aktiviteten. Se [åtkomst utökade egenskaper](#access-extended-properties) mer information.
- **logger**. Det här objektet kan du skriva debug kommentarer som surface i användarloggen för för pipelinen.

Metoden returnerar en ordlista som kan användas för att länka anpassade aktiviteter tillsammans i framtiden. Den här funktionen har inte implementerats ännu, så returnerar en tom ordlista från metoden.

### <a name="procedure"></a>Procedur
1. Skapa en **.NET-klassbiblioteket** projekt.
   <ol type="a">
     <li>Starta <b>Visual Studio 2017</b> eller <b>Visual Studio 2015</b> eller <b>Visual Studio 2013</b> eller <b>Visual Studio 2012</b>.</li>
     <li>Klicka på <b>Arkiv</b>, peka på <b>Nytt</b> och klicka på <b>Projekt</b>.</li>
     <li>Expandera <b>Mallar</b> och välj <b>Visual C#</b>. I den här genomgången ska du använda C#, men du kan använda valfritt .NET-språk för att utveckla den anpassade aktiviteten.</li>
     <li>Välj <b>klassbiblioteket</b> i listan över projekttyper till höger. I VS 2017 väljer <b>Class Library (.NET Framework)</b> </li>
     <li>Ange <b>MyDotNetActivity</b> för den <b>namn</b>.</li>
     <li>Välj <b>C:\ADFGetStarted</b> för den <b>plats</b>.</li>
     <li>Klicka på <b>OK</b> för att skapa projektet.</li>
   </ol>

2. Klicka på **Verktyg**, peka på **NuGet Package Manager** och klicka på **Package Manager Console**.

3. I Package Manager-konsolen kör du följande kommando för att importera **Microsoft.Azure.Management.DataFactories**.

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importera den **Azure Storage** NuGet-paket i i projektet.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Startprogram för data Factory-tjänsten krävs 4.3 av WindowsAzure.Storage. Om du lägger till en referens till en senare version av Azure Storage-sammansättningen i projektet anpassade aktiviteten, visas ett fel när aktiviteten körs. Lös felet genom att se [Appdomain isolering](#appdomain-isolation) avsnittet.
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
7. Ändra namnet på klassen för att **MyDotNetActivity** och få det från den **IDotNetActivity** gränssnitt som visas i följande kodavsnitt:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementera (Lägg till) det **kör** -metoden för den **IDotNetActivity** gränssnitt till den **MyDotNetActivity** klassen och kopiera följande exempelkod till metoden.

    I följande exempel räknar antalet förekomster av söktermen (”Microsoft”) i varje blob som är associerade med en datasektor.

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

        // get the first Azure Storage linked service from linkedServices object
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

    Metoden GetFolderPath returnerar sökvägen till den mapp som den här datauppsättningen pekar på och metoden GetFileName Returnerar namnet på blob/fil som datauppsättningen pekar på. Om du har folderPath definierar använda variabler, till exempel {Year}, {Month} {Day} osv, metoden returnerar strängen som den är utan att ersätta dem med runtime-värden. Se [åtkomst utökade egenskaper](#access-extended-properties) information om hur du använder SliceStart, SliceEnd osv.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoden Calculate beräknar antalet instanser av nyckelordet Microsoft i indatafilerna (blobbar i mappen). Söktermen (”Microsoft”) är hårdkodad i koden.
10. Kompilera projektet. Klicka på **skapa** i menyn och klicka på **skapa lösning**.

    > [!IMPORTANT]
    > Ange 4.5.2 version av .NET Framework som målramverk för ditt projekt: Högerklicka på projektet och klicka på **egenskaper** att ställa in målramverk. Data Factory stöder inte anpassade aktiviteter som är senare än 4.5.2 kompileras mot .NET Framework-versioner.

11. Starta **Windows Explorer**, och gå till **bin\debug** eller **bin\release** mappen, beroende på vilken typ av build.
12. Skapa en zip-fil **MyDotNetActivity.zip** som innehåller alla binärfiler i den \<projektmapp, till exempel\>\bin\Debug mapp. Inkludera den **MyDotNetActivity.pdb** filen så att du får ytterligare information, till exempel ett radnummer i källkoden som har orsakat problemet om det uppstod ett fel.

    > [!IMPORTANT]
    > Alla filer i zip-filen för den anpassade aktiviteten måste vara på den **översta nivån** utan undermappar.

    ![Binär utdatafiler](./media/data-factory-use-custom-activities/Binaries.png)
14. Skapa en blobbehållare med namnet **customactivitycontainer** om den inte redan finns.
15. Ladda upp MyDotNetActivity.zip som en blob till customactivitycontainer i en **allmänna** Azure blob storage (inte frekvent/lågfrekvent – Blob storage) som refereras av AzureStorageLinkedService.

> [!IMPORTANT]
> Om du lägga till det här projektet för .NET-aktivitet i en lösning i Visual Studio som innehåller en Data Factory-projektet och Lägg till en referens till projekt för .NET-aktivitet från programmet Data Factory-projektet, behöver du inte utföra de två sista stegen för att manuellt skapa ZIP-filen filen och överföra den till allmänna Azure blob storage. När du publicerar Data Factory-entiteter med hjälp av Visual Studio, utförs automatiskt de här stegen av publiceringsprocessen. Mer information finns i [Data Factory-projekt i Visual Studio](#data-factory-project-in-visual-studio) avsnittet.

## <a name="create-a-pipeline-with-custom-activity"></a>Skapa en pipeline med en anpassad aktivitet
Du har skapat en anpassad aktivitet och överfört zip-filen med binärfiler till en blobbehållare i en **allmänna** Azure Storage-konto. I det här avsnittet skapar du en Azure-datafabrik med en pipeline som använder den anpassade aktiviteten.

Den inkommande datauppsättningen för den anpassade aktiviteten representerar BLOB-objekt (filer) i mappen customactivityinput i adftutorial-behållaren i blob storage. Datauppsättningen för utdata för aktiviteten representerar utdata blobbar i mappen customactivityoutput i adftutorial-behållaren i blob storage.

Skapa **fil.txt** fil med följande innehåll och överför den till **customactivityinput** mappen för den **adftutorial** behållare. Skapa behållaren adftutorial om den inte redan finns.

```
test custom activity Microsoft test custom activity Microsoft
```

Indatamappen motsvarar en sektor i Azure Data Factory även om mappen har två eller flera filer. När varje sektor bearbetas av pipelinen, upprepas den anpassade aktiviteten över alla blobbar i Indatamappen för den sektorn.

Du ser en utdatafil med i mappen adftutorial\customactivityoutput med en eller flera rader (samma som antal blobbar i Indatamappen):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Här följer de steg du utför i det här avsnittet:

1. Skapa en **datafabrik**.
2. Skapa **länkade tjänster** för Azure Batch-pool med virtuella datorer på vilken den anpassade aktiviteten körs och Azure Storage som innehåller indata/utdata-BLOB.
3. Skapa indata och utdata **datauppsättningar** som representerar indata och utdata för den anpassade aktiviteten.
4. Skapa en **pipeline** som använder den anpassade aktiviteten.

> [!NOTE]
> Skapa den **fil.txt** och överföra den till en blob-behållare, om du inte redan gjort det. Se anvisningarna i föregående avsnitt.

### <a name="step-1-create-the-data-factory"></a>Steg 1: Skapa data factory
1. När du loggar in på Azure Portal, gör du följande:
   1. Klicka på **skapa en resurs** på den vänstra menyn.
   2. Klicka på **Data och analys** i den **New** bladet.
   3. Klicka på **Data Factory** på bladet **Dataanalys**.

    ![Meny för ny Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. I den **ny datafabrik** bladet ange **CustomActivityFactory** för namnet. Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande fel: **Datafabriksnamnet ”CustomActivityFactory” är inte tillgänglig**, ändra namnet på datafabriken (till exempel **yournameCustomActivityFactory**) och försöker skapa igen.

    ![Nytt Azure Data Factory-blad](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Klicka på **RESURSGRUPPENS namn**, och välj en befintlig resursgrupp eller skapa en resursgrupp.
4. Kontrollera att du använder rätt **prenumeration** och **region** där du vill att datafabriken ska skapas.
5. Klicka på **Skapa** på bladet **Ny datafabrik**.
6. Du ser att datafabriken skapas på den **instrumentpanelen** i Azure Portal.
7. När datafabriken har skapats, visas Data Factory-blad som visar innehållet i datafabriken.

    ![Bladet Datafabrik](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Steg 2: Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. I det här steget länkar du ditt Azure Storage-konto och Azure Batch-konto till datafabriken.

#### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
1. Klicka på den **författare och distribuera** panel på den **DATA FACTORY** bladet för **CustomActivityFactory**. Du ser Data Factory Editor.
2. Klicka på **Nytt datalager** på kommandoraden och välj **Azure storage**. Du bör se JSON-skriptet för att skapa en länkad Azure-lagringstjänst i redigeraren.

    ![Nytt datalager - Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Ersätt `<accountname>` med namnet på ditt Azure storage-konto och `<accountkey>` med åtkomstnyckeln för Azure storage-kontot. Information om hur du hämtar din lagringsåtkomstnyckel finns i [Visa, kopiera och återskapa lagringsåtkomstnycklar](../../storage/common/storage-account-manage.md#access-keys).

    ![Azure Storage gillade service](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Klicka på **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

#### <a name="create-azure-batch-linked-service"></a>Skapa Azure Batch-länkad tjänst
1. I Data Factory-redigeraren, klicka på **... Mer** på kommandofältet klickar du på **ny beräkning**, och välj sedan **Azure Batch** på menyn.

    ![Ny beräkning – Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Gör följande ändringar i JSON-skriptet:

   1. Ange Azure Batch-kontonamnet för den **accountName** egenskapen. Den **URL** från den **Azure Batch-kontobladet** finns i följande format: `http://accountname.region.batch.azure.com`. För den **batchUri** egenskap i JSON som du vill ta bort `accountname.` från URL: en och använder den `accountname` för den `accountName` JSON-egenskap.
   2. Ange nyckel för Azure Batch-konto för den **accessKey** egenskapen.
   3. Ange namnet på poolen som du har skapat som en del av förutsättningarna för den **poolName** egenskapen. Du kan även ange ID för poolen i stället för namnet på poolen.
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

       För den **poolName** egenskapen, du kan även ange ID för poolen i stället för namnet på poolen.

### <a name="step-3-create-datasets"></a>Steg 3: Skapa datauppsättningar
I det här steget skapar du datauppsättningar som representerar indata och utdata.

#### <a name="create-input-dataset"></a>Skapa indatauppsättning
1. I **redigeringsprogrammet** för Data Factory klickar du på **... Mer** på kommandofältet klickar du på **ny datauppsättning**, och välj sedan **Azure Blob storage** från den nedrullningsbara menyn.
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment:

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

   Du kan skapa en pipeline senare i den här genomgången med starttid: 2016-11-16T00:00:00Z-och Sluttid: 2016-11-16T05:00:00Z. Den är schemalagd att producera data per timme, så att det finns fem indata/utdata-segment (mellan **00**: 00:00 -> **05**: 00:00).

   Den **frekvens** och **intervall** för indatauppsättningen är inställd på **timme** och **1**, vilket innebär att indatasektorn är tillgänglig per timme. I det här exemplet är det samma fil (fil.txt) i intputfolder.

   Här följer starttider för varje sektor som representeras av SliceStart systemvariabeln i ovanstående JSON-kodfragmentet.
3. Klicka på **distribuera** i verktygsfältet för att skapa och distribuera den **InputDataset**. Kontrollera att du ser meddelandet **TABELLEN HAR SKAPATS** i namnlisten i redigeraren.

#### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
1. I den **Data Factory-redigeraren**, klickar du på **... Mer** på kommandofältet klickar du på **ny datauppsättning**, och välj sedan **Azure Blob storage**.
2. Ersätt JSON-skriptet i den högra rutan med följande JSON-skriptet:

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

     Platsen är **adftutorial/customactivityoutput/** och utdata-filnamn är åååå-MM-dd-HH.txt där åååå-MM-dd-HH är år, månad, datum och timme för den sektor som genereras. Se [Utvecklarreferens] [ adf-developer-reference] information.

    En blob/utdatafil genereras för varje indatasektorn. Här är hur en utdatafil har namnet för varje sektor. Alla utdatafiler som genereras i en utdatamapp: **adftutorial\customactivityoutput**.

   | Sektor | Starttid | Utdatafil |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Kom ihåg att alla filer i en Indatamappen är en del av ett segment med starttider som nämns ovan. När den här sektor bearbetas, den anpassade aktiviteten söker igenom varje fil och skapar en rad i filen med antalet förekomster av sökterm (”Microsoft”). Om det finns tre filer i inputfolder, finns det tre raderna i utdatafil för varje sektor som per timme: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt, etc.
3. Att distribuera den **OutputDataset**, klickar du på **distribuera** i kommandofältet.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Skapa och köra en pipeline som använder den anpassade aktiviteten
1. I Data Factory-redigeraren, klicka på **... Mer**, och välj sedan **ny pipeline** i kommandofältet.
2. Ersätt JSON i den högra rutan med följande JSON-skriptet:

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

   * **Samtidighet** är inställd på **2** så att två segment bearbetas parallellt med 2 virtuella datorer i Azure Batch-pool.
   * Det finns en aktivitet i aktivitetsavsnittet och är av typen: **DotNetActivity**.
   * **AssemblyName** anges till namnet på den DLL-filen: **MyDotnetActivity.dll**.
   * **EntryPoint** är inställd på **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** är inställd på **AzureStorageLinkedService** som pekar på blob-lagringen som innehåller anpassad aktivitet zip-filen. Om du använder olika Azure Storage-konton för indata/utdata-filer och anpassad aktivitet zip-filen, skapar du en annan länkad Azure Storage-tjänst. Den här artikeln förutsätter att du använder samma Azure Storage-kontot.
   * **PackageFile** är inställd på **customactivitycontainer/MyDotNetActivity.zip**. Det är i formatet: containerforthezip/nameofthezip.zip.
   * Den anpassade aktiviteten tar **InputDataset** som indata och **OutputDataset** som utdata.
   * LinkedServiceName-egenskapen för den anpassade aktiviteten pekar på den **AzureBatchLinkedService**, som talar om för Azure Data Factory som den anpassade aktiviteten ska köras på virtuella datorer i Azure Batch.
   * **isPaused** är inställd på **FALSKT** som standard. Pipelinen körs direkt i det här exemplet eftersom sektorer starta tidigare. Du kan ange den här egenskapen till true för att pausa pipelinen och Återställ den till false om du vill starta om.
   * Den **starta** tid och **slutet** tiderna är **fem** timmar från varandra och segment produceras per timme, så att fem sektorer som produceras av pipelinen.
3. Om du vill distribuera pipelinen klickar du på **distribuera** i kommandofältet.

### <a name="monitor-the-pipeline"></a>Övervaka pipeline
1. Data Factory-bladet i Azure-portalen klickar du på **Diagram**.

    ![Ikonen Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Klicka på OutputDataset i diagramvyn.

    ![Diagramvy](./media/data-factory-use-custom-activities/diagram.png)
3. Du bör se att fem utdatasegment är i tillståndet klar. Om de inte är i tillståndet klar, inte har de har skapats ännu.

   ![Utdatasegment](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Kontrollera att utdata genereras i blob storage i den **adftutorial** behållare.

   ![utdata från anpassad aktivitet][image-data-factory-output-from-custom-activity]
5. Om du öppnar filen, bör du se utdata som liknar följande utdata:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Använd den [Azure-portalen] [ azure-preview-portal] eller Azure PowerShell-cmdletar för att övervaka data factory, pipelines och datauppsättningar. Du kan ta emot meddelanden från den **ActivityLogger** i koden för den anpassade aktiviteten i loggarna (särskilt user-0.log) som du kan hämta från portalen eller med cmdlet: ar.

   ![Hämta loggar från anpassad aktivitet][image-data-factory-download-logs-from-custom-activity]

Se [övervaka och hantera Pipelines](data-factory-monitor-manage-pipelines.md) detaljerade anvisningar för att övervaka datauppsättningar och pipeliner.

## <a name="data-factory-project-in-visual-studio"></a>Data Factory-projekt i Visual Studio
Du kan skapa och publicera Data Factory-entiteter med hjälp av Visual Studio istället för att använda Azure-portalen. För detaljerad information om hur du skapar och publicerar Data Factory-entiteter med hjälp av Visual Studio, finns i [skapa din första pipeline med Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) och [kopiera data från Azure-Blob till Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) artiklar.

Om du skapar Data Factory-projekt i Visual Studio gör du följande åtgärder:

1. Lägg till Data Factory-projekt i Visual Studio-lösningen som innehåller anpassad aktivitet projektet.
2. Lägg till en referens till projekt för .NET-aktivitet från Data Factory-projektet. Högerklicka på Data Factory-projekt, peka på **Lägg till**, och klicka sedan på **referens**.
3. I den **Lägg till referens** dialogrutan den **MyDotNetActivity** projektet och klicka på **OK**.
4. Skapa och publicera lösningen.

    > [!IMPORTANT]
    > När du publicerar Data Factory-entiteter, en zip-fil skapas automatiskt åt dig och har överförts till blob-behållaren: customactivitycontainer. Om blob-behållaren inte finns skapas den automatiskt för.

## <a name="data-factory-and-batch-integration"></a>Data Factory och Batch-integrering
Data Factory-tjänsten skapar ett jobb i Azure Batch med namnet: **adf poolname: jobb-xxx**. Klicka på **jobb** menyn till vänster.

![Azure Data Factory - Batch-jobb](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

En uppgift skapas för varje aktivitet kör för en sektor. Om det finns fem segment som är redo att bearbetas, skapas fem uppgifter i det här jobbet. Om det finns flera beräkningsnoder i Batch-pool, kan två eller flera segment köras parallellt. Du kan också ha fler än en sektor som körs på samma beräkning om de maximala aktiviteterna per beräkningsnod är > 1.

![Azure Data Factory - uppgifter för Batch-jobb](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Följande diagram illustrerar förhållandet mellan Azure Data Factory och Batch-aktiviteter.

![Data Factory och Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Felsöka fel
Felsökning består av några grundläggande metoder:

1. Om du ser följande fel kan använder du en frekvent/lågfrekvent – blob storage i stället för med ett allmänt Azure blob storage. Ladda upp zip-filen till en **Allmänt Azure Storage-konto**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Om du ser följande fel kan du bekräfta att namnet på klassen i filen CS matchar det namn du angav för den **EntryPoint** egenskap i JSON-pipelinen. I den här genomgången är namnet på klassen: MyDotNetActivity och EntryPoint i JSON är: MyDotNetActivityNS.**MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Om namnen stämmer överens, bekräfta att alla binärfiler finns i den **rotmappen** i zip-filen. När du öppnar zip-filen, det vill säga bör du se alla filer i rotmappen, inte i alla undermappar.
3. Om indatasektorn inte har angetts **redo**, bekräfta att mappstrukturen för indata är korrekt och **fil.txt** finns i indatamapparna.
3. I den **kör** -metoden för din anpassade aktivitets den **IActivityLogger** objekt att logga information som hjälper dig att felsöka problem. De loggade meddelandena som visas i användarens loggfiler (en eller flera filer med namnen: user-0.log, user-1.log, user-2.log osv.).

   I den **OutputDataset** bladet klickar du på sektorn att se den **DATASEKTOR** bladet för den sektorn. Du ser **aktivitetskörningar** för den sektorn. Du bör se en aktivitetskörning för sektorn. Du kan starta en annan aktivitet som kör för samma sektorn om du klickar på Kör i kommandofältet.

   När du klickar på den aktivitet som körs, visas den **AKTIVITETSKÖRNINGSINFORMATION** blad med en lista med loggfiler. Du ser loggade meddelanden i filen user_0.log. När ett fel inträffar, se tre aktivitetskörningar eftersom antalet återförsök är inställd på 3 i pipeline/JSON-aktiviteten. När du klickar på kör aktiviteten, visas de loggfiler som du kan granska för att felsöka problemet.

   I listan över loggfiler, klickar du på den **user-0.log**. I den högra panelen är resultatet av att använda den **IActivityLogger.Write** metod. Om du inte ser alla meddelanden, kontrollera om du har fler loggfiler med namnet: user_1.log, user_2.log osv. I annat fall ha koden misslyckats efter senaste loggat meddelande.

   Du kan också kontrollera **system-0.log** för alla system felmeddelanden och undantag.
4. Inkludera den **PDB** filen i zip-filen så att felinformationen innehåller information som t.ex **anropsstacken** när ett fel uppstår.
5. Alla filer i zip-filen för den anpassade aktiviteten måste vara på den **översta nivån** utan undermappar.
6. Se till att den **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip) och **packageLinkedService** (bör peka på den **allmänna**Azure blob storage som innehåller zip-filen) är inställda på rätt värden.
7. Om du har korrigerat ett fel och vill bearbeta sektorn på nytt, högerklickar du på sektorn i **OutputDataset**-bladet och klickar på **Kör**.
8. Om du ser följande fel kan använder du Azure Storage-paketet med version > 4.3.0. Startprogram för data Factory-tjänsten krävs 4.3 av WindowsAzure.Storage. Se [Appdomain isolering](#appdomain-isolation) avsnittet för arbete-runt om du måste använda den senare versionen av Azure Storage-sammansättningen.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Om du kan använda 4.3.0 versionen av Azure Storage-paketet, ta bort befintliga referensen till Azure Storage-paketet med version > 4.3.0. Kör sedan följande kommando från NuGet Package Manager-konsolen.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Bygga projektet. Ta bort Azure.Storage sammanställningen av version > 4.3.0 från mappen bin\Debug. Skapa en zip-fil med binärfiler och PDB-filen. Vill du ersätta den gamla zipfilen med den här blobbehållaren (customactivitycontainer). Kör sektorer som inte (högerklickar du på sektorn och klicka på Kör).
8. Den anpassade aktiviteten använder inte den **app.config** filen från paketet. Därför, om din kod läser några anslutningssträngar från konfigurationsfilen, det fungerar inte vid körning. Den bästa metoden när du använder Azure Batch är att förvara eventuella hemligheter i ett **Azure KeyVault**, använda certifikatbaserad tjänstens huvudnamn för att skydda den **keyvault**, samt distribuera certifikatet till Azure Batch pool. Den anpassade .NET-aktiviteten kan sedan komma åt hemligheter i KeyVault vid körning. Den här lösningen är en allmän lösning som kan skalas till alla typer av hemligheter, inte bara anslutningssträngar.

   Det finns en enklare lösning (men inte bästa praxis): du kan skapa en **länkad Azure SQL-tjänst** med inställningar för anslutningssträngen, skapa en datauppsättning som använder den länkade tjänsten och länka datauppsättningen som en dummy datauppsättningen för indata till den Anpassad .NET-aktivitet. Du kan sedan komma åt den länkade tjänsten anslutningssträng i koden anpassad aktivitet.

## <a name="update-custom-activity"></a>Uppdatera anpassad aktivitet
Om du uppdaterar koden för den anpassade aktiviteten, skapa och ladda upp zip-filen som innehåller nya binärfiler till blob storage.

## <a name="appdomain-isolation"></a>AppDomain isolering
Se [mellan AppDomain exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) som visar hur du skapar en anpassad aktivitet som inte är begränsad till sammansättningen-versioner som används av Data Factory-startprogrammet (exempel: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.).

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

Det finns två utökade egenskaper i det här exemplet: **SliceStart** och **DataFactoryName**. Värdet för SliceStart baseras på systemvariabeln SliceStart. Se [systemvariabler](data-factory-functions-variables.md) en lista över systemvariabler som stöds. Värdet för DataFactoryName är hårdkodad att CustomActivityFactory.

Åtkomst till dessa utökade egenskaper i den **kör** metod, Använd-koden som liknar följande kod:

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

## <a name="auto-scaling-of-azure-batch"></a>Automatisk skalning i Azure Batch
Du kan också skapa ett Azure Batch-pool med **Autoskala** funktionen. Du kan till exempel skapa en azure batch-pool med 0 dedikerade virtuella datorer och en formel för automatisk skalning baserat på antalet väntande aktiviteter.

Exemplet formeln här uppnår på följande: När poolen skapas, börjar det med 1 virtuell dator. $PendingTasks mått definierar antalet uppgifter i körs + aktiv (köad) tillstånd.  Formeln hittar det genomsnittliga antalet väntande aktiviteter de senaste 180 sekunderna och anger TargetDedicated därefter. Det innebär att TargetDedicated aldrig är mer omfattande än 25 virtuella datorer. Så när nya aktiviteter skickas pool växer automatiskt och som aktiviteterna slutförs kan virtuella datorer blir kostnadsfria en i taget och autoskalning minskar storleken på de virtuella datorerna. startingNumberOfVMs och maxNumberofVMs kan justeras efter dina behov.

Formel för automatisk skalning:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Se [skala beräkningsnoder automatiskt i en Azure Batch-pool](../../batch/batch-automatic-scaling.md) mer information.

Om poolen använder standard [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), Batch-tjänsten kan ta 15-30 minuter att förbereda den virtuella datorn innan du kör den anpassade aktiviteten.  Om poolen använder en annan autoScaleEvaluationInterval, kan Batch-tjänsten ta autoScaleEvaluationInterval + 10: e minut.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Skapa en anpassad aktivitet med hjälp av .NET SDK
I den här genomgången i den här artikeln skapar du en datafabrik med en pipeline som använder den anpassade aktiviteten med hjälp av Azure portal. Följande kod visar hur du skapar data factory med hjälp av .NET SDK i stället. Du hittar mer information om hur du använder SDK för att programmässigt skapa pipelines i den [skapa en pipeline med en Kopieringsaktivitet med hjälp av .NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md) artikeln.

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
Den [Azure Data Factory - lokal miljö](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) exemplet på GitHub innehåller ett verktyg som hjälper dig att felsöka anpassad .NET-aktiviteter i Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Anpassade aktiviteter för exemplet på GitHub
| Exempel | Vilka anpassade aktiviteten ska hantera |
| --- | --- |
| [Data om HTTP-Installationshämtaren](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Hämtar data från en HTTP-slutpunkt till Azure Blob Storage med anpassad C#-aktivitet i Data Factory. |
| [Exempel för Twitter-Attitydanalys](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Anropar en Azure Machine Learning studio-modell och gör attitydanalys, bedömning, förutsägelse osv. |
| [Köra R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Anropar R-skriptet genom att köra RScript.exe i ditt HDInsight-kluster som redan finns R har installerats på datorn. |
| [Mellan AppDomain .NET-aktivitet](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Använder olika paketversionerna från de som används av Data Factory-startprogram |
| [Ombearbeta en modell i Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Ombearbeta en modell i Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
