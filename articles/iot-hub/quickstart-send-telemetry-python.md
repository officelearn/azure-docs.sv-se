---
title: Snabbstart – Skicka telemetri till Azure IoT Hub (Python) | Microsoft Docs
description: I den här snabbstarten kör du ett Python-exempelprogram som skickar simulerad telemetri till en IoT-hubb och använder ett verktyg för att läsa telemetrin från IoT-hubben.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/17/2019
ms.openlocfilehash: 73722a7e5581d5e6275ec23f31351c2e4d4561ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675373"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb och läs den med ett backend-program (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram via Azure IoT Hub till ett backend-program för bearbetning. IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. Den här snabbstarten använder ett förskrivet Python-program för att skicka telemetrin och ett CLI-verktyg för att läsa telemetrin från navet. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med navet.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7+](https://www.python.org/downloads/). Andra versioner av Python stöds finns i [Azure IoT-enhetsfunktioner](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Ett exempel på Python-projekt](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip).

* Port 8883 öppen i brandväggen. Enhetsprovet i den här snabbstarten använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Lägga till Azure IoT-tillägg

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans. IoT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS) till Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetsidentiteten.

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    **MyPythonDevice:** Detta är namnet på den enhet du registrerar. Det rekommenderas att använda **MyPythonDevice** som visas. Om du väljer ett annat namn för enheten måste du också använda det namnet i hela den här artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Kör följande kommando i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för den enhet du registrerade:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    Du ska använda det här värdet senare i snabbstarten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Navigera till Python-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

1. Öppna filen **SimulatedDevice.py** i en valfri textredigerare.

    Ersätt variabelns `CONNECTION_STRING` värde med den enhetsanslutningssträng som du har antecknat tidigare. Spara sedan ändringarna **i SimulatedDevice.py**.

1. Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. I det lokala terminalfönstret kör du följande kommandon för att köra programmet för simulerad enhet:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-python/SimulatedDevice.png)


## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

CLI-tillägget för IoT Hub kan ansluta till **Events**-slutpunkten för tjänstsidan på din IoT Hub. Tillägget tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

Kör följande kommandon i Azure Cloud Shell, där du ersätter `YourIoTHubName` med namnet på din IoT-hubb:

```azurecli-interactive
az iot hub monitor-events --hub-name {YourIoTHubName} --device-id MyPythonDevice 
```

Följande skärmbild visar utdata när tillägget tar emot telemetridata som skickats från den simulerade enheten till hubben:

![Kör serverdelsprogrammet](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerar du en IoT-hubb, registrerade en enhet, skickade simulerad telemetri till navet med ett Python-program och läser telemetrin från navet med ett enkelt backend-program.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-python.md)
