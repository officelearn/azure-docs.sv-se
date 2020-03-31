---
title: Komma igång med Azure Blob-lagring med Visual Studio (ASP.NET)
description: Komma igång med Azure Blob-lagring i ett ASP.NET projekt i Visual Studio, efter att ha anslutit till ett lagringskonto med hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6591cdb8f19484c87bb05f9007521adc34778f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298864"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Komma igång med Azure Blob storage och Visual Studio-anslutna tjänster (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob storage är en tjänst som lagrar ostrukturerade data i molnet som objekt eller blobbar. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring. Mer information om Blob-lagring finns i [Introduktion till Azure Blob storage](../storage/blobs/storage-blobs-introduction.md).

Den här självstudien visar hur du skriver ASP.NET kod för några vanliga scenarier som använder Blob-lagring. Scenarier inkluderar att skapa en blob-behållare och ladda upp, lista, hämta och ta bort blobbar.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Krav

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Skapa en MVC-styrenhet 

1. Högerklicka på **Styrenheter**i **Solution Explorer**.

2. Välj **Lägg till** > **styrenhet**på snabbmenyn .

    ![Skärmbild av Solution Explorer, med Lägg till och controller markerat](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. I dialogrutan **Lägg till byggnadsställning** väljer du **MVC 5 Controller - Tom**och väljer Lägg **till**.

    ![Skärmbild av dialogrutan Lägg till byggnadsställning](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. I dialogrutan **Lägg till styrenhet** namnger du styrenheten *BlobsController*och väljer **Lägg till**.

    ![Skärmbild av dialogrutan Lägg till handkontroll](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Lägg till `using` följande `BlobsController.cs` direktiv i filen:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Ansluta till ett lagringskonto och hämta en behållarreferens

En blob-behållare är en kapslad hierarki med blobbar och mappar. Resten av stegen i det här dokumentet kräver en referens till en blob-behållare, så att koden ska placeras i sin egen metod för återanvändning.

I följande steg skapas en metod för att ansluta till lagringskontot med hjälp av anslutningssträngen i **Web.config**. Stegen skapar också en referens till en behållare.  Inställningen för anslutningssträng i **Web.config** namnges med formatet `<storageaccountname>_AzureStorageConnectionString`. 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas **GetCloudBlobContainer** som returnerar en **CloudBlobContainer**.  Var noga `<storageaccountname>_AzureStorageConnectionString` med att ersätta med det faktiska namnet på nyckeln i **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Även om *test-blob-behållaren* inte finns ännu, skapar den här koden en referens till den. Detta för att behållaren ska `CreateIfNotExists` kunna skapas med den metod som visas i nästa steg.

## <a name="create-a-blob-container"></a>Skapa en blobcontainer

Följande steg illustrerar hur du skapar en blob-behållare:

1. Lägg till `CreateBlobContainer` en metod `ActionResult`som kallas som returnerar en .

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Hämta `CloudBlobContainer` ett objekt som representerar en referens till önskat blob-behållarnamn. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Anropa `CloudBlobContainer.CreateIfNotExists` metoden för att skapa behållaren om den ännu inte finns. Metoden `CloudBlobContainer.CreateIfNotExists` returnerar **true** om behållaren inte finns och har skapats. Annars returnerar metoden **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Uppdatera `ViewBag` med namnet på blob-behållaren.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Följande visar den `CreateBlobContainer` färdiga metoden:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. Högerklicka på mappen **Vyer** i **Solution Explorer.**

1. Om det inte finns en **Blobbar-mapp** skapar du en. Välj **Lägg till** > **ny mapp**på snabbmenyn . Ge den nya mappen *namn*. 
 
1. Expandera mappen **Vyer** i **Solution Explorer**och högerklicka på **Blobbar**.

1. Välj **Lägg till** > **vy**på snabbmenyn .

1. I dialogrutan **Lägg till vy** anger du **CreateBlobContainer** för visningsnamnet och väljer **Lägg till**.

1. Öppna `CreateBlobContainer.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Expandera mappen Delade **vyer** > **Shared** i Solution `_Layout.cshtml` **Explorer**och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Kör programmet och välj **Skapa Blob Container** om du vill se resultat som liknar följande skärmbild:
  
    ![Skärmbild av skapa blob-behållare](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Som tidigare nämnts returnerar `CloudBlobContainer.CreateIfNotExists` metoden bara **true** när behållaren inte finns och skapas. Om appen körs när behållaren finns returnerar metoden därför **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Ladda upp en blob till en blob-behållare

När [blob-behållaren skapas](#create-a-blob-container)överför du filer till den behållaren. Det här avsnittet går igenom att överföra en lokal fil till en blob-behållare. Stegen förutsätter att det finns en blob-behållare med namnet *test-blob-container*. 

1. Öppna filen `BlobsController.cs`.

1. Lägg till `UploadBlob` en metod som kallas som returnerar en sträng.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. I `UploadBlob` metoden hämtar `CloudBlobContainer` du ett objekt som representerar en referens till önskat blob-behållarnamn. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure storage stöder olika blobtyper. Den här självstudien använder blockblobar. Anropa `CloudBlobContainer.GetBlockBlobReference` metoden om du vill hämta en referens till en blockblob.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Blob-namnet är en del av webbadressen som används för att hämta en blob och kan vara vilken sträng som helst, inklusive namnet på filen.

1. När det finns en blob-referens kan du överföra alla dataströmmer `UploadFromStream` till den genom att anropa blob-referensobjektets metod. Metoden `UploadFromStream` skapar bloben om den inte finns, eller skriver över den om den finns. (Ändra * &lt;>till* en fullständigt kvalificerad sökväg till en fil som ska överföras.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Följande visar den `UploadBlob` färdiga metoden (med en fullständigt kvalificerad sökväg för filen som ska överföras):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. Expandera mappen Delade **vyer** > **Shared** i Solution `_Layout.cshtml` **Explorer**och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **Ladda upp blob**.  Ordet *framgång!* ska visas.
    
    ![Skärmbild av verifiering av lyckade lyckades](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Lista blobbar i en blob-behållare

Det här avsnittet illustrerar hur du listar blobbar i en blob-behållare. Exempelkoden refererar till *testblobbbehållaren* som skapats i avsnittet [Skapa en blob-behållare](#create-a-blob-container).

1. Öppna filen `BlobsController.cs`.

1. Lägg till `ListBlobs` en metod `ActionResult`som kallas som returnerar en .

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. I `ListBlobs` metoden hämtar `CloudBlobContainer` du ett objekt som representerar en referens till blob-behållaren. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Om du vill visa blobbar i `CloudBlobContainer.ListBlobs` en blob-behållare använder du metoden. Metoden `CloudBlobContainer.ListBlobs` returnerar `IListBlobItem` ett objekt som `CloudBlockBlob`kan `CloudPageBlob`kastas till ett , eller `CloudBlobDirectory` objekt. Följande kodavsnitt räknar upp alla blobbar i en blob-behållare. Varje blob kastas till rätt objekt, baserat på dess typ. Dess namn (eller URI när det gäller en **CloudBlobDirectory)** läggs till i en lista.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Förutom blobbar kan blob-behållare innehålla kataloger. Anta att det finns en blob-behållare som kallas *testblobbbehållare*med följande hierarki:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Med hjälp av föregående kodexempel innehåller **blobs** stränglistan värden som liknar följande:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Som visad innehåller listan endast de översta entiteterna, inte de kapslade (*bar.png* och *baz.png*). Om du vill visa en lista över alla entiteter i en blob-behållare ändrar du koden så att metoden **CloudBlobContainer.ListBlobs** skickas **true** för parametern **useFlatBlobListing.**    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Om du ställer in parametern **useFlatBlobListing** till **true** returneras en platt lista över alla entiteter i blob-behållaren. Detta ger följande resultat:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Följande visar den färdiga **ListBlobs-metoden:**

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. Expandera mappen **Vyer** i **Solution Explorer**och högerklicka på **Blobbar**.

2. Välj **Lägg till** > **vy**på snabbmenyn .

1. Ange **Add View** `ListBlobs` för visningsnamnet i dialogrutan Lägg till vy och välj Lägg **till**.

1. Öppna `ListBlobs.cshtml`och ersätt innehållet med följande kod:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. Expandera mappen Delade **vyer** > **Shared** i Solution `_Layout.cshtml` **Explorer**och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Kör programmet och välj **Listblobar** om du vill se resultat som liknar följande skärmbild:
  
    ![Skärmbild av listblobar](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Ladda ned blobbar

Det här avsnittet illustrerar hur du hämtar en blob. Du kan antingen spara den i lokal lagring eller läsa innehållet i en sträng. Exempelkoden refererar till *testblobbbehållaren* som skapats i avsnittet [Skapa en blob-behållare](#create-a-blob-container).

1. Öppna filen `BlobsController.cs`.

1. Lägg till `DownloadBlob` en metod som kallas som returnerar en sträng.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. I `DownloadBlob` metoden hämtar `CloudBlobContainer` du ett objekt som representerar en referens till blob-behållaren.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hämta ett blob-referensobjekt `CloudBlobContainer.GetBlockBlobReference` genom att anropa metoden. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Om du vill hämta `CloudBlockBlob.DownloadToStream` en blob använder du metoden. Följande kod överför innehållet i en blob till ett flödesobjekt. Objektet sparas sedan i en lokal fil. (Ändra * &lt;namnet på lokal fil>* till det fullständigt kvalificerade filnamnet som representerar var blobben ska hämtas.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Följande visar den `DownloadBlob` färdiga metoden (med en fullständigt kvalificerad sökväg för den lokala filen som skapas):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. Expandera mappen Delade **vyer** > **Shared** i Solution `_Layout.cshtml` **Explorer**och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **Hämta blob** för att hämta blobben. Bloben som anges `CloudBlobContainer.GetBlockBlobReference` i metodanropet hämtas `File.OpenWrite` till den plats som anges i metodanropet.  Texten *lyckas!* ska visas i webbläsaren. 

## <a name="delete-blobs"></a>Ta bort blobbar

Följande steg illustrerar hur du tar bort en blob:

1. Öppna filen `BlobsController.cs`.

1. Lägg till `DeleteBlob` en metod som kallas som returnerar en sträng.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. I `DeleteBlob` metoden hämtar `CloudBlobContainer` du ett objekt som representerar en referens till blob-behållaren.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hämta ett blob-referensobjekt `CloudBlobContainer.GetBlockBlobReference` genom att anropa metoden. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Om du vill ta `Delete` bort en blob använder du metoden.

    ```csharp
    blob.Delete();
    ```
    
    Den `DeleteBlob` färdiga metoden ska visas på följande sätt:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. Expandera mappen Delade **vyer** > **Shared** i Solution `_Layout.cshtml` **Explorer**och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **Ta bort blob för** att `CloudBlobContainer.GetBlockBlobReference` ta bort bloben som anges i metodanropet. Texten *lyckas!* ska visas i webbläsaren. Välj webbläsarens **bakåtknapp** och välj sedan **Listblobar** för att kontrollera att blobben inte längre finns i behållaren.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du lagrar, listar och hämtar blobbar i Azure Storage med hjälp av ASP.NET. Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Komma igång med Azure Table Storage och Visual Studio-anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Komma igång med Azure Queue storage och Visual Studio-anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
