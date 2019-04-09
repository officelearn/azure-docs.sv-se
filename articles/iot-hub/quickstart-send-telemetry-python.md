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
ms.date: 02/28/2019
ms.openlocfilehash: fc303a0ab53c80c91bb29c36a1a7e0f04c4a89e6
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006888"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb och läs den med ett serverdelsprogram (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram via IoT Hub till ett serverdelsprogram för bearbetning.

Snabbstarten använder ett färdigt Python-program för att skicka telemetrin och ett CLI-verktyg för att läsa telemetrin från navet. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med hubben.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Exempelprogrammet som du kör i den här snabbstarten är skrivna i Python. För närvarande stöder endast specifika versioner av Python för varje plattform med Microsoft Azure IoT SDK för Python. Mer information finns i den [Python SDK Readme](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).

Den här snabbstarten förutsätter att du använder en Windows-utvecklingsdator. För Windows System [Python 3.6.x](https://www.python.org/downloads/release/python-368/) stöds. Python-installationsprogrammet som du väljer ska baseras på arkitekturen i systemet som du arbetar med. Om systemet processorarkitektur är 32-bitars och sedan ladda ned x86 installer; ladda ned x86-64-installationsprogrammet för 64-bitars-arkitektur. Se även till den [Microsoft Visual C++ Redistributable för Visual Studio 2017](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) har installerats för din arkitektur (x86 eller x64).

Du kan hämta Python för andra plattformar från [Python.org](https://www.python.org/downloads/).

Du kan kontrollera den aktuella versionen av Python på utvecklingsdatorn med någon av följande kommandon:

```python
python --version
```

```python
python3 --version
```

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS) för vissa kommandon i Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ladda ned Python-exempelprojektet från https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetens identitet.

    **YourIoTHubName** : Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    **MyPythonDevice** : Det här är det namn du angav för den registrerade enheten. Använd MyPythonDevice som visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln, och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

    **YourIoTHubName** : Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Navigera till Python-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

1. Öppna filen **SimulatedDevice.py** i en valfri textredigerare.

    Ersätt värdet för `CONNECTION_STRING`-variabeln med den enhetsanslutningssträng du antecknade tidigare. Spara dina ändringar i filen **SimulatedDevice.py**.

1. Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Kör det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-python/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

CLI-tillägget för IoT Hub kan ansluta till **Events**-slutpunkten för tjänstsidan på din IoT Hub. Tillägget tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

Kör följande kommandon i Azure Cloud Shell, där du ersätter `YourIoTHubName` med namnet på din IoT-hubb:

```azurecli-interactive
az iot hub monitor-events --hub-name YourIoTHubName --device-id MyPythonDevice 
```

Följande skärmbild visar utdata när tillägget tar emot telemetridata som skickats från den simulerade enheten till hubben:

![Kör serverdelsprogrammet](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, skickat simulerad telemetri till hubben med hjälp av ett Python-program och läst telemetrin från hubben med hjälp av ett enkelt serverdelsprogram.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontroll som en enhet som är kopplad till en IoT hub](quickstart-control-device-python.md)