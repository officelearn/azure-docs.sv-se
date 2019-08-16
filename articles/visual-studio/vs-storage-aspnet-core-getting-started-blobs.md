---
title: Kom igång med Azure Blob Storage och anslutna Visual Studio-tjänster (ASP.NET Core) | Microsoft Docs
description: Komma igång med Azure Blob Storage i ett ASP.NET Core-projekt i Visual Studio, efter att ha anslutit till ett lagrings konto med hjälp av Visual Studio Connected Services
services: storage
documentationcenter: ''
author: ghogen
manager: jillfra
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: ghogen
ms.openlocfilehash: 8ae82548d4976e505dae055445c71a5c12cbf263
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516162"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Kom igång med Azure Blob Storage och anslutna Visual Studio-tjänster (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob Storage är en tjänst som lagrar ostrukturerade data i molnet som objekt eller blobbar. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring. Mer information om Blob Storage finns i [Introduktion till Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md).

Den här självstudien visar hur du skriver ASP.NET Core kod för några vanliga scenarier som använder Blob Storage. Scenarier innefattar att skapa en BLOB-behållare och ladda upp, lista, ladda ned och ta bort blobbar.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Det här avsnittet beskriver hur du konfigurerar utvecklings miljön. Detta innefattar att skapa en ASP.NET modell – MVC-app (View-Controller), lägga till en anslutning för anslutna tjänster, lägga till en kontroll enhet och ange nödvändiga direktiv för namn områden.

### <a name="create-an-aspnet-mvc-app-project"></a>Skapa ett ASP.NET MVC-appaket

1. Öppna Visual Studio.

1. Från huvud menyn väljer du **fil** > **nytt** > **projekt**.

