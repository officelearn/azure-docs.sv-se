---
title: "Kom igång med Azure blob storage och Visual Studio anslutna tjänster (ASP.NET) | Microsoft Docs"
description: "Hur du kommer igång med Azure blob storage i ett ASP.NET-projekt i Visual Studio efter anslutning till ett lagringskonto med hjälp av Visual Studio anslutna Services"
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
ms.date: 12/21/2016
ms.author: kraig
ms.openlocfilehash: e953c7978705379a28581213e8f1c665473ddd60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Kom igång med Azure blob storage och Visual Studio anslutna tjänster (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt

Azure blob storage är en tjänst som lagrar Ostrukturerade data i molnet som objekt/blobbar. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring.

Den här kursen visar hur du skriver ASP.NET-kod för några vanliga scenarier med Azure blob storage. Scenarier som inkluderar en blob-behållare och ladda upp, lista, hämtar och tar bort blobbar.

##<a name="prerequisites"></a>Krav

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-konto](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Skapa en MVC-enhet 

1. I den **Solution Explorer**, högerklicka på **domänkontrollanter**, och på snabbmenyn Välj **Lägg till -> styrenhet**.

    ![Lägg till en domänkontrollant i en ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. På den **Lägg till Kodskelett** markerar **MVC 5 styrenhet – tom**, och välj **Lägg till**.

    ![Ange typ av MVC-domänkontrollant](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. På den **Lägg till styrenhet** dialogrutan namn styrenheten *BlobsController*, och välj **Lägg till**.

    ![Namnet på MVC-enhet](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Lägg till följande *med* direktiven till den `BlobsController.cs` filen:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>Skapa en blobbbehållare

En blob-behållare är en kapslad hierarki med blobbar och mappar. Följande steg visar hur du skapar en blob-behållare:

> [!NOTE]
> 
> Koden i det här avsnittet förutsätter att du har slutfört stegen i avsnittet [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas **CreateBlobContainer** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **CreateBlobContainer** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfigurationen. (Ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta en **CloudBlobClient** -objektet representerar en klient för blob-tjänsten.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Hämta en **CloudBlobContainer** objekt som representerar en referens till önskad blob-behållarnamn. Den **CloudBlobClient.GetContainerReference** inte gör en begäran mot blob storage. Referensen returneras oavsett blob-behållaren finns. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Anropa den **CloudBlobContainer.CreateIfNotExists** metod för att skapa behållaren om den inte finns ännu. Den **CloudBlobContainer.CreateIfNotExists** metoden returnerar **SANT** om behållaren finns inte och har skapats. Annars **FALSKT** returneras.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Uppdatering av **ViewBag** med namnet på blob-behållaren.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

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

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Kör programmet och välj **skapa Blob-behållaren** att se resultatet liknar följande Skärmdump:
  
    ![Skapa blob-behållare](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Som nämnts tidigare i **CloudBlobContainer.CreateIfNotExists** metoden returnerar **SANT** endast när behållaren finns inte och har skapats. Om du kör appen när behållaren finns metoden returnerar därför **FALSKT**. Om du vill köra appen flera gånger, måste du ta bort behållaren innan du kör appen igen. Ta bort behållaren kan göras den **CloudBlobContainer.Delete** metod. Du kan också ta bort behållaren med hjälp av den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040) eller [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="upload-a-blob-into-a-blob-container"></a>Ladda upp en blobb till en blob-behållare

När du har [skapas en blob-behållare](#create-a-blob-container), kan du överföra filer till behållaren. Det här avsnittet vägleder dig genom att ladda upp en lokal fil till en blob-behållare. Anvisningarna förutsätter att du har skapat en blobbbehållare med namnet *test blobbehållaren*. 

> [!NOTE]
> 
> Koden i det här avsnittet förutsätter att du har slutfört stegen i avsnittet [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas **UploadBlob** som returnerar en **EmptyResult**.

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. I den **UploadBlob** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta en **CloudBlobClient** -objektet representerar en klient för blob-tjänsten.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Hämta en **CloudBlobContainer** objekt som representerar en referens till blob-behållarnamn. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Som beskrivits tidigare stöder Azure storage olika blob-typer. Om du vill hämta en referens till en sidblob anropa den **CloudBlobContainer.GetPageBlobReference** metod. Om du vill hämta en referens till en blockblobb anropa den **CloudBlobContainer.GetBlockBlobReference** metod. Blockblob är vanligtvis den rekommenderade typen. (Ändra < blob-namn > * namnet som du vill ge blob överförs en gång.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. När du har en blobbreferens kan du överföra alla dataström till den genom att anropa blob-referensobjekt **UploadFromStream** metod. Den **UploadFromStream** metoden skapas blobben om den finns inte eller skrivs över om den finns. (Ändra  *&lt;till filuppladdning >* till en fullständigt kvalificerad sökväg till filen som du vill överföra.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **Blobbar**, och på snabbmenyn Välj **Lägg till -> Visa**.

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **överför blob**.  
  
Avsnittet - [visa blobbar i en blob-behållare](#list-the-blobs-in-a-blob-container) -visar hur du kan visa blobbar i en blob-behållare.    

## <a name="list-the-blobs-in-a-blob-container"></a>Visa blobbar i en blob-behållare

Det här avsnittet visar hur du kan visa blobbar i en blob-behållare. Exempel koden refererar till den *test blobbehållaren* skapade i avsnittet [skapa en blobbbehållare](#create-a-blob-container).

> [!NOTE]
> 
> Koden i det här avsnittet förutsätter att du har slutfört stegen i avsnittet [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas **ListBlobs** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **ListBlobs** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta en **CloudBlobClient** -objektet representerar en klient för blob-tjänsten.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Hämta en **CloudBlobContainer** objekt som representerar en referens till blob-behållarnamn. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Om du vill visa blobbar i en blob-behållare, Använd den **CloudBlobContainer.ListBlobs** metod. Den **CloudBlobContainer.ListBlobs** metoden returnerar en **IListBlobItem** objekt som du omvandlas till en **CloudBlockBlob**, **CloudPageBlob**, eller **CloudBlobDirectory** objekt. Följande kodstycke räknar upp alla blobbar i en blobbbehållare. Varje blobb omvandlas till lämpligt objekt baserat på dess typ och dess namn (eller URI i händelse av en **CloudBlobDirectory**) har lagts till i en lista.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
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

    Förutom blobbar, kan blob-behållare innehålla kataloger. Anta att du har en blobbbehållare som kallas *test blobbehållaren* med följande hierarki:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Med hjälp av föregående kodexempel i **blobbar** stränglista innehåller värden som liknar följande:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Som du kan se innehåller listan endast de översta entiteterna; inte de kapslade (*bar.png* och *baz.png*). Om du vill visa en lista över alla enheter i en blob-behållare, måste du anropa den **CloudBlobContainer.ListBlobs** metod och pass **SANT** för den **useFlatBlobListing** parameter.    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    Ange den **useFlatBlobListing** parameter till **SANT** returnerar en platt lista över alla entiteter i blob-behållaren och ger följande resultat:

        foo.png
        dir1/bar.png
        dir2/baz.png

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **Blobbar**, och på snabbmenyn Välj **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **ListBlobs** för namn och välj **Lägg till**.

1. Öppna `ListBlobs.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

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

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Kör programmet och välj **visa blobbar** att se resultatet liknar följande Skärmdump:
  
    ![BLOB-lista](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Ladda ned blobbar

Det här avsnittet beskriver hur du hämtar en blob och antingen spara den lokal lagring eller läsa in innehållet i en sträng. Exempel koden refererar till den *test blobbehållaren* skapade i avsnittet [skapa en blobbbehållare](#create-a-blob-container).

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas **DownloadBlob** som returnerar en **ActionResult**.

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. I den **DownloadBlob** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta en **CloudBlobClient** -objektet representerar en klient för blob-tjänsten.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Hämta en **CloudBlobContainer** objekt som representerar en referens till blob-behållarnamn. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Hämta en referens för blob-objektet genom att anropa **CloudBlobContainer.GetBlockBlobReference** eller **CloudBlobContainer.GetPageBlobReference** metod. (Ändra  *&lt;blob-name >* hämtar namnet på blob.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Du kan hämta en blob den **CloudBlockBlob.DownloadToStream** eller **CloudPageBlob.DownloadToStream** metod beroende på blob-datatyp. I följande kod fragment används den **CloudBlockBlob.DownloadToStream** metod för att överföra en blob-innehåll till en stream-objektet som sparas sedan till en lokal fil: (ändra  *&lt;lokala filnamn >* till filen fullständigt kvalificerade namnet som representerar där du vill att blob hämtas.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **Download blob** att hämta blobben. Blob som anges i den **CloudBlobContainer.GetBlockBlobReference** metodanrop filhämtningar till den plats du anger i den **File.OpenWrite** metodanrop. 

## <a name="delete-blobs"></a>Ta bort blobbar

Följande steg visar hur du tar bort en blobb:

> [!NOTE]
> 
> Koden i det här avsnittet förutsätter att du har slutfört stegen i avsnittet [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `BlobsController.cs`.

1. Lägg till en metod som kallas **DeleteBlob** som returnerar en **ActionResult**.

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. Hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta en **CloudBlobClient** -objektet representerar en klient för blob-tjänsten.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Hämta en **CloudBlobContainer** objekt som representerar en referens till blob-behållarnamn. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Hämta en referens för blob-objektet genom att anropa **CloudBlobContainer.GetBlockBlobReference** eller **CloudBlobContainer.GetPageBlobReference** metod. (Ändra  *&lt;blob-name >* till namnet på blob som tas bort.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Kör programmet och välj **ta bort blob** att ta bort blobben som anges i den **CloudBlobContainer.GetBlockBlobReference** metodanrop. 

## <a name="next-steps"></a>Nästa steg
Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure-tabellagring och Visual Studio anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Kom igång med Azure queue storage- och Visual Studio anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
