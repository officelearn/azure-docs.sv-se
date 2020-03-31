---
title: Azure IoT Hub-enheten strömmar C# snabbstart för SSH och RDP
description: I den här snabbstarten kör du två exempel C#-program som aktiverar SSH- och RDP-scenarier via en IoT Hub-enhetsström.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c8ef6a87e839e6d8dfb296e7b24f3d0d95843d1c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675470"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snabbstart: Aktivera SSH och RDP via en IoT Hub-enhetsström med hjälp av ett C#-proxyprogram (förhandsversion)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub stöder för närvarande enhetsströmmar som en [förhandsgranskningsfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-enhetsströmmar](iot-hub-device-streams-overview.md) gör att tjänst- och enhetsprogram kan kommunicera på ett säkert och brandväggsvänligt sätt. Den här snabbstartsguiden omfattar två C#-program som gör det möjligt att skicka programtrafik för klient-server (till exempel Secure Shell [SSH] och Remote Desktop Protocol [RDP] via en enhetsström som upprättas via en IoT-hubb. En översikt över installationen finns i [Exempel på lokalt proxyprogram för SSH eller RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

I den här artikeln beskrivs först inställningarna för SSH (med port 22) och beskrivs sedan hur du ändrar installationens port för RDP. Eftersom enhetsströmmar är program- och protokolloberoende kan samma exempel ändras för att hantera andra typer av programtrafik. Den här ändringen innebär vanligtvis bara att ändra kommunikationsporten till den som används av det avsedda programmet.

## <a name="how-it-works"></a>Hur det fungerar

Följande bild visar hur de enhetslokala och tjänstlokala proxyprogrammen i det här exemplet möjliggör end-to-end-anslutning mellan SSH-klienten och SSH-demonprocesserna. Här antar vi att demonen körs på samma enhet som det enhetslokala proxyprogrammet.

