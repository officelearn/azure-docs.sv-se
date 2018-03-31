---
title: Kom igång med Azure queue storage- och Visual Studio anslutna tjänster (ASP.NET) | Microsoft Docs
description: Hur du kommer igång med Azure queue storage i ASP.NET-projekt i Visual Studio efter anslutning till ett lagringskonto med hjälp av Visual Studio anslutna Services
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 89baa741dcd916b70514f9703255b9e6d01c689b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Kom igång med Azure queue storage- och Visual Studio anslutna tjänster (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt

Azure queue storage erbjuder molntjänster meddelandehantering mellan programkomponenter. När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Den här kursen visar hur du skriver ASP.NET-kod för några vanliga scenarier med hjälp av Azure queue storage entiteter. Dessa scenarier som inkluderar vanliga aktiviteter som att skapa en Azure-kö och lägga till, ändra, läsa och tar bort Kömeddelanden.

##<a name="prerequisites"></a>Förutsättningar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-konto](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Skapa en MVC-enhet 

1. I den **Solution Explorer**, högerklicka på **domänkontrollanter**, och på snabbmenyn Välj **Lägg till -> styrenhet**.

    ![Lägg till en domänkontrollant i en ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. På den **Lägg till Kodskelett** markerar **MVC 5 styrenhet – tom**, och välj **Lägg till**.

    ![Ange typ av MVC-domänkontrollant](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. På den **Lägg till styrenhet** dialogrutan namn styrenheten *QueuesController*, och välj **Lägg till**.

    ![Namnet på MVC-enhet](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Lägg till följande *med* direktiven till den `QueuesController.cs` filen:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Skapa en kö

Följande steg visar hur du skapar en kö:

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`. 

1. Lägg till en metod som kallas **CreateQueue** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I den **CreateQueue** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en **CloudQueueClient** -objektet representerar en queue-tjänstklienten.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Hämta en **CloudQueue** objekt som representerar en referens till önskad kö-namn. Den **CloudQueueClient.GetQueueReference** inte gör en begäran mot queue storage. Referensen returneras om kön finns eller inte. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa den **CloudQueue.CreateIfNotExists** metod för att skapa kön om det inte finns ännu. Den **CloudQueue.CreateIfNotExists** metoden returnerar **SANT** om kön finns inte och har skapats. Annars **FALSKT** returneras.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Uppdatering av **ViewBag** med namnet på kön.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **CreateQueue** för namn och välj **Lägg till**.

1. Öppna `CreateQueue.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Kör programmet och välj **Skapa kö** att se resultatet liknar följande Skärmdump:
  
    ![Skapa kö](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Som nämnts tidigare i **CloudQueue.CreateIfNotExists** metoden returnerar **SANT** endast när kön finns inte och har skapats. Om du kör appen när kön finns, metoden returnerar därför **FALSKT**. Om du vill köra appen flera gånger, måste du ta bort kön innan du kör appen igen. Ta bort kön kan göras den **CloudQueue.Delete** metod. Du kan också ta bort en kö med hjälp av den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040) eller [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande till en kö

När du har [skapade en kö](#create-a-queue), du kan lägga till meddelanden till kön. Det här avsnittet vägleder dig genom att lägga till ett meddelande till en kö *test-kön*. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **AddMessage** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **AddMessage** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
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

1. Skapa den **CloudQueueMessage** objekt som representerar det meddelande som du vill lägga till i kön. En **CloudQueueMessage** objekt kan skapas från en sträng (i UTF-8-format) eller en byte-matris.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Anropa den **CloudQueue.AddMessage** metod för att lägga till den messaged i kön.

    ```csharp
    queue.AddMessage(message);
    ```

1. Skapa och ange några **ViewBag** egenskaper som ska visas i vyn.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **AddMessage** för namn och välj **Lägg till**.

1. Öppna `AddMessage.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Kör programmet och välj **Lägg till meddelandet** att se resultatet liknar följande Skärmdump:
  
    ![Lägga till meddelande](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Två avsnitt - [läsa ett meddelande från en kö utan att ta bort den](#read-a-message-from-a-queue-without-removing-it) och [Läs- och ta bort ett meddelande från en kö](#read-and-remove-a-message-from-a-queue) -illustrerar hur du kan läsa meddelanden från en kö.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Läsa ett meddelande från en kö utan att ta bort den

Det här avsnittet visar hur du kan kika på meddelandet i kön (läsa det första meddelandet inte ta bort).  

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **PeekMessage** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **PeekMessage** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
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

1. Anropa den **CloudQueue.PeekMessage** metod för att läsa det första meddelandet i kön utan att ta bort den från kön. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Uppdatering av **ViewBag** med två värden: könamnet och meddelandet som lästes. Den **CloudQueueMessage** objektet innehåller två egenskaper för att hämta objektets värde: **CloudQueueMessage.AsBytes** och **CloudQueueMessage.AsString**. **AsString** (används i det här exemplet) returnerar en sträng, medan **AsBytes** returnerar en bytematris.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **PeekMessage** för namn och välj **Lägg till**.

1. Öppna `PeekMessage.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

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

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Kör programmet och välj **titt meddelandet** att se resultatet liknar följande Skärmdump:
  
    ![Granska meddelande](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Läsa och ta bort ett meddelande från en kö

I det här avsnittet lär du dig läsa och ta bort ett meddelande från en kö.   

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **ReadMessage** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **ReadMessage** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
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

1. Anropa den **CloudQueue.GetMessage** metod för att läsa det första meddelandet i kön. Den **CloudQueue.GetMessage** metoden gör meddelandet osynligt i 30 sekunder (som standard) till andra meddelanden som läser så att ingen annan kod kan ändra eller ta bort meddelandet när din bearbeta denna kod. Om du vill ändra hur lång tid som meddelandet är osynliga ändra den **visibilityTimeout** parameter som skickas till den **CloudQueue.GetMessage** metod.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Anropa den **CloudQueueMessage.Delete** metod för att ta bort meddelandet från kön.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Uppdatering av **ViewBag** med meddelandet tas bort och namnet på kön.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **ReadMessage** för namn och välj **Lägg till**.

1. Öppna `ReadMessage.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

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

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Kör programmet och välj **läsa/ta bort meddelandet** att se resultatet liknar följande Skärmdump:
  
    ![Läsa och ta bort meddelandet](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Hämta kölängden

Det här avsnittet visar hur du kan hämta kölängden (antal meddelanden). 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **GetQueueLength** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **ReadMessage** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
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

6. Åtkomst till den **CloudQueue.ApproximateMessageCount** egenskapen för att hämta köns längd.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Uppdatering av **ViewBag** med namnet på kön och dess längd.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **GetQueueLength** för namn och välj **Lägg till**.

1. Öppna `GetQueueLengthMessage.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Kör programmet och välj **hämta kölängden** att se resultatet liknar följande Skärmdump:
  
    ![Hämta kölängden](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Ta bort en kö
Det här avsnittet visas hur du tar bort en kö. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **DeleteQueue** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I den **DeleteQueue** metod, hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använda följande kod för att hämta anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration: (ändra  *&lt;behållarens kontonamn >* till namnet på Azure storage-konto du försöker komma åt.)
   
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

1. Uppdatering av **ViewBag** med namnet på kön och dess längd.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. I den **Solution Explorer**, expandera den **vyer** mappen, högerklicka på **köer**, och på snabbmenyn väljer **Lägg till -> Visa**.

1. På den **Lägg till vy** dialogrutan Ange **DeleteQueue** för namn och välj **Lägg till**.

1. Öppna `DeleteQueue.cshtml`, och ändra den så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. I den **Solution Explorer**, expandera den **vyer -> delade** och öppna `_Layout.cshtml`.

1. Efter senast **Html.ActionLink**, Lägg till följande **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Kör programmet och välj **hämta kölängden** att se resultatet liknar följande Skärmdump:
  
    ![Ta bort kö](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Nästa steg
Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure blob storage och Visual Studio anslutna tjänster (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Kom igång med Azure-tabellagring och Visual Studio anslutna tjänster (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
