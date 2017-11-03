---
title: "Använda Queue storage från Node.js | Microsoft Docs"
description: "Lär dig hur du använder Azure-Kötjänsten för att skapa och ta bort köer, infoga, hämta och ta bort meddelanden. Exempel som skrivits i Node.js."
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a8a92db0-4333-43dd-a116-28b3147ea401
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: ce61687e0f64b5971daca85265ef0e7d2b6f43b8
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Använda Queue Storage från Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av Microsoft Azure-kötjänsten. Exemplen är skrivna med Node.js-API. Scenarier som tas upp inkluderar **infoga**, **granskning**, **komma**, och **bort** kö meddelanden, samt **skapa och ta bort köer**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Skapa en Node.js-program
Skapa ett tomt Node.js-program. Instruktioner om att skapa en Node.js-program finns i [skapa en Node.js-webbapp i Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [skapa och distribuera ett Node.js-program till en Azure-molntjänst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) med Windows PowerShell eller [skapa och distribuera en Node.js-webbapp till Azure med hjälp av Web Matrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för att komma åt lagringsutrymme
Om du vill använda Azure storage, behöver du Azure Storage SDK: N för Node.js som innehåller en uppsättning bekvämlighet bibliotek som kommunicerar med storage REST-tjänster.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använd noden Package Manager (NPM) för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix), navigera till mappen där du skapade exempelprogrammet.
2. Typen **npm installera azure-lagring** i kommandofönstret. Utdata från kommandot liknar följande exempel.
 
    ```
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

3. Du kan köra manuellt på **ls** kommando för att kontrollera att en **nod\_moduler** mappen har skapats. I mappen hittar du den **azure storage** paket som innehåller de bibliotek som du behöver åtkomst till lagring.

### <a name="import-the-package"></a>Importera paketet
I anteckningar eller något annat textredigeringsprogram, lägger du till följande upp den **server.js** filen för programmet som du tänker använda lagring:

```
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Skapa ett Azure Storage-anslutning
Azure-modulen läses miljövariablerna AZURE\_lagring\_konto och AZURE\_lagring\_åtkomst\_nyckel eller AZURE\_lagring\_anslutning\_sträng för information som krävs för att ansluta till Azure storage-konto. Om de här miljövariablerna inte har angetts måste du ange kontoinformationen vid anrop av **createQueueService**.

Ett exempel på Ange miljövariabler i den [Azure Portal](https://portal.azure.com) en Azure-webbplats finns i [Node.js-webbapp som använder tjänsten Azure tabell].

## <a name="how-to-create-a-queue"></a>Så här: Skapa en kö
Följande kod skapar en **QueueService** -objektet, vilket gör att du kan arbeta med köer.

```
var queueSvc = azure.createQueueService();
```

Använd den **createQueueIfNotExists** metod som returnerar den angivna kön om det redan finns eller skapar en ny kö med det angivna namnet om det inte redan finns.

```
queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
  if(!error){
    // Queue created or exists
  }
});
```

Om kön har skapats, `result.created` är true. Om kön finns, `result.created` är false.

### <a name="filters"></a>Filter
Valfria filtrering åtgärder kan användas för åtgärder som utförs med hjälp av **QueueService**. Filtrering operations kan innehålla loggning, försöker automatiskt igen och så vidare. Filtren är objekt som implementerar en metod med signaturen:

```
function handle (requestOptions, next)
```

Metoden måste anropa ”nästa” Skicka en motringning med följande signatur när du har gjort dess förbearbetning på begäran-alternativ:

```
function (returnObject, finalCallback, next)
```

I den här motringning och efter bearbetning returnObject (svar från begäran till servern), måste återanropet anropa sedan om den finns för att fortsätta att bearbeta filter eller anropa bara finalCallback på annat sätt för att få service-anrop.

Två filter som implementerar logik som medföljer Azure SDK för Node.js, **ExponentialRetryPolicyFilter** och **LinearRetryPolicyFilter**. Följande kod skapar en **QueueService** objekt som använder den **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Så här: Infoga ett meddelande i en kö
Om du vill infoga ett meddelande i en kö, använda den **createMessage** metoden för att skapa ett nytt meddelande och lägga till den i kön.

```
queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Så här: Granska nästa meddelande
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa den **peekMessages** metod. Som standard **peekMessages** tittar på ett enda meddelande.

```
queueSvc.peekMessages('myqueue', function(error, result, response){
  if(!error){
    // Message text is in messages[0].messageText
  }
});
```

Den `result` innehåller meddelandet.

> [!NOTE]
> Med hjälp av **peekMessages** när det finns inga meddelanden i kön inte returneras ett fel, men inga meddelanden ska returneras.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Så här: Status Created nästa meddelande
Bearbetning av ett meddelande är en process i två steg:

1. Meddelandet har status Created.
2. Ta bort meddelandet.

För att ett meddelande har status Created använda **GetMessage**. Det gör att meddelanden i kön, så att inga andra klienter kan bearbeta dem. När programmet har bearbetats ett meddelande kan anropa **deleteMessage** ta bort det från kön. I följande exempel hämtar ett meddelande och sedan tar bort den:

```
queueSvc.getMessages('myqueue', function(error, result, response){
  if(!error){
    // Message text is in messages[0].messageText
    var message = result[0];
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
> Med hjälp av **GetMessage** när det finns inga meddelanden i kön inte returneras ett fel, men inga meddelanden ska returneras.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Så här: Ändra innehållet i ett meddelande i kön
Du kan ändra innehållet i ett meddelande direkt i kön med **updateMessage**. I följande exempel uppdateras texten för ett meddelande:

```
queueSvc.getMessages('myqueue', function(error, result, response){
  if(!error){
    // Got the message
    var message = result[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Så här: Ytterligare alternativ för mellan köer meddelanden
Det finns två sätt som du kan anpassa meddelandehämtningen från en kö:

* `options.numOfMessages`-Hämta en grupp med meddelanden (upp till 32).
* `options.visibilityTimeout`-Ange en tidsgräns för osynlighet längre eller kortare.

I följande exempel används den **GetMessage** metod för att hämta 15 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en for-loop. Den anger också tidsgränsen för osynlighet till fem minuter för alla meddelanden som returneras av den här metoden.

```
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
  if(!error){
    // Messages retreived
    for(var index in result){
      // text is available in result[index].messageText
      var message = result[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

## <a name="how-to-get-the-queue-length"></a>Så här: Hämta kölängden
Den **getQueueMetadata** returnerar metadata om kön, inklusive det ungefärliga antalet väntande meddelanden i kön.

```
queueSvc.getQueueMetadata('myqueue', function(error, result, response){
  if(!error){
    // Queue length is available in result.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Så här: Listan köer
Använd för att hämta en lista över köer **listQueuesSegmented**. Använd för att hämta en lista filtreras efter ett specifikt prefix **listQueuesSegmentedWithPrefix**.

```
queueSvc.listQueuesSegmented(null, function(error, result, response){
  if(!error){
    // result.entries contains the list of queues
  }
});
```

Om inte det går att returnera alla köer `result.continuationToken` kan användas som den första parametern för **listQueuesSegmented** eller den andra parametern för **listQueuesSegmentedWithPrefix** att hämta fler resultat.

## <a name="how-to-delete-a-queue"></a>Så här: Ta bort en kö
Om du vill ta bort en kö och alla meddelanden som finns i den anropar den **deleteQueue** metoden för köobjektet.

```
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Om du vill rensa alla meddelanden från en kö utan att ta bort den använda **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Så här: arbeta med signaturer för delad åtkomst
Delad åtkomst signaturer (SAS) är ett säkert sätt att tillhandahålla detaljerade åtkomst till köer utan att erbjuda dina lagringskontonamn eller nycklar. SAS används ofta för att ge begränsad åtkomst till din köer, till exempel att tillåta en mobil app att skicka meddelanden.

En betrodda program, till exempel en molnbaserad tjänst genererar en SAS med hjälp av den **generateSharedAccessSignature** av den **QueueService**, och ger den till ett program som inte är betrodd eller delvis betrodda. Till exempel en mobil app. SAS genereras med hjälp av en princip som beskriver de då SAS är giltig start- och slutdatum samt den åtkomstnivå som beviljats till SAS-innehavare.

I följande exempel skapar en ny princip för delad åtkomst som låter innehavaren SAS att lägga till meddelanden i kön och upphör att gälla 100 minuter efter den tidpunkt som den har skapats.

```
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

Observera att information om värden måste anges, eftersom det är nödvändigt när SAS-innehavaren försöker få åtkomst till kön.

Klientprogrammet sedan använder SAS med **QueueServiceWithSAS** att utföra åtgärder mot kön. I följande exempel ansluter till kön och skapar ett meddelande.

```
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Eftersom SAS genererades med Lägg till åtkomst om ett försök gjordes att läsa, uppdatera eller ta bort meddelanden, returneras ett fel.

### <a name="access-control-lists"></a>Listor för åtkomstkontroll
Du kan också använda en åtkomstkontrollista (ACL) för att ange åtkomstprincipen för en SAS. Detta är användbart om du vill att flera klienter kan komma åt kön, men ger olika åtkomstprinciper för varje klient.

En ACL implementeras med hjälp av en matris med principer för åtkomst med ett ID som är associerade med varje princip. I följande exempel definierar två principer; en för 'Användare1' och en för 'användare2':

```
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

I följande exempel hämtar den aktuella ACL för **MinKö**, lägger till nya principer med hjälp av **setQueueAcl**. Den här metoden kan:

```
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

Du kan sedan skapa en SAS baserat på ID för en princip när du har angett ACL. I följande exempel skapas en ny SAS för 'användare2':

```
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i queue storage följa dessa länkar för att lära dig mer komplexa lagringsuppgifter.

* Besök den [Azure Storage-teamets blogg][Azure Storage Team Blog].
* Besök den [Azure Storage SDK: N för noden] [ Azure Storage SDK for Node] databasen på GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Skapa en Node.js-webbapp i Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Skapa och distribuera ett Node.js-program till en Azure-molntjänst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
