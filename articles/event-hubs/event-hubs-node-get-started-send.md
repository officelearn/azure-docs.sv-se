---
title: Skicka händelser till Azure Event Hubs med hjälp av Node.js | Microsoft Docs
description: Kom igång med att skicka händelser till Event Hubs med hjälp av Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: bb5a7b477b2d19c74cc645a15cc3d891c76f28c5
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427203"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Skicka händelser till Azure Event Hubs med hjälp av Node.js

Azure Event Hubs är en mycket skalbar Händelsehanteringssystem som kan hantera flera miljoner händelser per sekund programmen kan bearbeta och analysera stora datamängder som produceras av anslutna enheter och andra system. När samlats in i en händelsehubb, du kan ta emot och hantera händelser med hjälp av pågående hanterare eller vidarebefordran till andra system för analys av.

Mer information om Händelsehubbar finns i [översikt av Händelsehubbar](event-hubs-about.md).

Den här självstudien beskrivs hur du skickar händelser till en händelsehubb från ett program som skrivits i Node.js. Om du vill ta emot händelser med hjälp av Node.js Event Processor Host-paketet, se [motsvarande Receive-artikel](event-hubs-node-get-started-receive.md).

Kod för den här snabbstarten på [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- Node.js-version 8.x och högre. Hämta den senaste versionen LTS från [ https://nodejs.org ](https://nodejs.org).
- Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.
- Visual Studio Code (rekommenderas) eller andra IDE

## <a name="create-a-namespace-and-event-hub"></a>Skapa ett namnområde och en händelsehubb
Det första steget är att använda Azure portal för att skapa ett namnområde för Event Hubs med en händelsehubb. Om du inte har en befintlig, du kan skapa dessa enheter genom att följa instruktionerna i [skapa ett Event Hubs-namnområde och en event hub med Azure-portalen](event-hubs-create.md).

## <a name="clone-the-sample-git-repository"></a>Klona Git-lagringsplatsen för exemplet
Klona Git-lagringsplatsen för exemplet från [Github](https://github.com/Azure/azure-event-hubs-node) på din dator. 

## <a name="install-nodejs-package"></a>Installera Node.js-paket
Installera Node.js-paket för Azure Event Hubs på din dator. 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Klona Git-lagringsplats
Ladda ned eller klona den [exempel](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) från Github. 

## <a name="send-events"></a>Skicka händelser
SDK: N som du har klonat innehåller flera exempel som visar dig hur du skickar händelser till en event hub med node.js. I den här snabbstarten använder du den **simpleSender.js** exempel. Om du vill se händelser som tas emot, öppnar du en annan terminal och ta emot händelser med hjälp av den [får exempel](event-hubs-node-get-started-receive.md).

1. Öppna projektet i Visual Studio Code. 
2. Skapa en fil med namnet **.env** under den **klienten** mapp. Kopiera och klistra in exemplet miljövariabler från den **sample.env** i rotmappen.
3. Konfigurera händelsehubbens anslutningssträng, händelsehubbens namn och slutpunkt för lagring. Du kan kopiera anslutningssträngen för din händelsehubb från **anslutning anslutningssträng-primär** viktiga under **RootManageSharedAccessKey** på sidan Händelsehubb i Azure-portalen. Detaljerade anvisningar finns i [hämta anslutningssträngen](event-hubs-create.md#create-an-event-hubs-namespace).
4. På din Azure-CLI, navigerar du till den **klienten** mappsökväg. Installera paket i noden och skapa projektet genom att köra följande kommandon:

    ```nodejs
    npm i
    npm run build
    ```
5. Börja skicka händelser genom att köra följande kommando: 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Granska exempelkoden 
Här är exempelkod för att skicka händelser till en event hub med node.js. Du kan manuellt skapa en sampleSender.js-fil och kör den för att skicka händelser till en händelsehubb. 


```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
}

main().catch((err) => {
    console.log(err);
});

```

Kom ihåg att ange din miljövariabler innan du kör skriptet. Du kan konfigurera det på kommandoraden som visas i följande exempel eller Använd den [dotenv paketet](https://www.npmjs.com/package/dotenv#dotenv). 

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för att lära dig mer om Event Hubs:

* [Ta emot händelser med hjälp av Node.js](event-hubs-node-get-started-receive.md)
* [Exempel på GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Links -->
[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
