---
title: C#-snabbstart för Azure IoT Hub-enhetsströmmar (förhandsversion) | Microsoft Docs
description: I den här snabbstarten kör du två C#-exempelprogram som kommunicerar genom en enhetsström som upprättas via IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: edd3912b3674f3a80a81fd47ed490479f663852c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830831"
---
# <a name="quickstart-communicate-to-device-applications-in-c-via-iot-hub-device-streams-preview"></a>Snabbstart: Kommunicera med enhetsprogram i C# via IoT Hub-enhetsströmmar (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

[IoT Hub-enhetsströmmar](./iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Den här snabbstarten omfattar två C#-program som utnyttjar enhetsströmmar för att skicka data fram och tillbaka (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

De två exempelprogram som du kör i den här snabbstarten skrivs med C#. Du måste ha .NET Core SDK 2.1.0 eller senare på utvecklingsdatorn.

Du kan ladda ned .NET Core-SDK:n för flera plattformar från [.NET](https://www.microsoft.com/net/download/all).

Du kan kontrollera den aktuella versionen av C# på utvecklingsdatorn med följande kommando:

```
dotnet --version
```

Ladda ned exempelprojektet för C# från https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip och extrahera ZIP-arkivet.


## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommandon i Azure Cloud Shell för att lägga till IoT Hub CLI-tillägget och skapa enhetens identitet. 

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyDevice**: Det här är det namn du angav för den registrerade enheten. Använd MyDevice såsom det visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln, och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

3. Du behöver även _tjänstanslutningssträngen_ från din IoT-hubb för att göra så att programmet på tjänstsidan kan ansluta till din IoT-hubb och upprätta en enhetsström. Följande kommando hämtar det här värdet för din IoT-hubb:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --hub-name YourIoTHubName
    ```

    Anteckna det returnerade värdet, som ser ut så här:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`
    

## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunicera mellan enhet och tjänst via enhetsströmmar

### <a name="run-the-service-side-application"></a>Köra programmet på tjänstsidan

Gå till `device-streams-echo/service` i den uppackade projektmappen. Du behöver ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `ServiceConnectionString` | Tjänstanslutningssträngen för din IoT-hubb. |
| `DeviceId` | Identifieraren för den enhet som du skapade tidigare. |

Kompilera och kör koden på följande sätt:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $ServiceConnectionString MyDevice

# In Windows
dotnet run %ServiceConnectionString% MyDevice
```

### <a name="run-the-device-side-application"></a>Köra programmet på enhetssidan

Gå till katalogen `device-streams-echo/device` i den uppackade projektmappen. Du behöver ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `DeviceConnectionString` | Anslutningssträngen för den enhet som du skapade tidigare. |

Kompilera och kör koden på följande sätt:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $DeviceConnectionString

# In Windows
dotnet run %DeviceConnectionString%
```

I slutet av det sista steget initierar programmet på tjänstsidan en dataström till enheten, och när den har upprättats skickas en strängbuffert till tjänsten via dataströmmen. I det här exemplet returnerar programmet på tjänstsidan helt enkelt tillbaka samma data till enheten, vilket demonstrerar lyckad dubbelriktad kommunikation mellan de två programmen. Se bilden nedan.

Konsolens utdata på enhetssidan: ![alternativ text](./media/quickstart-device-streams-echo-csharp/device-console-output.png "Konsolens utdata på enhetssidan")


Konsolens utdata på tjänstsidan: ![alternativ text](./media/quickstart-device-streams-echo-csharp/service-console-output.png "Konsolens utdata på tjänstsidan")



Den trafik som skickas via en strömmen dirigeras via IoT-hubben i stället för att skickas direkt. Detta ger [dessa fördelar](./iot-hub-device-streams-overview.md#benefits).


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, upprättat en enhetsström mellan C#-program på enhetssidan och tjänstsidan samt använt dataströmmen för att skicka data fram och tillbaka mellan programmen.

Använd länkarna nedan om du vill läsa mer om enhetsströmmar:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
