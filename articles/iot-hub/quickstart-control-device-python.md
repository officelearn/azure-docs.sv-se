---
title: Snabbstart – Kontrollera en enhet från Azure IoT Hub (Python) | Microsoft Docs
description: I den här snabbstarten kör du två Python-exempelprogram. Ett program är en serverdelsprogram som kan fjärrstyra enheter som är anslutna till hubben. Det andra programmet simulerar en enhet ansluten till din hubb och som kan fjärrstyras.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: c48faa70154f59bae35045b623d6533c241115bb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611352"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet och hantera dina enheter från molnet. I den här snabbstarten använder du en *direktmetod* för att styra en simulerad enhet som är ansluten till IoT Hub. Du kan använda direkta metoder för att fjärrändra beteendet hos en enhet ansluten till din IoT-hubb.

Snabbstarten använder två färdiga Python-program:

* Ett simulerat enhetsprogram som svarar på direkta metoder anropas från ett serverdelsprogram. För att kunna ta emot direkta metodanrop ansluter det här programmet till en enhetsspecifik slutpunkt på din IoT-hubb.
* Ett serverdelsprogram som anropar de dirEkta metoderna på den simulerade enheten. För att kunna anropa en direkt metod på en enhet ansluter det här programmet till en serverdelsslutpunkt på din IoT-hubb.

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

Ladda ned Python-exempelprojektet från https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip, om du inte redan har gjort det, och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md) kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure CLI till att registrera en simulerad enhet.

1. Lägg till CLI-tillägget för IoT Hub och skapa enhetens identitet. Ersätt `{YourIoTHubName}` med det namnet på din IoT-hubb:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

    Om du väljer ett annat namn för din enhet måste du uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

1. Kör följande kommando för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som `Hostname=...=`. Du kommer att använda det här värdet senare i snabbstarten.

1. Du måste också ha en _tjänstanslutningssträng_ för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

    Anteckna tjänstanslutningssträngen. Den ser ut ungefär som `Hostname=...=`. Du kommer att använda det här värdet senare i snabbstarten. Tjänstanslutningssträngen skiljer sig från enhetsanslutningssträngen.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar tillbaka en bekräftelse till din hubb när den har kört den direkta metoden.

1. Navigera till Python-exempelprojektets rotmapp i ett terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device-2**.

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

    ![Kör den simulerade enheten](media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Serverdelsprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metodanrop till en enhet via din IoT-hubb och lyssnar efter bekräftelser. Ett IoT Hub-serverdelsprogram körs normalt i molnet.

1. Navigera till Python-exempelprojektets rotmapp i ett annat terminalfönster. Gå sedan till mappen **Quickstarts\back-end-application**.

1. Öppna filen **BackEndApplication.py** i en valfri textredigerare.

    Ersätt värdet för `CONNECTION_STRING`-variabeln med den tjänstanslutningssträng du antecknade tidigare. Spara dina ändringar i filen **BackEndApplication.py**.

1. Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. Kör serverdelsprogrammet genom att köra följande kommandon i terminalfönstret:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Följande skärmbild visar utdata när programmet gör ett direkt metodanrop till enheten och tar emot en bekräftelse:

    ![Kör serverdelsprogrammet](media/quickstart-control-device-python/BackEndApplication.png)

    När du har kört serverdelsprogrammet visas ett meddelande i det konsolfönster som kör den simulerade enheten och hastigheten med vilken den skickar meddelandeändringar:

    ![Ändring i den simulerade klienten](media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du anropat en direktmetod på en enhet från ett serverdelsprogram och svarat på det direkta metodanropet i ett simulerat enhetsprogram.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudier: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)