---
title: Så här använder du Azure Queue Storage från Node.js-Azure Storage
description: Lär dig att använda Azure-Kötjänst för att skapa och ta bort köer. Lär dig att infoga, hämta och ta bort meddelanden med hjälp av Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: c5a9fb1a179164d24c84213762ee7e2332a1aa25
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345949"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Så här använder du Azure Queue Storage från Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Översikt

Den här guiden visar hur du utför vanliga scenarier med hjälp av Microsoft Azure Kötjänst. Exemplen skrivs med hjälp av Node.js-API: et. De scenarier som beskrivs är att infoga, granska, hämta och ta bort Kömeddelanden. Lär dig också att skapa och ta bort köer.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Om du vill skapa en tom Node.js-app, se [skapa en Node.js webbapp i Azure App Service][Create a Node.js web app in Azure App Service], [skapa och distribuera ett Node.js program till en Azure-moln tjänst][Build and deploy a Node.js application to an Azure Cloud Service] med hjälp av Windows PowerShell eller [Visual Studio Code][Visual Studio Code].

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för åtkomst till lagring

[Azure Storage klient bibliotek för Java Script][Azure Storage client library for JavaScript] innehåller en uppsättning av praktiska bibliotek som kommunicerar med lagrings rest tjänsterna.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Hämta paketet med hjälp av Node Pack Manager (NPM)

1. Använd ett kommando rads gränssnitt som PowerShell (Windows), Terminal (Mac) eller bash (UNIX) och navigera till mappen där du skapade exempel programmet.

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

1. Skriv **NPM installera \@ Azure/Storage – Queue** i kommando fönstret.

1. Verifiera att en mapp för **Node- \_ moduler** har skapats. I den mappen hittar du **\@ Azure/Storage-kösystemet** , som innehåller klient biblioteket som du behöver för att komma åt lagringen.

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

1. Skriv **npm install azure-storage** i kommandofönstret.

1. Verifiera att en mapp för **Node- \_ moduler** har skapats. I den mappen hittar du **Azure-Storage-** paketet, som innehåller de bibliotek som du behöver för att komma åt lagringen.

---

### <a name="import-the-package"></a>Importera paketet

Använd kod redigeraren och Lägg till följande i den övre JavaScript-filen där du tänker använda köer.

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Så skapar du en resurs

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Följande kod hämtar värdet för en miljö variabel som kallas `AZURE_STORAGE_CONNECTION_STRING` och använder den för att skapa ett [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient) -objekt. **QueueServiceClient** -objektet används sedan för att skapa ett [QueueClient](/javascript/api/@azure/storage-queue/queueclient) -objekt. Med **QueueClient** -objektet kan du arbeta med en speciell kö.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Om kön redan finns genereras ett undantag.

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Azure-modulen läser miljövariablerna `AZURE_STORAGE_ACCOUNT` och `AZURE_STORAGE_ACCESS_KEY` , eller `AZURE_STORAGE_CONNECTION_STRING` information som krävs för att ansluta till ditt Azure Storage-konto. Om de här miljövariablerna inte har angetts måste du ange konto informationen när du anropar **createQueueService**.

Följande kod skapar ett **QueueService** -objekt som gör att du kan arbeta med köer.

```javascript
var queueSvc = azure.createQueueService();
```

Anropa **createQueueIfNotExists** -metoden för att skapa en ny kö med det angivna namnet eller returnera kön om den redan finns.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Om kön har skapats `result.created` är sant. Om kön finns `result.created` är false.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Så här infogar du ett meddelande i en kö

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Anropa metoden [SendMessage](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) om du vill lägga till ett meddelande i en kö.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Om du vill infoga ett meddelande i en kö anropar du **createMessage** -metoden för att skapa ett nytt meddelande och lägger till det i kön.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Granska nästa meddelande

