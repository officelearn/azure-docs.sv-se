---
title: Interagera med en IoT Plug and Play Preview-enhet som är ansluten till din Azure IoT-lösning | Microsoft-dokument
description: Använd Java för att ansluta till och interagera med en IoT Plug and Play Preview-enhet som är ansluten till din Azure IoT-lösning.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 53812e68fe397b81f29869565e0e4a0640a9ef23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964642"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Snabbstart: Interagera med en IoT Plug and Play Preview-enhet som är ansluten till din lösning (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview förenklar IoT genom att du kan interagera med en enhets funktioner utan att du känner till den underliggande enhetsimplementeringen. Den här snabbstarten visar hur du använder Java för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Krav

För att slutföra denna snabbstart behöver du Java SE 8 på din utvecklingsmaskin. Du måste också installera Maven 3.

Mer information om hur du konfigurerar med dessa finns i [Förbereda din utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) i Microsoft Azure IoT-enheten SDK för Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutningssträngen för IoT-hubben_ för navet (observera för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Kör exempelenheten

I den här snabbstarten använder du en exempelmiljösensor som är skriven i Java som IoT Plug and Play-enhet. Följande instruktioner visar hur du installerar och kör enheten:

1. Öppna ett terminalfönster i valfri katalog. Kör följande kommando för att klona [Azure IoT-exempel för Java](https://github.com/Azure-Samples/azure-iot-samples-java) GitHub-databasen till den här platsen:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Detta terminalfönster kommer nu att användas som _din enhetsterminal._ Gå till mappen i den klonade databasen och navigera till mappen **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample.** Installera de bibliotek som krävs och skapa det simulerade enhetsprogrammet genom att köra följande kommando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurera _enhetens anslutningssträng:_

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Kör följande kommando för att köra exemplet från enhetsmappen.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Du ser meddelanden som säger att enheten är ansluten, utför olika installationssteg och väntar på tjänstuppdateringar, följt av telemetriloggar. Detta indikerar att enheten nu är redo att ta emot kommandon och egenskapsuppdateringar och har börjat skicka telemetridata till navet. Håll exemplet igång när du slutför nästa steg. Stäng inte terminalen, du behöver den senare för att bekräfta att serviceproverna också fungerade.

## <a name="run-the-sample-solution"></a>Kör exempellösningen

I den här snabbstarten använder du en exempel-IoT-lösning i Java för att interagera med exempelenheten.

1. Öppna ett annat terminalfönster (detta kommer att vara din _serviceterminal)._ Gå till mappen i den klonade databasen och navigera till mappen **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample.**

1. Installera de bibliotek som krävs och skapa tjänstexemplet genom att köra följande kommando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurera _anslutningssträngen för IoT-hubb_ och _enhets-ID_ så att tjänsten kan ansluta till båda dessa:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Läsa en egenskap

1. När du anslöt _enheten_ i terminalen var ett av utdatameddelandena följande meddelande för att ange dess onlinestatus. Egenskapen, `state` som används för att ange om enheten är online, är _sant:_

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Gå till _serviceterminalen_ och använd följande kommando för att köra tjänstexemplet för att läsa enhetsinformation:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Bläddra till komponenten i `environmentalSensor` serviceterminalutdata. _service_ Du ser `state` att fastigheten har rapporterats som _sant:_
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
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

1. Använd följande kommando för att köra tjänstexemplet för att uppdatera egenskapen:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. _Serviceterminalutdata_ visar den uppdaterade enhetsinformationen. Bläddra till `environmentalSensor` komponenten för att se det nya ljusstyrvvärdet 42.

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Gå till _enhetsterminalen_ visas när enheten har fått uppdateringen:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Gå tillbaka till _din serviceterminal_ och kör kommandot nedan för att hämta enhetsinformationen igen för att bekräfta att egenskapen har uppdaterats.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. I _service_ serviceterminalutdata, `environmentalSensor` under komponenten, ser du att det uppdaterade ljusstyrhetsvärdet har rapporterats. Det kan ta ett tag innan enheten slutför uppdateringen. Du kan upprepa det här steget tills enheten faktiskt har bearbetat egenskapsuppdateringen.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Anropa ett kommando

1. Gå till _serviceterminalen_ och ange följande variabler för att definiera vilket kommando som ska anropas:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Använd följande kommando för att köra tjänstexemplet för att anropa kommandot:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. Utdata i _serviceterminalen_ bör visa följande bekräftelse:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Gå till _enhetsterminalen,_ du ser kommandot har bekräftats:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du ansluter en IoT Plug and Play-enhet till en IoT-lösning. Mer information om hur du skapar en lösning som interagerar med dina IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Så här: Ansluta till och interagera med en enhet](howto-develop-solution.md)
