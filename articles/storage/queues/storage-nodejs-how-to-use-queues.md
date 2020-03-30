---
title: Använda Azure-kölagring från Node.js - Azure Storage
description: Lär dig hur du använder Azure Queue-tjänsten för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden. Exempel skrivna i Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 7abcad03678131668700f5d2c64b9c971081cb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060926"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Använda Azure Queue Service för att skapa och ta bort köer från Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av Tjänsten Microsoft Azure Queue. Exemplen skrivs med API:et för nod.js. Scenarierna som omfattas omfattar **att infoga,** **kika,** **hämta**och **ta bort** kömeddelanden samt skapa och ta **bort köer**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Skapa ett nod.js-program
Skapa ett tomt nod.js-program. Instruktioner om hur du skapar ett Node.js-program finns [i Skapa en node.js-webbapp i Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), Skapa och distribuera ett [Node.js-program till en Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) med Windows PowerShell eller Visual Studio [Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för åtkomst till lagring
Om du vill använda Azure-lagring behöver du Azure Storage SDK för Node.js, som innehåller en uppsättning bekvämlighetsbibliotek som kommunicerar med REST-lagringstjänsterna.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använd Nod Package Manager (NPM) för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell (Windows,)** **Terminal** (Mac) eller **Bash** (Unix), navigera till mappen där du skapade exempelprogrammet.
2. Skriv **npm install azure-storage** i kommandofönstret. Kommandot returnerar utdata liknande dem i exemplet nedan.
 
    ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. Du kan köra **kommandot ls** manuellt för att kontrollera att en mapp för **\_nodmoduler har skapats.** I mappen hittar du paketet **azure storage**, som innehåller de bibliotek som du behöver för att få åtkomst till lagring.

### <a name="import-the-package"></a>Importera paketet
Med Anteckningar eller annan textredigerare lägger du till följande högst upp i **filen server.js** i programmet där du tänker använda lagring:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Konfigurera en Azure Storage-anslutning
Azure-modulen\_läser miljövariablerna\_AZURE\_STORAGE\_\_ACCOUNT och\_AZURE\_\_STORAGE ACCESS KEY, eller AZURE STORAGE CONNECTION STRING för information som krävs för att ansluta till ditt Azure-lagringskonto. Om dessa miljövariabler inte har angetts måste du ange kontoinformation när du anropar **createQueueService**.

## <a name="how-to-create-a-queue"></a>Så här skapar du en kö
Följande kod skapar ett **QueueService-objekt** som gör att du kan arbeta med köer.

```javascript
var queueSvc = azure.createQueueService();
```

Använd metoden **createQueueIfNotExists,** som returnerar den angivna kön om den redan finns eller skapar en ny kö med det angivna namnet om den inte redan finns.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Om kön skapas `result.created` är det sant. Om kön finns, `result.created` är falskt.

### <a name="filters"></a>Filter
Valfria filtreringsåtgärder kan tillämpas på åtgärder som utförs med **QueueService**. Filtreringsåtgärder kan omfatta loggning, automatiskt återförsök, etc. Filter är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

När du har gjort sin förbehandling på begäran alternativ, måste metoden ringa "nästa" passerar en motringning med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I den här motringningen, och efter bearbetning av returnObject (svaret från begäran till servern), måste motringningen antingen anropa nästa om det finns för att fortsätta bearbeta andra filter eller helt enkelt anropa finalCallback annars för att hamna tjänsten Åkallan.

Azure SDK för Node.js innehåller två filter som implementerar logik för omförsök: **ExponentialRetryPolicyFilter** och **LinearRetryPolicyFilter**. Följande skapar ett **QueueService-objekt** som använder **ExponentialRetryPolicyFilter:**

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Så här infogar du ett meddelande i en kö
Om du vill infoga ett meddelande i en kö använder du metoden **createMessage** för att skapa ett nytt meddelande och lägga till det i kön.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Så här: Kika på nästa meddelande
Du kan granska meddelandet framför en kö utan att ta bort det från kön genom att anropa metoden **peekMessages.** Som standard **peekMessages kikar** på ett enda meddelande.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Innehåller `result` meddelandet.

> [!NOTE]
> Om du använder **peekMessages** när det inte finns några meddelanden i kön returneras inget fel, men inga meddelanden returneras.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Så här: Dequeue nästa meddelande
Bearbetning av ett meddelande är en process i två steg:

1. Avqueue meddelandet.
2. Ta bort meddelandet.

Om du vill avqueue ett meddelande använder du **getMessages**. Detta gör meddelandena osynliga i kön, så att inga andra klienter kan bearbeta dem. När ditt program har bearbetat ett meddelande anropar du **deleteMessage** för att ta bort det från kön. I följande exempel hämtas ett meddelande och det tas sedan bort:

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

> [!NOTE]
> Som standard döljs ett meddelande bara i 30 sekunder, varefter det är synligt för andra klienter. Du kan ange ett `options.visibilityTimeout` annat värde genom att använda med **getMessages**.
> 
> [!NOTE]
> Om du använder **getMessages** när det inte finns några meddelanden i kön returneras inget fel, men inga meddelanden returneras.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Så här ändrar du innehållet i ett köat meddelande
Du kan ändra innehållet i ett meddelande på plats i kön med hjälp av **updateMessage**. I följande exempel uppdateras texten i ett meddelande:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Så här: Ytterligare alternativ för att skicka meddelanden
Det finns två sätt att anpassa meddelandehämtning från en kö:

* `options.numOfMessages`- Hämta en grupp meddelanden (upp till 32.)
* `options.visibilityTimeout`- Ställ in en längre eller kortare timeout för osynlighet.

I följande exempel används metoden **getMessages** för att få 15 meddelanden i ett samtal. Sedan bearbetar varje meddelande med hjälp av en för-loop. Den anger också tidsgränsen för osynlighet till fem minuter för alla meddelanden som returneras med den här metoden.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
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

## <a name="how-to-get-the-queue-length"></a>Så här: Få kölängd
**GetQueueMetadata** returnerar metadata om kön, inklusive det ungefärliga antalet meddelanden som väntar i kön.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Så här: Lista köer
Om du vill hämta en lista över köer använder du **listQueuesSegmented**. Om du vill hämta en lista som filtrerats efter ett visst prefix använder du **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Om alla köer inte `result.continuationToken` kan returneras kan användas som den första parametern **i listQueuesSegmented** eller den andra parametern **i listQueuesSegmentedWithPrefix** för att hämta fler resultat.

## <a name="how-to-delete-a-queue"></a>Så här tar du bort en kö
Om du vill ta bort en kö och alla meddelanden som finns i den anropar du **metoden deleteQueue** i köobjektet.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Om du vill ta bort alla meddelanden från en kö utan att ta bort den använder du **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Så här: Arbeta med signaturer för delad åtkomst
SAS (Shared Access Signatures) är ett säkert sätt att ge detaljerad åtkomst till köer utan att ange ditt namn eller nycklar för lagringskontot. SAS används ofta för att ge begränsad åtkomst till dina köer, till exempel att tillåta en mobilapp att skicka meddelanden.

Ett betrott program, till exempel en molnbaserad tjänst, genererar en SAS med hjälp av **genereraSharedAccessSignature** för **QueueService**och tillhandahåller den till ett icke-betrodd eller halvanslutet program. Till exempel en mobilapp. Signaturen för delad åtkomst genereras med hjälp av en princip, som definierar mellan vilket start- och slutdatum signaturen för delad åtkomst är giltig, samt vilken åtkomstnivå SAS-innehavaren beviljas.

I följande exempel genereras en ny princip för delad åtkomst som gör det möjligt för SAS-innehavaren att lägga till meddelanden i kön och upphör att gälla 100 minuter efter den tid den skapas.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Observera att värdinformationen måste tillhandahållas också, eftersom det krävs när SAS-innehavaren försöker komma åt kön.

Klientprogrammet använder sedan SAS med **QueueServiceWithSAS** för att utföra åtgärder mot kön. Följande exempel ansluter till kön och skapar ett meddelande.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Eftersom SAS genererades med lägg till åtkomst, om ett försök gjordes att läsa, uppdatera eller ta bort meddelanden, skulle ett fel returneras.

### <a name="access-control-lists"></a>Listor för åtkomstkontroll
Du kan också använda en åtkomstkontrollista (ACL) för att definiera åtkomstprincipen för en signatur för delad åtkomst. Detta är användbart om du vill tillåta flera klienter att komma åt kön, men ger olika åtkomstprinciper för varje klient.

En åtkomstkontrollista implementeras med hjälp av en matris med åtkomstprinciper, med ett ID som associeras med varje princip. I följande exempel definieras två principer. en för "user1" och en för "user2":

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

I följande exempel får det aktuella ACL för **myqueue**och sedan läggs de nya principerna till **setQueueAcl**. Med den här metoden kan du göra följande:

```javascript
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

När åtkomstkontrollistan har angetts kan du skapa en SAS-baserad på ID för en princip. I följande exempel skapas en ny signatur för delad åtkomst för ”user2”:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Efterföljande moment
Nu när du har lärt dig grunderna i kölagring följer du de här länkarna för att lära dig mer komplexa lagringsuppgifter.

* Besök [Azure Storage-teamets blogg][Azure Storage Team Blog].
* Besök [Azure Storage SDK for Node-databasen][Azure Storage SDK for Node] på GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Skapa en Node.js-webbapp i Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Skapa och distribuera en Node.js-app till en Azure-molntjänst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
