---
title: Ansluta IoT Plug and Play Preview exempel C#-komponent enhets kod till IoT Hub | Microsoft Docs
description: Skapa och köra IoT Plug and Play Preview exempel C# enhets kod som använder flera komponenter och ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 67b71399332fb29a277381a8c2806dbe7fb31d85
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552177"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Självstudie: ansluta en IoT Plug and Play flera komponent enhets program som körs på Windows till IoT Hub (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

I den här självstudien får du lära dig hur du skapar ett exempel på IoT Plug and Play enhets program med komponenter och rot gränssnitt, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet är skrivet i C# och ingår i Azure IoT-enhetens SDK för C#. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill slutföra den här självstudien i Windows installerar du följande program vara i din lokala Windows-miljö:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om du vill interagera med exempel enheten i den andra delen av den här självstudien använder du **Azure IoT Explorer** -verktyget. [Hämta och installera den senaste versionen av Azure IoT Explorer](./howto-use-iot-explorer.md) för ditt operativ system.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för hubben. Anteckna den här anslutnings strängen, du använder den senare i den här självstudien:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Du kan också använda Azure IoT Explorer-verktyget för att hitta anslutnings strängen för IoT Hub.

Kör följande kommando för att hämta _enhets anslutnings strängen_ för den enhet som du har lagt till i hubben. Anteckna den här anslutnings strängen, du använder den senare i den här självstudien:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Ladda ned koden

I den här självstudien förbereder du en utvecklings miljö som du kan använda för att klona och bygga Azure IoT Hub Device C# SDK.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Azure IoT C# SDK: er och bibliotek](https://github.com/Azure/azure-iot-sdk-csharp) GitHub-lagringsplatsen på den här platsen:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-csharp.git
```

## <a name="build-the-code"></a>Skapa koden

Öppna lösnings filen **azureiot. SLN** i Visual Studio 2019 och ange **TemperatureController** -projektet som start projekt. I **Solution Explorer**kan du hitta den här projekt filen i **iothub > Device >-exempel**.

Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

## <a name="run-the-device-sample"></a>Kör enhets exemplet

Skapa en miljö variabel med namnet **IOTHUB_DEVICE_CONNECTION_STRING** för att lagra enhets anslutnings strängen som du antecknade tidigare.

Om du vill spåra kod körningen i Visual Studio på Windows lägger du till en Bryt punkt i `main` funktionen i program.cs-filen.

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

Det här exemplet implementerar en IoT Plug and Play temperatur styrenhets enhet. Modellen som det här exemplet implementerar använder [flera komponenter](concepts-components.md). [DTDL-modell filen (Digital Definition Language) för temperatur enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

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

När enheten ansluter till din IoT-hubb registreras kommando hanterare i koden. `reboot`Kommandot definieras i rot gränssnittet. `getMaxMinReport`Kommandot definieras i var och en av de två termostat-komponenterna:

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

Det finns separata hanterare för önskade egenskaps uppdateringar för de två termostat-komponenterna:

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

Exempel koden skickar telemetri från varje termostat-komponent:

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

`SendTemperature`Metoden använder `PnpHhelper` klassen för att skapa meddelanden för varje komponent:

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper`Klassen innehåller andra exempel metoder som du kan använda med en modell för flera komponenter.

Använd Azure IoT Explorer-verktyget för att Visa telemetri och egenskaper från de två termostat-komponenterna:

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Enhet för flera komponenter i Azure IoT Explorer":::

Du kan också använda Azure IoT Explorer-verktyget för att anropa kommandon i någon av de två termostat-komponenterna eller i rot gränssnittet.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du ansluter en IoT Plug and Play-enhet med komponenter till en IoT-hubb. Mer information om IoT Plug and Play enhets modeller finns i:

> [!div class="nextstepaction"]
> [IoT Plug and Play Preview Modeling Developer Guide](concepts-developer-guide.md)
