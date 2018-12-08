---
title: Skicka händelser med hjälp av Node.js – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genomgång för att skapa ett Node.js-program som skickar händelser från Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 15ba584e882dd5a28b74094f949843ed653ad400
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104075"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Skicka händelser till Azure Event Hubs med hjälp av Node.js

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudien beskrivs hur du skickar händelser till en händelsehubb från ett program som skrivits i Node.js.

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- Node.js-version 8.x och högre. Hämta den senaste versionen LTS från [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (rekommenderas) eller andra IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett namnområde för Event Hubs och en händelsehubb
Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md) och fortsätter sedan enligt följande steg i den här självstudien.

Hämta anslutningssträngen för händelsehubbens namnområde genom att följa anvisningarna i artikeln: [hämta anslutningssträngen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du kan använda anslutningssträngen för senare i den här självstudien.

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
I den här snabbstarten har du skickat meddelanden till en event hub med Node.js. Läs hur du tar emot händelser från en event hub med Node.js i [ta emot händelser från event hub - Node.js](event-hubs-node-get-started-receive.md)

Titta på andra Node.js-exempel för Event Hubs på [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
