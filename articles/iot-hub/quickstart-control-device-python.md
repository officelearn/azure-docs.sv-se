---
title: Snabbstart – Kontrollera en enhet från Azure IoT Hub (Python) | Microsoft Docs
description: I den här snabbstarten kör du två Python-exempelprogram. Ett program är en serverdelsprogram som kan fjärrstyra enheter som är anslutna till hubben. Det andra programmet simulerar en enhet ansluten till din hubb som kan fjärrstyras.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/26/2019
ms.openlocfilehash: 58b4acb3ebfc1ff22c04dafb3063f197d2866311
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59005843"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Snabbstart: Kontroll som en enhet som är kopplad till en IoT hub (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet och hantera dina enheter från molnet. I den här snabbstarten använder du en *direktmetod* för att styra en simulerad enhet som är ansluten till IoT Hub. Du kan använda direkta metoder för att fjärrändra beteendet hos en enhet ansluten till din IoT-hubb.

Snabbstarten använder två färdiga Python-program:

* Ett simulerat enhetsprogram som svarar på direkta metoder anropas från ett serverdelsprogram. För att kunna ta emot direkta metodanrop ansluter det här programmet till en enhetsspecifik slutpunkt på din IoT-hubb.

* Ett serverdelsprogram som anropar de dirEkta metoderna på den simulerade enheten. För att kunna anropa en direkt metod på en enhet ansluter det här programmet till en serverdelsslutpunkt på din IoT-hubb.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

De två exempelprogram som du kör i den här snabbstarten skrivs med Python. För närvarande stöder endast specifika versioner av Python för varje plattform med Microsoft Azure IoT SDK för Python. Mer information finns i den [Python SDK Readme](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).

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

Ladda ned Python-exempelprojektet från https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip, om du inte redan har gjort det, och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md). Du kan hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md). Du kan hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetens identitet.

    **YourIoTHubName** : Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

    **MyPythonDevice** : Det här är det namn du angav för den registrerade enheten. Använd MyPythonDevice som visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln, och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

    **YourIoTHubName** : Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

3. Du måste också ha en _tjänstanslutningssträng_ för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

    **YourIoTHubName** : Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --name YourIoTHubName \
      --output table
    ```

    Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten. Tjänstanslutningssträngen skiljer sig från enhetsanslutningssträngen.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar tillbaka en bekräftelse till din hubb när den har kört den direkta metoden.

1. Navigera till Python-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device-2**.

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

    ![Kör den simulerade enheten](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Serverdelsprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metodanrop till en enhet via din IoT-hubb och lyssnar efter bekräftelser. Ett IoT Hub-serverdelsprogram körs normalt i molnet.

1. Navigera till Python-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Gå sedan till mappen **Quickstarts\back-end-application**.

1. Öppna filen **BackEndApplication.py** i en valfri textredigerare.

    Ersätt värdet för `CONNECTION_STRING`-variabeln med den tjänstanslutningssträng du antecknade tidigare. Spara dina ändringar i filen **BackEndApplication.py**.

1. Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. I det lokala terminalfönstret kör du följande kommandon för att köra serverdelsprogrammet:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Följande skärmbild visar utdata när programmet gör ett direkt metodanrop till enheten och tar emot en bekräftelse:

    ![Kör serverdelsprogrammet](./media/quickstart-control-device-python/BackEndApplication.png)

    När du har kört serverdelsprogrammet visas ett meddelande i det konsolfönster som kör den simulerade enheten och hastigheten med vilken den skickar meddelandeändringar:

    ![Ändring i den simulerade klienten](./media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du anropat en direktmetod på en enhet från ett serverdelsprogram och svarat på det direkta metodanropet i ett simulerat enhetsprogram.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudie: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)