---
title: Snabbstart – Kontrollera en enhet från Azure IoT Hub (Python) | Microsoft Docs
description: I den här snabbstarten kör du två Python-exempelprogram. Ett program är en serverdel som kan fjärrstyra enheter som är anslutna till hubben. Det andra programmet simulerar en enhet ansluten till din hubb som kan fjärrstyras.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/09/2020
ms.openlocfilehash: 23f9a88cd0accbf8716c706643e7b67f4ecaf05c
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110521"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan hantera dina IoT-enheter från molnet och mata in stora mängder enhets telemetri till molnet för lagring eller bearbetning. I den här snabbstarten använder du en *direktmetod* för att styra en simulerad enhet som är ansluten till IoT Hub. Du kan använda direkta metoder för att fjärrändra beteendet hos en enhet ansluten till din IoT-hubb.

Snabbstarten använder två färdiga Python-program:

* Ett simulerat enhetsprogram som svarar på direkta metoder anropas från ett serverdelsprogram. För att kunna ta emot direkta metodanrop ansluter det här programmet till en enhetsspecifik slutpunkt på din IoT-hubb.

* Ett serverdelsprogram som anropar de dirEkta metoderna på den simulerade enheten. För att kunna anropa en direkt metod på en enhet ansluter det här programmet till en serverdelsslutpunkt på din IoT-hubb.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen. IOT-tillägget lägger till IoT Hub-, IoT Edge-och IoT Device Provisioning-tjänst (DPS)-kommandon i Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ladda ned Python-exempelprojektet från https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip, om du inte redan har gjort det, och extrahera ZIP-arkivet.

Ha [python version 3,7 eller senare](https://www.python.org/downloads/) installerat på utvecklings datorn. Andra versioner av python som stöds finns i [Azure IoT Device-funktioner](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features) i SDK-dokumentationen.

Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här snabb starten använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT-hubb

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md) kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    **MyPythonDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyPythonDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

3. Du måste också ha en _tjänstanslutningssträng_ för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

    **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten. Den här tjänst anslutnings strängen skiljer sig från den enhets anslutnings sträng som du antecknade i föregående steg.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar en bekräftelse tillbaka till hubben när den har kört den direkta metoden.

1. Navigera till Python-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device-2**.

1. Öppna filen **SimulatedDevice.py** i en valfri textredigerare.

    Ersätt värdet för variabeln `CONNECTION_STRING` med enhets anslutnings strängen som du antecknade tidigare. Spara sedan ändringarna i **SimulatedDevice.py**.

1. Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Kör det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](./media/quickstart-control-device-python/simulated-device-1.png)

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Serverdelsprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metod anrop till en enhet via din IoT Hub och lyssnar efter bekräftelser. Ett IoT Hub-serverdelsprogram körs normalt i molnet.

1. Navigera till Python-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Gå sedan till mappen **Quickstarts\back-end-application**.

1. Öppna filen **BackEndApplication.py** i en valfri textredigerare.

    Ersätt värdet för `CONNECTION_STRING`-variabeln med tjänst anslutnings strängen som du antecknade tidigare. Spara sedan ändringarna i **BackEndApplication.py**.

1. Installera de bibliotek som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Kör serverdelsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Följande skärm bild visar resultatet när programmet gör ett direkt metod anrop till enheten och får en bekräftelse:

    ![Kör serverdelsprogrammet](./media/quickstart-control-device-python/backend-application.png)

    När du har kört serverdelsprogrammet visas ett meddelande i det konsolfönster som kör den simulerade enheten och hastigheten med vilken den skickar meddelandeändringar:

    ![Ändring i den simulerade klienten](./media/quickstart-control-device-python/simulated-device-2.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du anropat en direktmetod på en enhet från ett serverdelsprogram och svarat på det direkta metodanropet i ett simulerat enhetsprogram.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudier: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)