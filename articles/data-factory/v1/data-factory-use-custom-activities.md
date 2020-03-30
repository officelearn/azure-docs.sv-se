---
title: Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)
description: Lär dig hur du skapar anpassade aktiviteter och använder dem i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 54cb06f1c77ab68818d8531b57d6eb936deda8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265732"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-use-custom-activities.md)
> * [Version 2 (aktuell version)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Anpassade aktiviteter i V2](../transform-data-using-dotnet-custom-activity.md).

Det finns två typer av aktiviteter som du kan använda i en Azure Data Factory-pipeline.

- [Dataflyttningsaktiviteter](data-factory-data-movement-activities.md) om du vill flytta data mellan [käll- och sinkdatalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Dataomvandlingsaktiviteter](data-factory-data-transformation-activities.md) för att omvandla data med hjälp av beräkningstjänster som Azure HDInsight, Azure Batch och Azure Machine Learning.

Om du vill flytta data till/från ett datalager som Data Factory inte stöder skapar du en **anpassad aktivitet** med din egen datarörelselogik och använder aktiviteten i en pipeline. På samma sätt, för att omvandla/bearbeta data på ett sätt som inte stöds av Data Factory, skapa en anpassad aktivitet med din egen dataomvandlingslogik och använda aktiviteten i en pipeline.

Du kan konfigurera en anpassad aktivitet så att den körs på en **Azure Batch-pool** med virtuella datorer. När du använder Azure Batch kan du bara använda en befintlig Azure Batch-pool.

Följande genomgång innehåller steg-för-steg-instruktioner för att skapa en anpassad .NET-aktivitet och använda den anpassade aktiviteten i en pipeline. Genomgången använder en **Azure** Batch-länkad tjänst.

> [!IMPORTANT]
> - Det går inte att använda en datahanteringsgateway från en anpassad aktivitet för att komma åt lokala datakällor. För närvarande stöder [Data Management Gateway](data-factory-data-management-gateway.md) endast kopieringsaktiviteten och den lagrade proceduraktiviteten i Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Genomgång: skapa en anpassad aktivitet
### <a name="prerequisites"></a>Krav
* Visual Studio 2012/2013/2015/2017
* Ladda ned och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Förutsättningar för Azure Batch
I genomgången kör du dina anpassade .NET-aktiviteter med Azure Batch som en beräkningsresurs. **Azure Batch** är en plattformstjänst för effektiv körning av storskaliga parallella program och HPC-program (databehandling med höga prestanda) i molnet. Azure Batch schemalägger beräkningsintensivt arbete för att köras på en hanterad **samling virtuella datorer**och kan automatiskt skala beräkningsresurser för att uppfylla behoven för dina jobb. Se azure [batch basics-artikeln][batch-technical-overview] för en detaljerad översikt över Azure Batch-tjänsten.

Skapa ett Azure Batch-konto med en pool med virtuella datorer för självstudiekursen. Här är stegen:

