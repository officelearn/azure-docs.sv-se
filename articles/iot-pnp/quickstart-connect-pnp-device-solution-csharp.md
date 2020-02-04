---
title: Interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din Azure IoT-lösning | Microsoft Docs
description: Använd C# (.net) för att ansluta till och interagera med en IoT plug and Play för hands version som är ansluten till din Azure IoT-lösning.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963979"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Snabb start: interagera med en IoT Plug and Play förhands gransknings enhet som ärC#ansluten till din lösning ()

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview fören klar IoT genom att göra det möjligt för dig att interagera med enhetens funktioner utan att du behöver känna till den underliggande enhets implementeringen. Den här snabb starten visar hur du C# använder (med .net) för att ansluta till och styra en IoT plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten måste du installera .NET Core (2. x. x eller 3. x) på din utvecklings dator. Du kan ladda ned den önskade versionen av .NET Core SDK för flera plattformar från [Ladda ned .net Core](https://dotnet.microsoft.com/download/dotnet-core/).

Du kan kontrol lera vilken version av .NET som finns på utvecklings datorn genom att köra följande kommando i ett lokalt terminalfönster: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för din hubb (Anmärkning för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

I den här snabb starten använder du en exempel miljö sensor som är skriven C# som IoT plug and Play-enheten. Följande instruktioner visar hur du installerar och kör enheten:

1. Öppna ett terminalfönster i valfri katalog. Kör följande kommando för att klona [Azure IoT-exempel C# för (.net) GitHub-](https://github.com/Azure-Samples/azure-iot-samples-csharp) lagringsplatsen till den här platsen:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Det här terminalfönstret används nu som din _enhets_ Terminal. Gå till mappen för den klonade lagrings platsen och navigera till mappen **/Azure-IoT-samples-csharp/digitaltwin/samples/Device/EnvironmentalSensorSample** .

1. Konfigurera _enhets anslutnings strängen_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Skapa de nödvändiga paketen och kör exemplet med följande kommando:

    ```cmd\sh
        dotnet run
    ```

1. Du ser meddelanden som säger att enheten har registrerats och väntar på uppdateringar från molnet. Detta anger att enheten nu är redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Stäng inte den här terminalen, du behöver den senare för att bekräfta att tjänst exemplen också fungerade.

## <a name="run-the-sample-solution"></a>Kör exempel lösningen

I den här snabb starten använder du en exempel-IoT C# -lösning i för att interagera med exempel enheten.

1. Öppna ett annat terminalfönster (det här är din _tjänsts_ Terminal). Gå till mappen för den klonade lagrings platsen och navigera till mappen **/Azure-IoT-samples-csharp/digitaltwin/samples/service** .

1. Konfigurera _IoT Hub-anslutningssträngen_ och _enhets-ID: t_ så att tjänsten kan ansluta till båda dessa:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Läsa en egenskap

1. När du anslöt _enheten_ i terminalen såg du följande meddelande som visar att statusen är online:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Gå till _tjänsten_ Terminal och Använd följande kommandon för att köra exemplet för att läsa enhets information:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Bläddra till `environmentalSensor`-komponenten i _tjänstens_ Terminal-utdata. Du ser att egenskapen `state`, som används för att ange om enheten är online, har rapporter ATS som _Sant_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Uppdatera en skrivbar egenskap

1. Gå till _tjänsten_ Terminal och ange följande variabler för att definiera vilken egenskap som ska uppdateras:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Använd följande kommandon för att köra exemplet för att uppdatera egenskapen:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. I _tjänstens Terminal-_ utdata visas den uppdaterade enhets informationen. Rulla till `environmentalSensor`-komponenten för att se det nya värdet för ljus styrka på 42.

    ```json
        "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

1. Gå till _enhetens_ Terminal. du ser att enheten har tagit emot uppdateringen:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Gå tillbaka till _tjänstens_ Terminal och kör kommandona nedan för att hämta enhets informationen igen för att bekräfta att egenskapen har uppdaterats.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. I _tjänstens_ Terminal-utdata under `environmentalSensor`-komponenten ser du att det uppdaterade värdet för ljus styrka har rapporter ATS. Obs: det kan ta en stund innan enheten har slutfört uppdateringen. Du kan upprepa det här steget tills enheten faktiskt har bearbetat egenskaps uppdateringen.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Anropa ett kommando

1. Gå till _tjänsten_ Terminal och ange följande variabler för att definiera vilket kommando som ska anropas:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Använd följande kommandon för att köra exemplet för att anropa kommandot:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. Utdata i _tjänstens_ Terminal ska visa följande bekräftelse:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Gå till _enhetens_ Terminal. du ser att kommandot har bekräftats:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-lösning. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Anvisningar: ansluta till och interagera med en enhet](howto-develop-solution.md)
