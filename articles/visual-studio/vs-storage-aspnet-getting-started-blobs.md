---
title: Kom igång med Azure Blob Storage och ASP.NET (Visual Studio Connected Services) | Microsoft Docs
description: Komma igång med Azure Blob Storage i ett ASP.NET-projekt i Visual Studio, efter att ha anslutit till ett lagrings konto med hjälp av Visual Studio Connected Services
services: storage
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ms.openlocfilehash: 229522d16db0571c7dbe50e05fceaca9491c4c58
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034171"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Kom igång med Azure Blob Storage och ASP.NET (Visual Studio Connected Services)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob Storage är en tjänst som lagrar ostrukturerade data i molnet som objekt eller blobbar. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring. Mer information om Blob Storage finns i [Introduktion till Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md).

Den här självstudien visar hur du skriver ASP.NET-kod för några vanliga scenarier som använder Blob Storage. Scenarier innefattar att skapa en BLOB-behållare och ladda upp, lista, ladda ned och ta bort blobbar.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Skapa en MVC-kontrollant 

1. Högerklicka på **kontrollanter**i **Solution Explorer**.

2. I snabb menyn väljer du **Lägg till** > **kontrollant**.

    ![Skärm bild av Solution Explorer, med Lägg till och kontroll enhet markerat](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. I dialog rutan **Lägg till Autogenerera** väljer du **MVC 5 Controller-Töm**och väljer **Lägg till**.

    ![Skärm bild av dialog rutan Lägg till Autogenerera](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. I dialog rutan **Lägg till kontrollant** namnger du kontrollantens *BlobsController*och väljer **Lägg till**.

    ![Skärm bild av dialog rutan Lägg till kontrollant](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Lägg till följande `using` direktiv `BlobsController.cs` i filen:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Anslut till ett lagrings konto och få en behållar referens

En BLOB-behållare är en kapslad hierarki med blobbar och mappar. Resten av stegen i det här dokumentet kräver en referens till en BLOB-behållare, så att koden ska placeras i sin egen metod för åter användning.

Följande steg skapar en metod för att ansluta till lagrings kontot med hjälp av anslutnings strängen i **Web. config**. Stegen skapar också en referens till en behållare.  Inställningen för anslutnings strängen i **Web. config** heter med formatet `<storageaccountname>_AzureStorageConnectionString`. 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod med namnet **GetCloudBlobContainer** som returnerar en **CloudBlobContainer**.  Se till att ersätta `<storageaccountname>_AzureStorageConnectionString` med det faktiska namnet på nyckeln i **Web. config**.
    
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
> Även om *test-BLOB-containern* inte finns, skapar den här koden en referens till den. Detta är så att behållaren kan skapas med den `CreateIfNotExists` metod som visas i nästa steg.

## <a name="create-a-blob-container"></a>Skapa en blobcontainer

Följande steg visar hur du skapar en BLOB-behållare:

1. Lägg till en metod `CreateBlobContainer` som kallas som `ActionResult`returnerar en.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Hämta ett `CloudBlobContainer` objekt som representerar en referens till önskat BLOB container-namn. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. `CloudBlobContainer.CreateIfNotExists` Anropa metoden för att skapa behållaren om den inte redan finns. Metoden returnerar true om behållaren inte finns och har skapats. `CloudBlobContainer.CreateIfNotExists` Annars returnerar metoden false.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Uppdatera `ViewBag` med namnet på BLOB-behållaren.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Följande visar den slutförda `CreateBlobContainer` metoden:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. I **Solution Explorer**högerklickar du på mappen **vyer** .

1. Om det inte finns någon blobs-mapp skapar du en. I snabb menyn väljer du **Lägg till** > **ny mapp**. Namnge den nya Folder- *blobben*. 
 
1. I **Solution Explorer**expanderar du mappen **vyer** och högerklickar på **blobbar**.

1. Välj **Lägg till** > **vy**från snabb menyn.

1. I dialog rutan **Lägg till vy** anger du **CreateBlobContainer** som namn på vyn och väljer **Lägg till**.

1. Öppna `CreateBlobContainer.cshtml`och ändra det så att det ser ut som i följande kodfragment:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. I **Solution Explorer**, expanderar > du den**delade** mappen vyer och `_Layout.cshtml`öppnar.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Kör programmet och välj **skapa BLOB-behållare** för att se resultat som liknar följande skärm bild:
  
    ![Skärm bild av skapa BLOB-behållare](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Som tidigare `CloudBlobContainer.CreateIfNotExists` nämnts returnerar metoden **True** bara när behållaren inte finns och skapas. Om appen körs när behållaren finns returnerar metoden **falskt**.

## <a name="upload-a-blob-into-a-blob-container"></a>Ladda upp en blob till en BLOB-behållare

När [BLOB-behållaren skapas](#create-a-blob-container)laddar du upp filer i den behållaren. Det här avsnittet beskriver hur du laddar upp en lokal fil till en BLOB-behållare. Stegen förutsätter att det finns en BLOB-behållare med namnet *test-BLOB-container*. 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod `UploadBlob` som kallas som returnerar en sträng.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. I- `CloudBlobContainer` metoden kan du hämta ett objekt som representerar en referens till önskat BLOB container-namn. `UploadBlob` 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure Storage har stöd för olika BLOB-typer. I den här självstudien används block blobbar. Anropa `CloudBlobContainer.GetBlockBlobReference` metoden för att hämta en referens till en Block-Blob.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > BLOB-namnet är en del av den URL som används för att hämta en blob och kan vara valfri sträng, inklusive namnet på filen.

1. När det finns en BLOB-referens kan du ladda upp alla data strömmar till den genom att anropa `UploadFromStream` metoden blobb referens objekt. `UploadFromStream` Metoden skapar blobben om den inte finns, eller skriver över den om den finns. (Ändra  *&lt;fil till uppladdning >* till en fullständigt kvalificerad sökväg till en fil som ska överföras.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Nedan visas den slutförda `UploadBlob` metoden (med en fullständigt kvalificerad sökväg för filen som ska överföras):

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

1. I **Solution Explorer**, expanderar > du den**delade** mappen vyer och `_Layout.cshtml`öppnar.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **Ladda upp BLOB**.  Ordet *lyckades!* ska visas.
    
    ![Skärm bild av lyckad verifiering](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Visa en lista över blobarna i en BLOB-behållare

I det här avsnittet beskrivs hur du visar en lista över blobarna i en BLOB-behållare. Exempel koden refererar till *test-BLOB-behållaren* som skapades i avsnittet och [skapar en BLOB-behållare](#create-a-blob-container).

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod `ListBlobs` som kallas som `ActionResult`returnerar en.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. I- `CloudBlobContainer` metoden kan du hämta ett objekt som representerar en referens till BLOB-behållaren. `ListBlobs` 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Om du vill visa blobarna i en BLOB-behållare använder `CloudBlobContainer.ListBlobs` du-metoden. `IListBlobItem` `CloudBlockBlob` `CloudPageBlob`Metoden returnerar ett objekt som kan omvandlas till ett-,-eller `CloudBlobDirectory` -objekt. `CloudBlobContainer.ListBlobs` Följande kodfragment räknar upp alla blobar i en BLOB-behållare. Varje Blob omvandlas till lämpligt objekt, baserat på dess typ. Dess namn (eller URI när det gäller en **CloudBlobDirectory**) läggs till i en lista.

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

    Förutom blobbar kan BLOB-behållare innehålla kataloger. Anta att det finns en BLOB-behållare med namnet *test-BLOB-container*, med följande hierarki:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Med föregående kod exempel innehåller blobs -sträng listan värden som liknar följande:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Som visas innehåller listan endast entiteter på den översta nivån, inte de kapslade (*bar. png* och *Baz. png*). Om du vill visa en lista över alla entiteter i en BLOB-behållare ändrar du koden så att **CloudBlobContainer. ListBlobs** -metoden skickas som **True** för parametern **useFlatBlobListing** .    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Om du anger parametern **useFlatBlobListing** till **True** returneras en platt lista över alla entiteter i BLOB-behållaren. Detta ger följande resultat:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Följande visar den slutförda **ListBlobs** -metoden:

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

1. I **Solution Explorer**expanderar du mappen **vyer** och högerklickar på **blobbar**.

2. Välj **Lägg till** > **vy**från snabb menyn.

1. I dialog rutan **Lägg till vy** anger `ListBlobs` du för visnings namnet och väljer **Lägg till**.

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

1. I **Solution Explorer**, expanderar > du den**delade** mappen vyer och `_Layout.cshtml`öppnar.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Kör programmet och välj **list blobbar** för att se resultat som liknar följande skärm bild:
  
    ![Skärm bild av list blobbar](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Ladda ned blobbar

I det här avsnittet beskrivs hur du laddar ned en blob. Du kan antingen behålla den till lokal lagring eller läsa innehållet i en sträng. Exempel koden refererar till *test-BLOB-behållaren* som skapades i avsnittet och [skapar en BLOB-behållare](#create-a-blob-container).

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod `DownloadBlob` som kallas som returnerar en sträng.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. I- `CloudBlobContainer` metoden kan du hämta ett objekt som representerar en referens till BLOB-behållaren. `DownloadBlob`
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hämta ett BLOB-referens objekt genom att `CloudBlobContainer.GetBlockBlobReference` anropa-metoden. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Använd `CloudBlockBlob.DownloadToStream` -metoden för att ladda ned en blob. Följande kod överför en blobs innehåll till ett Stream-objekt. Objektet sparas sedan i en lokal fil. (Ändra  *&lt;namnet på den lokala filen >* till det fullständiga fil namnet som representerar var blobben ska laddas ned.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Nedan visas den slutförda `DownloadBlob` metoden (med en fullständigt kvalificerad sökväg för den lokala filen som skapas):
    
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

1. I **Solution Explorer**, expanderar > du den**delade** mappen vyer och `_Layout.cshtml`öppnar.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **Ladda ned BLOB** för att ladda ned blobben. Den blob som anges i `CloudBlobContainer.GetBlockBlobReference` metod anropet hämtas till den plats som anges `File.OpenWrite` i metod anropet.  Texten *lyckades!* ska visas i webbläsaren. 

## <a name="delete-blobs"></a>Ta bort blobbar

Följande steg illustrerar hur du tar bort en BLOB:

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod `DeleteBlob` som kallas som returnerar en sträng.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. I- `CloudBlobContainer` metoden kan du hämta ett objekt som representerar en referens till BLOB-behållaren. `DeleteBlob`
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hämta ett BLOB-referens objekt genom att `CloudBlobContainer.GetBlockBlobReference` anropa-metoden. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Använd `Delete` -metoden för att ta bort en blob.

    ```csharp
    blob.Delete();
    ```
    
    Den färdiga `DeleteBlob` metoden bör se ut så här:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. I **Solution Explorer**, expanderar > du den**delade** mappen vyer och `_Layout.cshtml`öppnar.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **ta bort BLOB** för att ta bort den blob som anges `CloudBlobContainer.GetBlockBlobReference` i metod anropet. Texten *lyckades!* ska visas i webbläsaren. Välj knappen **bakåt** i webbläsaren och välj sedan **list blobbar** för att kontrol lera att blobben inte längre finns i behållaren.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du lagrar, listar och hämtar blobbar i Azure Storage med hjälp av ASP.NET. Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure Table Storage och ASP.NET (Visual Studio Connected Services)](vs-storage-aspnet-getting-started-tables.md)
  * [Kom igång med Azure Queue Storage och Visual Studio Connected Services (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
