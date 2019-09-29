---
title: Använd Azure Queue Storage från Node. js – Azure Storage
description: Lär dig hur du använder Azure-Kötjänst för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden. Exempel som skrivits i Node. js.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/08/2016
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 9eee7f6b321a5ba660e1a0a5a48771c599746bcb
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673083"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Använd Azure Queue Service för att skapa och ta bort köer från Node. js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av Microsoft Azure Kötjänst. Exemplen skrivs med Node. js-API: et. De scenarier som beskrivs är att **Infoga**, **Granska**, **Hämta**och **ta bort** Kömeddelanden, samt **skapa och ta bort köer**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Skapa ett Node. js-program
Skapa ett tomt Node. js-program. Instruktioner för hur du skapar ett Node. js-program finns [i skapa en Node. js-webbapp i Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [skapa och distribuera ett Node. js-program till en Azure-moln tjänst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) med hjälp av Windows PowerShell eller [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för åtkomst till lagring
Om du vill använda Azure Storage behöver du Azure Storage SDK för Node. js, som innehåller en uppsättning med bekvämlighets bibliotek som kommunicerar med lagrings REST tjänsterna.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Hämta paketet med hjälp av Node Pack Manager (NPM)
1. Använd ett kommando rads gränssnitt som **PowerShell** (Windows,) **Terminal** (Mac) eller **bash** (UNIX) och navigera till mappen där du skapade exempel programmet.
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

3. Du kan köra **ls** -kommandot manuellt för att kontrol lera att en mapp för **Node\_-moduler** har skapats. I mappen hittar du paketet **azure storage**, som innehåller de bibliotek som du behöver för att få åtkomst till lagring.

### <a name="import-the-package"></a>Importera paketet
Använd anteckningar eller något annat text redigerings program och Lägg till följande i den översta filen **Server. js** för programmet där du tänker använda Storage:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Konfigurera en Azure Storage anslutning
Azure\_-modulen läser miljövariablerna Azure Storage\_-kontot och åtkomst\_\_nyckeln\_för Azure Storage eller Azure\_Storage\_-anslutning\_ STRÄNG för information som krävs för att ansluta till ditt Azure Storage-konto. Om de här miljövariablerna inte har angetts måste du ange konto informationen när du anropar **createQueueService**.

## <a name="how-to-create-a-queue"></a>Instruktioner: Skapa en kö
Följande kod skapar ett **QueueService** -objekt som gör att du kan arbeta med köer.

```javascript
var queueSvc = azure.createQueueService();
```

Använd metoden **createQueueIfNotExists** , som returnerar den angivna kön om den redan finns eller skapar en ny kö med det angivna namnet om den inte redan finns.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Om kön har skapats `result.created` är sant. Om kön finns `result.created` är false.

### <a name="filters"></a>Filter
Valfria filtrerings åtgärder kan tillämpas på åtgärder som utförs med hjälp av **QueueService**. Filtrerings åtgärder kan omfatta loggning, automatiskt försök igen osv. Filter är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

När du har bearbetat på begäran-alternativen måste metoden anropa "Nästa" för att skicka ett motanrop med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I det här återanropet, och efter bearbetning av returnObject (svaret från begäran till servern), måste återanropet antingen anropas härnäst om det finns för att fortsätta bearbeta andra filter eller bara anropa finalCallback för att avsluta tjänsten anrop.

Azure SDK för Node.js innehåller två filter som implementerar logik för omförsök: **ExponentialRetryPolicyFilter** och **LinearRetryPolicyFilter**. Följande skapar ett **QueueService** -objekt som använder **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instruktioner: Infoga ett meddelande i en kö
Om du vill infoga ett meddelande i en kö använder du metoden **createMessage** för att skapa ett nytt meddelande och lägger till det i kön.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Instruktioner: Titta på nästa meddelande
Du kan titta på meddelandet överst i en kö utan att ta bort det från kön genom att anropa metoden **peekMessages** . Som standard tittar **peekMessages** i ett enda meddelande.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` Innehåller meddelandet.

> [!NOTE]
> Om du använder **peekMessages** när det inte finns några meddelanden i kön returneras inget fel, men inga meddelanden returneras.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Instruktioner: Ta bort nästa meddelande i kö
Bearbetning av ett meddelande är en process i två steg:

1. Ta bort kön för meddelandet.
2. Ta bort meddelandet.

Om du vill ta bort ett meddelande från kön använder du **GetMessage**. Detta gör meddelandena osynliga i kön, så inga andra klienter kan bearbeta dem. När ditt program har bearbetat ett meddelande anropar du **deleteMessage** för att ta bort det från kön. Följande exempel hämtar ett meddelande och tar sedan bort det:

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
> Som standard är ett meddelande endast dolt i 30 sekunder, efter vilket det är synligt för andra klienter. Du kan ange ett annat värde med hjälp `options.visibilityTimeout` av with **GetMessage**.
> 
> [!NOTE]
> Om du använder **GetMessage** när det inte finns några meddelanden i kön returneras inget fel, men inga meddelanden returneras.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instruktioner: Ändra innehållet i ett köat meddelande
Du kan ändra innehållet i ett meddelande på plats i kön med **updateMessage**. I följande exempel uppdateras texten i ett meddelande:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Instruktioner: Ytterligare alternativ för demsmq-meddelanden
Det finns två sätt att anpassa meddelande hämtning från en kö:

* `options.numOfMessages`– Hämta en batch med meddelanden (upp till 32.)
* `options.visibilityTimeout`-Ange en längre eller kortare tids gräns för insikt.

I följande exempel används metoden **GetMessage** för att få 15 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en for-slinga. Den anger också timeout för insikter till fem minuter för alla meddelanden som returneras av den här metoden.

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

## <a name="how-to-get-the-queue-length"></a>Instruktioner: Hämta Kölängd
**GetQueueMetadata** Returnerar metadata om kön, inklusive det ungefärliga antalet meddelanden som väntar i kön.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Instruktioner: Lista köer
Använd **listQueuesSegmented**om du vill hämta en lista över köer. Om du vill hämta en lista som filtrerats efter ett angivet prefix använder du **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Om det inte går att returnera `result.continuationToken` alla köer kan användas som den första parametern för **listQueuesSegmented** eller den andra parametern för **listQueuesSegmentedWithPrefix** för att hämta fler resultat.

## <a name="how-to-delete-a-queue"></a>Instruktioner: Ta bort en kö
Om du vill ta bort en kö och alla meddelanden som finns i den anropar du metoden **deleteQueue** på objektet köobjekt.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Om du vill rensa alla meddelanden från en kö utan att ta bort den, använder du **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Anvisningar: Arbete med signaturer för delad åtkomst
Signaturer för delad åtkomst (SAS) är ett säkert sätt att ge detaljerad åtkomst till köer utan att ange ditt lagrings konto namn eller nycklar. SAS används ofta för att ge begränsad åtkomst till dina köer, till exempel att tillåta en mobilapp att skicka meddelanden.

Ett betrott program, till exempel en molnbaserad tjänst, genererar en SAS med **generateSharedAccessSignature** i **QueueService**och ger den ett icke betrott eller delvis betrott program. Till exempel en mobilapp. Signaturen för delad åtkomst genereras med hjälp av en princip, som definierar mellan vilket start- och slutdatum signaturen för delad åtkomst är giltig, samt vilken åtkomstnivå SAS-innehavaren beviljas.

I följande exempel skapas en ny princip för delad åtkomst som gör att SAS-innehavaren kan lägga till meddelanden i kön och förfaller 100 minuter efter den tidpunkt då den skapades.

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

Observera att värd informationen också måste anges, eftersom den krävs när SAS-innehavaren försöker komma åt kön.

Klient programmet använder sedan SAS med **QueueServiceWithSAS** för att utföra åtgärder mot kön. Följande exempel ansluter till kön och skapar ett meddelande.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Eftersom SAS genererades med Lägg till åtkomst, skulle ett fel returneras om ett försök gjordes att läsa, uppdatera eller ta bort meddelanden.

### <a name="access-control-lists"></a>Listor för åtkomstkontroll
Du kan också använda en åtkomstkontrollista (ACL) för att definiera åtkomstprincipen för en signatur för delad åtkomst. Detta är användbart om du vill tillåta att flera klienter får åtkomst till kön, men ger olika åtkomst principer för varje klient.

En åtkomstkontrollista implementeras med hjälp av en matris med åtkomstprinciper, med ett ID som associeras med varje princip. I följande exempel definieras två principer. en för "Användare1" och en för "användare2":

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

Följande exempel hämtar den aktuella ACL: en för **kön**och lägger sedan till de nya principerna med **setQueueAcl**. Med den här metoden kan du göra följande:

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

När du har angett ACL: en, kan du skapa en SAS baserad på ID för en princip. I följande exempel skapas en ny signatur för delad åtkomst för ”user2”:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagrings uppgifter.

* Besök [Azure Storage-teamets blogg][Azure Storage Team Blog].
* Besök [Azure Storage SDK för Node][Azure Storage SDK for Node] -lagringsplatsen på GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Skapa en Node. js-webbapp i Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Bygg och distribuera ett Node. js-program till en Azure-moln tjänst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
