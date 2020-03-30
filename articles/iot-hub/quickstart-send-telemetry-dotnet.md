---
title: Snabbstart – Skicka telemetri till Azure IoT Hub (C#) | Microsoft Docs
description: I den här snabbstarten kör du två C#-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: f542c07456d60572dc70692a1ab0111acc9103f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675573"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-net"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb och läs den med ett backend-program (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram via IoT Hub till ett serverdelsprogram för bearbetning.

Snabbstarten använder två färdiga C#-program – ett för att skicka telemetrin och ett för att läsa telemetrin från hubben. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med navet.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

De två exempelprogram som du kör i den här snabbstarten skrivs med C#. Du måste ha .NET Core SDK 2.1.0 eller senare på utvecklingsdatorn.

Du kan ladda ned .NET Core-SDK:n för flera plattformar från [.NET](https://www.microsoft.com/net/download/all).

Du kan kontrollera den aktuella versionen av C# på utvecklingsdatorn med följande kommando:

```cmd/sh
dotnet --version
```

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS) till Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Hämta Azure IoT C#-exemplen från [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) och extrahera ZIP-arkivet.

Kontrollera att port 8883 är öppen i brandväggen. Enhetsprovet i den här snabbstarten använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetsidentiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyDotnetDevice:** Detta är namnet på den enhet du registrerar. Det rekommenderas att använda **MyDotnetDevice** som visas. Om du väljer ett annat namn för enheten måste du också använda det namnet i hela den här artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Kör följande kommando i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för den enhet du just registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Du ska använda det här värdet senare i snabbstarten.

3. Du behöver också den _händelsehubbarkompatibla slutpunkten,_ _händelsehubbar-kompatibel sökväg_och _tjänstens primärnyckel_ från IoT-hubben för att backend-programmet ska kunna ansluta till din IoT-hubb och hämta meddelandena. Följande kommandon hämtar dessa värden för din IoT-hubb:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Anteckna dessa tre värden, som du ska använda senare i snabbstarten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Navigera till C#-exempelprojektets rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

2. Öppna filen **SimulatedDevice.cs** i en valfri textredigerare.

    Ersätt variabelns `s_connectionString` värde med den enhetsanslutningssträng som du har antecknat tidigare. Spara sedan ändringarna **i SimulatedDevice.cs**.

3. Installera de paket som krävs för programmet för simulerad enhet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet restore
    ```

4. Kör programmet för simulerad enhet genom att skapa och köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet run
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-dotnet/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

Serverdelsprogrammet ansluter till **Events**-slutpunkten för tjänstsidan på din IoT-hubb. Programmet tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

1. Navigera till C#-exempelprojektets rotmapp i ett annat lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\read-d2c-messages**.

2. Öppna filen **ReadDeviceToCloudMessages.cs** i en valfri textredigerare. Uppdatera följande variabler och spara ändringarna i filen.

    | Variabel | Värde |
    | -------- | ----------- |
    | `s_eventHubsCompatibleEndpoint` | Ersätt värdet för variabeln med den slutpunkt som är kompatibel med händelsehubben som du har antecknat tidigare. |
    | `s_eventHubsCompatiblePath`     | Ersätt värdet för variabeln med den sökväg som är kompatibel med händelsehubbar som du har gjort en anteckning om tidigare. |
    | `s_iotHubSasKey`                | Ersätt variabelns värde med den primära nyckeln för tjänsten som du har antecknat tidigare. |

3. Installera de bibliotek som krävs för serverdelsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet restore
    ```

4. Skapa och kör serverdelsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet run
    ```

    Följande skärmbild visar utdata när serverdelsprogrammet tar emot telemetri som skickats av den simulerade enheten till hubben:

    ![Kör serverdelsprogrammet](media/quickstart-send-telemetry-dotnet/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerar du en IoT-hubb, registrerade en enhet, skickade simulerad telemetri till navet med hjälp av ett C#-program och läser telemetrin från navet med ett enkelt backend-program.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-dotnet.md)