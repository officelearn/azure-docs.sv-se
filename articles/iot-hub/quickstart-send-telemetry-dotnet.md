---
title: Snabbstart – Skicka telemetri till Azure IoT Hub (C#) | Microsoft Docs
description: I den här snabbstarten kör du två C#-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/20/2018
ms.author: dobett
ms.openlocfilehash: 501a8f2dad0537f0288dfd6a6d7bedcac054ab86
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332294"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-c"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb och läs telemetrin från navet med ett serverdelsprogram (C#)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram via IoT Hub till ett serverdelsprogram för bearbetning.

Snabbstarten använder två färdiga C#-program – ett för att skicka telemetrin och ett för att läsa telemetrin från hubben. Innan du kör dessa två program skapar du en IoT-hubb och registrerar en enhet med hubben.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

De två exempelprogram som du kör i den här snabbstarten skrivs med C#. Du måste ha .NET Core SDK 2.1.0 eller senare på utvecklingsdatorn.

Du kan ladda ned .NET Core-SDK:n för flera plattformar från [.NET](https://www.microsoft.com/net/download/all).

Du kan kontrollera den aktuella versionen av C# på utvecklingsdatorn med följande kommando:

```cmd/sh
dotnet --version
```

Ladda ned exempelprojektet för C# från https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure CLI till att registrera en simulerad enhet.

1. Lägg till CLI-tillägget för IoT Hub och skapa enhetens identitet. Ersätt `{YourIoTHubName}` med det namn du angav för din IoT-hubb:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

    Om du väljer ett annat namn för din enhet måste du uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

2. Kör följande kommando för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som `Hostname=...=`. Du kommer att använda det här värdet senare i snabbstarten.

3. Du behöver också den _Event Hubs-kompatibla slutpunkten_, den _Event Hubs-kompatibla sökvägen_ och _iothubowner-primärnyckeln_ från din IoT-hubb för att kunna aktivera serverdelsprogrammet och ansluta till din IoT-hubb och hämta meddelandena. Följande kommandon hämtar dessa värden för din IoT-hubb:

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name iothubowner --query primaryKey --hub-name {your IoT Hub name}
    ```

    Anteckna dessa tre värden, vilka du komer att använda senare i snabbstarten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar simulerad telemetri om temperatur och luftfuktighet.

1. Navigera till C#-exempelprojektets rotmapp i ett terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

2. Öppna filen **SimulatedDevice.cs** i en valfri textredigerare.

    Ersätt värdet för `s_connectionString`-variabeln med den enhetsanslutningssträng du antecknade tidigare. Spara dina ändringar i filen **SimulatedDevice.cs**.

3. Installera de paket som krävs för det simulerade enhetsprogrammet genom att köra följande kommandon i terminalfönstret:

    ```cmd/sh
    dotnet restore
    ```

4. Kör det simulerade enhetsprogrammet genom att skapa och köra följande kommandon i terminalfönstret:

    ```cmd/sh
    dotnet run
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-dotnet/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

Serverdelsprogrammet ansluter till **Events**-slutpunkten för tjänstsidan på din IoT-hubb. Programmet tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

1. Navigera till C#-exempelprojektets rotmapp i ett annat terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\read-d2c-messages**.

2. Öppna filen **ReadDeviceToCloudMessages.cs** i en valfri textredigerare. Uppdatera följande variabler och spara ändringarna i filen.

    | Variabel | Värde |
    | -------- | ----------- |
    | `s_eventHubsCompatibleEndpoint` | Ersätt värdet för variabeln med den Event Hubs-kompatibla slutpunkt du antecknade tidigare. |
    | `s_eventHubsCompatiblePath`     | Ersätt värdet för variabeln med den Event Hubs-kompatibla sökväg du antecknade tidigare. |
    | `s_iotHubSasKey`                | Ersätt värdet för variabeln med den iothubowner-primärnyckel du antecknade tidigare. |

3. Installera de bibliotek som krävs för serverdelsprogrammet genom att köra följande kommandon i terminalfönstret:

    ```cmd/sh
    dotnet restore
    ```

4. Skapa och kör serverdelsprogrammet genom att köra följande kommandon i terminalfönstret:

    ```cmd/sh
    dotnet run
    ```

    Följande skärmbild visar utdata när serverdelsprogrammet tar emot telemetri som skickats av den simulerade enheten till hubben:

    ![Kör serverdelsprogrammet](media/quickstart-send-telemetry-dotnet/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, skickat simulerad telemetri till hubben med hjälp av ett C#-program och läst telemetrin från hubben med hjälp av ett enkelt serverdelsprogram.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-dotnet.md)