---
title: Interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din Azure IoT-lösning (Node.js) | Microsoft Docs
description: Använd Node.js för att ansluta till och interagera med en IoT Plug and Play-förhands gransknings enhet som är ansluten till din Azure IoT-lösning.
author: elhorton
ms.author: elhorton
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: fd65dcc9ce0be07daa5848a0ac583cf795150e47
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184779"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Snabb start: interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din lösning (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug and Play Preview fören klar IoT genom att göra det möjligt för dig att interagera med enhetens funktioner utan att du behöver känna till den underliggande enhets implementeringen. Den här snabb starten visar hur du använder Node.js för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten behöver du Node.js på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [NodeJS.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Kör följande kommando för att hämta _enhets anslutnings strängen_ för den enhet som du har lagt till i hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klona SDK-lagringsplatsen med exempel koden

Service SDK är i för hands version, så du måste klona exemplen från en [förhands gransknings gren av nodens SDK](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh). Öppna ett terminalfönster i valfri mapp. Kör följande kommando för att klona **PnP-Preview-Refresh-** grenen i [Microsoft Azure IoT SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub-lagringsplatsen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

I den här snabb starten kan du använda ett exempel på en termostat-enhet som är skriven i Node.js som IoT Plug and Play-enheten. Så här kör du exempel enheten:

1. Öppna ett terminalfönster och navigera till den lokala mappen som innehåller Microsoft Azure IoT SDK för Node.js lagrings plats som du har klonat från GitHub.

1. Det här terminalfönstret används som **enhetens** Terminal. Gå till mappen för den klonade lagrings platsen och navigera till mappen */Azure-IoT-SDK-Node/Device/samples/PnP* . Installera alla beroenden genom att köra följande kommando:

    ```cmd/sh
    npm install
    ```

1. Konfigurera _enhets anslutnings strängen_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Kör exempel termostat-enheten med följande kommando:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Du ser meddelanden som säger att enheten har skickat viss information och rapporterat online. Dessa meddelanden indikerar att enheten har börjat skicka telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar. Stäng inte den här terminalen, du behöver den för att bekräfta att tjänst exemplet fungerar.

## <a name="run-the-sample-solution"></a>Kör exempel lösningen

I den här snabb starten använder du en exempel-IoT-lösning i Node.js för att interagera med den exempel enhet som du nyss konfigurerat.

1. Öppna ett annat terminalfönster som ska användas som **tjänstens** Terminal. Service SDK är i för hands version, så du måste klona exemplen från en [förhands gransknings gren av nodens SDK](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
    ```

1. Gå till mappen för den här klonade databas grenen och navigera till mappen */Azure-IoT-SDK-Node/digitaltwins/samples/service/JavaScript* . Installera alla beroenden genom att köra följande kommando:

    ```cmd/sh
    npm install
    ```

1. Konfigurera miljövariabler för enhets-ID och _IoT Hub anslutnings sträng_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>Läsa en egenskap

1. När du körde exempel termostat-enheten i **enhets** terminalen såg du följande meddelanden som visar sin onlinestatus:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Gå till **tjänsten** Terminal och Använd följande kommando för att köra exemplet för att läsa enhets information:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Lägg märke till svaret på den digitala dubbla i **tjänstens** Terminal-utdata. Du ser enhetens modell-ID och tillhör ande egenskaper som rapporter ATS:

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. I följande kodfragment visas koden i *get_digital_twin.js* som hämtar enhetens modell-ID:

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

I det här scenariot matas den ut `Model Id: dtmi:com:example:Thermostat;1` .

### <a name="update-a-writable-property"></a>Uppdatera en skrivbar egenskap

1. Öppna filen *update_digital_twin.js* i en kod redigerare.

1. Granska exempel koden. Du kan se hur du skapar en JSON-korrigering för att uppdatera enhetens digitala enhet. I det här exemplet ersätter koden termostat temperatur med värdet 42:

    ```javascript
    const patch = [{
        op: 'add',
        path: '/targetTemperature',
        value: '42'
      }]
    ```

1. I **tjänsten** Terminal använder du följande kommando för att köra exemplet för att uppdatera egenskapen:

    ```cmd/sh
    node update_digital_twin.js
    ```

1. I **enhetens** Terminal ser du att enheten har tagit emot uppdateringen:

    ```cmd/sh
    The following properties will be updated for root interface:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    Properties have been reported for component
    ```

1. Kör följande kommando i din **tjänst-** Terminal för att bekräfta att egenskapen har uppdaterats:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. I **tjänstens** Terminal-utdata, i det digitala dubbla svaret under `thermostat1` komponenten, ser du att den uppdaterade mål temperaturen har rapporter ATS. Det kan ta en stund innan enheten har slutfört uppdateringen. Upprepa det här steget tills enheten har bearbetat egenskaps uppdateringen:

    ```json
    targetTemperature: 42,
    ```

### <a name="invoke-a-command"></a>Anropa ett kommando

1. Öppna filen *invoke_command.js* och granska koden.

1. Gå till **tjänsten** Terminal. Använd följande kommando för att köra exemplet för att anropa kommandot:

    ```cmd/sh
    set IOTHUB_COMMAND_NAME=getMaxMinReport
    set IOTHUB_COMMAND_PAYLOAD=commandpayload
    node invoke_command.js
    ```

1. Utdata i **tjänst** terminalen visar följande bekräftelse:

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. I **enhetens** Terminal ser du att kommandot har bekräftats:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-lösning. Mer information om IoT Plug and Play enhets modeller finns i:

> [!div class="nextstepaction"]
> [IoT Plug and Play Preview Modeling Developer Guide](concepts-developer-guide.md)
