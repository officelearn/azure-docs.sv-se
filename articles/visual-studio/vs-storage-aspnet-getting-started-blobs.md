---
title: "Kom igång med Azure Blob storage och Visual Studio anslutna tjänster (ASP.NET) | Microsoft Docs"
description: "Hur du kommer igång anslutna med Azure Blob-lagring i en ASP.NET-projekt i Visual Studio när du har anslutit till ett lagringskonto med hjälp av Visual Studio tjänster"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: kraig
ms.openlocfilehash: cb406e528568dafd1e142943f5273ad58e550609
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Kom igång med Azure Blob storage och Visual Studio anslutna tjänster (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob storage är en tjänst som lagrar Ostrukturerade data i molnet som objekt eller BLOB. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring.

Den här kursen visar hur du skriver ASP.NET-kod för några vanliga scenarier som använder Blob storage. Scenarier som inkluderar en blob-behållare och ladda upp, lista, hämtar och tar bort blobbar.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]


[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Skapa en MVC-enhet 

1. I **Solution Explorer**, högerklicka på **styrenheter**.

2. På snabbmenyn Välj **Lägg till** > **domänkontrollant**.

    ![Skärmbild av Solution Explorer med Lägg till och Controller markerat](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. I den **Lägg till Kodskelett** dialogrutan **MVC 5 styrenhet – tom**, och välj **Lägg till**.

    ![Dialogrutan Lägg till Kodskelett skärmbild](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. I den **Lägg till styrenhet** dialogrutan namn styrenheten *BlobsController*, och välj **Lägg till**.

    ![Dialogrutan Lägg till styrenhet skärmbild](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Lägg till följande `using` direktiven till den `BlobsController.cs` filen:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Anslut till ett lagringskonto och få en referens för behållaren

En blob-behållare är en kapslad hierarki med blobbar och mappar. Resten av stegen i det här dokumentet kräver en referens till en blob-behållare så att koden ska placeras i sin egen metod för återanvändning.

Följande steg skapar du en metod för att ansluta till storage-konto med hjälp av anslutningssträngen i **Web.config**. Stegen kan du också skapa en referens till en behållare.  Inställningen för anslutningssträngen i **Web.config** heter med formatet `<storageaccountname>_AzureStorageConnectionString`. 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas **GetCloudBlobContainer** som returnerar en **CloudBlobContainer**.  Se till att ersätta `<storageaccountname>_AzureStorageConnectionString` med det faktiska namnet på nyckeln i **Web.config**.
    
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
> Även om *test blobbehållaren* inte finns ännu, den här koden skapar en referens till den. Detta är så att du kan skapa behållaren med den `CreateIfNotExists` metod som visas i nästa steg.

## <a name="create-a-blob-container"></a>Skapa en blobbbehållare

Följande steg visar hur du skapar en blob-behållare:

1. Lägg till en metod som kallas `CreateBlobContainer` som returnerar en `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Hämta en `CloudBlobContainer` objekt som representerar en referens till önskad blob-behållarnamn. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Anropa den `CloudBlobContainer.CreateIfNotExists` metoden för att skapa behållaren, om det inte finns ännu. Den `CloudBlobContainer.CreateIfNotExists` metoden returnerar **SANT** om behållaren finns inte och har skapats. Annars returnerar-metoden **FALSKT**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Uppdatera `ViewBag` med namnet på blob-behållaren.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Följande visar den färdiga `CreateBlobContainer` metoden:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. I **Solution Explorer**, högerklicka på den **vyer** mapp.

2. På snabbmenyn Välj **Lägg till** > **ny mapp**. Namnge den nya mappen *Blobbar*. 
 
1. I **Solution Explorer**, expandera den **vyer** mappen och högerklicka på **Blobbar**.

4. På snabbmenyn Välj **Lägg till** > **visa**.

1. I den **Lägg till vy** dialogrutan Ange **CreateBlobContainer** för namn och välj **Lägg till**.

1. Öppna `CreateBlobContainer.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. I **Solution Explorer**, expandera den **vyer** > **delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Kör programmet och välj **skapa Blob-behållaren** att se resultatet liknar följande skärmbild:
  
    ![Skärmbild av skapa blob-behållare](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Som nämnts tidigare i `CloudBlobContainer.CreateIfNotExists` metoden returnerar **SANT** endast när behållaren finns inte och har skapats. Om appen körs när behållaren finns, metoden returnerar därför **FALSKT**.

## <a name="upload-a-blob-into-a-blob-container"></a>Ladda upp en blobb till en blob-behållare

När den [blob-behållare skapas](#create-a-blob-container), överföra filer till behållaren. Det här avsnittet går igenom ladda upp en lokal fil till en blob-behållare. Anvisningarna förutsätter att det finns en blobbbehållare med namnet *test blobbehållaren*. 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas `UploadBlob` som returnerar en sträng.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. I den `UploadBlob` metod, hämta en `CloudBlobContainer` objekt som representerar en referens till önskad blob-behållarnamn. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure storage har stöd för olika blob-typer. Den här kursen använder blockblobar. Om du vill hämta en referens till en blockblobb anropa den `CloudBlobContainer.GetBlockBlobReference` metoden.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Blob-namnet är en del av URL: en som används för att hämta en blob och kan vara en sträng, inklusive namnet på filen.

1. När det är en blobbreferens kan du kan ladda upp en dataström till den genom att anropa blob-referensobjekt `UploadFromStream` metod. Den `UploadFromStream` metoden skapas blobben om den finns inte eller skrivs över om den finns. (Ändra  *&lt;till filuppladdning >* till en fullständigt kvalificerad sökväg till en fil som ska laddas upp.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Följande visar den färdiga `UploadBlob` metod (med en fullständigt kvalificerad sökväg till filen som laddas upp):

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

1. I **Solution Explorer**, expandera den **vyer** > **delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **överför blob**.  Ordet *lyckades!* ska visas.
    
    ![Skärmbild av verifieringen lyckades](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Visa blobbar i en blob-behållare

Det här avsnittet visar hur du kan visa blobbar i en blob-behållare. Exempel koden refererar till den *test blobbehållaren* skapade i avsnittet [skapa en blobbbehållare](#create-a-blob-container).

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas `ListBlobs` som returnerar en `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. I den `ListBlobs` metod, hämta en `CloudBlobContainer` objekt som representerar en referens till blob-behållaren. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Om du vill visa blobbar i en blob-behållare, använda den `CloudBlobContainer.ListBlobs` metoden. Den `CloudBlobContainer.ListBlobs` metoden returnerar en `IListBlobItem` objekt som kan omvandlas till en `CloudBlockBlob`, `CloudPageBlob`, eller `CloudBlobDirectory` objekt. Följande kodstycke räknar upp alla blobbar i en blobbbehållare. Varje blobb omvandlas till lämpligt objekt, baserat på dess typ. Sitt namn (eller URI i händelse av en **CloudBlobDirectory**) har lagts till i en lista.

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

    Förutom blobbar, kan blob-behållare innehålla kataloger. Anta att det finns en blobbbehållare som kallas *test blobbehållaren*, med följande hierarki:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Med hjälp av föregående kodexempel i **blobbar** stränglista innehåller värden som liknar följande:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Visas i listan innehåller endast de översta enheterna, inte de kapslade (*bar.png* och *baz.png*). Ändra koden för att visa en lista över alla enheter i en blob-behållare, så att den **CloudBlobContainer.ListBlobs** metoden skickas **SANT** för den **useFlatBlobListing** parameter.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Ange den **useFlatBlobListing** parameter till **SANT** returnerar en platt lista över alla entiteter i blob-behållaren. Detta ger följande resultat:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Följande visar den färdiga **ListBlobs** metod:

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

1. I **Solution Explorer**, expandera den **vyer** mappen och högerklicka på **Blobbar**.

2. På snabbmenyn Välj **Lägg till** > **visa**.

1. I den **Lägg till vy** dialogrutan Ange `ListBlobs` för namn och välj **Lägg till**.

1. Öppna `ListBlobs.cshtml`, och Ersätt innehållet med följande kod:

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

1. I **Solution Explorer**, expandera den **vyer** > **delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Kör programmet och välj **visa blobbar** att se resultatet liknar följande skärmbild:
  
    ![Skärmbild av lista blobbar](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Ladda ned blobbar

Det här avsnittet beskriver hur du hämtar en blob. Du kan spara den till lokal lagring eller läsa in innehållet i en sträng. Exempel koden refererar till den *test blobbehållaren* skapade i avsnittet [skapa en blobbbehållare](#create-a-blob-container).

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas `DownloadBlob` som returnerar en sträng.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. I den `DownloadBlob` metod, hämta en `CloudBlobContainer` objekt som representerar en referens till blob-behållaren.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hämta en referens för blob-objektet genom att anropa den `CloudBlobContainer.GetBlockBlobReference` metoden. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Du kan hämta en blob den `CloudBlockBlob.DownloadToStream` metoden. Följande kod Överför innehållet för en blobb till en stream-objektet. Objektet sparas sedan till en lokal fil. (Ändra  *&lt;lokala filnamn >* och det fullt kvalificerade namnet som representerar där blob som ska hämtas.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Följande visar den färdiga `ListBlobs` metod (med en fullständigt kvalificerad sökväg för den lokala filen skapas):
    
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

1. I **Solution Explorer**, expandera den **vyer** > **delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **Download blob** att hämta blobben. Blob som anges i den `CloudBlobContainer.GetBlockBlobReference` metodanrop filhämtningar till den plats som anges i den `File.OpenWrite` metodanrop.  Texten *lyckades!* ska visas i webbläsaren. 

## <a name="delete-blobs"></a>Ta bort blobbar

Följande steg visar hur du tar bort en blobb:

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas `DeleteBlob` som returnerar en sträng.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. I den `DeleteBlob` metod, hämta en `CloudBlobContainer` objekt som representerar en referens till blob-behållaren.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hämta en referens för blob-objektet genom att anropa den `CloudBlobContainer.GetBlockBlobReference` metoden. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Om du vill ta bort en blobb, använder den `Delete` metoden.

    ```csharp
    blob.Delete();
    ```
    
    Den slutförda `DeleteBlob` metod ska visas på följande sätt:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. I **Solution Explorer**, expandera den **vyer** > **delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **ta bort blob** att ta bort blobben som anges i den `CloudBlobContainer.GetBlockBlobReference` metodanrop. Texten *lyckades!* ska visas i webbläsaren. Välj webbläsarens **tillbaka** och välj sedan **visa blobbar** att verifiera att blobben är inte längre i behållaren.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen beskrivs hur du lagrar, lista och hämta blobbar i Azure Storage med hjälp av ASP.NET. Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure Table storage och Visual Studio anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Kom igång med Azure Queue storage och Visual Studio anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
