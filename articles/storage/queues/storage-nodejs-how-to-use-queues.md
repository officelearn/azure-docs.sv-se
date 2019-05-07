---
title: Hur du använder Queue storage från Node.js – Azure Storage
description: Lär dig hur du använder Azure-Kötjänsten för att skapa och ta bort köer, infoga, hämta och ta bort meddelanden. Exempel som skrivits i Node.js.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 01afe1ab7b9028f3f77d52f7d6f8ced27f6a79c7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142701"
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Använda Queue Storage från Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med Microsoft Azure-kötjänsten. Exemplen är skrivna med hjälp av Node.js-API. Scenarier som omfattas är **infoga**, **granskning**, **komma**, och **tar bort** köa meddelanden, samt  **Skapa och ta bort köer**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Skapa en Node.js-program
Skapa en tom Node.js-program. Anvisningar hur man skapar en Node.js-program finns i [skapa en Node.js-webbapp i Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [skapa och distribuera ett Node.js-program till en Azure-molntjänst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) med Windows PowerShell eller [ Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för att komma åt lagringsutrymme
Om du vill använda Azure storage, behöver du Azure Storage SDK för Node.js, som innehåller en uppsättning bekvämlighet bibliotek som kommunicerar med storage REST-tjänster.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använda Node Package Manager (NPM) för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix), navigera till mappen där du skapade exempelprogrammet.
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

3. Du kan köra manuellt i **ls** kommando för att kontrollera att en **nod\_moduler** mappen har skapats. I mappen hittar du paketet **azure storage**, som innehåller de bibliotek som du behöver för att få åtkomst till lagring.

### <a name="import-the-package"></a>Importera paketet
Med hjälp av anteckningar eller något annat textredigeringsprogram, Lägg till följande överst i **server.js** filen för programmet som du tänker använda lagring:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Skapa ett Azure Storage-anslutning
Azure-modulen läses miljövariablerna AZURE\_STORAGE\_konto och AZURE\_STORAGE\_åtkomst\_nyckel eller AZURE\_STORAGE\_anslutning\_ STRÄNG information som krävs för att ansluta till Azure storage-kontot. Om de här miljövariablerna inte har angetts måste du ange informationen när du anropar **createQueueService**.

## <a name="how-to-create-a-queue"></a>Instruktioner: Skapa en kö
Följande kod skapar en **QueueService** -objektet, vilket gör det möjligt att arbeta med köer.

```javascript
var queueSvc = azure.createQueueService();
```

Använd den **createQueueIfNotExists** metod som returnerar den angivna kön om det redan finns eller skapar en ny kö med det angivna namnet om det inte redan finns.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Om kön skapas `result.created` är sant. Om kön finns, `result.created` är FALSKT.

### <a name="filters"></a>Filter
Valfritt filtrering åtgärder kan användas för åtgärder som utförs med hjälp av **QueueService**. Filtrering av åtgärder kan innehålla loggning, försöker automatiskt igen och så vidare. Filter är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

När du har gjort dess Förbearbeta på begäran-alternativ, metoden som behöver anropa ”nästa” Skicka en motringning med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I den här återanrop, och efter bearbetning av returnObject (svar från begäran till servern), måste motringningen antingen anropa därefter om det finns för att fortsätta att bearbeta andra filter eller helt enkelt anropa annars finalCallback för att få tjänsten anrop.

Azure SDK för Node.js innehåller två filter som implementerar logik för omförsök: **ExponentialRetryPolicyFilter** och **LinearRetryPolicyFilter**. Detta skapar en **QueueService** objekt som använder den **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Instruktioner: Infoga ett meddelande i en kö
Om du vill infoga ett meddelande i en kö, använda den **createMessage** metod för att skapa ett nytt meddelande och lägga till den i kön.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Instruktioner: En titt på nästa meddelande
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa den **peekMessages** metod. Som standard **peekMessages** tittar på ett enda meddelande.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Den `result` innehåller meddelandet.

> [!NOTE]
> Med hjälp av **peekMessages** när det finns inga meddelanden i kön inte returnerar ett fel, men inga meddelanden kommer att returneras.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Instruktioner: Ta bort från kön nästa meddelande
Bearbetar ett meddelande som är en process i två steg:

1. Ta bort från kön meddelandet.
2. Ta bort meddelandet.

Om du vill ta bort från kön på ett meddelande, använda **GetMessage**. Detta gör meddelandena i kön, så kan bearbeta dem på några andra klienter. När ditt program har bearbetats ett meddelande kan anropa **deleteMessage** att ta bort det från kön. I följande exempel får ett meddelande och sedan tar bort den:

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
> Som standard döljs bara ett meddelande i 30 sekunder, efter vilken den är synlig för andra klienter. Du kan ange ett annat värde med hjälp av `options.visibilityTimeout` med **GetMessage**.
> 
> [!NOTE]
> Med hjälp av **GetMessage** när det finns inga meddelanden i kön inte returnerar ett fel, men inga meddelanden kommer att returneras.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instruktioner: Ändra innehållet i ett meddelande i kön
Du kan ändra innehållet i ett meddelande direkt i kön med **updateMessage**. I följande exempel uppdateras texten i meddelandet:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Instruktioner: Ytterligare alternativ för meddelanden mellan köer
Det finns två sätt som du kan anpassa meddelandehämtningen från en kö:

* `options.numOfMessages` – Hämta en grupp med meddelanden (upp till 32).
* `options.visibilityTimeout` -Ange en tidsgräns för osynlighet längre eller kortare.

I följande exempel används den **getMessages** metod för att hämta 15 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en slinga. Den anger också in tidsgränsen för osynlighet till fem minuter för alla meddelanden som returneras av den här metoden.

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

## <a name="how-to-get-the-queue-length"></a>Instruktioner: Hämta kölängden
Den **getQueueMetadata** returnerar metadata om kön, inklusive det ungefärliga antalet väntande meddelanden i kön.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Instruktioner: Lista köer
Använd för att hämta en lista över köer **listQueuesSegmented**. Använd för att hämta en lista som filtreras efter ett specifikt prefix **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Om alla köer inte kan returneras, `result.continuationToken` kan användas som den första parametern i **listQueuesSegmented** eller den andra parametern för **listQueuesSegmentedWithPrefix** att hämta fler resultat.

## <a name="how-to-delete-a-queue"></a>Instruktioner: Ta bort en kö
Ta bort en kö och alla meddelanden som finns i den genom att anropa den **deleteQueue** metoden för köobjektet.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Om du vill ta bort alla meddelanden från en kö utan att ta bort det, Använd **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Anvisningar: Arbete med signaturer för delad åtkomst
Signaturer för delad åtkomst (SAS) är ett säkert sätt att ge detaljerad åtkomst till köer utan att ange ditt lagringskontonamn eller nycklar. SAS används ofta för att bevilja begränsad åtkomst till din köer, till exempel att en mobil app att skicka meddelanden.

En betrodda program, till exempel en molnbaserad tjänst som genererar en SAS med hjälp av den **generateSharedAccessSignature** av den **QueueService**, och ger den till ett program som inte är betrodd eller delvis betrodd. Exempelvis kan en mobil app. Signaturen för delad åtkomst genereras med hjälp av en princip, som definierar mellan vilket start- och slutdatum signaturen för delad åtkomst är giltig, samt vilken åtkomstnivå SAS-innehavaren beviljas.

I följande exempel skapar en ny princip för delad åtkomst som gör att SAS-användare för att lägga till meddelanden i kön och upphör att gälla 100 minuter efter den tid som den har skapats.

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

Observera att värdinformationen måste anges även när den behövs när SAS-innehavaren försöker få åtkomst till kön.

Klientprogrammet sedan använder SAS med **QueueServiceWithSAS** att utföra åtgärder mot kön. I följande exempel ansluter till kön och skapar ett meddelande.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Eftersom SAS genererades med Lägg till åtkomst om ett försök gjordes att läsa, uppdatera eller ta bort meddelanden, returneras ett fel.

### <a name="access-control-lists"></a>Listor för åtkomstkontroll
Du kan också använda en åtkomstkontrollista (ACL) för att definiera åtkomstprincipen för en signatur för delad åtkomst. Detta är användbart om du vill att flera klienter kan få åtkomst till kön, men ger olika åtkomstprinciper för varje klient.

En åtkomstkontrollista implementeras med hjälp av en matris med åtkomstprinciper, med ett ID som associeras med varje princip. Följande exempel definierar två principer; en för 'user1 ”och en för 'användare2”:

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

I följande exempel hämtas den aktuella ACL för **myqueue**, lägger till nya principer med hjälp av **setQueueAcl**. Med den här metoden kan du göra följande:

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

När du har angett ACL: du kan skapa en SAS baserat på ID för en princip. I följande exempel skapas en ny signatur för delad åtkomst för ”user2”:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i queue storage kan du följa dessa länkar om du vill lära dig mer komplexa lagringsuppgifter.

* Gå till den [Azure Storage-teamets blogg][Azure Storage Team Blog].
* Gå till den [Azure Storage SDK för Node] [ Azure Storage SDK for Node] arkivet på GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Skapa en Node.js-webbapp i Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Skapa och distribuera ett Node.js-program till en Azure-molntjänst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
