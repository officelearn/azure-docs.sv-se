---
title: Azure IoT Hub device strömmar C# Snabbstart för SSH- och RDP (förhandsversion) | Microsoft Docs
description: I den här snabbstarten kör du två exempel C# program som möjliggör scenarier för SSH och RDP över en IoT Hub device-dataström.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 914568ee4b669605807c8a0e386cd540145c9522
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446103"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snabbstart: Aktivera SSH och RDP över en ström för IoT Hub-enhet med hjälp av en C# proxyprogrammet (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub stöder för närvarande enheten strömmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Den här snabbstartsguiden omfattar två C# program som låter klient-server programtrafik (till exempel Secure Shell [SSH] och [RDP] Remote Desktop Protocol skickas via en ström av enhet som har skapats i en IoT-hubb. En översikt över installationen finns i [lokal proxy-programexemplet för SSH eller RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Den här artikeln beskrivs först installationen för SSH (använder port 22) och beskriver hur du ändrar den installationen port för RDP. Eftersom enheten strömmar och protocol-programoberoende, kan samma prov ändras för att hantera andra typer av trafik. Den här ändringen innebär vanligtvis endast ändrar kommunikationsporten till det som används av det avsedda programmet.

## <a name="how-it-works"></a>Hur det fungerar

Följande bild visar hur enheten lokala och service-lokala proxy-program i det här exemplet aktiverar slutpunkt till slutpunkt-anslutning mellan SSH-klient och SSH-daemon processer. Vi förutsätter här att daemonen körs på samma enhet som enheten-lokal proxy-program.

