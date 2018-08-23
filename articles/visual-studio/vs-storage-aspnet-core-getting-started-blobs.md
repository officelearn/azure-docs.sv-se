---
title: Kom igång med Azure Blob storage och Visual Studio-anslutna tjänster (ASP.NET Core) | Microsoft Docs
description: Hur du kommer igång anslutna med Azure Blob storage i ett ASP.NET Core-projekt i Visual Studio när du har anslutit till ett lagringskonto med hjälp av Visual Studio tjänster
services: storage
documentationcenter: ''
author: camsoper
manager: wpickett
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 82c5fb0f3f3e8edad948b82f77c9c336636f3077
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42442749"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Kom igång med Azure Blob storage och Visual Studio-anslutna tjänster (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob storage är en tjänst som lagrar Ostrukturerade data i molnet som objekt eller BLOB-objekt. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring.

Den här självstudien visar hur du skriver ASP.NET Core-kod för några vanliga scenarier som använder Blob storage. Scenarier är en blob-behållare och laddar upp, lista, ladda ned och tar bort blobbar.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Det här avsnittet beskriver hur du konfigurerar utvecklingsmiljön. Detta omfattar att skapa en app i ASP.NET Model-View-Controller (MVC), lägger till en anslutning för anslutna tjänster, att lägga till en domänkontrollant och ange nödvändiga namnområde-direktiv.

### <a name="create-an-aspnet-mvc-app-project"></a>Skapa en ASP.NET MVC-app-projekt

1. Öppna Visual Studio.

1. På huvudmenyn väljer **filen** > **New** > **projekt**.

