---
title: Snabb start – Lägg till VOIP-anrop till en webbapp med Azure Communication Services
description: I den här självstudien får du lära dig hur du använder Azure Communication Services som anropar klient bibliotek för Java Script
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d35e51d097c2d5e0b66c23efa27ae70c065d547c
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96584464"
---
I den här snabb starten får du lära dig hur du startar ett samtal med Azure Communication Services som anropar klient bibliotek för Java Script.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktiva LTS-och underhålls LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
- En Active Communication Services-resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- En token för användar åtkomst för att instansiera anrops klienten. Lär dig hur du [skapar och hanterar användar åtkomst-token](../../access-tokens.md).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Öppna terminalen eller kommando fönstret Skapa en ny katalog för din app och navigera till den.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Kör `npm init -y` för att skapa en **package.jspå** en fil med standardinställningar.

```console
npm init -y
```

### <a name="install-the-package"></a>Installera paketet

Använd `npm install` kommandot för att installera Azure Communication Services som anropar klient bibliotek för Java Script.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Följande versioner av WebPack rekommenderas för den här snabb starten:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

I det här `--save` alternativet visas biblioteket som ett beroende i **package.jsi** filen.

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

I den här snabb starten används WebPack för att paketera program till gångarna. Kör följande kommando för att installera WebPack-, WebPack-CLI-och WebPack-dev-Server NPM-paket och lista dem som utvecklings beroenden i din **package.jspå**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Skapa en **index.html** -fil i projektets rot Katalog. Vi använder den här filen för att konfigurera en grundläggande layout som gör det möjligt för användaren att placera ett anrop till en Azure Communications-robot.

Här är koden:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Skapa en fil i projektets rot Katalog som kallas **client.js** som innehåller program logiken för den här snabb starten. Lägg till följande kod för att importera den anropande klienten och få referenser till DOM-element så att vi kan koppla vår affärs logik. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services som anropar klient biblioteket:

| Namn                             | Beskrivning                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient är den huvudsakliga start punkten för det anropande klient biblioteket.                                                                       |
| CallAgent                        | CallAgent används för att starta och hantera samtal.                                                                                            |
| AzureCommunicationUserCredential | AzureCommunicationUserCredential-klassen implementerar CommunicationUserCredential-gränssnittet som används för att instansiera CallAgent. |


## <a name="authenticate-the-client"></a>Autentisera klienten

Du måste ersätta `<USER_ACCESS_TOKEN>` med en giltig åtkomsttoken för din resurs. Se dokumentationen för [användar åtkomst-token](../../access-tokens.md) om du inte redan har en tillgänglig token. Med hjälp av `CallClient` initierar du en `CallAgent` instans med en som gör det `CommunicationUserCredential` möjligt för oss att ringa och ta emot samtal. Lägg till följande kod i **client.js**:

```javascript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;
}
init();
```

## <a name="start-a-call"></a>Starta ett anrop

Lägga till en händelse hanterare för att initiera ett anrop när `callButton` användaren klickar på den:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Avsluta ett anrop

Lägg till en händelse lyssnare för att avsluta det aktuella anropet när `hangUpButton` användaren klickar på den:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

`forEveryone`Egenskapen avslutar anropet för alla anrops deltagare.

## <a name="run-the-code"></a>Kör koden

Använd `webpack-dev-server` för att skapa och köra din app. Kör följande kommando för att paketera program värden i på en lokal webserver:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Öppna webbläsaren och gå till http://localhost:8080/ . Du bör se följande:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Skärm bild av det färdiga JavaScript-programmet.":::

Du kan göra ett utgående VOIP-anrop genom att ange ett användar-ID i fältet text och klicka på knappen **starta samtal** . `8:echo123`Genom att anropa ansluter du med en eko robot är det bra för att komma igång och kontrol lera att ljud enheterna fungerar.
