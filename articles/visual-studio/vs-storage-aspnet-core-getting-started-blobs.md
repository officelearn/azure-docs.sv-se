---
title: "Kom igång med Azure blob storage och Visual Studio anslutna Services (ASP.NET Core) | Microsoft Docs"
description: "Hur du kommer igång med Azure blob storage i ASP.NET Core-projekt i Visual Studio efter anslutning till ett lagringskonto med hjälp av Visual Studio anslutna Services"
services: storage
documentationcenter: 
author: camsoper
manager: wpickett
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 889afa471eeb556662cddf8eb383a905f08b1f01
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Kom igång med Azure blob storage och Visual Studio anslutna Services (ASP.NET kärnor)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure blob storage är en tjänst som lagrar Ostrukturerade data i molnet som objekt/blobbar. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring.

Den här kursen visar hur du skriver ASP.NET Core kod för några vanliga scenarier med Azure blob storage. Scenarier som inkluderar en blob-behållare och ladda upp, lista, hämtar och tar bort blobbar.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Krav

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Det här avsnittet går igenom Konfigurera utvecklingsmiljön, inklusive att skapa en ASP.NET MVC-app, lägger till en ansluten Services-anslutning, lägger till en domänkontrollant och att ange de nödvändiga namnområde direktiv.

### <a name="create-an-aspnet-mvc-app-project"></a>Skapa en ASP.NET MVC-app-projekt

1. Öppna Visual Studio.

1. Välj **Arkiv -> Ny -> projekt** från huvudmenyn