1. I dialog rutan **nytt projekt** väljer du **webb** > **ASP.net Core webb program** > **AspNetCoreStorage**. Välj sedan **OK**.

    ![Skärm bild av dialog rutan nytt projekt i Visual Studio](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. I dialog rutan **ny ASP.net Core webbapp** väljer du **.net Core** > **ASP.net Core 2,0** >  **-webb program (modell-Visa-kontrollant)** . Välj sedan **OK**.

    ![Skärm bild av dialog rutan ny ASP.NET Core webb program](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Använda anslutna tjänster för att ansluta till ett Azure Storage-konto

1. Högerklicka på projektet i **Solution Explorer**.

2. I snabb menyn väljer du **Lägg till** > **ansluten tjänst**.

1. I dialog rutan **anslutna tjänster** väljer du **moln lagring med Azure Storage**och väljer sedan **Konfigurera**.

    ![Skärm bild av dialog rutan anslutna tjänster](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. I dialog rutan **Azure Storage** väljer du det Azure Storage-konto som ska användas för den här självstudien. Om du vill skapa ett nytt Azure Storage-konto väljer du **skapa ett nytt lagrings konto**och fyller i formuläret. När du har valt ett befintligt lagrings konto eller skapat ett nytt, väljer du **Lägg till**. Visual Studio installerar NuGet-paketet för Azure Storage och en lagrings anslutnings sträng till **appSettings. JSON**.

> [!TIP]
> Information om hur du skapar ett lagrings konto med [Azure Portal](https://portal.azure.com)finns i [skapa ett lagrings konto](../storage/common/storage-quickstart-create-account.md).
>
> Du kan också skapa ett lagrings konto med hjälp av [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md)eller [Azure Cloud Shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Skapa en MVC-kontrollant 

1. Högerklicka på **kontrollanter**i **Solution Explorer**.

2. I snabb menyn väljer du **Lägg till** > **kontrollant**.

    ![Skärm bild av Solution Explorer](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. I dialog rutan **Lägg till Autogenerera** väljer du **MVC-kontrollant-tom**och väljer **Lägg till**.

    ![Skärm bild av dialog rutan Lägg till Autogenerera](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. I dialog rutan **Lägg till Tom MVC-styrenhet** namnger du kontrollantens *BlobsController*och väljer **Lägg till**.

    ![Skärm bild av dialog rutan Lägg till Tom MVC-styrenhet](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Lägg till följande `using` direktiv `BlobsController.cs` i filen:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Anslut till ett lagrings konto och få en behållar referens

En BLOB-behållare är en kapslad hierarki med blobbar och mappar. Resten av stegen i det här dokumentet kräver en referens till en BLOB-behållare, så att koden ska placeras i sin egen metod för åter användning.

Följande steg skapar en metod för att ansluta till lagrings kontot med hjälp av anslutnings strängen i **appSettings. JSON**. Stegen skapar också en referens till en behållare. Inställningen för anslutnings strängen i **appSettings. JSON** heter med formatet `<storageaccountname>_AzureStorageConnectionString`. 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod med namnet **GetCloudBlobContainer** som returnerar en **CloudBlobContainer**. Se till att ersätta `<storageaccountname>_AzureStorageConnectionString` med det faktiska namnet på nyckeln i **Web. config**.
    
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
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
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
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. I **Solution Explorer**högerklickar du på mappen **vyer** .

2. I snabb menyn väljer du **Lägg till** > **ny mapp**. Namnge den nya Folder- *blobben*. 

1. I **Solution Explorer**expanderar du mappen **vyer** och högerklickar på **blobbar**.

4. Välj **Lägg till** > **vy**från snabb menyn.

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

1. Leta efter den osorterade listan som ser ut så här: `<ul class="nav navbar-nav">`.  Efter det sista `<li>` elementet i listan lägger du till följande HTML för att lägga till ett annat navigerings meny alternativ:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Kör programmet och välj **skapa BLOB-behållare** för att se resultat som liknar följande skärm bild:
  
    ![Skärm bild av skapa BLOB-behållare](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

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
        blob.UploadFromStreamAsync(fileStream).Wait();
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
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. I **Solution Explorer**, expanderar > du den**delade** mappen vyer och `_Layout.cshtml`öppnar.

1. Efter det sista `<li>` elementet i listan lägger du till följande HTML för att lägga till ett annat navigerings meny alternativ:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Kör programmet och välj **Ladda upp BLOB**. Ordet *lyckades!* ska visas.
    
    ![Skärm bild av lyckad verifiering](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
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
   
1. Om du vill visa blobarna i en BLOB-behållare använder `CloudBlobContainer.ListBlobsSegmentedAsync` du-metoden. Metoden returnerar en `BlobResultSegment`. `CloudBlobContainer.ListBlobsSegmentedAsync` Innehåller `IListBlobItem` objekt som kan omvandlas till `CloudBlockBlob`-, `CloudPageBlob`-eller `CloudBlobDirectory` -objekt. Följande kodfragment räknar upp alla blobar i en BLOB-behållare. Varje Blob omvandlas till lämpligt objekt, baserat på dess typ. Dess namn (eller URI i fallet med `CloudBlobDirectory`) läggs till i en lista.

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

1. Efter det sista `<li>` elementet i listan lägger du till följande HTML för att lägga till ett annat navigerings meny alternativ:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Kör programmet och välj **list blobbar** för att se resultat som liknar följande skärm bild:
  
    ![Skärm bild av list blobbar](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

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
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Nedan visas den slutförda `ListBlobs` metoden (med en fullständigt kvalificerad sökväg för den lokala filen som skapas):
    
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

1. I **Solution Explorer**, expanderar > du den**delade** mappen vyer och `_Layout.cshtml`öppnar.

1. Efter det sista `<li>` elementet i listan lägger du till följande HTML för att lägga till ett annat navigerings meny alternativ:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Kör programmet och välj **Ladda ned BLOB** för att ladda ned blobben. Den blob som anges i `CloudBlobContainer.GetBlockBlobReference` metod anropet hämtas till den plats som anges `File.OpenWrite` i metod anropet. Texten *lyckades!* ska visas i webbläsaren. 

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
    blob.DeleteAsync().Wait();
    ```
    
    Den färdiga `DeleteBlob` metoden bör se ut så här:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. I **Solution Explorer**, expanderar > du den**delade** mappen vyer och `_Layout.cshtml`öppnar.

1. Efter det sista `<li>` elementet i listan lägger du till följande HTML för att lägga till ett annat navigerings meny alternativ:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Kör programmet och välj **ta bort BLOB** för att ta bort den blob som anges `CloudBlobContainer.GetBlockBlobReference` i metod anropet. Texten *lyckades!* ska visas i webbläsaren. Välj knappen **bakåt** i webbläsaren och välj sedan **list blobbar** för att kontrol lera att blobben inte längre finns i behållaren.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att lagra, lista och hämta blobbar i Azure Storage med hjälp av ASP.NET Core. Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure Table Storage och ASP.NET (Visual Studio Connected Services)](vs-storage-aspnet-getting-started-tables.md)
  * [Kom igång med Azure Queue Storage och Visual Studio Connected Services (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
