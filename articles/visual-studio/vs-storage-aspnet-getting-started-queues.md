---
title: Kom igång med Azure Queue Storage och Visual Studio Connected Services (ASP.NET) | Microsoft Docs
description: Komma igång med Azure Queue Storage i ett ASP.NET-projekt i Visual Studio efter anslutning till ett lagrings konto med hjälp av Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 19cf2dd912968d0a5df8743c1e720776b8a949f0
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515982"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Kom igång med Azure Queue Storage och Visual Studio Connected Services (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt

Azure Queue Storage tillhandahåller moln meddelanden mellan program komponenter. När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Den här självstudien visar hur du skriver ASP.NET-kod för några vanliga scenarier med hjälp av Azure Queue Storage-entiteter. Dessa scenarier omfattar vanliga uppgifter som att skapa en Azure-kö och lägga till, ändra, läsa och ta bort Kömeddelanden.

## <a name="prerequisites"></a>Förutsättningar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-konto](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Skapa en MVC-kontrollant 

1. I **Solution Explorer**högerklickar du på **kontrollanter**och väljer **Lägg till > kontrollant**på snabb menyn.

    ![Lägga till en kontrollant i en ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. I dialog rutan **Lägg till Autogenerera** väljer du **MVC 5 Controller-Töm**och väljer **Lägg till**.

    ![Ange typ av MVC-kontrollant](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. I dialog rutan **Lägg till kontrollant** namnger du kontrollantens *QueuesController*och väljer **Lägg till**.

    ![Namnge MVC-kontrollanten](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Lägg till följande *med hjälp av* direktiv `QueuesController.cs` i filen:

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
> Det här avsnittet förutsätter att du har slutfört stegen i [utvecklings miljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`. 

1. Lägg till en metod med namnet **CreateQueue** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I **CreateQueue** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudQueueClient** -objekt representerar en Queue Service-klient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Hämta ett **CloudQueue** -objekt som representerar en referens till önskat könamn. Metoden **CloudQueueClient. GetQueueReference** gör ingen begäran mot Queue Storage. Referensen returneras oavsett om kön finns eller inte. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa metoden **CloudQueue. CreateIfNotExists** för att skapa kön om den inte redan finns. Metoden **CloudQueue. CreateIfNotExists** returnerar **True** om kön inte finns och har skapats. Annars returneras **false** .    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Uppdatera **ViewBag** med namnet på kön.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. I **Solution Explorer**expanderar du mappen **vyer** , högerklickar på **köer**och på snabb menyn väljer du **Lägg till > vy**.

1. I dialog rutan **Lägg till vy** anger du **CreateQueue** som namn på vyn och väljer **Lägg till**.

1. Öppna `CreateQueue.cshtml`och ändra det så att det ser ut som i följande kodfragment:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Kör programmet och välj **skapa kö** för att se resultat som liknar följande skärm bild:
  
    ![Skapa kö](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Som tidigare nämnts returnerar metoden **CloudQueue. CreateIfNotExists** endast **True** när kön inte finns och skapas. Om du kör appen när kön finns returnerar metoden **falskt**. Om du vill köra appen flera gånger måste du ta bort kön innan du kör appen igen. Borttagning av kön kan göras via metoden **CloudQueue. Delete** . Du kan också ta bort kön med hjälp av [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) eller [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande i en kö

När du har [skapat en kö](#create-a-queue)kan du lägga till meddelanden i kön. Det här avsnittet vägleder dig genom att lägga till ett meddelande i en kö *-testkö*. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [utvecklings miljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod med namnet **AddMessage** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I **AddMessage** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta ett **CloudQueueClient** -objekt representerar en Queue Service-klient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta ett **CloudQueueContainer** -objekt som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Skapa **CloudQueueMessage** -objektet som representerar det meddelande som du vill lägga till i kön. Ett **CloudQueueMessage** -objekt kan skapas antingen från en sträng (i UTF-8-format) eller en byte mat ris.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Anropa metoden **CloudQueue. AddMessage** för att lägga till meddelandet i kön.

    ```csharp
    queue.AddMessage(message);
    ```

1. Skapa och ange ett par **ViewBag** -egenskaper som ska visas i vyn.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. I **Solution Explorer**expanderar du mappen **vyer** , högerklickar på **köer**och på snabb menyn väljer du **Lägg till > vy**.

1. I dialog rutan **Lägg till vy** anger du **AddMessage** som namn på vyn och väljer **Lägg till**.

1. Öppna `AddMessage.cshtml`och ändra det så att det ser ut som i följande kodfragment:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Kör programmet och välj **Lägg till meddelande** för att se resultat som liknar följande skärm bild:
  
    ![Lägg till meddelande](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

De två avsnitten – [läsa ett meddelande från en kö utan att ta bort det](#read-a-message-from-a-queue-without-removing-it) och [läsa och ta bort ett meddelande från en kö](#read-and-remove-a-message-from-a-queue) – visar hur du läser meddelanden från en kö.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Läsa ett meddelande från en kö utan att ta bort det

Det här avsnittet beskriver hur du tittar på ett köat meddelande (Läs det första meddelandet utan att ta bort det).  

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [utvecklings miljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod med namnet **PeekMessage** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I **PeekMessage** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta ett **CloudQueueClient** -objekt representerar en Queue Service-klient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta ett **CloudQueueContainer** -objekt som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa metoden **CloudQueue. PeekMessage** för att läsa det första meddelandet i kön utan att ta bort det från kön. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Uppdatera **ViewBag** med två värden: könamnet och meddelandet som lästes. **CloudQueueMessage** -objektet exponerar två egenskaper för att hämta objektets värde: **CloudQueueMessage.** reCloudQueueMessages och **. String**. **Sträng** (används i det här exemplet) returnerar en sträng, medan ombyte returnerar en byte mat ris.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. I **Solution Explorer**expanderar du mappen **vyer** , högerklickar på **köer**och på snabb menyn väljer du **Lägg till > vy**.

1. I dialog rutan **Lägg till vy** anger du **PeekMessage** som namn på vyn och väljer **Lägg till**.

1. Öppna `PeekMessage.cshtml`och ändra det så att det ser ut som i följande kodfragment:

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

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Kör programmet och välj gransknings **meddelande** för att se resultat som liknar följande skärm bild:
  
    ![Granska meddelande](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Läsa och ta bort ett meddelande från en kö

I det här avsnittet får du lära dig hur du läser och tar bort ett meddelande från en kö.   

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [utvecklings miljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod med namnet **ReadMessage** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I **ReadMessage** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta ett **CloudQueueClient** -objekt representerar en Queue Service-klient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta ett **CloudQueueContainer** -objekt som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa metoden **CloudQueue. GetMessage** för att läsa det första meddelandet i kön. Metoden **CloudQueue. GetMessage** gör meddelandet osynligt i 30 sekunder (som standard) till alla andra kod läsnings meddelanden så att ingen annan kod kan ändra eller ta bort meddelandet medan den bearbetas. Om du vill ändra hur lång tid meddelandet är osynligt ändrar du parametern **visibilityTimeout** som skickas till metoden **CloudQueue. GetMessage** .

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Anropa metoden **CloudQueueMessage. Delete** för att ta bort meddelandet från kön.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Uppdatera **ViewBag** med meddelandet Borttaget och köns namn.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. I **Solution Explorer**expanderar du mappen **vyer** , högerklickar på **köer**och på snabb menyn väljer du **Lägg till > vy**.

1. I dialog rutan **Lägg till vy** anger du **ReadMessage** som namn på vyn och väljer **Lägg till**.

1. Öppna `ReadMessage.cshtml`och ändra det så att det ser ut som i följande kodfragment:

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

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Kör programmet och välj **Läs/ta bort meddelande** för att se resultat som liknar följande skärm bild:
  
    ![Läsa och ta bort meddelande](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Hämta kölängden

I det här avsnittet visas hur du hämtar Kölängd (antal meddelanden). 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [utvecklings miljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod med namnet **GetQueueLength** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I **ReadMessage** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta ett **CloudQueueClient** -objekt representerar en Queue Service-klient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta ett **CloudQueueContainer** -objekt som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa metoden **CloudQueue. FetchAttributes** för att hämta köns attribut (inklusive dess längd). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Använd egenskapen **CloudQueue. ApproximateMessageCount** för att hämta köns längd.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Uppdatera **ViewBag** med namnet på kön och dess längd.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. I **Solution Explorer**expanderar du mappen **vyer** , högerklickar på **köer**och på snabb menyn väljer du **Lägg till > vy**.

1. I dialog rutan **Lägg till vy** anger du **GetQueueLength** som namn på vyn och väljer **Lägg till**.

1. Öppna `GetQueueLengthMessage.cshtml`och ändra det så att det ser ut som i följande kodfragment:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Kör programmet och välj **Hämta Kölängd** för att se resultat som liknar följande skärm bild:
  
    ![Hämta Kölängd](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Ta bort en kö
I det här avsnittet visas hur du tar bort en kö. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen i [utvecklings miljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod med namnet **DeleteQueue** som returnerar en **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I **DeleteQueue** -metoden hämtar du ett **CloudStorageAccount** -objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen: (Ändra  *&lt;lagrings konto-namn >* till namnet på det Azure Storage-konto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta ett **CloudQueueClient** -objekt representerar en Queue Service-klient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta ett **CloudQueueContainer** -objekt som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa metoden **CloudQueue. Delete** för att ta bort kön som representeras av **CloudQueue** -objektet.

    ```csharp
    queue.Delete();
    ```

1. Uppdatera **ViewBag** med namnet på kön och dess längd.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. I **Solution Explorer**expanderar du mappen **vyer** , högerklickar på **köer**och på snabb menyn väljer du **Lägg till > vy**.

1. I dialog rutan **Lägg till vy** anger du **DeleteQueue** som namn på vyn och väljer **Lägg till**.

1. Öppna `DeleteQueue.cshtml`och ändra det så att det ser ut som i följande kodfragment:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. Expandera mappen **vyer-> delad** i `_Layout.cshtml` **Solution Explorer**och öppna.

1. Efter den sista **HTML. ActionLink**lägger du till följande **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Kör programmet och välj **Hämta Kölängd** för att se resultat som liknar följande skärm bild:
  
    ![Ta bort kö](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Nästa steg
Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Kom igång med Azure Blob Storage och ASP.NET (Visual Studio Connected Services)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Kom igång med Azure Table Storage och ASP.NET (Visual Studio Connected Services)](vs-storage-aspnet-getting-started-tables.md)
