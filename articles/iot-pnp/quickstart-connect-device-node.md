---
title: Snabb start – ansluta IoT Plug and Play exempel Node.js enhets kod till Azure IoT Hub | Microsoft Docs
description: Snabb start – Använd Node.js för att skapa och köra IoT Plug and Play exempel enhets kod som ansluts till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ericmitt
ms.author: ericmitt
ms.date: 07/10/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 24f6cc1e9656957d6894ecb6b7c3e0476b233cf6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421591"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-to-iot-hub-nodejs"></a>Snabb start: ansluta ett exempel på IoT Plug and Play enhets program till IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. Exempel programmet är skrivet i Node.js och ingår i Azure IoT-enhetens SDK för Node.js. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

För att slutföra den här snabb starten behöver du Node.js på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [NodeJS.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

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

I det här exemplet implementeras en enkel IoT Plug and Play termostat-enhet. Modellen som det här exemplet implementerar använder inte IoT Plug and Play- [komponenter](concepts-components.md). [DTDL-modell filen för termostat-enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

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

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

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

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Interagera med en IoT Plug and Play-enhet som är ansluten till din lösning](quickstart-service-node.md)