1. På den **nytt projekt** dialogrutan, ange alternativ som är markerade i följande bild:

    ![Skapa ASP.NET Core-projekt](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Välj **OK**.

1. På den **nytt ASP.NET-projekt** dialogrutan, ange alternativ som är markerade i följande bild:

    ![Ange MVC](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

1. Välj **OK**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Använda anslutna Services för att ansluta till ett Azure storage-konto

1. I den **Solution Explorer**, högerklicka på projektet och på snabbmenyn väljer **Lägg till -> ansluten Service**.

1. På den **Lägg till ansluten tjänst** markerar **lagringsutrymmet i molnet med Azure Storage**, och välj sedan **konfigurera**.

    ![Dialogrutan för anslutna Service](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. På den **Azure Storage** dialogrutan Välj Azure Storage-konto som ska användas för den här kursen.  Om du vill skapa ett nytt Azure Storage-konto, klickar du på **skapa ett nytt Lagringskonto** och Fyll i formuläret.  När du väljer ett befintligt lagringskonto eller skapa en ny, klickar du på **Lägg till**.  Visual Studio ska installera NuGet-paketet för Azure Storage och en lagringsanslutningssträng till **appsettings.json**.

> [!TIP]
> Information om hur du skapar ett lagringskonto med den [Azure-portalen](https://portal.azure.com), se [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Ett Azure storage-konto kan även skapas med hjälp av [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md), eller [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Skapa en MVC-enhet 

1. I den **Solution Explorer**, högerklicka på **domänkontrollanter**, och på snabbmenyn Välj **Lägg till -> styrenhet**.

    ![Lägg till en domänkontrollant i en ASP.NET Core MVC-app](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. På den **Lägg till Kodskelett** markerar **MVC styrenhet – tom**, och välj **Lägg till**.

    ![Ange typ av MVC-domänkontrollant](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. På den **Lägg till styrenhet** dialogrutan namn styrenheten *BlobsController*, och välj **Lägg till**.

    ![Namnet på MVC-enhet](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Lägg till följande *med* direktiven till den `BlobsController.cs` filen:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Anslut till ett lagringskonto och få en referens för behållaren

En blob-behållare är en kapslad hierarki med blobbar och mappar.  Resten av stegen i det här dokumentet kräver en referens till en blob-behållare så att koden ska placeras sin egen metod för återanvändning.

Följande steg skapar du en metod för att ansluta till lagringskontot med hjälp av anslutningssträngen i **appsettings.json** och skapa en referens till en behållare.  Inställningen för anslutningssträngen i **appsettings.json** kommer att namnges med formatet `<storageaccountname>_AzureStorageConnectionString`. 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas **GetCloudBlobContainer** som returnerar en **CloudBlobContainer**.  Se till att ersätta `<storageaccountname>_AzureStorageConnectionString` med det faktiska namnet på nyckeln i **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Även om *test blobbehållaren* inte finns ännu, den här koden skapar en referens till den så att du kan skapa behållaren med den `CreateIfNotExists` metod som visas i nästa steg.

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

1. Anropa den `CloudBlobContainer.CreateIfNotExists` metoden för att skapa behållaren om den inte finns ännu. Den `CloudBlobContainer.CreateIfNotExists` metoden returnerar **SANT** om behållaren finns inte och har skapats. Annars **FALSKT** returneras.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Uppdatering av `ViewBag` med namnet på blob-behållaren.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Följande visar den färdiga `CreateBlobContainer` metoden:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. I den **Solution Explorer**, högerklicka på den **vyer** mapp, och på snabbmenyn Välj **Lägg till -> Ny mapp**. Namnge den nya mappen *Blobbar*. 

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **Blobbar**, och på snabbmenyn Välj **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **CreateBlobContainer** för namn och välj **Lägg till**.

1. Öppna `CreateBlobContainer.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Leta efter osorterad lista som ser ut så här: `<ul class="nav navbar-nav">`.  Efter senast `<li>` element i listan Lägg till följande HTML att lägga till en annan navigering menyalternativ:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Kör programmet och välj **skapa Blob-behållaren** att se resultatet liknar följande Skärmdump:
  
    ![Skapa blobbehållare](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Som nämnts tidigare i `CloudBlobContainer.CreateIfNotExists` metoden returnerar **SANT** endast när behållaren finns inte och har skapats. Om appen körs när behållaren finns, metoden returnerar därför **FALSKT**.

## <a name="upload-a-blob-into-a-blob-container"></a>Ladda upp en blobb till en blob-behållare

En gång i [blob-behållare skapas](#create-a-blob-container), överföra filer till behållaren. Det här avsnittet går igenom ladda upp en lokal fil till en blob-behållare. Anvisningarna förutsätter att det finns en blobbbehållare med namnet *test blobbehållaren*. 

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

1. Som beskrivits tidigare stöder Azure storage olika blob-typer. Den här kursen använder blockblobar.  Om du vill hämta en referens till en blockblobb anropa den `CloudBlobContainer.GetBlockBlobReference` metoden.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Blob-namnet är en del av URL: en som används för att hämta en blob och kan vara en sträng, inklusive namnet på filen.

1. När en blobbreferens kan ladda upp en dataström till den genom att anropa blob-referensobjekt `UploadFromStream` metod. Den `UploadFromStream` metoden skapas blobben om den finns inte eller skrivs över om den finns. (Ändra  *&lt;till filuppladdning >* till en fullständigt kvalificerad sökväg till en fil som ska laddas upp.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
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
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast `<li>` element i listan Lägg till följande HTML att lägga till en annan navigering menyalternativ:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Kör programmet och välj **överför blob**.  Ordet ”success”! ska innehålla.
    
    ![Verifieringen lyckades](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
Avsnittet - [visa blobbar i en blob-behållare](#list-the-blobs-in-a-blob-container) -visar hur du kan visa blobbar i en blob-behållare.    

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
   
1. Om du vill visa blobbar i en blob-behållare, använda den `CloudBlobContainer.ListBlobsSegmentedAsync` metoden. Den `CloudBlobContainer.ListBlobsSegmentedAsync` metoden returnerar en `BlobResultSegment` som innehåller `IListBlobItem` objekt som kan omvandlas till `CloudBlockBlob`, `CloudPageBlob`, eller `CloudBlobDirectory` objekt. Följande kodstycke räknar upp alla blobbar i en blobbbehållare. Varje blobb omvandlas till lämpligt objekt baserat på dess typ och dess namn (eller URI i händelse av en `CloudBlobDirectory`) har lagts till i en lista.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
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
    Följande visar den färdiga `ListBlobs` metoden:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
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

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **Blobbar**, och på snabbmenyn Välj **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange `ListBlobs` för namn och välj **Lägg till**.

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

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast `<li>` element i listan Lägg till följande HTML att lägga till en annan navigering menyalternativ:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Kör programmet och välj **visa blobbar** att se resultatet liknar följande Skärmdump:
  
    ![BLOB-lista](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Ladda ned blobbar

Det här avsnittet beskriver hur du hämtar en blob och antingen spara den lokal lagring eller läsa in innehållet i en sträng. Exempel koden refererar till den *test blobbehållaren* skapade i avsnittet [skapa en blobbbehållare](#create-a-blob-container).

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

1. Hämta en referens för blob-objektet genom att anropa `CloudBlobContainer.GetBlockBlobReference` metod. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Du kan hämta en blob den `CloudBlockBlob.DownloadToStream` metoden. Följande kod Överför innehållet för en blobb till en stream-objektet som sparas sedan till en lokal fil (ändra  *&lt;lokala filnamn >* och det fullt kvalificerade namnet som representerar där blob som ska hämtas.): 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
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
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast `<li>` element i listan Lägg till följande HTML att lägga till en annan navigering menyalternativ:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Kör programmet och välj **Download blob** att hämta blobben. Blob som anges i den `CloudBlobContainer.GetBlockBlobReference` metodanrop filhämtningar till den plats som anges i den `File.OpenWrite` metodanrop.  Texten ”success”! ska visas i webbläsaren. 

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

1. Hämta en referens för blob-objektet genom att anropa `CloudBlobContainer.GetBlockBlobReference` metod. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Om du vill ta bort en blobb, använder den `Delete` metoden.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    Den slutförda `DeleteBlob` metod ska visas på följande sätt:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast `<li>` element i listan Lägg till följande HTML att lägga till en annan navigering menyalternativ:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Kör programmet och välj **ta bort blob** att ta bort blobben som anges i den `CloudBlobContainer.GetBlockBlobReference` metodanrop.  Texten ”success”! ska visas i webbläsaren.  Klicka på webbläsarens **tillbaka** knappen och markera **visa blobbar** att verifiera blob är inte längre i behållaren.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen beskrivs hur du lagrar, lista och hämta blobbar i Azure Storage med hjälp av ASP.NET Core.  Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure-tabellagring och Visual Studio anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Kom igång med Azure queue storage- och Visual Studio anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
