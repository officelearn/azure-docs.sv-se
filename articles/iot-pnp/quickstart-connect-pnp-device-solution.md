---
title: Interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din Azure IoT-lösning | Microsoft Docs
description: Använd Node. js för att ansluta till och interagera med en IoT-Plug and Play förhands gransknings enhet som är ansluten till din Azure IoT-lösning.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a082e4b7896b317bf2b28971d3693bada95a3445
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806559"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>Snabbstart: Interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din lösning

IoT Plug and Play Preview fören klar IoT genom att göra det möjligt för dig att interagera med enhetens funktioner utan att du behöver känna till den underliggande enhets implementeringen. Den här snabb starten visar hur du använder Node. js för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Förutsättningar

Hämta och installera Node. js från [NodeJS.org](https://nodejs.org).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Förbered en IoT-hubb

Du behöver också en Azure IoT-hubb i din Azure-prenumeration för att slutföra den här snabb starten. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Under den offentliga för hands versionen är IoT Plug and Play-funktioner bara tillgängliga på IoT-hubbar som skapats i regionerna **Central USA**, **Nord Europa**och **Östra Japan** .

Lägg till Microsoft Azure IoT-tillägg för Azure CLI:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Kör följande kommando för att skapa enhets identiteten i din IoT-hubb. Ersätt **YourIoTHubName** och **YourDevice** med dina faktiska namn. Om du inte har något IoT Hub, följer du [dessa anvisningar](../iot-hub/iot-hub-create-using-cli.md) för att skapa ett:

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Kör följande kommandon för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Kör följande kommandon för att hämta _anslutnings strängen för IoT Hub_ för hubben:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>Anslut enheten

I den här snabb starten använder du en exempel miljö sensor som är skriven i Node. js som IoT Plug and Play-enhet. Följande instruktioner visar hur du installerar och kör enheten:

1. Klona GitHub-lagringsplatsen:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. I en Terminal går du till rotmappen för den klonade lagrings platsen, navigerar till mappen **/Azure-IoT-samples-Node/Digital-Twins/Quickstarts/Device** och installerar sedan alla beroenden genom att köra följande kommando:

    ```cmd/sh
    npm install
    ```

1. Konfigurera _enhets anslutnings strängen_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. Kör exemplet med följande kommando:

    ```cmd/sh
    node sample_device.js
    ```

1. Du ser meddelanden som säger att enheten har skickat telemetri och dess egenskaper. Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar. Stäng inte den här terminalen, du behöver den senare för att bekräfta att tjänst exemplen också fungerade.

## <a name="build-the-solution"></a>Skapa lösningen

I den här snabb starten använder du en exempel-IoT-lösning i Node. js för att interagera med exempel enheten.

1. Öppna en annan terminal. Gå till mappen för den klonade lagrings platsen och navigera till mappen **/Azure-IoT-samples-Node/Digital-Twins/Quickstarts/service** . Installera alla beroenden genom att köra följande kommando:

    ```cmd/sh
    npm install
    ```

1. Konfigurera _navets anslutnings sträng_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>Läsa en egenskap

1. När du ansluter enheten i terminalen visas följande meddelande:

    ```cmd/sh
    reported state property as online
    ```

1. Öppna filen **get_digital_twin. js**. `deviceID` Ersätt med ditt enhets-ID och spara filen.

1. Gå till den terminal som du öppnade för att köra tjänst exempel och kör följande kommando:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. I utdata, under komponenten _environmentalSensor_ , ser du att samma tillstånd har rapporter ATS:

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>Uppdatera en skrivbar egenskap

1. Öppna filen **update_digital_twin_property. js**.

1. I början av filen finns en uppsättning konstanter definierade med plats hållare för versaler. Ersätt **deviceID** med ditt faktiska enhets-ID, uppdatera konstanterna med följande värden och spara filen:

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. Gå till den terminal som du öppnade för att köra tjänst exempel och Använd följande kommando för att köra exemplet:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. I terminalen ser du den digitala dubbla informationen som är kopplad till din enhet. Hitta komponenten _environmentalSensor_. du ser det nya värdet för ljus styrka 60.

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
            }
          },
          "state": {
            "reported": {
              "value": "online"
            }
          }
        }
      }
    ```

1. Gå till _enhetens_ Terminal. du ser att enheten har tagit emot uppdateringen:

    ```cmd/sh
    Received an update for brightness: 60
    updated the property
    ```
2. Gå tillbaka till din _tjänst_ Terminal och kör kommandot nedan för att bekräfta att egenskapen har uppdaterats.
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. I utdata, under environmentalSensor-komponenten, ser du att det uppdaterade värdet för ljus styrka har rapporter ATS. Obs: det kan ta en stund innan enheten har slutfört uppdateringen. Du kan upprepa det här steget tills enheten faktiskt har bearbetat egenskaps uppdateringen.
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>Anropa ett kommando

1. Öppna filen **invoke_command. js**.

1. Ersätt `deviceID` med det aktuella enhets-ID: t i början av filen. Uppdatera konstanterna med följande värden och spara sedan filen:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. Gå till den terminal som du öppnade för att köra tjänst exempel. Använd följande kommando för att köra exemplet:

    ```cmd/sh
    node invoke_command.js
    ```

1. I terminalen ser framgång ut som följande utdata:

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. Gå till _enhetens_ Terminal. du ser att kommandot har bekräftats:

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med senare artiklar kan du behålla de resurser som du använde i den här snabb starten. Annars kan du ta bort de resurser som du har skapat för den här snabb starten för att undvika ytterligare kostnader.

Om du vill ta bort hubben och den registrerade enheten utför du följande steg med hjälp av Azure CLI:

```azurecli-interactive
az group delete --name <Your group name>
```

Om du bara vill ta bort den enhet som du registrerade med IoT Hub utför du följande steg med hjälp av Azure CLI:

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-lösning. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Instruktioner: Ansluta till och interagera med en enhet](howto-develop-solution.md)
