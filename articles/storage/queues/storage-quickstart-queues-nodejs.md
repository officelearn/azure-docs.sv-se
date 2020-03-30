---
title: 'Snabbstart: Azure Queue storage library v12 - JavaScript'
description: Lär dig hur du använder Azure Queue JavaScript v12-biblioteket för att skapa en kö och lägga till meddelanden i kön. Därefter får du lära dig att läsa och ta bort meddelanden från kön. Du får också lära dig hur du tar bort en kö.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 59a5308d2c0a1fa2e1f38f2fe3da3a2cc29448be
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199792"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Snabbstart: Azure Queue storage client library v12 för JavaScript

Kom igång med Azure Queue storage client library version 12 för JavaScript. Azure Queue storage är en tjänst för att lagra ett stort antal meddelanden för senare hämtning och bearbetning. Följ dessa steg för att installera paketet och prova exempelkod för grundläggande uppgifter.

Använd Azure Queue storage client library v12 för JavaScript för att:

* Skapa en kö
* Lägga till meddelanden i en kö
* Granska meddelanden i en kö
* Uppdatera ett meddelande i en kö
* Ta emot meddelanden från en kö
* Ta bort meddelanden från en kö
* Ta bort en kö

[API-referensdokumentation](https://docs.microsoft.com/javascript/api/@azure/storage-queue/) | [Bibliotekskodpaket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue) | [(Nod Package Manager)](https://www.npmjs.com/package/@azure/storage-queue) | [Exempel](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
* Azure storage-konto - [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Aktuella [nod.js](https://nodejs.org/en/download/) för ditt operativsystem.

## <a name="setting-up"></a>Inrätta

I det här avsnittet beskrivs hur du förbereder ett projekt för att arbeta med Azure Queue storage client library v12 för JavaScript.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Node.js-program med namnet *queues-quickstart-v12*.

1. Skapa en ny katalog för projektet i ett konsolfönster (till exempel cmd, PowerShell eller Bash).

    ```console
    mkdir queues-quickstart-v12
    ```

1. Växla till den nyskapade *kökatalogen-quickstart-v12.*

    ```console
    cd queues-quickstart-v12
    ```

1. Skapa en ny textfil som heter *package.json*. Den här filen definierar nod.js-projektet. Spara filen i katalogen *köer-quickstart-v12.* Här är innehållet i filen:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Du kan sätta ditt eget `author` namn i för fältet, om du vill.

### <a name="install-the-package"></a>Installera paketet

Medan du fortfarande är i *katalogen köer-quickstart-v12* installerar du Azure `npm install` Queue storage client library för JavaScript-paketet med kommandot.

```console
npm install
```

 Det här kommandot läser *filen package.json* och installerar Azure Queue storage client library v12 for JavaScript-paketet och alla bibliotek som det är beroende av.

### <a name="set-up-the-app-framework"></a>Konfigurera ramverket för appen

Från projektkatalogen:

1. Öppna en ny textfil i kodredigeraren
1. Lägga `require` till anrop för att läsa in Azure- och Node.js-moduler
1. Skapa strukturen för programmet, inklusive mycket grundläggande undantagshantering

    Här är koden:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Spara den nya filen som *köer-quickstart-v12.js* i *katalogen köer-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Ett kömeddelande kan vara upp till 64 kB i storlek. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Köer används ofta för att skapa en eftersläpning av arbete för att bearbeta asynkront. Kölagring erbjuder tre typer av resurser:

* Lagringskontot
* En kö i lagringskontot
* Meddelanden i kön

Följande diagram visar relationen mellan de här resurserna.

![Diagram över lagringsarkitektur för kö](./media/storage-queues-introduction/queue1.png)

Använd följande JavaScript-klasser för att interagera med dessa resurser:

* [QueueServiceClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueserviceclient): `QueueServiceClient` Med möjlighet att hantera alla köer i ditt lagringskonto.
* [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient): `QueueClient` Klassen låter dig hantera och manipulera en enskild kö och dess meddelanden.
* [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage): `QueueMessage` Klassen representerar de enskilda objekt som returneras när du [anropar mottaMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient?view=azure-node-latest#receivemessages-queuereceivemessageoptions-) i en kö.

## <a name="code-examples"></a>Kodexempel

I det här exemplet visar kodavsnitten hur du utför följande åtgärder med Azure Queue storage-klientbiblioteket för JavaScript:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en kö](#create-a-queue)
* [Lägga till meddelanden i en kö](#add-messages-to-a-queue)
* [Granska meddelanden i en kö](#peek-at-messages-in-a-queue)
* [Uppdatera ett meddelande i en kö](#update-a-message-in-a-queue)
* [Ta emot meddelanden från en kö](#receive-messages-from-a-queue)
* [Ta bort meddelanden från en kö](#delete-messages-from-a-queue)
* [Ta bort en kö](#delete-a-queue)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutningssträngen för lagringskontot från den miljövariabel som skapas i avsnittet [Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den `main` här koden i funktionen:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be 
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Skapa en kö

Bestäm ett namn för den nya kön. Koden nedan lägger till ett UUID-värde i könamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Könamn får bara innehålla gemener, siffror och bindestreck och måste börja med en bokstav eller ett tal. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck. Namnet måste också vara mellan 3 och 63 tecken långt. Mer information om namngivning av köer finns i [Namnge köer och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata).

Skapa en instans av klassen [QueueClient.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) Anropa sedan [metoden create](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) för att skapa kön i ditt lagringskonto.

Lägg till den här `main` koden i slutet av funktionen:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Lägga till meddelanden i en kö

Följande kodavsnitt lägger till meddelanden i kö genom att anropa [metoden sendMessage.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) Den sparar också [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage) som returneras från det tredje `sendMessage` samtalet. Den returnerade `sendMessageResponse` används för att uppdatera meddelandeinnehållet senare i programmet.

Lägg till den här `main` koden i slutet av funktionen:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Granska meddelanden i en kö

Kika på meddelandena i kön genom att anropa [peekMessages-metoden.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) Metoden `peekMessages` hämtar ett eller flera meddelanden från framsidan av kön men ändrar inte meddelandets synlighet.

Lägg till den här `main` koden i slutet av funktionen:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Uppdatera ett meddelande i en kö

Uppdatera innehållet i ett meddelande genom att anropa [metoden updateMessage.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) Metoden `updateMessage` kan ändra ett meddelandes synlighetstidsutgång och innehåll. Meddelandeinnehållet måste vara en UTF-8-kodad sträng som är upp till 64 kB i storlek. Tillsammans med det nya `messageId` innehållet, skicka in och `popReceipt` från svaret som sparades tidigare i koden. Egenskaperna `sendMessageResponse` identifierar vilket meddelande som ska uppdateras.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Hämta tidigare tillagda meddelanden genom att anropa metoden [receiveMessages.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-)  I `numberOfMessages` fältet skickar du in det maximala antalet meddelanden som ska mottars för det här samtalet.

Lägg till den här `main` koden i slutet av funktionen:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Ta bort meddelanden från en kö

Ta bort meddelanden från kön när de har tagits emot och bearbetats. I det här fallet är bearbetning bara visar meddelandet på konsolen.

Ta bort meddelanden genom att anropa [metoden deleteMessage.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) Alla meddelanden som inte uttryckligen tas bort kommer så småningom att bli synliga i kön igen för en ny chans att bearbeta dem.

Lägg till den här `main` koden i slutet av funktionen:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Ta bort en kö

Följande kod rensar de resurser som appen har skapat genom att ta bort kön med [borttagningsmetoden.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-)

Lägg till den här `main` koden i slutet av funktionen och spara filen:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar och lägger till tre meddelanden i en Azure-kö. Koden visar meddelandena i kön och hämtar och tar sedan bort dem innan du slutligen tar bort kön.

I konsolfönstret navigerar du till katalogen som innehåller filen *köer-quickstart-v12.js* och kör sedan följande `node` kommando för att köra appen.

```console
node queues-quickstart-v12.js
```

Utdata för appen liknar följande exempel:

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Gå igenom koden i felsökaren och kontrollera din [Azure-portal](https://portal.azure.com) under hela processen. Kontrollera ditt lagringskonto för att verifiera att meddelanden i kön skapas och tas bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar en kö och lägger till meddelanden i den med JavaScript-kod. Sedan lärde du dig att granska, hämta och ta bort meddelanden. Slutligen har du lärt dig hur du tar bort en meddelandekö.

För självstudier, exempel, snabbstarter och annan dokumentation, besök:

> [!div class="nextstepaction"]
> [Azure för JavaScript-dokumentation](https://docs.microsoft.com/azure/javascript/)

* Mer information finns i [Azure Storage Queue-klientbiblioteket för JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
* Om du vill se fler exempelappar för Azure Queue storage fortsätter du till [Azure Queue storage client library v12 JavaScript samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