![Konfigurera lokalt proxyprogram](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Det servicelokala proxyprogrammet ansluter till IoT-hubben och initierar en enhetsström till målenheten.

1. Det enhetslokala proxyprogrammet slutför handskakningen för strömstart och upprättar en direktuppspelningstunnel från slutpunkt till slutpunkt för strömning via IoT-hubbens slutpunkt för direktuppspelning till tjänstsidan.

1. Det enhetslokala proxyprogrammet ansluter till SSH-demonen som lyssnar på port 22 på enheten. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "Kör det enhetslokala proxyprogrammet".

1. Det servicelokala proxyprogrammet väntar på nya SSH-anslutningar från en användare genom att lyssna på en angiven port, som i detta fall är port 2222. Den här inställningen kan konfigureras enligt beskrivningen i avsnittet "Kör det servicelokala proxyprogrammet". När användaren ansluter via SSH-klienten gör tunneln att SSH-programtrafik kan överföras mellan SSH-klienten och serverprogrammet.

> [!NOTE]
> SSH-trafik som skickas via en enhetsström är tunnel via IoT-hubbens slutpunkt för direktuppspelning i stället för att skickas direkt mellan tjänst och enhet. Mer information finns i [fördelarna med att använda Iot Hub-enhetsströmmar](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

* Förhandsgranskningen av enhetsströmmar stöds för närvarande endast för IoT-hubbar som skapas i följande regioner:

  * USA, centrala
  * Centrala USA EUAP
  * Sydostasien
  * Europa, norra

* De två exempelprogram som du kör i den här snabbstarten skrivs i C#. Du behöver .NET Core SDK 2.1.0 eller senare på utvecklingsmaskinen.

  Du kan hämta [.NET Core SDK för flera plattformar från .NET](https://www.microsoft.com/net/download/all).

* Verifiera den aktuella versionen av C# på utvecklingsmaskinen med hjälp av följande kommando:

    ```
    dotnet --version
    ```

* Kör följande kommando för att lägga till Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS)-specifika kommandon i Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Hämta Azure IoT C#-exemplen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)och extrahera ZIP-arkivet.

* Ett giltigt användarkonto och autentiseringsuppgifter på enheten (Windows eller Linux) som används för att autentisera användaren.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell för att registrera en simulerad enhet.

1. Om du vill skapa enhetsidentiteten kör du följande kommando i Cloud Shell:

   > [!NOTE]
   > * Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.
   > * För namnet på den enhet du registrerar rekommenderar vi att du använder *MyDevice* som visas. Om du väljer ett annat namn för enheten använder du det namnet i den här artikeln och uppdaterar enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Om du vill hämta *enhetsanslutningssträngen* för den enhet som du just registrerade kör du följande kommandon i Cloud Shell:

   > [!NOTE]
   > Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observera den returnerade enhetsanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Om du vill ansluta till IoT-hubben och upprätta en enhetsström behöver du också *serviceanslutningssträngen* från IoT-hubben för att aktivera tjänstprogrammet. Följande kommando hämtar det här värdet för din IoT-hubb:

   > [!NOTE]
   > Ersätt platshållaren *YourIoTHubName* med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observera den returnerade serviceanslutningssträngen för senare användning i den här snabbstarten. Det ser ut som i följande exempel:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH till en enhet via enhetsströmmar

I det här avsnittet upprättar du en end-to-end-ström till tunnel-SSH-trafik.

### <a name="run-the-device-local-proxy-application"></a>Kör det enhetslokala proxyprogrammet

I ett lokalt terminalfönster `device-streams-proxy/device` navigerar du till katalogen i den uppackade projektmappen. Ha följande information till hands:

| Argumentnamn | Argumentvärde |
|----------------|-----------------|
| `DeviceConnectionString` | Enhetsanslutningssträngen för den enhet som du skapade tidigare. |
| `targetServiceHostName` | IP-adressen där SSH-servern lyssnar. Adressen skulle `localhost` vara om det var samma IP där enheten-lokala proxyprogram körs. |
| `targetServicePort` | Porten som används av ditt programprotokoll (för SSH, som standard, skulle detta vara port 22).  |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Köra tjänstlokalt proxyprogram

I ett annat lokalt `iot-hub/quickstarts/device-streams-proxy/service` terminalfönster navigerar du till i den uppackade projektmappen. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `ServiceConnectionString` | Tjänstanslutningssträngen för din IoT-hubb. |
| `MyDevice` | Identifieraren för den enhet som du skapade tidigare. |
| `localPortNumber` | En lokal port som SSH-klienten ansluter till. Vi använder port 2222 i det här exemplet, men du kan använda andra godtyckliga nummer. |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Kör SSH-klienten

Använd nu ditt SSH-klientprogram och anslut till servicelokalt proxyprogram på port 2222 (i stället för SSH-demonen direkt).

```
ssh {username}@localhost -p 2222
```

Nu uppmanas du att ange dina autentiseringsuppgifter i inloggningsfönstret ssh.

Konsolutdata på tjänstsidan (det servicelokala proxyprogrammet lyssnar på port 2222):

![Utdata för servicelokalt proxyprogram](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Konsolutdata på det enhetslokala proxyprogrammet, som ansluter till SSH-demonen *vid IP_address:22:*

![Utdata för enhetslokalt proxyprogram](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Konsolutdata för SSH-klientprogrammet. SSH-klienten kommunicerar till SSH-demonen genom att ansluta till port 22, som det servicelokala proxyprogrammet lyssnar på:

![Utdata för SSH-klientprogram](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP till en enhet via enhetsströmmar

Inställningen för RDP liknar inställningen för SSH (beskrivs ovan). Du använder IP-adressen för RDP-målet och port 3389 i stället och använder RDP-klienten (i stället för SSH-klienten).

### <a name="run-the-device-local-proxy-application-rdp"></a>Kör det enhetslokala proxyprogrammet (RDP)

I ett lokalt terminalfönster `device-streams-proxy/device` navigerar du till katalogen i den uppackade projektmappen. Ha följande information till hands:

| Argumentnamn | Argumentvärde |
|----------------|-----------------|
| `DeviceConnectionString` | Enhetsanslutningssträngen för den enhet som du skapade tidigare. |
| `targetServiceHostName` | Värdnamnet eller IP-adressen där RDP-servern körs. Adressen skulle `localhost` vara om det var samma IP där enheten-lokala proxyprogram körs. |
| `targetServicePort` | Porten som används av ditt programprotokoll (för RDP, som standard, skulle detta vara port 3389).  |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Kör det tjänstlokala proxyprogrammet (RDP)

I ett annat lokalt `device-streams-proxy/service` terminalfönster navigerar du till i den uppackade projektmappen. Ha följande information till hands:

| Parameternamn | Parametervärde |
|----------------|-----------------|
| `ServiceConnectionString` | Tjänstanslutningssträngen för din IoT-hubb. |
| `MyDevice` | Identifieraren för den enhet som du skapade tidigare. |
| `localPortNumber` | En lokal port som SSH-klienten ansluter till. Vi använder port 2222 i det här exemplet, men du kan ändra detta till andra godtyckliga nummer. |

Kompilera och kör koden med följande kommandon:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>Köra RDP-klient

Använd nu rdp-klientprogrammet och anslut till det servicelokala proxyprogrammet på port 2222 (det här var en godtycklig tillgänglig port som du valde tidigare).

![RDP ansluter till det servicelokala proxyprogrammet](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerar du en IoT-hubb, registrerade en enhet, distribuerade enhetslokala och tjänstlokala proxyprogram för att upprätta en enhetsström via IoT-hubben och använde proxyprogrammen för att tunnel-SSH- eller RDP-trafik. Samma paradigm kan rymma andra klient-server protokoll, där servern körs på enheten (till exempel SSH demon).

Mer information om enhetsströmmar finns i:

> [!div class="nextstepaction"]
> [Översikt över enhetsströmmar](./iot-hub-device-streams-overview.md)
