---
title: Kommunicera till enhetsapp i C# med Azure IoT Hub-enhetsströmmar
description: I den här snabbstarten kör du två exempel C#-program som kommunicerar via en enhetsström som upprättats via IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 64af62cb6c2c56ca8c7e67e2f1467d4a7e8335a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675526"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snabbstart: Kommunicera med ett enhetsprogram i C# via IoT Hub-enhetsströmmar (förhandsgranskning)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub stöder för närvarande enhetsströmmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Den här snabbstarten omfattar två C#-program som utnyttjar enhetsströmmar för att skicka data fram och tillbaka (eko).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

* Förhandsgranskningen av enhetsströmmar stöds för närvarande endast för IoT-hubbar som skapas i följande regioner:
  * USA, centrala
  * Centrala USA EUAP
  * Europa, norra
  * Sydostasien

* De två exempelprogram som du kör i den här snabbstarten skrivs i C#. Du behöver .NET Core SDK 2.1.0 eller senare på utvecklingsmaskinen.
  * Hämta [.NET Core SDK för flera plattformar från .NET](https://www.microsoft.com/net/download/all).
  * Verifiera den aktuella versionen av C# på utvecklingsmaskinen med hjälp av följande kommando:

   ```
   dotnet --version
   ```

* Lägg till Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans genom att köra följande kommando. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS)-specifika kommandon i Azure CLI.

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Ladda ned Azure IoT C#-exemplen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) och extrahera ZIP-arkivet. Du behöver det på både enhetens sida och servicesidan.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Om du vill skapa enhetsidentiteten kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.
   > * För namnet på den enhet du registrerar rekommenderar vi att du använder *MyDevice* som visas. Om du väljer ett annat namn för enheten använder du det namnet i den här artikeln och uppdaterar enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Om du vill hämta *enhetsanslutningssträngen* för den enhet som du just registrerade kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observera den returnerade enhetsanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Du behöver även *tjänstanslutningssträngen* från din IoT-hubb för att göra så att programmet på tjänstsidan kan ansluta till din IoT-hubb och upprätta en enhetsström. Följande kommando hämtar det här värdet för din IoT-hubb:

   > [!NOTE]
   > Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observera den returnerade serviceanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunicera mellan enheten och tjänsten via enhetsströmmar

I det här avsnittet kör du både enhetsprogrammet och tjänstprogrammet och kommunicerar mellan de två.

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

I ett lokalt terminalfönster `iot-hub/Quickstarts/device-streams-echo/service` navigerar du till katalogen i den uppackade projektmappen. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `ServiceConnectionString` | Serviceanslutningssträngen för din IoT-hubb. |
| `MyDevice` | Identifieraren för den enhet som du skapade tidigare. |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
Programmet väntar på att enhetsprogrammet ska bli tillgängligt.

> [!NOTE]
> En timeout uppstår om programmet på enhetssidan inte svarade i tid.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

I ett annat lokalt terminalfönster navigerar du till katalogen `iot-hub/Quickstarts/device-streams-echo/device` i den uppackade projektmappen. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `DeviceConnectionString` | Enhetsanslutningssträngen för din IoT Hub. |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

I slutet av det sista steget initierar programmet på tjänstsidan en ström till enheten. När strömmen har upprättats skickar programmet en strängbuffert till tjänsten via dataströmmen. I det här exemplet ekar tjänstprogrammet helt enkelt tillbaka samma data till enheten, vilket visar en lyckad dubbelriktad kommunikation mellan de två programmen.

Konsolens utgång på enhetssidan:

![Konsolutgång på enhetssidan](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Konsolutgång på tjänstsidan:

![Konsolutgång på tjänstsidan](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Trafiken som skickas över strömmen är tunnel genom IoT-hubben i stället för att skickas direkt. Fördelarna som tillhandahålls [beskrivs i fördelar för enhetsströmmar](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerar du en IoT-hubb, registrerade en enhet, upprättade en enhetsström mellan C#-program på enhetens och tjänstsidorna och använde strömmen för att skicka data fram och tillbaka mellan programmen.

Mer information om enhetsströmmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
