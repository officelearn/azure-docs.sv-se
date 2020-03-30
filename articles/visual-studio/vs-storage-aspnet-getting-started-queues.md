---
title: Komma igång med Azure-kölagring med Visual Studio (ASP.NET)
description: Komma igång med Azure-kölagring i ett ASP.NET projekt i Visual Studio efter anslutning till ett lagringskonto med Visual Studio Connected Services
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f229661ca78dc75adbc0b49073dc6f0feaf2ba22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980744"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Komma igång med Azure-kölagring och Visual Studio Connected Services (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt

Azure-kölagring tillhandahåller molnmeddelanden mellan programkomponenter. När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Den här självstudien visar hur du skriver ASP.NET kod för några vanliga scenarier med hjälp av Azure-kölagringsentiteter. Dessa scenarier inkluderar vanliga uppgifter som att skapa en Azure-kö och lägga till, ändra, läsa och ta bort kömeddelanden.

## <a name="prerequisites"></a>Krav

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure-lagringskonto](../storage/common/storage-account-create.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Skapa en MVC-styrenhet 

1. Högerklicka på Handkontroller i **Lösningsutforskaren**och välj **Lägg till >Controller**på snabbmenyn . **Controllers**

    ![Lägga till en styrenhet i en ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. I dialogrutan **Lägg till byggnadsställning** väljer du **MVC 5 Controller - Tom**och väljer Lägg **till**.

    ![Ange MVC-styrenhetstyp](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. I dialogrutan **Lägg till styrenhet** namnger du styrenhetens *köerkontroller*och väljer **Lägg till**.

    ![Namnge MVC-styrenheten](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Lägg till följande med `QueuesController.cs` hjälp *av* direktiv i filen:

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
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`. 

1. Lägg till en metod som kallas **CreateQueue** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. I metoden **CreateQueue** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett **CloudQueueClient-objekt** representerar en kötjänstklient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Hämta ett **CloudQueue-objekt** som representerar en referens till önskat könamn. **Metoden CloudQueueClient.GetQueueReference** gör ingen begäran mot kölagring. Referensen returneras oavsett om kön finns eller inte. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa metoden **CloudQueue.CreateIfNotExists** för att skapa kön om den ännu inte finns. Metoden **CloudQueue.CreateIfNotExists** returnerar **sant** om kön inte finns och har skapats. Annars returneras **falskt.**    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Uppdatera **ViewBag** med namnet på kön.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Köer**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du **CreateQueue** för visningsnamnet och väljer **Lägg till**.

1. Öppna `CreateQueue.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Kör programmet och välj **Skapa kö** för att se resultat som liknar följande skärmbild:
  
    ![Skapa kö](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Som tidigare nämnts returnerar metoden **CloudQueue.CreateIfNotExists** **endast sant** när kön inte finns och skapas. Om du kör appen när kön finns returnerar metoden därför **false**. Om du vill köra appen flera gånger måste du ta bort kön innan du kör appen igen. Ta bort kön kan göras via **metoden CloudQueue.Delete.** Du kan också ta bort kön med [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040) eller [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande i en kö

När du har [skapat en kö](#create-a-queue)kan du lägga till meddelanden i den kön. I det här avsnittet går du igenom att lägga till ett meddelande i en *kötestkö*. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **AddMessage** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I **metoden AddMessage** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta ett **CloudQueueClient-objekt** representerar en kötjänstklient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta ett **CloudQueueContainer-objekt** som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Skapa **CloudQueueMessage-objektet** som representerar det meddelande som du vill lägga till i kön. Ett **CloudQueueMessage-objekt** kan skapas från antingen en sträng (i UTF-8-format) eller en bytematris.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Anropa **metoden CloudQueue.AddMessage** för att lägga till meddelandet i kön.

    ```csharp
    queue.AddMessage(message);
    ```

1. Skapa och ange ett par **ViewBag-egenskaper** för visning i vyn.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Köer**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du Lägg till för **visningsnamnet** och väljer **Lägg till**.

1. Öppna `AddMessage.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Kör programmet och välj **Lägg till meddelande** om du vill se resultat som liknar följande skärmbild:
  
    ![Lägg till meddelande](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

De två avsnitten - [Läs ett meddelande från en kö utan att ta bort det](#read-a-message-from-a-queue-without-removing-it) och Läs och ta bort ett meddelande från en [kö](#read-and-remove-a-message-from-a-queue) - illustrerar hur du läser meddelanden från en kö.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Läsa ett meddelande från en kö utan att ta bort det

Det här avsnittet illustrerar hur du tittar på ett köat meddelande (läs det första meddelandet utan att ta bort det).  

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **PeekMessage** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I **PeekMessage-metoden** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta ett **CloudQueueClient-objekt** representerar en kötjänstklient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta ett **CloudQueueContainer-objekt** som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa **metoden CloudQueue.PeekMessage** för att läsa det första meddelandet i kön utan att ta bort det från kön. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Uppdatera **ViewBag** med två värden: könamnet och meddelandet som lästes. **CloudQueueMessage-objektet** exponerar två egenskaper för att hämta objektets värde: **CloudQueueMessage.AsBytes** och **CloudQueueMessage.AsString**. **AsString** (används i det här exemplet) returnerar en sträng, medan **AsBytes returnerar** en bytematris.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Köer**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du **PeekMessage för visningsnamnet** och väljer Lägg **till**.

1. Öppna `PeekMessage.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

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

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Kör programmet och välj **Peek-meddelande** för att se resultat som liknar följande skärmbild:
  
    ![Granska meddelande](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Läsa och ta bort ett meddelande från en kö

I det här avsnittet får du lära dig att läsa och ta bort ett meddelande från en kö.   

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **ReadMessage** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I **Metoden ReadMessage** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta ett **CloudQueueClient-objekt** representerar en kötjänstklient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta ett **CloudQueueContainer-objekt** som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa **metoden CloudQueue.GetMessage** för att läsa det första meddelandet i kön. **Metoden CloudQueue.GetMessage** gör meddelandet osynligt i 30 sekunder (som standard) för andra kodläsningsmeddelanden så att ingen annan kod kan ändra eller ta bort meddelandet medan du bearbetar det. Om du vill ändra hur lång tid meddelandet är osynligt ändrar du **parametern visibilityTimeout** som skickas till metoden **CloudQueue.GetMessage.**

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Anropa metoden **CloudQueueMessage.Delete** för att ta bort meddelandet från kön.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Uppdatera **ViewBag** med meddelandet borttaget och namnet på kön.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Köer**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du **ReadMessage för visningsnamnet** och väljer Lägg **till**.

1. Öppna `ReadMessage.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

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

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Kör programmet och välj **Läs/Ta bort meddelande** om du vill se resultat som liknar följande skärmbild:
  
    ![Läsa och ta bort meddelande](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Hämta kölängden

Det här avsnittet illustrerar hur du får kölängden (antal meddelanden). 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **GetQueueLength** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I **Metoden ReadMessage** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta ett **CloudQueueClient-objekt** representerar en kötjänstklient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta ett **CloudQueueContainer-objekt** som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa metoden **CloudQueue.FetchAttributes** för att hämta köns attribut (inklusive dess längd). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Öppna egenskapen **CloudQueue.ApproximateMessageCount** för att hämta köns längd.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Uppdatera **ViewBag** med namnet på kön och dess längd.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Köer**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du **GetQueueLength** för visningsnamnet och väljer **Lägg till**.

1. Öppna `GetQueueLengthMessage.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Kör programmet och välj **Hämta kölängd** för att se resultat som liknar följande skärmbild:
  
    ![Hämta kölängd](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Ta bort en kö
Det här avsnittet visar hur du tar bort en kö. 

> [!NOTE]
> 
> Det här avsnittet förutsätter att du har slutfört stegen [Konfigurera utvecklingsmiljön](#set-up-the-development-environment). 

1. Öppna filen `QueuesController.cs`.

1. Lägg till en metod som kallas **DeleteQueue** som returnerar ett **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. I metoden **DeleteQueue** får du ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen: (Ändra * &lt;lagringskonto-namn>* till namnet på det Azure-lagringskonto som du använder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Hämta ett **CloudQueueClient-objekt** representerar en kötjänstklient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Hämta ett **CloudQueueContainer-objekt** som representerar en referens till kön. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Anropa metoden **CloudQueue.Delete** för att ta bort kön som representeras av **CloudQueue-objektet.**

    ```csharp
    queue.Delete();
    ```

1. Uppdatera **ViewBag** med namnet på kön och dess längd.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. Expandera mappen **Vyer** i **Lösningsutforskaren,** högerklicka på **Köer**och välj **Lägg till >Visa**på snabbmenyn.

1. I dialogrutan **Lägg till vy** anger du **DeleteQueue** för visningsnamnet och väljer **Lägg till**.

1. Öppna `DeleteQueue.cshtml`och ändra det så att det ser ut som följande kodavsnitt:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. Expandera mappen **Vyer >delad** i **Lösningsutforskaren** `_Layout.cshtml`och öppna .

1. Efter den senaste **Html.ActionLink**lägger du till följande **Html.ActionLink:**

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Kör programmet och välj **Hämta kölängd** för att se resultat som liknar följande skärmbild:
  
    ![Ta bort kö](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Nästa steg
Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.

  * [Komma igång med Azure blob storage och Visual Studio Connected Services (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Komma igång med Azure-tabelllagring och Visual Studio Connected Services (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
