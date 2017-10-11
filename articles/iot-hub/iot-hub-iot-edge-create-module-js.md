---
title: Skapa en Azure IoT-Edge-modul med Node.js | Microsoft Docs
description: "Den här kursen visar hur du skriver en TIVERA data konverteraren modul med de senaste Azure IoT kant NPM-paketen och Yeoman generator."
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.openlocfilehash: ba466f47e157d805600c41fa3d84ed5a0363969c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>Skapa en Azure IoT-Edge-modul med Node.js

Den här kursen visar hur du skapar en modul för Azure IoT gränsen i JS.

I den här självstudiekursen kommer vi att gå igenom miljökonfiguration och hur du skriver en [TIVERA](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) data konverteraren modul med de senaste Azure IoT kant NPM-paket.

## <a name="prerequisites"></a>Krav

I det här avsnittet kan du ställa in din miljö för utveckling av IoT kant-modulen. Det gäller både *64-bitars Windows* och *64-bitars Linux (Ubuntu 14 +)* operativsystem.

Följande programvara krävs:
* [Git klienten](https://git-scm.com/downloads).
* [Noden LTS](https://nodejs.org).
* `npm install -g yo`.
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>Arkitektur

Azure IoT kant-plattformen kraftigt antar den [Von Neumann arkitektur](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Vilket innebär att hela kant för Azure IoT-arkitekturen är ett system som bearbetar indata och utdata; och att varje enskild modul är en liten input-output-undersystemet. I den här självstudiekursen introducerar vi följande två moduler:

1. En modul som tar emot en simulerad [TIVERA](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) signalerar och konverterar den till en formaterad [JSON](https://en.wikipedia.org/wiki/JSON) meddelande.
2. En modul som skriver ut den mottagna [JSON](https://en.wikipedia.org/wiki/JSON) meddelande.

Följande bild visar den vanliga slutpunkt till slutpunkt-dataflöde för det här projektet:

![Dataflöde mellan tre moduler](media/iot-hub-iot-edge-create-module/dataflow.png "indata: simulerade TIVERA modulen. Processor: Konverterare modulen. Utdata: Skrivare modul")

## <a name="set-up-the-environment"></a>Konfigurera miljön
Nedan hur vi du snabbt ställa in miljön att börja skriva ditt första TIVERA konverteraren modulen med JS.

### <a name="create-module-project"></a>Skapa modul-projekt
1. Öppna ett kommandoradsfönster, kör `yo az-iot-gw-module`.
2. Följ anvisningarna på skärmen för att slutföra initieringen av projektet modulen.

### <a name="project-structure"></a>Projektstruktur
Ett JS modulen Projekt består av följande komponenter:

`modules`-Anpassade JS modulen källfilerna. Ersätt standard `sensor.js` och `printer.js` med modulen filerna.

`app.js`-Posten filen som ska starta Edge-instans.

`gw.config.json`-Konfigurationsfilen att anpassa modulerna som ska startas av kant.

`package.json`-Metadata-information för modulen projekt.

`README.md`-Den grundläggande dokumentationen för modulen projekt.


### <a name="package-file"></a>Paketfilen

Detta `package.json` deklarerar alla metadata-information som krävs av en modul-projekt som innehåller namn, version, post, skript, runtime och utveckling beroenden.

Följande kodavsnitt visar hur du konfigurerar för TIVERA konverteraren exempelprojektet.
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>Den första filen
Den `app.js` definierar sätt att initiera edge-instans. Här behöver vi inte göra några ändringar.

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>Gränssnittet för modulen
Du kan hantera en Azure IoT kant-modul som en data-processor vars uppgift är att: indata, behandlas och resultat.

Indata kan vara data från maskinvara (till exempel en rörelsedetektor), ett meddelande från andra moduler eller något annat (till exempel ett slumptal som genererats med jämna mellanrum av en timer).

Utdata liknar indata, den kan utlösa beteendet för maskinvara (till exempel blinkande LED), ett meddelande till andra moduler eller något annat (till exempel utskrift i konsolen).

Moduler som kommunicerar med varandra med hjälp av `message` objekt. Den **innehåll** av en `message` är en bytematris som kan som representerar alla typer av data som du vill. **Egenskaper för** är också tillgängliga i den `message` och är helt enkelt en string-string-mappning. Du kan tänka dig **egenskaper** som rubriker i en HTTP-begäran eller metadata för en fil.

För att utveckla en kant för Azure IoT-modul i JS, måste du skapa en ny Modulobjekt som implementerar obligatoriska metoder `receive()`. Du kan nu även välja att implementera den valfria `create()` eller `start()`, eller `destroy()` samt metoder. Följande kodavsnitt visar JS Modulobjekt scaffold-teknik.

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>Konverteraren modul
| Indata                    | Processor                              | Resultat                 | Källfilen            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Meddelande för temperatur-data | Analysera och skapa ett nytt JSON-meddelande | Strukturen JSON-meddelande | `converter.js` |

Denna modul är en typisk Azure IoT kant-modul. Den godkänner temperatur meddelanden från andra moduler (maskinvara modulen, eller i det här fallet våra simulerade Bell-modulen); och normaliserar temperatur meddelande i en strukturerad JSON-meddelandet (inklusive bifoga meddelande-ID, ställer in egenskapen för om vi behöver utlösa aviseringen temperatur och så vidare).

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>Modul för skrivare
| Indata                          | Processor | Resultat                     | Källfilen          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Alla meddelanden från andra moduler | Saknas       | Logga meddelandet-konsolen | `printer.js` |

Denna modul är enkla, självförklarande, som matar ut de mottagna meddelandena (egenskap, innehåll) till fönstret terminal.

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>Konfiguration
Det sista steget innan du kör modulerna som är att konfigurera Azure IoT kant och upprätta anslutningar mellan moduler.

Vi måste först deklarera vår `node` inläsaren (eftersom Azure IoT kant stöder inläsare med olika språk) som kan refereras till av dess `name` i avsnitten efteråt.

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

När vi har deklarerat våra inläsare, behöver vi även deklarera samt våra moduler. Precis som deklarerar inläsare, de kan också refereras av deras `name` attribut. När du deklarerar en modul, vi behöver ange ska det använda inläsaren (som ska vara det som vi definierade innan) och startpunkten (ska vara normaliserade klassnamnet för våra modulen) för varje modul. Den `simulated_device` modul är en inbyggd modul som ingår i Azure IoT kant core runtime-paketet. Inkludera `args` i JSON-filen även om det är `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

I slutet av konfigurationen upprätta vi anslutningar. Varje anslutning uttrycks av `source` och `sink`. De bör både referera en fördefinierad modul. Det utgående meddelandet i `source` modulen vidarebefordras till indata för `sink` modul.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>Kör moduler
1. `npm install`
2. `npm start`

Om du vill avsluta programmet trycker du på `<Enter>` nyckel.

> [!IMPORTANT]
> Det rekommenderas inte att använda Ctrl + C för att avsluta programmet IoT kant. Som det här sättet kan orsaka processen att avbrytas onormalt.
