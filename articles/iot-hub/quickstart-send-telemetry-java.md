---
title: 'Snabb start: skicka telemetri till Azure IoT Hub med Java'
description: I den här snabbstarten kör du två Java-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: cb115b8658850fc85f93fc7a9508a82ecee920d8
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166446"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Snabb start: skicka telemetri till en Azure IoT-hubb och läsa det med ett Java-program

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Snabb starten visar hur du skickar telemetri till en Azure IoT-hubb och läser det med ett Java-program. IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram via IoT Hub till ett serverdelsprogram för bearbetning.

Snabbstarten använder två färdiga Java-program – ett för att skicka telemetrin och ett för att läsa telemetrin från hubben. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med navet.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

De två exempelprogram som du kör i den här snabbstarten skrivs med Java. Du behöver Java SE 8 på din utvecklings dator.

Du kan ladda ned Java SE Development Kit 8 för flera plattformar från [Java-långsiktigt stöd för Azure och Azure Stack](https://docs.microsoft.com/en-us/java/azure/jdk/?view=azure-java-stable). Se till att du väljer **Java 8** under **långsiktigt stöd** för att hämta hämtningar för JDK 8.

Du kan kontrollera den aktuella versionen av Java på utvecklingsdatorn med följande kommando:

```cmd/sh
java -version
```

Du måste installera Maven 3 för att skapa exemplen. Du kan hämta Maven för flera plattformar från [Apache Maven](https://maven.apache.org/download.cgi).

Du kan kontrollera den aktuella versionen av Maven på utvecklingsdatorn med följande kommando:

```cmd/sh
mvn --version
```

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen. IOT-tillägget lägger till IoT Hub-, IoT Edge-och IoT Device Provisioning-tjänst (DPS)-kommandon i Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ladda ned exempelprojektet för Java från https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyJavaDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyJavaDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Kör följande kommando i Azure Cloud Shell för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabb starten.

3. Du behöver också _Event Hubs-kompatibel slut punkt_, _Event Hubs-kompatibel sökväg_och _tjänstens primära nyckel_ från din IoT Hub för att aktivera backend-programmet för att ansluta till din IoT-hubb och hämta meddelandena. Följande kommandon hämtar dessa värden för din IoT-hubb:

     **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Anteckna de här tre värdena, som du kommer att använda senare i snabb starten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Navigera till Java-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

2. Öppna filen **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** i en textredigerare som du väljer.

    Ersätt värdet för variabeln `connString` med enhets anslutnings strängen som du antecknade tidigare. Spara sedan ändringarna i **SimulatedDevice. java**.

3. I det lokala terminalfönstret kör du följande kommandon för att installera de bibliotek som krävs och skapa programmet för simulerad enhet:

    ```cmd/sh
    mvn clean package
    ```

4. Kör det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Utdata från telemetri som skickas av enheten till din IoT-hubb](media/quickstart-send-telemetry-java/iot-hub-simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

Serverdelsprogrammet ansluter till **Events**-slutpunkten för tjänstsidan på din IoT-hubb. Programmet tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

1. Navigera till Java-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\read-d2c-messages**.

2. Öppna filen **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** i en textredigerare som du väljer. Uppdatera följande variabler och spara ändringarna i filen.

    | Variabel | Värde |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Ersätt värdet för variabeln med den Event Hubs-kompatibla slut punkten som du antecknade tidigare. |
    | `eventHubsCompatiblePath`     | Ersätt värdet för variabeln med Event Hubs-kompatibel sökväg som du antecknade tidigare. |
    | `iotHubSasKey`                | Ersätt värdet för variabeln med tjänstens primära nyckel som du gjorde en anteckning om tidigare. |

3. I det lokala terminalfönstret kör du följande kommandon för att installera de bibliotek som krävs och skapa serverdelsprogrammet:

    ```cmd/sh
    mvn clean package
    ```

4. Kör serverdelsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    Följande skärmbild visar utdata när serverdelsprogrammet tar emot telemetri som skickats av den simulerade enheten till hubben:

    ![Utdata som backend-program tar emot telemetri som skickas till din IoT-hubb](media/quickstart-send-telemetry-java/iot-hub-read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, skickade simulerad telemetri till hubben med ett Java-program och läser Telemetrin från hubben med hjälp av ett enkelt Server dels program.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-java.md)