Du kan granska meddelanden i kön utan att ta bort dem från kön genom att anropa metoden **peekMessages** .

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Som standard tittar [peekMessages](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) i ett enda meddelande. Följande exempel tittar på de första fem meddelandena i kön. Om färre än fem meddelanden visas returneras bara de synliga meddelandena.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Som standard tittar **peekMessages** i ett enda meddelande.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result`Innehåller meddelandet.

---

Anrop av **peekMessages** när det inte finns några meddelanden i kön returnerar inte ett fel. Inga meddelanden returneras dock.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett köade meddelande

I följande exempel uppdateras texten i ett meddelande.

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Ändra innehållet i ett meddelande på plats i kön genom att anropa [updateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-).

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Ändra innehållet i ett meddelande på plats i kön genom att anropa **updateMessage**.

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Ta ett meddelande ur kö

Att köa ett meddelande är en process i två steg:

1. Hämta meddelandet.

1. Ta bort meddelandet.

Följande exempel hämtar ett meddelande och tar sedan bort det.

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Anropa [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) -metoden för att få ett meddelande. Anropet gör meddelandena osynliga i kön, så inga andra klienter kan bearbeta dem. När ditt program har bearbetat ett meddelande anropar du [deleteMessage](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) för att ta bort det från kön.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Som standard är ett meddelande endast dolt i 30 sekunder. Efter 30 sekunder är det synligt för andra klienter. Du kan ange ett annat värde genom att ange [Options. visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) när du anropar **receiveMessages**.

Anrop av **receiveMessages** när det inte finns några meddelanden i kön returnerar inte ett fel. Inga meddelanden kommer dock att returneras.

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Anropa **GetMessage** -metoden för att få ett meddelande. Anropet gör meddelandena osynliga i kön, så inga andra klienter kan bearbeta dem. När ditt program har bearbetat ett meddelande anropar du **deleteMessage** för att ta bort det från kön.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Som standard är ett meddelande endast dolt i 30 sekunder. Efter 30 sekunder är det synligt för andra klienter. Du kan ange ett annat värde med hjälp av `options.visibilityTimeout` with **GetMessage**.

Om du använder **GetMessage** när det inte finns några meddelanden i kön returneras inget fel. Inga meddelanden kommer dock att returneras.

---

## <a name="additional-options-for-dequeuing-messages"></a>Ytterligare alternativ för demsmq-meddelanden

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Det finns två sätt att anpassa meddelande hämtning från en kö:

- [Options. numberOfMessages](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages) – hämta en batch med meddelanden (upp till 32.)
- [Options. visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) – ange en längre eller kortare tids gräns för insikt.

I följande exempel används metoden **receiveMessages** för att hämta fem meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en `for` loop. Den anger också timeout för insikter till fem minuter för alla meddelanden som returneras av den här metoden.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Det finns två sätt att anpassa meddelande hämtning från en kö:

- `options.numOfMessages` – Hämta en batch med meddelanden (upp till 32.)
- `options.visibilityTimeout` -Ange en längre eller kortare tids gräns för insikt.

I följande exempel används metoden **GetMessage** för att få 15 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en `for` loop. Den anger också timeout för insikter till fem minuter för alla meddelanden som returneras av den här metoden.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>Så här hämtar du Kölängd

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

[GetProperties](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) -metoden returnerar metadata om kön, inklusive det ungefärliga antalet meddelanden som väntar i kön.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

**GetQueueMetadata** -metoden returnerar metadata om kön, inklusive det ungefärliga antalet meddelanden som väntar i kön.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Så här listar du köer

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Anropa [QueueServiceClient. listQueues]()om du vill hämta en lista över köer. Om du vill hämta en lista som filtrerats efter ett särskilt prefix anger du [Options. prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) i ditt anrop till **listQueues**.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Använd **listQueuesSegmented** om du vill hämta en lista över köer. Om du vill hämta en lista som filtrerats efter ett angivet prefix använder du **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Om alla köer inte kan returneras skickar du `result.continuationToken` som den första parametern för **listQueuesSegmented** eller den andra parametern för **listQueuesSegmentedWithPrefix** för att hämta fler resultat.

---

## <a name="how-to-delete-a-queue"></a>Så här tar du bort en kö

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du metoden [deleteQueue](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) på **QueueClient** -objektet.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Om du vill rensa alla meddelanden från en kö utan att ta bort den, anropa [clearMessages](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-).

# <a name="javascript-v2"></a>[Java Script v2](#tab/javascript2)

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du metoden **deleteQueue** på objektet köobjekt.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Om du vill rensa alla meddelanden från en kö utan att ta bort den, anropa **clearMessages**.

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagrings uppgifter.

- Besök [Azure Storage teamets blogg][Azure Storage Team Blog] och lär dig vad som är nytt
- Besök [Azure Storage klient bibliotek för Java Script][Azure Storage client library for JavaScript] -lagringsplats på GitHub

[Azure Storage client library for JavaScript]: https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
[Build and deploy a Node.js application to an Azure Cloud Service]: ../../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Create a Node.js web app in Azure App Service]: ../../app-service/quickstart-nodejs.md
[Visual Studio Code]: https://code.visualstudio.com/docs/nodejs/nodejs-tutorial