1. Skapa ett **Azure Batch-konto** med [Azure-portalen](https://portal.azure.com). Se [Skapa och hantera en artikel i Azure Batch-konto][batch-create-account] för instruktioner.
2. Anteckna azure batch-kontonamn, kontonyckel, URI och poolnamn. Du behöver dem för att skapa en Azure Batch-länkad tjänst.
    1. På startsidan för Azure Batch-konto **URL** visas en URL `https://myaccount.westus.batch.azure.com`i följande format: . I det här exemplet är **mykonto** namnet på Azure Batch-kontot. URI som du använder i den länkade tjänstdefinitionen är webbadressen utan namnet på kontot. Till exempel: `https://<region>.batch.azure.com`.
    2. Klicka på **Tangenter** på den vänstra menyn och kopiera **PRIMÄR ÅTKOMSTNYCKEL**.
    3. Om du vill använda en befintlig pool klickar du på **Pooler** på menyn och noterar poolens **ID.** Om du inte har en befintlig pool går du vidare till nästa steg.
2. Skapa en **Azure Batch-pool**.

   1. Klicka på **Bläddra** i den vänstra menyn i [Azure-portalen](https://portal.azure.com)och klicka på **Batchkonton**.
   2. Välj ditt Azure Batch-konto för att öppna **bladet Batch-konto.**
   3. Klicka på panelen **Pooler.**
   4. Klicka på Lägg till i **verktygsfältet** i bladet Pooler om du vill lägga till en pool.
      1. Ange ett ID för poolen (pool-ID). Observera **poolens ID;** du behöver det när du skapar Data Factory-lösningen.
      2. Ange **Windows Server 2012 R2** för inställningen Operativsystemfamilj.
      3. Välj en **nodprisnivå**.
      4. Ange **2** som värde för inställningen **Måldedikerad.**
      5. Ange **2** som värde för inställningen **Max aktiviteter per nod.**
   5. Klicka på **OK** för att skapa poolen.
   6. Anteckna **poolens ID.**

### <a name="high-level-steps"></a>Övergripande steg
Här är de två högnivåstegen som du utför som en del av den här genomgången:

1. Skapa en anpassad aktivitet som innehåller enkel dataomvandlings-/bearbetningslogik.
2. Skapa en Azure-datafabrik med en pipeline som använder den anpassade aktiviteten.

### <a name="create-a-custom-activity"></a>Skapa en anpassad aktivitet
Skapa ett .NET-anpassat aktivitet genom att skapa ett **.NET-klassbiblioteksprojekt** med en klass som implementerar gränssnittet **IDotNetActivity.** Det här gränssnittet har bara en metod: [Kör](https://msdn.microsoft.com/library/azure/mt603945.aspx) och dess signatur är:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

Metoden tar fyra parametrar:

- **linkedServices**. Den här egenskapen är en uppräkningsbar lista över datalagerlänkade tjänster som refereras av datauppsättningar för in- och utdata för aktiviteten.
- **datauppsättningar**. Den här egenskapen är en uppräkningsbar lista över indata-/utdatauppsättningar för aktiviteten. Du kan använda den här parametern för att hämta de platser och scheman som definieras av indata- och utdatauppsättningar.
- **aktivitet**. Den här egenskapen representerar den aktuella aktiviteten. Den kan användas för att komma åt utökade egenskaper som är associerade med den anpassade aktiviteten. Mer information finns i [Utökade accessegenskaper.](#access-extended-properties)
- **logger**. Med det här objektet kan du skriva felsökningskommentarer som visas i användarloggen för pipelinen.

Metoden returnerar en ordlista som kan användas för att kedja ihop anpassade aktiviteter i framtiden. Den här funktionen har inte implementerats ännu, så returnera en tom ordlista från metoden.

### <a name="procedure"></a>Procedur
1. Skapa ett **.NET-klassbiblioteksprojekt.**
   <ol type="a">
     <li>Starta Visual Studio.</li>
     <li>Klicka på <b>Arkiv</b>, peka på <b>Nytt</b> och klicka på <b>Projekt</b>.</li>
     <li>Expandera <b>Mallar</b> och välj <b>Visual C#</b>. I den här genomgången använder du C#, men du kan använda vilket .NET-språk som helst för att utveckla den anpassade aktiviteten.</li>
     <li>Välj <b>Klassbibliotek</b> i listan över projekttyper till höger. Välj <b>Klassbibliotek (.NET Framework) i</b> Visual Studio </li>
     <li>Ange <b>MyDotNetActivity</b> för <b>namnet</b>.</li>
     <li>Välj <b>C:\ADFGetStarted</b> för <b>platsen</b>.</li>
     <li>Klicka på <b>OK</b> för att skapa projektet.</li>
   </ol>

2. Klicka på **Verktyg**, peka på **NuGet Package Manager**och klicka på Package Manager **Console**.

3. I Package Manager-konsolen kör du följande kommando för att importera **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importera **Azure Storage** NuGet-paketet till projektet.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Data Factory-tjänststartprogrammet kräver 4.3-versionen av WindowsAzure.Storage. Om du lägger till en referens till en senare version av Azure Storage-sammansättning i ditt anpassade aktivitetsprojekt visas ett fel när aktiviteten körs. LÃ¶s problemet genom att lÃ¶sa avsnittet [Appdomain isolation.](#appdomain-isolation)
5. Lägg till följande **med hjälp av** satser i källfilen i projektet.

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
6. Ändra namnet på **namnområdet** till **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Ändra namnet på klassen till **MyDotNetActivity** och härleda den från **gränssnittet IDotNetActivity** enligt följande kodavsnitt:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementera (Lägg till) **kör-metoden** för **IDotNetActivity-gränssnittet** i klassen **MyDotNetActivity** och kopiera följande exempelkod till metoden.

    I följande exempel räknas antalet förekomster av söktermen ("Microsoft") i varje blob som är associerad med ett datasegment.

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
9. Lägg till följande hjälpmetoder:

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

    GetFolderPath-metoden returnerar sökvägen till mappen som datauppsättningen pekar på och metoden GetFileName returnerar namnet på blob/filen som datauppsättningen pekar på. Om du har folderPath definierar med hjälp av variabler som {Year}, {Month}, {Day} etc., returnerar metoden strängen som den är utan att ersätta dem med körningsvärden. Se [Avsnittet Utökade egenskaper](#access-extended-properties) i Access för mer information om hur du kommer åt SegmentStart, SegmentEnd osv.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoden Beräkna beräknar antalet instanser av nyckelordet Microsoft i indatafilerna (blobbar i mappen). Söktermen ("Microsoft") är hårdkodad i koden.
10. Kompilera projektet. Klicka på **Bygg** på menyn och klicka på **Bygg lösning**.

    > [!IMPORTANT]
    > Ange 4.5.2-versionen av .NET Framework som målramverk för projektet: högerklicka på projektet och klicka på **Egenskaper** för att ange målramverket. Data Factory stöder inte anpassade aktiviteter som kompilerats mot .NET Framework-versioner senare än 4.5.2.

11. Starta **Utforskaren**och navigera till **mappen bin\debug** eller **bin\release** beroende på typen av version.
12. Skapa en zip-fil **MyDotNetActivity.zip** som innehåller \<alla\>binärfiler i projektmappen \bin\Debug-mappen. Inkludera filen **MyDotNetActivity.pdb** så att du får ytterligare information, till exempel radnummer i källkoden som orsakade problemet om det uppstod ett fel.

    > [!IMPORTANT]
    > Alla filer i zip-filen för den anpassade aktiviteten måste vara på den **översta nivån** utan undermappar.

    ![Binära utdatafiler](./media/data-factory-use-custom-activities/Binaries.png)
14. Skapa en blob-behållare med namnet **customactivitycontainer** om den inte redan finns.
15. Ladda upp MyDotNetActivity.zip som en blob till customactivitycontainer i en allmän Azure **blob-lagring** (inte frekvent/cool Blob-lagring) som refereras av AzureStorageLinkedService.

> [!IMPORTANT]
> Om du lägger till det här .NET-aktivitetsprojektet i en lösning i Visual Studio som innehåller ett Data Factory-projekt och lägger till en referens till .NET-aktivitetsprojekt från datafabriksprogrammet behöver du inte utföra de två sista stegen för att manuellt skapa zip-programmet och ladda upp den till azure-blob-lagring av allmänt syfte. När du publicerar Data Factory-entiteter med Visual Studio utförs dessa steg automatiskt av publiceringsprocessen. Mer information finns [i Data Factory-projektet i](#data-factory-project-in-visual-studio) avsnittet Visual Studio.

## <a name="create-a-pipeline-with-custom-activity"></a>Skapa en pipeline med anpassad aktivitet
Du har skapat en anpassad aktivitet och överfört **zip-filen** med binärfiler till en blob-behållare i ett allmänt Azure Storage-konto. I det här avsnittet skapar du en Azure-datafabrik med en pipeline som använder den anpassade aktiviteten.

Indatauppsättningen för den anpassade aktiviteten representerar blobbar (filer) i mappen customactivityinput i adftutorial-behållaren i blob-lagringen. Utdatauppsättningen för aktiviteten representerar utdatablobar i mappen customactivityoutput i adftutorial-behållaren i blob-lagringen.

Skapa **filen file.txt** med följande innehåll och ladda upp den till **customactivityinput-mappen** i **adftutorial-behållaren.** Skapa adftutorial-behållaren om den inte redan finns.

```
test custom activity Microsoft test custom activity Microsoft
```

Indatamappen motsvarar ett segment i Azure Data Factory även om mappen har två eller flera filer. När varje segment bearbetas av pipelinen itererar den anpassade aktiviteten igenom alla blobbar i indatamappen för det segmentet.

Du ser en utdatafil med i mappen adftutorial\customactivityoutput med en eller flera rader (samma som antalet blobbar i indatamappen):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Här är stegen du utför i det här avsnittet:

1. Skapa en **datafabrik**.
2. Skapa **länkade tjänster** för Azure Batch-poolen med virtuella datorer där den anpassade aktiviteten körs och Azure Storage som innehåller indata-/utdatablobar.
3. Skapa indata- och **utdatauppsättningar** som representerar indata och utdata för den anpassade aktiviteten.
4. Skapa en **pipeline** som använder den anpassade aktiviteten.

> [!NOTE]
> Skapa **file.txt** och ladda upp den till en blob-behållare om du inte redan har gjort det. Se anvisningar i föregående avsnitt.

### <a name="step-1-create-the-data-factory"></a>Steg 1: Skapa datafabriken
1. När du har loggat in på Azure-portalen gör du följande:
   1. Klicka på **Skapa en resurs** på den vänstra menyn.
   2. Klicka på **Data + Analytics** i det **nya** bladet.
   3. Klicka på **Data Factory** på bladet **Dataanalys**.

      ![Ny Azure Data Factory-meny](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. I bladet **Nytt datafabrik** anger du **CustomActivityFactory** för namnet. Namnet på Azure Data Factory måste vara globalt unikt. Om felet visas: **Datafabriksnamnet "CustomActivityFactory" inte är tillgängligt**ändrar du namnet på datafabriken (till exempel ditt **namnCustomActivityFactory)** och försöker skapa igen.

    ![Nytt Azure Data Factory-blad](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Klicka på **RESURSGRUPPNAMN**och välj en befintlig resursgrupp eller skapa en resursgrupp.
4. Kontrollera att du använder rätt **prenumeration** och **region** där du vill att datafabriken ska skapas.
5. Klicka på **Skapa** på bladet **Ny datafabrik**.
6. Du ser datafabriken som skapas i **instrumentpanelen** i Azure-portalen.
7. När datafabriken har skapats visas bladet Data Factory, som visar innehållet i datafabriken.

    ![Bladet Datafabrik](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Steg 2: Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. I det här steget länkar du ditt Azure Storage-konto och Azure Batch-konto till din datafabrik.

#### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
1. Klicka på panelen **Författare och distribuera** på **datafabriken** för **CustomActivityFactory**. Du ser Data Factory Editor.
2. Klicka på **Nytt datalager** i kommandofältet och välj **Azure storage**. Du bör se JSON-skriptet för att skapa en länkad Azure-lagringstjänst i redigeraren.

    ![Nytt datalager - Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Ersätt `<accountname>` med namnet på ditt `<accountkey>` Azure-lagringskonto och med åtkomstnyckeln för Azure-lagringskontot. Mer information om hur du hämtar åtkomstnyckeln för lagring finns i [Hantera åtkomstnycklar för lagringskonto](../../storage/common/storage-account-keys-manage.md).

    ![Azure Storage-gillad tjänst](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Klicka på **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

#### <a name="create-azure-batch-linked-service"></a>Skapa azure batch-länkad tjänst
1. I Data Factory Editor klickar du på **... Mer** information om kommandofältet klickar du på **Ny beräkning**och väljer sedan **Azure Batch** på menyn.

    ![Ny beräkning - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Gör följande ändringar i JSON-skriptet:

   1. Ange Azure Batch-kontonamn för egenskapen **accountName.** **URL:en** från **Azure Batch-kontobladet** är `http://accountname.region.batch.azure.com`i följande format: . För egenskapen **batchUri** i JSON måste `accountname.` du ta bort `accountname` från `accountName` URL:en och använda egenskapen För JSON.
   2. Ange Azure Batch-kontonyckeln för egenskapen **accessKey.**
   3. Ange namnet på poolen som du skapade som en del av förutsättningarna för egenskapen **poolName.** Du kan också ange ID för poolen i stället för namnet på poolen.
   4. Ange Azure Batch URI för egenskapen **batchUri.** Exempel: `https://westus.batch.azure.com`.
   5. Ange **AzureStorageLinkedService** för egenskapen **linkedServiceName.**

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

       För egenskapen **poolName** kan du också ange ID för poolen i stället för namnet på poolen.

### <a name="step-3-create-datasets"></a>Steg 3: Skapa datauppsättningar
I det här steget skapar du datauppsättningar som representerar in- och utdata.

#### <a name="create-input-dataset"></a>Skapa indatauppsättning
1. I **Redigeraren** för Data Factory, klicka på **... Mer** information om kommandofältet klickar du på **Ny datauppsättning**och väljer sedan **Azure Blob-lagring** på den nedrullningsbara menyn.
2. Ersätt JSON i den högra rutan med följande JSON-utdrag:

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

   Du skapar en pipeline senare i den här genomgången med starttid: 2016-11-16T00:00:00Z och sluttid: 2016-11-16T05:00:00Z. Den är schemalagd att producera data per timme, så det **00**finns fem indata/utdatasegment (mellan 00:00:00 -> 05:00:00). **05**

   **Frekvensen** och **intervallet** för indatauppsättningen är inställt på **Timme** och **1**, vilket innebär att indatasegmentet är tillgängligt varje timme. I det här exemplet är det samma fil (file.txt) i intputfoldern.

   Här är starttiderna för varje segment, som representeras av Systemvariabeln SegmentStart i ovanstående JSON-utdrag.
3. Klicka på **Distribuera** i verktygsfältet om du vill skapa och distribuera **InputDataset**. Kontrollera att du ser meddelandet **TABELLEN HAR SKAPATS** i namnlisten i redigeraren.

#### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
1. I **Data Factory editor**, klicka på **... Mer** i kommandofältet klickar du på **Ny datauppsättning**och väljer sedan **Azure Blob storage**.
2. Ersätt JSON-skriptet i den högra rutan med följande JSON-skript:

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

     Utdataplatsen är **adftutorial/customactivityoutput/** och utgångsfilnamnet är yyyy-MM-dd-HH.txt där yyyy-MM-dd-HH är år, månad, datum och timme för det segment som produceras. Mer information finns i [Utvecklarreferens.][adf-developer-reference]

    En utdatablob/fil genereras för varje indatasegment. Så här namnges en utdatafil för varje segment. Alla utdatafiler genereras i en utdatamapp: **adftutorial\customactivityoutput**.

   | Skiva | Starttid | Utdatafil |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Kom ihåg att alla filer i en indatamapp är en del av ett segment med de starttider som nämns ovan. När det här segmentet bearbetas söker den anpassade aktiviteten igenom varje fil och skapar en rad i utdatafilen med antalet förekomster av söktermen ("Microsoft"). Om det finns tre filer i indatamappen finns det tre rader i utdatafilen för varje timsegment: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt, etc.
3. Om du vill distribuera **OutputDataset**klickar du på **Distribuera** i kommandofältet.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Skapa och köra en pipeline som använder den anpassade aktiviteten
1. I Data Factory Editor klickar du på **... Mer**och välj sedan **Ny pipeline** i kommandofältet.
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

   * **Samtidighet** är inställd på **2** så att två segment bearbetas parallellt av 2 virtuella datorer i Azure Batch-poolen.
   * Det finns en aktivitet i avsnittet aktiviteter och den är av typen **DotNetActivity**.
   * **AssemblyName** är inställt på namnet på DLL: **MyDotnetActivity.dll**.
   * **EntryPoint** är inställt på **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** är inställt på **AzureStorageLinkedService** som pekar på blob-lagringen som innehåller den anpassade zip-filen för aktivitet. Om du använder olika Azure Storage-konton för indata-/utdatafiler och zip-filen för anpassad aktivitet skapar du en annan Azure Storage-länkad tjänst. Den här artikeln förutsätter att du använder samma Azure Storage-konto.
   * **PackageFile** är inställd på **customactivitycontainer/MyDotNetActivity.zip**. Det är i formatet: containerforthezip/nameofthezip.zip.
   * Den anpassade aktiviteten tar **InputDataset** som input och **OutputDataset** som utdata.
   * Egenskapen linkedServiceName för den anpassade aktiviteten pekar på **AzureBatchLinkedService**, som talar om för Azure Data Factory att den anpassade aktiviteten måste köras på virtuella Azure Batch-datorer.
   * **egenskapen isPaused** är inställd på **false** som standard. Pipelinen körs omedelbart i det här exemplet eftersom segmenten börjar tidigare. Du kan ange den här egenskapen så att den är true så att den pausas och att den ska startas om igen.
   * **Starttiden** och **sluttiderna** är **fem** timmars mellanrum och segment produceras varje timme, så fem segment produceras av rörledningen.
3. Om du vill distribuera pipelinen klickar du på **Distribuera** i kommandofältet.

### <a name="monitor-the-pipeline"></a>Övervaka pipeline
1. Klicka på **Diagram**i bladet Data Factory i Azure-portalen .

    ![Ikonen Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Klicka nu på OutputDataset i diagramvyn.

    ![Diagramvy](./media/data-factory-use-custom-activities/diagram.png)
3. Du bör se att de fem utdatasegmenten är i läget Klar. Om de inte är i redo tillstånd, har de inte producerats ännu.

   ![Utdatasegment](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Kontrollera att utdatafilerna genereras i blob-lagringen i **adftutorial-behållaren.**

   ![utdata från anpassad aktivitet][image-data-factory-output-from-custom-activity]
5. Om du öppnar utdatafilen bör du se utdata som liknar följande utdata:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Använd [Azure-portalen][azure-preview-portal] eller Azure PowerShell-cmdlets för att övervaka dina datafabriker, pipelines och datauppsättningar. Du kan se meddelanden från **ActivityLogger** i koden för den anpassade aktiviteten i loggarna (särskilt användare-0.log) som du kan ladda ner från portalen eller använda cmdlets.

   ![ladda ner loggar från anpassad aktivitet][image-data-factory-download-logs-from-custom-activity]

Se [Övervaka och hantera pipelines](data-factory-monitor-manage-pipelines.md) för detaljerade steg för övervakning av datauppsättningar och pipelines.

## <a name="data-factory-project-in-visual-studio"></a>Data Factory-projektet i Visual Studio
Du kan skapa och publicera Data Factory-entiteter med hjälp av Visual Studio i stället för att använda Azure-portalen. Detaljerad information om hur du skapar och publicerar Data Factory-entiteter med hjälp av Visual Studio finns i [Skapa din första pipeline med Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) och Kopiera data från Azure [Blob till Azure SQL-artiklar.](data-factory-copy-activity-tutorial-using-visual-studio.md)

Gör följande ytterligare steg om du skapar Data Factory-projekt i Visual Studio:

1. Lägg till Data Factory-projektet i Visual Studio-lösningen som innehåller det anpassade aktivitetsprojektet.
2. Lägg till en referens till .NET-aktivitetsprojektet från datafabriksprojektet. Högerklicka på Projektet Data Factory, peka **på Lägg till**och klicka sedan på **Referens**.
3. Markera Projektet **MyDotNetActivity** i dialogrutan **Lägg till referens** och klicka på **OK**.
4. Skapa och publicera lösningen.

    > [!IMPORTANT]
    > När du publicerar Data Factory-entiteter skapas en zip-fil automatiskt åt dig och överförs till blob-behållaren: customactivitycontainer. Om blob-behållaren inte finns skapas den också automatiskt.

## <a name="data-factory-and-batch-integration"></a>Integrering av datafabrik och batch
Tjänsten Data Factory skapar ett jobb i Azure Batch med namnet: **adf-poolname: job-xxx**. Klicka på **Jobb** från den vänstra menyn.

![Azure Data Factory – batchjobb](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

En aktivitet skapas för varje aktivitetskörning i ett segment. Om det finns fem segment som är klara att bearbetas skapas fem aktiviteter i det här jobbet. Om det finns flera beräkningsnoder i batchpoolen kan två eller flera segment köras parallellt. Om den maximala uppgiften per beräkningsnod är inställd på > 1 kan du också ha mer än ett segment som körs på samma beräkning.

![Azure Data Factory - Batch-jobbuppgifter](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Följande diagram illustrerar förhållandet mellan Azure Data Factory och Batch-uppgifter.

![Data Factory & Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Felsöka fel
Felsökning består av några grundläggande tekniker:

1. Om du ser följande fel kan du använda en Hot/Cool blob-lagring i stället för att använda en allmän Azure-bloblagring. Ladda upp zip-filen till ett **allmänt Azure Storage-konto**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Om följande fel visas bekräftar du att namnet på klassen i CS-filen matchar det namn som du har angett för **egenskapen EntryPoint** i pipeline-JSON. I genomgången är namnet på klassen: MyDotNetActivity och EntryPoint i JSON är: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Om namnen matchar bekräftar du att alla binärfiler finns i zip-filens **rotmapp.** Det vill säga när du öppnar zip-filen bör du se alla filer i rotmappen, inte i några undermappar.
3. Om indatasegmentet inte är inställt **på Klar**bekräftar du att indatamappstrukturen är korrekt och **att filen.txt** finns i indatamapparna.
3. Använd **IActivityLogger-objektet** i metoden **Kör** för att logga information som hjälper dig att felsöka problem. De loggade meddelandena visas i användarloggfilerna (en eller flera filer med namnet: user-0.log, user-1.log, user-2.log, etc.).

   Klicka på segmentet i **bladet OutputDataset** för att se **dataskivabladet** för det segmentet. Du ser **aktivitetskörningar** för det segmentet. Du bör se en aktivitet köra för segmentet. Om du klickar på Kör i kommandofältet kan du starta en annan aktivitetskörning för samma segment.

   När du klickar på aktivitetskörningen visas bladet **AKTIVITETSKÖRNINGSINFORMATION** med en lista med loggfiler. Du ser loggade meddelanden i filen user_0.log. När ett fel inträffar visas tre aktivitetskörningar eftersom antalet försök på nytt är inställt på 3 i pipeline/aktivitets-JSON. När du klickar på aktivitetskörningen visas loggfilerna som du kan granska för att felsöka felet.

   Klicka på **användaren-0.log**i listan över loggfiler . I den högra panelen är resultatet av att använda **IActivityLogger.Write-metoden.** Om du inte ser alla meddelanden kontrollerar du om du har fler loggfiler med namnet: user_1.log, user_2.log etc. Annars kan koden ha misslyckats efter det senast loggade meddelandet.

   Kontrollera dessutom **system-0.log** för systemfelmeddelanden och undantag.
4. Inkludera **PDB-filen** i zip-filen så att felinformationen har information som **anropsstack** när ett fel inträffar.
5. Alla filer i zip-filen för den anpassade aktiviteten måste vara på den **översta nivån** utan undermappar.
6. Kontrollera att **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) och **packageLinkedService** (bör peka på den allmänna Azure-bloblagringen som innehåller **zip-filen)** är inställda på korrekta värden.
7. Om du har korrigerat ett fel och vill bearbeta sektorn på nytt, högerklickar du på sektorn i **OutputDataset**-bladet och klickar på **Kör**.
8. Om du ser följande fel använder du Azure Storage-paketet för version > 4.3.0. Data Factory-tjänststartprogrammet kräver 4.3-versionen av WindowsAzure.Storage. Se [Avsnittet Appdomain isolation](#appdomain-isolation) för en lösning om du måste använda den senare versionen av Azure Storage-sammansättning.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Om du kan använda 4.3.0-versionen av Azure Storage-paketet tar du bort den befintliga referensen till Azure Storage-paketet för version > 4.3.0. Kör sedan följande kommando från NuGet Package Manager Console.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Bygga projektet. Ta bort Azure.Storage-sammansättning av version > 4.3.0 från mappen bin\Debug. Skapa en zip-fil med binärfiler och PDB-filen. Ersätt den gamla zip-filen med den här i blob-behållaren (customactivitycontainer). Kör segmenten igen (högerklicka segment och klicka på Kör).
8. Den anpassade aktiviteten använder inte **filen app.config** från paketet. Om koden läser några anslutningssträngar från konfigurationsfilen fungerar den därför inte vid körning. Den bästa metoden när du använder Azure Batch är att hålla alla hemligheter i en **Azure KeyVault**, använda ett certifikatbaserat tjänsthuvudnamn för att skydda **keyvault**och distribuera certifikatet till Azure Batch-pool. Den anpassade .NET-aktiviteten kan sedan komma åt hemligheter i KeyVault vid körning. Denna lösning är en generisk lösning och kan skala till alla typer av hemliga, inte bara anslutningssträng.

   Det finns en enklare lösning (men inte en metod för bästa metod): du kan skapa en **Azure SQL-länkad tjänst** med anslutningsstränginställningar, skapa en datauppsättning som använder den länkade tjänsten och kedja datauppsättningen som en dummy indatauppsättning till den anpassade .NET-aktiviteten. Du kan sedan komma åt den länkade tjänstens anslutningssträng i den anpassade aktivitetskoden.

## <a name="update-custom-activity"></a>Uppdatera anpassad aktivitet
Om du uppdaterar koden för den anpassade aktiviteten skapar du den och överför zip-filen som innehåller nya binärfiler till blob-lagringen.

## <a name="appdomain-isolation"></a>Isolering av appdomäner
Se [Exempel på KorsAppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) som visar hur du skapar en anpassad aktivitet som inte är begränsad till monteringsversioner som används av datafabrikens startprogram (exempel: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.).

## <a name="access-extended-properties"></a>Utökade egenskaper för Access
Du kan deklarera utökade egenskaper i aktiviteten JSON enligt följande exempel:

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

I exemplet finns det två utökade egenskaper: **SegmentStart** och **DataFactoryName**. Värdet för SegmentStart baseras på systemvariabeln SegmentStart. Se [Systemvariabler](data-factory-functions-variables.md) för en lista över systemvariabler som stöds. Värdet för DataFactoryName är hårdkodat till CustomActivityFactory.

Om du vill komma **Execute** åt dessa utökade egenskaper i kör-metoden använder du kod som liknar följande kod:

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
Du kan också skapa en Azure Batch-pool med funktionen **för automatisk skalning.** Du kan till exempel skapa en azure batch-pool med 0 dedikerade virtuella datorer och en formel för automatisk skalning baserat på antalet väntande aktiviteter.

Exempelformeln här uppnår följande beteende: När poolen skapas börjar den med 1 virtuell dator. $PendingTasks mått definierar antalet aktiviteter i körning + aktivt tillstånd (i kö).  Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger TargetDedicated därefter. Det säkerställer att TargetDedicated aldrig går längre än 25 virtuella datorer. Så, när nya uppgifter skickas, pool växer automatiskt och när uppgifter slutförs, virtuella datorer blir gratis en efter en och den automatiska skalning krymper dessa virtuella datorer. startingNumberOfVMs och maxNumberofVMs kan justeras efter dina behov.

Formel för automatisk skalning:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Mer information finns i Beräkna noder för [automatisk skalar i en Azure Batch-pool.](../../batch/batch-automatic-scaling.md)

Om poolen använder standardvärdet [för automatisk skalningInvärdering](https://msdn.microsoft.com/library/azure/dn820173.aspx)kan batch-tjänsten ta 15-30 minuter att förbereda den virtuella datorn innan den anpassade aktiviteten körs.  Om poolen använder en annan automatisk skalningInvärderingInterval kan batchtjänsten ta autoScaleEvaluationInterval + 10 minuter.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Skapa en anpassad aktivitet med hjälp av .NET SDK
I genomgången i den här artikeln skapar du en datafabrik med en pipeline som använder den anpassade aktiviteten med hjälp av Azure-portalen. Följande kod visar hur du skapar datafabriken med hjälp av .NET SDK i stället. Du hittar mer information om hur du använder SDK för att programmässigt skapa pipelines i [skapa en pipeline med kopieringsaktivitet med hjälp av .NET API-artikeln.](data-factory-copy-activity-tutorial-using-dotnet-api.md)

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

## <a name="debug-custom-activity-in-visual-studio"></a>Felsök anpassad aktivitet i Visual Studio
[Exemplet Azure Data Factory – lokal miljö](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) på GitHub innehåller ett verktyg som gör att du kan felsöka anpassade .NET-aktiviteter i Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Exempel på anpassade aktiviteter på GitHub
| Exempel | Vad anpassad aktivitet gör |
| --- | --- |
| [HTTP-data hämtare](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Hämtar data från en HTTP-slutpunkt till Azure Blob Storage med anpassad C#-aktivitet i Data Factory. |
| [Exempel på sentimentanalys på Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Anropar en Azure Machine Learning studio-modell och gör sentimentanalys, bedömning, förutsägelse etc. |
| [Kör R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Anropar R-skript genom att köra RScript.exe på ditt HDInsight-kluster som redan har R Installerat på det. |
| [Cross AppDomain .NET-aktivitet](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Använder olika monteringsversioner från de som används av datafabrikens startikon |
| [Bearbeta om en modell i Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Bearbetar om en modell i Azure Analysis Services. |

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
