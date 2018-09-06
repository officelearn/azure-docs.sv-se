---
title: Snabbstart – Skicka telemetri till Azure IoT Hub (Python) | Microsoft Docs
description: I den här snabbstarten kör du ett Python-exempelprogram som skickar simulerad telemetri till en IoT-hubb och använder ett verktyg för att läsa telemetrin från IoT-hubben.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: 7d5f2246eec20144a30e0abbc31038bdf04ab2b0
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43339284"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-python"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb och läs telemetrin från navet med ett serverdelsprogram (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram via IoT Hub till ett serverdelsprogram för bearbetning.

Snabbstarten använder ett färdigt Python-program för att skicka telemetrin och ett CLI-verktyg för att läsa telemetrin från navet. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med hubben.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

De två exempelprogram som du kör i den här snabbstarten skrivs med Python. Du behöver ha Python 2.7.x eller 3.5.x på utvecklingsdatorn.

Du kan hämta Python för flera plattformar från [Python.org](https://www.python.org/downloads/).

Du kan kontrollera den aktuella versionen av Python på utvecklingsdatorn med någon av följande kommandon:

```python
python --version
```

```python
python3 --version
```

Ladda ned Python-exempelprojektet från https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip och extrahera ZIP-arkivet.

Om du vill installera CLI-verktyget som läser telemetri från IoT-hubben måste du först installera Node.js v4.x.x eller senare installerat på utvecklingsdatorn. Du kan ladda ned Node.js för flera plattformar från [nodejs.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

Kör följande kommando om du vill installera `iothub-explorer` CLI-verktyget:

```cmd/sh
npm install -g iothub-explorer
```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure CLI till att registrera en simulerad enhet.

1. Lägg till CLI-tillägget för IoT Hub och skapa enhetens identitet. Ersätt `{YourIoTHubName}` med det namn du angav för din IoT-hubb:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

    Om du väljer ett annat namn för din enhet måste du uppdatera enhetsnamnet i exempelprogrammet innan du kör det.

1. Kör följande kommando för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som `Hostname=...=`. Du kommer att använda det här värdet senare i snabbstarten.

1. Du måste också ha en _tjänstanslutningssträng_ för att kunna aktivera `iothub-explorer` CLI-verktyget och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

    Anteckna tjänstanslutningssträngen. Den ser ut ungefär som `Hostname=...=`. Du kommer att använda det här värdet senare i snabbstarten. Tjänstanslutningssträngen skiljer sig från enhetsanslutningssträngen.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Navigera till Python-exempelprojektets rotmapp i ett terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

1. Öppna filen **SimulatedDevice.py** i en valfri textredigerare.

    Ersätt värdet för `CONNECTION_STRING`-variabeln med den enhetsanslutningssträng du antecknade tidigare. Spara dina ändringar i filen **SimulatedDevice.py**.

1. Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Kör det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-python/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

CLI-verktyget för `iothub-explorer` ansluter till slutpunkten **Events** på tjänstsidan i din IoT-hubb. Verktyget tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

Kör följande kommandon i ett annat terminalfönster och ersätt `{your hub service connection string}` med den tjänstanslutningssträng du antecknade tidigare:

```cmd/sh
iothub-explorer monitor-events MyPythonDevice --login "{your hub service connection string}"
```

Följande skärmbild visar utdata när verktyget tar emot telemetri som skickats av den simulerade enheten till hubben:

![Kör serverdelsprogrammet](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, skickat simulerad telemetri till hubben med hjälp av ett Python-program och läst telemetrin från hubben med hjälp av ett enkelt serverdelsprogram.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-python.md)