![Konfigurationen av lokal proxy-program](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Service-lokal-proxyprogrammet ansluter till IoT hub och initierar en enhet ström till målenheten.

1. Enhet-lokal-proxyprogrammet Slutför stream initiation handshake och upprättar en strömmande slutpunkt till slutpunkt-tunnel via IoT-hubbens slutpunkt för direktuppspelning till serversidan.

1. Enhet-lokal proxy-program som ansluter till SSH-daemon som lyssnar på port 22 på enheten. Den här inställningen kan konfigureras, enligt beskrivningen i avsnittet ”kör enhet-lokal proxy-program”.

1. Service-lokal-proxyprogrammet väntar tills nya SSH-anslutningar från en användare genom att lyssna på en avsedda port, som i det här fallet är port 2222. Den här inställningen kan konfigureras, enligt beskrivningen i avsnittet ”kör service-lokal proxy-program”. När användaren ansluter via SSH-klienten, kan tunneln SSH programtrafik överförs mellan klienten och servern programmet för SSH.

> [!NOTE]
> SSH-trafik som skickas över en enhet dataström är väg genom IoT-hubbens slutpunkten för direktuppspelning i stället skickas direkt mellan tjänster och enheter. Mer information finns i den [fördelarna med att använda Iot Hub device strömmar](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Förhandsversionen av enheten strömmar stöds för närvarande endast för IoT-hubbar som har skapats i följande regioner:

  * Centrala USA
  * USA, centrala – EUAP

* Två exempelprogram som du kör i den här snabbstarten är skrivna med hjälp av C#. Du behöver .NET Core SDK 2.1.0 eller senare på utvecklingsdatorn.

  Du kan ladda ned den [.NET Core SDK för flera plattformar från .NET](https://www.microsoft.com/net/download/all).

* Kontrollera den aktuella versionen av C# på en utvecklingsdator med hjälp av följande kommando:

    ```
    dotnet --version
    ```

* Kör följande kommando för att lägga till Azure IoT-tillägget för Azure CLI i Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS)-specifika kommandon för att Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* [Ladda ned exemplet C# projekt](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip), och extrahera ZIP-arkivet.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Om du vill skapa enhetens identitet, kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.
   > * Använd *MyDevice*, som visas. Det är det namn du angav för den registrerade enheten. Om du väljer ett annat namn för din enhet kan använda det namnet i den här artikeln och uppdatera namnet på enheten i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Att hämta den *enhetsanslutningssträngen* för den enhet som du just registrerade, kör du följande kommandon i Cloud Shell:

   > [!NOTE]
   > Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Observera enhetens anslutningssträng för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Om du vill ansluta till din IoT-hubb och upprätta en dataström med enheten, måste du också den *tjänstanslutningssträngen* från IoT hub att aktivera tjänsten på klientsidan programmet. Följande kommando hämtar det här värdet för din IoT-hubb:

   > [!NOTE]
   > Ersätt den *YourIoTHubName* med det namn du väljer för din IoT hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Observera det returnerade värdet för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet ska upprätta du en slutpunkt till slutpunkt-dataström för att tunnla SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

Gå till den *-strömmar-proxy/enhet* katalogen i mappen uppzippade projektet. Ha följande information till hands:

| Argumentnamn | Argumentvärde |
|----------------|-----------------|
| `deviceConnectionString` | Anslutningssträngen för den enhet som du skapade tidigare. |
| `targetServiceHostName` | IP-adressen där SSH-servern lyssnar. Adressen skulle vara `localhost` om det är samma IP-Adressen där enheten-lokal proxy-program körs. |
| `targetServicePort` | Den port som används av ditt programprotokoll (SSH, som standard, skulle detta port 22).  |

Kompilera och kör koden på följande sätt:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $deviceConnectionString localhost 22

# In Windows
dotnet run %deviceConnectionString% localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Köra tjänstlokalt proxyprogram

Gå till `device-streams-proxy/service` i den uppackade projektmappen. Du behöver ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `iotHubConnectionString` | Tjänstanslutningssträngen för din IoT-hubb. |
| `deviceId` | Identifieraren för den enhet som du skapade tidigare. |
| `localPortNumber` | En annan lokal port som SSH-klienten ska ansluta till. Vi använder port 2222 i det här exemplet, men du kan använda andra godtyckliga siffror. |

Kompilera och kör koden på följande sätt:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Kör SSH-klient

Använd SSH klientprogrammet och Anslut till service-lokal proxy-program på port 2222 (i stället för SSH-daemon direkt).

```
ssh <username>@localhost -p 2222
```

Nu uppmanas fönstret för SSH du att ange dina autentiseringsuppgifter.

Konsolens utdata på serversidan (service-lokal proxy-program som lyssnar på port 2222):

![Utdata för Service-lokal proxy-program](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Konsolens utdata på enhet-lokal proxy-program, som ansluter till SSH-daemon på *IP_address:22*:

![Utdata för enhet-lokal proxy-program](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Konsolens utdata för SSH-klientprogram. SSH-klienten kommunicerar med SSH-daemon genom att ansluta till port 22, som service-lokal proxy-program lyssnar på:

![Utdata för SSH-klienten program](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP till en enhet via enhetsströmmar

Inställningarna för RDP påminner om konfigurationen för SSH (beskrivs ovan). Du använder RDP mål-IP och port 3389 i stället och använder RDP-klient (i stället för SSH-klient).

### <a name="run-the-device-local-proxy-application-rdp"></a>Kör enhet-lokal-proxyprogrammet (RDP)

Gå till den *-strömmar-proxy/enhet* katalogen i mappen uppzippade projektet. Ha följande information till hands:

| Argumentnamn | Argumentvärde |
|----------------|-----------------|
| `DeviceConnectionString` | Anslutningssträngen för den enhet som du skapade tidigare. |
| `targetServiceHostName` | Värdnamnet eller IP-adressen där RDP-servern körs. Adressen skulle vara `localhost` om det är samma IP-Adressen där enheten-lokal proxy-program körs. |
| `targetServicePort` | Porten som används av ditt programprotokoll (RDP, som standard, skulle detta port 3389).  |

Kompilera och kör koden på följande sätt:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run $DeviceConnectionString localhost 3389

# In Windows
dotnet run %DeviceConnectionString% localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Köra service-lokal-proxyprogrammet (RDP)

Gå till `device-streams-proxy/service` i den uppackade projektmappen. Du behöver ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `iotHubConnectionString` | Tjänstanslutningssträngen för din IoT-hubb. |
| `deviceId` | Identifieraren för den enhet som du skapade tidigare. |
| `localPortNumber` | En annan lokal port som SSH-klienten ska ansluta till. Vi använder port 2222 i det här exemplet, men du kan ändra detta till andra godtyckliga nummer. |

Kompilera och kör koden på följande sätt:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-rdp-client"></a>Köra RDP-klient

Använd RDP klientprogrammet och Anslut till service-lokal proxy-program på port 2222 (detta var en godtycklig tillgänglig port som du valde tidigare).

![RDP ansluter till tjänsten-lokal proxyprogrammet](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du ställa in en IoT-hubb, registrerade en enhet, distribueras enheten lokala och service-lokala proxy-program att upprätta en enhet stream via IoT hub och används för proxy-program för att tunnel SSH eller RDP-trafik. Samma paradigmet kan behandla andra klient / server-protokoll, där servern körs på enheten (till exempel SSH-daemon).

Mer information om enheten strömmar finns:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
