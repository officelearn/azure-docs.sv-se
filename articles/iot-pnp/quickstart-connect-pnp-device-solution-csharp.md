---
title: Interagera med en IoT Plug and Play Preview-enhet som är ansluten till din Azure IoT-lösning | Microsoft-dokument
description: Använd C# (.NET) för att ansluta till och interagera med en IoT Plug and Play Preview-enhet som är ansluten till din Azure IoT-lösning.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76963979"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Snabbstart: Interagera med en IoT Plug and Play Preview-enhet som är ansluten till din lösning (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview förenklar IoT genom att du kan interagera med en enhets funktioner utan att du känner till den underliggande enhetsimplementeringen. Den här snabbstarten visar hur du använder C# (med .NET) för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten måste du installera .NET Core (2.x.x eller 3.x.x) på utvecklingsmaskinen. Du kan hämta önskad version av .NET Core SDK för flera plattformar från [Hämta .NET Core](https://dotnet.microsoft.com/download/dotnet-core/).

Du kan verifiera den version av .NET som finns på utvecklingsdatorn genom att köra följande kommando i ett lokalt terminalfönster: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutningssträngen för IoT-hubben_ för navet (observera för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Kör exempelenheten

I den här snabbstarten använder du en exempelmiljösensor som är skriven i C# som IoT Plug and Play-enhet. Följande instruktioner visar hur du installerar och kör enheten:

1. Öppna ett terminalfönster i valfri katalog. Kör följande kommando för att klona [Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-databasen till den här platsen:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Detta terminalfönster kommer nu att användas som _din enhetsterminal._ Gå till mappen i den klonade databasen och navigera till mappen **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample.**

1. Konfigurera _enhetens anslutningssträng:_

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Skapa nödvändiga paket och kör exemplet med följande kommando:

    ```cmd\sh
        dotnet run
    ```

1. Du ser meddelanden som säger att enheten har registrerats och väntar på uppdateringar från molnet. Detta indikerar att enheten nu är redo att ta emot kommandon och egenskapsuppdateringar och har börjat skicka telemetridata till navet. Stäng inte terminalen, du behöver den senare för att bekräfta att serviceproverna också fungerade.

## <a name="run-the-sample-solution"></a>Kör exempellösningen

I den här snabbstarten använder du en prov-IoT-lösning i C# för att interagera med exempelenheten.

1. Öppna ett annat terminalfönster (detta kommer att vara din _serviceterminal)._ Gå till mappen i den klonade databasen och navigera till mappen **/azure-iot-samples-csharp/digitaltwin/Samples/service.**

1. Konfigurera _anslutningssträngen för IoT-hubb_ och _enhets-ID_ så att tjänsten kan ansluta till båda dessa:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Läsa en egenskap

1. När du anslöt _enheten_ i terminalen såg du följande meddelande som anger dess onlinestatus:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Gå till _serviceterminalen_ och använd följande kommandon för att köra exemplet för att läsa enhetsinformation:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Bläddra till komponenten i `environmentalSensor` serviceterminalutdata. _service_ Du ser `state` att egenskapen, som används för att ange om enheten är online, har rapporterats som _sant:_

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

1. Gå till _serviceterminalen_ och ange följande variabler för att definiera vilken egenskap som ska uppdateras:
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

1. _Serviceterminalutdata_ visar den uppdaterade enhetsinformationen. Bläddra till `environmentalSensor` komponenten för att se det nya ljusstyrvvärdet 42.

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

1. Gå till _enhetsterminalen_ visas när enheten har fått uppdateringen:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Gå tillbaka till _din serviceterminal_ och kör nedanstående kommandon för att hämta enhetsinformationen igen för att bekräfta att egenskapen har uppdaterats.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. I _service_ serviceterminalutdata, `environmentalSensor` under komponenten, ser du att det uppdaterade ljusstyrhetsvärdet har rapporterats. Det kan ta ett tag innan enheten slutför uppdateringen. Du kan upprepa det här steget tills enheten faktiskt har bearbetat egenskapsuppdateringen.
    
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

1. Gå till _serviceterminalen_ och ange följande variabler för att definiera vilket kommando som ska anropas:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Använd följande kommandon för att köra exemplet för att anropa kommandot:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. Utdata i _serviceterminalen_ bör visa följande bekräftelse:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Gå till _enhetsterminalen,_ du ser kommandot har bekräftats:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du ansluter en IoT Plug and Play-enhet till en IoT-lösning. Mer information om hur du skapar en lösning som interagerar med dina IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Så här: Ansluta till och interagera med en enhet](howto-develop-solution.md)
