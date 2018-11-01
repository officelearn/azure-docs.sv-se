---
title: Kontrollera en enhet från snabbstart för Azure IoT Hub (Java) | Microsoft Docs
description: I den här snabbstarten kör du två Java-exempelprogram. Ett program är en serverdel som kan fjärrstyra enheter som är anslutna till hubben. Det andra programmet simulerar en enhet ansluten till din hubb som kan fjärrstyras.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/22/2018
ms.author: dobett
ms.openlocfilehash: 3e936b3e08884c1728809aea9054278ffdb99045
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416995"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-java"></a>Snabbstart: Kontrollera en enhet ansluten till en IoT Hub (Java)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet och hantera dina enheter från molnet. I den här snabbstarten använder du en *direktmetod* för att styra en simulerad enhet som är ansluten till IoT Hub. Du kan använda direktmetoder för att fjärrändra beteendet hos en enhet ansluten till IoT Hub.

Snabbstarten använder två färdiga Java-program:

* Ett simulerat enhetsprogram som svarar på direkta metoder anropas från ett serverdelsprogram. För att kunna ta emot direkta metodanrop ansluter det här programmet till en enhetsspecifik slutpunkt på din IoT-hubb.
* Ett serverdelsprogram som anropar de dirEkta metoderna på den simulerade enheten. För att kunna anropa en direkt metod på en enhet ansluter det här programmet till en serverdelsslutpunkt på din IoT-hubb.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

De två exempelprogram som du kör i den här snabbstarten skrivs med Java. Du måste ha Java SE 8 eller senare installerat på utvecklingsdatorn.

Du kan hämta Java för flera plattformar från [Oracle](https://aka.ms/azure-jdks).

Du kan kontrollera den aktuella versionen av Java på utvecklingsdatorn med följande kommando:

```cmd/sh
java --version
```

Du måste installera Maven 3 för att skapa exemplen. Du kan hämta Maven för flera plattformar från [Apache Maven](https://maven.apache.org/download.cgi).

Du kan kontrollera den aktuella versionen av Maven på utvecklingsdatorn med följande kommando:

```cmd/sh
mvn --version
```

Ladda ned Java-exempelprojektet från https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip, om du inte redan har gjort det, och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md) kan du hoppa över det här steget.

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-java.md) kan du hoppa över det här steget.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommandon i Azure Cloud Shell för att lägga till IoT Hub CLI-tillägget och skapa enhetens identitet. 

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

   **MyJavaDevice** : Det här är det namn som du angav för den registrerade enheten. Använd MyJavaDevice såsom det visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln, och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyJavaDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyJavaDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="retrieve-the-service-connection-string"></a>Hämta anslutningssträngen för tjänsten

Du måste också ha en _tjänstanslutningssträng_ för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:
   
**YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIoTHubName --output table
```

Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

Du kommer att använda det här värdet senare i snabbstarten. Tjänstanslutningssträngen skiljer sig från enhetsanslutningssträngen.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar tillbaka en bekräftelse till din hubb när den har kört den direkta metoden.

1. Navigera till Java-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device-2**.

2. Öppna filen **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** i en textredigerare som du väljer.

    Ersätt värdet för `connString`-variabeln med den enhetsanslutningssträng du antecknade tidigare. Spara dina ändringar i filen **SimulatedDevice.java**.

3. I det lokala terminalfönstret kör du följande kommandon för att installera de bibliotek som krävs och skapa programmet för simulerad enhet:

    ```cmd/sh
    mvn clean package
    ```

4. Kör det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-control-device-java/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Serverdelsprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metodanrop till en enhet via din IoT-hubb och lyssnar efter bekräftelser. Ett IoT Hub-serverdelsprogram körs normalt i molnet.

1. Navigera till Java-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Gå sedan till mappen **Quickstarts\back-end-application**.

2. Öppna filen **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** i en textredigerare som du väljer.

    Ersätt värdet för `iotHubConnectionString`-variabeln med den tjänstanslutningssträng du antecknade tidigare. Spara dina ändringar i filen **BackEndApplication.java**.

3. I det lokala terminalfönstret kör du följande kommandon för att installera de bibliotek som krävs och skapa serverdelsprogrammet:

    ```cmd/sh
    mvn clean package
    ```

4. I det lokala terminalfönstret kör du följande kommandon för att köra serverdelsprogrammet:

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    Följande skärmbild visar utdata när programmet gör ett direkt metodanrop till enheten och tar emot en bekräftelse:

    ![Kör serverdelsprogrammet](media/quickstart-control-device-java/BackEndApplication.png)

    När du har kört serverdelsprogrammet visas ett meddelande i det konsolfönster som kör den simulerade enheten och hastigheten med vilken den skickar meddelandeändringar:

    ![Ändring i den simulerade klienten](media/quickstart-control-device-java/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du anropat en direktmetod på en enhet från ett serverdelsprogram och svarat på det direkta metodanropet i ett simulerat enhetsprogram.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudier: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)