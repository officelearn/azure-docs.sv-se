---
title: Kommunicera med en app för enheter i C# via Azure IoT Hub device strömmar (förhandsversion) | Microsoft Docs
description: I den här snabbstarten kör du två exempel C# program som kommunicerar via en enhet-dataström som upprättats via IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 74a8fc40cff12070f7cea99981eb4e8321d7c1ef
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735136"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snabbstart: Kommunicera med ett program för enhet i C# via IoT Hub device strömmar (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub stöder för närvarande enheten strömmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Den här snabbstarten omfattar två C# program som drar nytta av enheten strömmar för att skicka data fram och tillbaka (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Förhandsversionen av enheten strömmar stöds för närvarande endast för IoT-hubbar som har skapats i följande regioner:
  * Centrala USA
  * USA, centrala – EUAP

* Två exempelprogram som du kör i den här snabbstarten är skrivna med hjälp av C#. Du behöver .NET Core SDK 2.1.0 eller senare på utvecklingsdatorn.
  * Ladda ned den [.NET Core SDK för flera plattformar från .NET](https://www.microsoft.com/net/download/all).
  * Kontrollera den aktuella versionen av C# på en utvecklingsdator med hjälp av följande kommando:

   ```
   dotnet --version
   ```

* Lägg till Azure IoT-tillägget för Azure CLI till din Cloud Shell-instans genom att köra följande kommando. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS)-specifika kommandon för att Azure CLI.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Ladda ned exemplet C# projekt](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) och extrahera ZIP-arkivet. Du behöver den för både enheten och serversidan.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Om du vill skapa enhetens identitet, kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.
   > * Använd *MyDevice*, som visas. Det är det namn du angav för den registrerade enheten. Om du väljer ett annat namn för din enhet kan använda det namnet i den här artikeln och uppdatera namnet på enheten i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Att hämta den *enhetsanslutningssträngen* för den enhet som du just registrerade, kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Observera enhetens anslutningssträng för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Du behöver även *tjänstanslutningssträngen* från din IoT-hubb för att göra så att programmet på tjänstsidan kan ansluta till din IoT-hubb och upprätta en enhetsström. Följande kommando hämtar det här värdet för din IoT-hubb:

   > [!NOTE]
   > Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Observera det returnerade värdet för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunicera mellan enheten och tjänsten via enheten strömmar

I det här avsnittet kör både enhetssidan programmet och tjänstsidan programmet och kommunicera mellan två.

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

Gå till den *iot-hubb/Snabbstarter/enhet-strömmar-echo/service* katalogen i mappen uppzippade projektet. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `ServiceConnectionString` | Ange tjänstanslutningssträngen för din IoT hub. |
| `DeviceId` | Ange ID för den enhet som du skapade tidigare (till exempel *MyDevice*). |

Kompilera och kör koden på följande sätt:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> En timeout uppstår om programmet på enhetssidan inte svarade i tid.

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Gå till den *iot-hubb/Snabbstarter /-strömmar-echo/enhet* katalogen i mappen uppzippade projektet. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `DeviceConnectionString` | Ange enhetsanslutningssträngen för din IoT-hubb. |

Kompilera och kör koden på följande sätt:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

I slutet av det sista steget initierar tjänstsidan programmet en dataström som din enhet. När dataströmmen har upprättats, skickar programmet en strängbufferten till tjänsten via dataströmmen. I det här exemplet ekar på tjänstsidan programmet bara tillbaka samma data till enheten, vilket visar en lyckad dubbelriktad kommunikation mellan de två programmen.

Konsolens utdata på enheten:

![Konsolens utdata på enheten](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Konsolens utdata på serversidan:

![Konsolens utdata på serversidan](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Trafik som skickas över dataströmmen är väg genom IoT-hubb snarare än skickas direkt. Fördelarna som erbjuds finns beskrivna i [enheten strömmar fördelar](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten du har skapat en IoT-hubb, registrerade en enhet, upprätta en enhet dataström mellan C# program på enheten och tjänsten sidor, och används dataströmmen för att skicka data fram och tillbaka mellan program.

Mer information om enheten strömmar finns:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
