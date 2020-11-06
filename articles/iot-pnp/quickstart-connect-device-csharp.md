---
title: Snabb start – ansluta IoT-Plug and Play exempel C#-enhets kod till Azure IoT Hub | Microsoft Docs
description: Snabb start – skapa och kör IoT Plug and Play exempel enhets kod i Windows som ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 01e87b08f8d0abcf3659e0d7bfe081fd3516ff08
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421625"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-c"></a>Snabb start: ansluta ett exempel på IoT Plug and Play Device-program som körs på Windows till IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. Exempel programmet är skrivet i C# och ingår i Azure IoT-exemplen för C#. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

För att slutföra den här snabb starten i Windows behöver du följande program vara installerad på utvecklings datorn:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>Ladda ned koden

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och skapa Azure IoT-exempel för C#-lagringsplatsen.

Öppna en kommando tolk i valfri mapp. Kör följande kommando för att klona [Microsoft Azure IoT-exempel för c# (.net)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-lagringsplatsen till den här platsen:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Skapa koden

Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

1. Öppna projekt filen *Azure-IoT-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.CSPROJ* i Visual Studio 2019.

1. I Visual Studio navigerar du till **Project > termostat-egenskaper > Felsök**. Lägg sedan till följande miljövariabler i projektet:

    | Name | Värde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | – |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Värdet du antecknade när du slutförde [konfiguration av din miljö](set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | min-PnP-enhet |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Värdet du antecknade när du slutförde [konfiguration av din miljö](set-up-environment.md) |

Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

## <a name="run-the-device-sample"></a>Kör enhets exemplet

Om du vill spåra kod körningen i Visual Studio på Windows lägger du till en Bryt punkt i `main` funktionen i program.cs-filen.

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

I det här exemplet implementeras en enkel IoT Plug and Play termostat-enhet. Modellen som det här exemplet implementerar använder inte IoT Plug and Play- [komponenter](concepts-components.md). [DTDL-Model-filen (Digital Definition Language) för termostat-enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Enhets koden ansluter till IoT-hubben med hjälp av standard `CreateFromConnectionString` metoden. Enheten skickar modell-ID: t för DTDL-modellen som den implementerar i anslutningsbegäran. En enhet som skickar ett modell-ID är en IoT Plug and Play-enhet:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

Modell-ID: t lagras i koden som visas i följande kodfragment:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Den kod som uppdaterar egenskaper, hanterar kommandon och skickar telemetri är identisk med koden för en enhet som inte använder IoT Plug and Play konventioner.

Exemplet använder ett JSON-bibliotek för att parsa JSON-objekt i de nytto laster som skickas från din IoT Hub:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Anvisningar: ansluta till och interagera med en enhet](./quickstart-service-node.md)