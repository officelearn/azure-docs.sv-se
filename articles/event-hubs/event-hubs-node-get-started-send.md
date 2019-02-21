---
title: Skicka händelser med hjälp av Node.js – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genomgång för att skapa ett Node.js-program som skickar händelser från Azure Event Hubs.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: ec3182d11f1b2ffa31acd05fa1f2db695f3f2cf7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447726"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Skicka händelser till Azure Event Hubs med hjälp av Node.js

Azure Event Hubs är en Big Data som strömmas-plattformen och händelsen händelseinmatningstjänst som kan ta emot och bearbeta miljontals händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudien beskrivs hur du skickar händelser till en händelsehubb från ett program som skrivits i Node.js.

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- Node.js-version 8.x och högre. Hämta den senaste versionen LTS från [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (rekommenderas) eller andra IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett namnområde för Event Hubs och en händelsehubb
Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [i den här artikeln](event-hubs-create.md), fortsätt sedan med följande steg i den här självstudien.

Hämta anslutningssträngen för händelsehubbens namnområde genom att följa anvisningarna i artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du kan använda anslutningssträngen senare i den här självstudien.

## <a name="clone-the-sample-git-repository"></a>Klona Git-lagringsplatsen för exemplet
Klona Git-lagringsplatsen för exemplet från [GitHub](https://github.com/Azure/azure-event-hubs-node) på din dator. 

## <a name="install-nodejs-package"></a>Installera Node.js-paket
Installera Node.js-paket för Azure Event Hubs på din dator. 

```shell
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Klona Git-lagringsplats
Ladda ned eller klona den [exempel](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) från GitHub. 

## <a name="send-events"></a>Skicka händelser
SDK: N som du har klonat innehåller flera exempel som visar dig hur du skickar händelser till en event hub med node.js. I den här snabbstarten använder du den **simpleSender.js** exempel. Om du vill se händelser som tas emot, öppnar du en annan terminal och ta emot händelser med hjälp av den [får exempel](event-hubs-node-get-started-receive.md).

1. Öppna projektet i Visual Studio Code. 
2. Skapa en fil med namnet **.env** under den **klienten** mapp. Kopiera och klistra in exemplet miljövariabler från den **sample.env** i rotmappen.
3. Konfigurera händelsehubbens anslutningssträng, händelsehubbens namn och slutpunkt för lagring. Mer information om hur du hämtar en anslutningssträng för en händelsehubb [hämta anslutningssträngen](event-hubs-create.md#create-an-event-hubs-namespace).
4. På din Azure-CLI, navigerar du till den **klienten** mappsökväg. Installera paket i noden och skapa projektet genom att köra följande kommandon:

    ```shell
    npm i
    npm run build
    ```
5. Börja skicka händelser genom att köra följande kommando: 

    ```shell
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Granska exempelkoden 
Granska exempelkoden i filen simpleSender.js att skicka händelser till en händelsehubb.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Kom ihåg att ange din miljövariabler innan du kör skriptet. Du kan konfigurera dem på kommandoraden som visas i följande exempel eller Använd den [dotenv paketet](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
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
