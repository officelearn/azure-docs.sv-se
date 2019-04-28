---
title: Kom igång med Azure queue storage och Visual Studio anslutna tjänster (ASP.NET) | Microsoft Docs
description: Hur du kommer igång med Azure queue storage i ett ASP.NET-projekt i Visual Studio när du har anslutit till ett lagringskonto med hjälp av Visual Studio Connected Services
services: storage
author: ghogen
manager: douge
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 3f3a649308449a8ba4e1dbaebd4db4afbf731c5b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110493"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Kom igång med Azure queue storage och Visual Studio anslutna tjänster (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt

Azure queue storage innehåller molnmeddelandehantering mellan programkomponenter. När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Den här självstudiekursen visar hur du skriver ASP.NET-kod för några vanliga scenarier med hjälp av Azure queue storage-entiteter. De här scenarierna omfattar vanliga åtgärder, till exempel skapa en Azure-kö och att lägga till, ändra, läsa och ta bort Kömeddelanden.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-konto](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Skapa ett MVC-kontrollant 

1. I den **Solution Explorer**, högerklicka på **domänkontrollanter**, och välj sedan från snabbmenyn **Lägg till -> Controller**.

    ![Lägg till en domänkontrollant i en ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. På den **Lägg till Kodskelett** dialogrutan **MVC 5 styrenhet – tom**, och välj **Lägg till**.

    ![Ange typ av MVC-domänkontrollant](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. På den **Lägg till styrenhet** dialogrutan namn kontrollanten *QueuesController*, och välj **Lägg till**.

    ![Namn på MVC-kontrollant](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Lägg till följande *med* direktiv den `QueuesController.cs` fil:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Skapa en kö

Följande steg illustrerar hur du skapar en kö:

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [ställer in utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`. 

1. Lägg till en metod som kallas **CreateQueue** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **CreateQueue** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudQueueClient** -objektet representerar en queue-tjänstklienten.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Hämta en **CloudQueue** objekt som representerar en referens till önskad könamnet. Den **CloudQueueClient.GetQueueReference** inte gör en begäran mot kölagring. Referensen returneras eller inte finns i kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa den **CloudQueue.CreateIfNotExists** metod för att skapa kön om det inte finns ännu. Den **CloudQueue.CreateIfNotExists** metoden returnerar **SANT** om kön finns inte och har skapats. I annat fall **FALSKT** returneras.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Uppdatera den **ViewBag** med namnet på kön.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **CreateQueue** för namn och välj **Lägg till**.

1. Öppna `CreateQueue.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Kör programmet och välj **Skapa kö** att se resultat som liknar följande skärmbild:
  
    ![Skapa kö](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Som nämnts tidigare i **CloudQueue.CreateIfNotExists** metoden returnerar **SANT** endast när kön inte finns och har skapats. Därför, om du kör appen när kön finns, returnerar metoden **FALSKT**. För att köra appen flera gånger, måste du ta bort kön innan du kör appen igen. Ta bort kön kan ske via den **CloudQueue.Delete** metod. Du kan också ta bort en kö med hjälp av den [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040) eller [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Lägg till ett meddelande till en kö

När du har [skapat en kö](#create-a-queue), du kan lägga till meddelanden i kön. Det här avsnittet beskriver hur du lägger till ett meddelande i en kö *test-kön*. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [ställer in utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **AddMessage** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **AddMessage** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta en **CloudQueueClient** -objektet representerar en queue-tjänstklienten.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta en **CloudQueueContainer** objekt som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Skapa den **CloudQueueMessage** objekt som representerar det meddelande du vill lägga till i kön. En **CloudQueueMessage** objekt kan skapas från en sträng (i UTF-8-format) eller en bytematris.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Anropa den **CloudQueue.AddMessage** metod för att lägga till den messaged i kön.

    ```csharp
    queue.AddMessage(message);
    ```

1. Skapa och ange några **ViewBag** egenskaper för visning i vyn.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **AddMessage** för namn och välj **Lägg till**.

1. Öppna `AddMessage.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Kör programmet och välj **Lägg till meddelande** att se resultat som liknar följande skärmbild:
  
    ![Lägg till meddelande](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

De två avsnitten - [läsa ett meddelande från en kö utan att ta bort den](#read-a-message-from-a-queue-without-removing-it) och [Läs- och ta bort ett meddelande från en kö](#read-and-remove-a-message-from-a-queue) -illustrerar hur du kan läsa meddelanden från en kö.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Läsa ett meddelande från en kö utan att ta bort den

Det här avsnittet visar hur du kan kika på meddelandet i kö (läsa det första meddelandet utan att ta bort det).  

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [ställer in utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **PeekMessage** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **PeekMessage** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta en **CloudQueueClient** -objektet representerar en queue-tjänstklienten.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta en **CloudQueueContainer** objekt som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa den **CloudQueue.PeekMessage** metod för att läsa det första meddelandet i kön utan att ta bort det från kön. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Uppdatera den **ViewBag** med två värden: namnet på kön och meddelandet som lästes. Den **CloudQueueMessage** objektet innehåller två egenskaper för att hämta objektets värde: **CloudQueueMessage.AsBytes** och **CloudQueueMessage.AsString**. **AsString** (används i det här exemplet) returnerar en sträng, medan **AsBytes** returnerar en byte-matris.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **PeekMessage** för namn och välj **Lägg till**.

1. Öppna `PeekMessage.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Kör programmet och välj **Peek meddelande** att se resultat som liknar följande skärmbild:
  
    ![Granska meddelande](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Läsa och ta bort ett meddelande från en kö

I det här avsnittet lär du dig att läsa och ta bort ett meddelande från en kö.   

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [ställer in utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **ReadMessage** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **ReadMessage** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta en **CloudQueueClient** -objektet representerar en queue-tjänstklienten.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta en **CloudQueueContainer** objekt som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa den **CloudQueue.GetMessage** metod för att läsa det första meddelandet i kön. Den **CloudQueue.GetMessage** metoden gör meddelandet osynligt i 30 sekunder (som standard) till all kod som läser meddelanden så att ingen annan kod kan ändra eller ta bort meddelandet vid din bearbetning av den. Om du vill ändra hur lång tid som meddelandet är osynligt, ändra den **visibilityTimeout** parameter som skickas till den **CloudQueue.GetMessage** metod.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Anropa den **CloudQueueMessage.Delete** metod för att ta bort meddelandet från kön.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Uppdatera den **ViewBag** med meddelandet tas bort och namnet på kön.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **ReadMessage** för namn och välj **Lägg till**.

1. Öppna `ReadMessage.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Kör programmet och välj **läsa/ta bort meddelandet** att se resultat som liknar följande skärmbild:
  
    ![Läsa och ta bort meddelande](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Hämta kölängden

Det här avsnittet visar hur du kan hämta kölängden (antal meddelanden). 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [ställer in utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **GetQueueLength** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **ReadMessage** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta en **CloudQueueClient** -objektet representerar en queue-tjänstklienten.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta en **CloudQueueContainer** objekt som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa den **CloudQueue.FetchAttributes** metod för att hämta köattributen (inklusive dess längd). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Åtkomst till den **CloudQueue.ApproximateMessageCount** egenskapen för att hämta köns längd.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Uppdatera den **ViewBag** med namnet på kön och hur långt.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **GetQueueLength** för namn och välj **Lägg till**.

1. Öppna `GetQueueLengthMessage.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Kör programmet och välj **hämta kölängden** att se resultat som liknar följande skärmbild:
  
    ![Hämta kölängden](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Ta bort en kö
Det här avsnittet visas hur du tar bort en kö. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [ställer in utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **DeleteQueue** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **DeleteQueue** metod, hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service: (Ändra  *&lt;lagringskontonamn->* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta en **CloudQueueClient** -objektet representerar en queue-tjänstklienten.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta en **CloudQueueContainer** objekt som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa den **CloudQueue.Delete** metod för att ta bort den kö som representeras av den **CloudQueue** objekt.

    ```csharp
    queue.Delete();
    ```

1. Uppdatera den **ViewBag** med namnet på kön och hur långt.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. I den **Solution Explorer**, expandera den **vyer** mapp, högerklickar du på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **DeleteQueue** för namn och välj **Lägg till**.

1. Öppna `DeleteQueue.cshtml`, och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delad** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Kör programmet och välj **hämta kölängden** att se resultat som liknar följande skärmbild:
  
    ![Ta bort kö](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Nästa steg
Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure blob storage och Visual Studio anslutna tjänster (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Kom igång med Azure table storage och Visual Studio anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