1. I den **nytt projekt** dialogrutan **Web** > **ASP.NET Core-Webbapp** > **AspNetCoreStorage**. Välj sedan **OK**.

    ![Skärmbild av Visual Studio dialogrutan Nytt projekt](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. I den **ny ASP.NET Core-Webbapp** dialogrutan **.NET Core** > **ASP.NET Core 2.0** > **()-webbprogram Model-View-Controller)**. Välj sedan **OK**.

    ![Skärmbild av nytt ASP.NET Core-Webbapp i dialogrutan som visas](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Använd anslutna tjänster för att ansluta till ett Azure storage-konto

1. I **Solution Explorer**, högerklicka på projektet.

2. På snabbmenyn väljer **Lägg till** > **Connected Service**.

1. I den **Connected Services** dialogrutan **Molnlagring med Azure Storage**, och välj sedan **konfigurera**.

    ![Skärmbild av Connected Services dialogrutan](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. I den **Azure Storage** dialogrutan Välj Azure storage-kontonamnet som ska användas för den här självstudien. Om du vill skapa ett nytt Azure storage-konto, Välj **skapa ett nytt Lagringskonto**, och Fyll i formuläret. När du väljer ett befintligt lagringskonto eller skapa en ny, väljer **Lägg till**. Visual Studio installerar NuGet-paketet för Azure Storage och en anslutningssträng för lagring till **appsettings.json**.

> [!TIP]
> Lär dig hur du skapar ett lagringskonto med den [Azure-portalen](https://portal.azure.com), se [skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).
>
> Du kan också skapa ett lagringskonto med hjälp av [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md), eller [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Skapa ett MVC-kontrollant 

1. I **Solution Explorer**, högerklicka på **styrenheter**.

2. På snabbmenyn väljer **Lägg till** > **Controller**.

    ![Skärmbild av Solution Explorer](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. I den **Lägg till Kodskelett** dialogrutan **MVC-kontrollant – tom**, och välj **Lägg till**.

    ![Dialogrutan Lägg till Kodskelett skärmbild](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. I den **lägga till tom MVC-kontrollant** dialogrutan namn kontrollanten *BlobsController*, och välj **Lägg till**.

    ![Skärmbild av dialogrutan Lägg till tom MVC styrenhet](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Lägg till följande `using` direktiv den `BlobsController.cs` fil:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Ansluta till ett lagringskonto och hämta en referens för behållaren

En blobbehållare är en kapslad hierarki med blobbar och mappar. Resten av stegen i det här dokumentet kräver en referens till en blob-behållare så att koden ska placeras i en egen metod för återanvändning.

Följande steg skapar du en metod för att ansluta till lagringskontot med hjälp av anslutningssträngen i **appsettings.json**. Stegen kan du också skapa en referens till en behållare. Inställningen för anslutningssträngen i **appsettings.json** heter med formatet `<storageaccountname>_AzureStorageConnectionString`. 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas **GetCloudBlobContainer** som returnerar en **CloudBlobContainer**. Se till att ersätta `<storageaccountname>_AzureStorageConnectionString` med det faktiska namnet på nyckeln i **Web.config**.
    
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
> Även om *test blobbehållare* inte finns ännu, den här koden skapar en referens till den. Detta är så att du kan skapa behållaren med den `CreateIfNotExists` metoden som visas i nästa steg.

## <a name="create-a-blob-container"></a>Skapa en blobcontainer

Följande steg illustrerar hur du skapar en blob-behållare:

1. Lägg till en metod som kallas `CreateBlobContainer` som returnerar en `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Hämta en `CloudBlobContainer` objekt som representerar en referens till önskad blobbehållarens namn. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Anropa den `CloudBlobContainer.CreateIfNotExists` metod för att skapa behållaren, om det inte finns ännu. Den `CloudBlobContainer.CreateIfNotExists` metoden returnerar **SANT** om behållaren finns inte och har skapats. I annat fall returnerar-metoden **FALSKT**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Uppdatera `ViewBag` med namnet på blob-behållaren.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Följande visar den slutförda `CreateBlobContainer` metoden:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. I **Solution Explorer**, högerklicka på den **vyer** mapp.

2. På snabbmenyn väljer **Lägg till** > **ny mapp**. Namnge den nya mappen *Blobar*. 

1. I **Solution Explorer**, expandera den **vyer** mappen och högerklicka på **Blobar**.

4. På snabbmenyn väljer **Lägg till** > **visa**.

1. I den **Lägg till vy** dialogrutan anger **CreateBlobContainer** för namn och välj **Lägg till**.

1. Öppna `CreateBlobContainer.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. I **Solution Explorer**, expandera den **vyer** > **delad** och öppna `_Layout.cshtml`.

1. Leta efter osorterad lista som ser ut så här: `<ul class="nav navbar-nav">`.  Efter senast `<li>` element i listan lägger du till följande HTML att lägga till en annan menyalternativet för navigering:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Kör programmet och välj **skapa Blobbehållare** att se resultat som liknar följande skärmbild:
  
    ![Skärmbild av skapa blob-behållare](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Som nämnts tidigare i `CloudBlobContainer.CreateIfNotExists` metoden returnerar **SANT** endast när behållaren finns inte och har skapats. Därför om appen körs när behållaren finns, returnerar metoden **FALSKT**.

## <a name="upload-a-blob-into-a-blob-container"></a>Ladda upp en blob till en blobbehållare

När den [blob-behållare skapas](#create-a-blob-container), överföra filer till den behållaren. Det här avsnittet beskriver ladda upp en lokal fil till en blob-behållare. Anvisningarna förutsätter att det finns en blobbehållare med namnet *test blobbehållare*. 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas `UploadBlob` som returnerar en sträng.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. I den `UploadBlob` metod, hämta en `CloudBlobContainer` objekt som representerar en referens till önskad blobbehållarens namn. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure storage stöder olika typer. Den här självstudien använder blockblobar. Om du vill hämta en referens till en blockblob, anropa den `CloudBlobContainer.GetBlockBlobReference` metoden.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Blobbnamnet är en del av URL: en som används för att hämta en blob och kan vara valfri sträng, inklusive namnet på filen.

1. När det är en blobbreferens kan du överföra strömmad data till den genom att anropa det blob referensobjektet `UploadFromStream` metod. Den `UploadFromStream` metoden skapar bloben om den inte finns eller skriver över den om den finns. (Ändra  *&lt;till Filhämtning >* till en fullständigt kvalificerad sökväg till en fil som ska laddas upp.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Följande visar den slutförda `UploadBlob` metod (med en fullständigt kvalificerad sökväg för filen som ska laddas upp):

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

1. I **Solution Explorer**, expandera den **vyer** > **delad** och öppna `_Layout.cshtml`.

1. Efter senast `<li>` element i listan lägger du till följande HTML att lägga till en annan menyalternativet för navigering:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Kör programmet och välj **ladda upp blob**. Ordet *lyckades!* bör visas.
    
    ![Skärmbild av verifieringen lyckades](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Lista blobarna i en blob-behållare

Det här avsnittet visas hur du listar blobarna i en blob-behållare. Exemplet kod referenser i *test blobbehållare* skapade i avsnittet, [har en blobbehållare](#create-a-blob-container).

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas `ListBlobs` som returnerar en `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. I den `ListBlobs` metod, hämta en `CloudBlobContainer` objekt som representerar en referens till blobbehållaren. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Om du vill visa blobbar i en blobbehållare, använda den `CloudBlobContainer.ListBlobsSegmentedAsync` metoden. Den `CloudBlobContainer.ListBlobsSegmentedAsync` metoden returnerar en `BlobResultSegment`. Innehåller `IListBlobItem` objekt som kan typkonverteras till `CloudBlockBlob`, `CloudPageBlob`, eller `CloudBlobDirectory` objekt. Följande kodfragment räknar upp alla blobbar i en blob-behållare. Varje blob typkonverteras till lämpligt objekt, baserat på dess typ. Sitt namn (eller URI i fall med en `CloudBlobDirectory`) har lagts till i en lista.

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
    Följande visar den slutförda `ListBlobs` metoden:

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

1. I **Solution Explorer**, expandera den **vyer** mappen och högerklicka på **Blobar**.

2. På snabbmenyn väljer **Lägg till** > **visa**.

1. I den **Lägg till vy** dialogrutan anger `ListBlobs` för namn och välj **Lägg till**.

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

1. I **Solution Explorer**, expandera den **vyer** > **delad** och öppna `_Layout.cshtml`.

1. Efter senast `<li>` element i listan lägger du till följande HTML att lägga till en annan menyalternativet för navigering:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Kör programmet och välj **lista blobar** att se resultat som liknar följande skärmbild:
  
    ![Skärmbild av lista blobar](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Ladda ned blobbar

Det här avsnittet visas hur du laddar ned en blob. Du kan spara den till lokal lagring, eller så kan du läsa innehållet till en sträng. Exemplet kod referenser i *test blobbehållare* skapade i avsnittet, [har en blobbehållare](#create-a-blob-container).

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas `DownloadBlob` som returnerar en sträng.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. I den `DownloadBlob` metod, hämta en `CloudBlobContainer` objekt som representerar en referens till blobbehållaren.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hämta en referens för blob-objektet genom att anropa den `CloudBlobContainer.GetBlockBlobReference` metoden. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Ladda ned en blob genom att använda den `CloudBlockBlob.DownloadToStream` metoden. Följande kod överför en blob-innehållet till en stream-objektet. Objektet sparas sedan till en lokal fil. (Ändra  *&lt;lokala filnamn >* och det fullt kvalificerade namnet som representerar där bloben är hämtas.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Följande visar den slutförda `ListBlobs` metod (med en fullständigt kvalificerad sökväg för den lokala filen som skapas):
    
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

1. I **Solution Explorer**, expandera den **vyer** > **delad** och öppna `_Layout.cshtml`.

1. Efter senast `<li>` element i listan lägger du till följande HTML att lägga till en annan menyalternativet för navigering:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Kör programmet och välj **Download blob** ladda ned bloben. Den blob som anges i den `CloudBlobContainer.GetBlockBlobReference` metodanrop laddar ned till den plats som anges i den `File.OpenWrite` metodanrop. Texten *lyckades!* ska visas i webbläsaren. 

## <a name="delete-blobs"></a>Ta bort blobbar

Följande steg illustrerar hur du tar bort en blob:

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas `DeleteBlob` som returnerar en sträng.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. I den `DeleteBlob` metod, hämta en `CloudBlobContainer` objekt som representerar en referens till blobbehållaren.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Hämta en referens för blob-objektet genom att anropa den `CloudBlobContainer.GetBlockBlobReference` metoden. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Ta bort en blob genom att använda den `Delete` metoden.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    Den slutförda `DeleteBlob` metoden ska visas på följande sätt:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. I **Solution Explorer**, expandera den **vyer** > **delad** och öppna `_Layout.cshtml`.

1. Efter senast `<li>` element i listan lägger du till följande HTML att lägga till en annan menyalternativet för navigering:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Kör programmet och välj **ta bort blob** att ta bort den blob som anges i den `CloudBlobContainer.GetBlockBlobReference` metodanrop. Texten *lyckades!* ska visas i webbläsaren. Välj webbläsarens **tillbaka** och välj sedan **lista blobar** att kontrollera att bloben är inte längre i behållaren.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att lagra, lista och hämta blobar i Azure Storage med hjälp av ASP.NET Core. Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure Table storage och Visual Studio-anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Kom igång med Azure Queue storage och Visual Studio-anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
