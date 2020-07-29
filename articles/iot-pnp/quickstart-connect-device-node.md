---
title: Ansluta IoT Plug and Play Preview-exempel Node.js enhets kod till Azure IoT Hub | Microsoft Docs
description: Använd Node.js för att skapa och köra IoT Plug and Play Preview-exempel enhets kod som ansluts till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ericmitt
ms.author: ericmitt
ms.date: 07/10/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: f6a3190ec87c0b06aba8f065a3e7518f4a76cf24
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352988"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Snabb start: ansluta ett exempel på IoT Plug and Play Preview enhets program till IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. Exempel programmet är skrivet i Node.js och ingår i Azure IoT-enhetens SDK för Node.js. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten behöver du Node.js på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [NodeJS.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om du vill interagera med exempel enheten i den andra delen av den här snabb starten använder du **Azure IoT Explorer** -verktyget. [Hämta och installera den senaste versionen av Azure IoT Explorer](./howto-use-iot-explorer.md) för ditt operativ system.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Du kan också använda Azure IoT Explorer-verktyget för att hitta anslutnings strängen för IoT Hub.

Kör följande kommando för att hämta _enhets anslutnings strängen_ för den enhet som du har lagt till i hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Ladda ned koden

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och skapa Azure IoT Hub Device SDK för Node.js.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Microsoft Azure IoT SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub-lagringsplatsen på den här platsen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Installera nödvändiga bibliotek

Du kan använda enhets-SDK: n för att bygga den inkluderade exempel koden. Det program som du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. I ett lokalt terminalfönster går du till mappen för den klonade lagrings platsen och navigerar till mappen */Azure-IoT-SDK-Node/Device/samples/PnP* . Kör sedan följande kommando för att installera de bibliotek som krävs:

    ```cmd/sh
    npm install
    ```

1. Konfigurera miljövariabeln med enhets anslutnings strängen som du antecknade tidigare:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Kör exempel enheten

Öppna _simple_thermostat.js_ -filen. I den här filen kan du se hur du:

1. Importera de nödvändiga gränssnitten.
1. Skriv en uppdaterings hanterare för egenskapen och en kommando hanterare.
1. Hantera önskade egenskaps korrigeringar och skicka telemetri.
1. Du kan också etablera din enhet med hjälp av Azure Device Provisioning-tjänsten (DPS).

I huvud funktionen kan du se hur det kommer samman:

1. Skapa enheten från anslutnings strängen eller etablera den med hjälp av DPS.)
1. Använd alternativet **modelID** för att ange IoT plug and Play enhets modellen.
1. Aktivera kommando hanteraren.
1. Skicka telemetri från enheten till hubben.
1. Hämta enheterna och uppdatera de rapporterade egenskaperna.
1. Aktivera önskad egenskaps uppdaterings hanterare.

Kör exempel programmet för att simulera en IoT Plug and Play-enhet som skickar telemetri till IoT Hub. Använd följande kommando för att köra exempel programmet:

```cmd\sh
node simple_thermostat.js
```

Följande utdata visas, vilket anger att enheten har börjat skicka telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar.

![Enhets bekräftelse meddelanden](media/quickstart-connect-device-node/device-confirmation-node.png)

Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din lösning](quickstart-service-node.md)
