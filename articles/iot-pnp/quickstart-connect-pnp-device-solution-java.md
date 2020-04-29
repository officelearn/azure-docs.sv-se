---
title: Interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din Azure IoT-lösning | Microsoft Docs
description: Använd Java för att ansluta till och interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din Azure IoT-lösning.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 53812e68fe397b81f29869565e0e4a0640a9ef23
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76964642"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Snabb start: interagera med en IoT Plug and Play förhands gransknings enhet som är ansluten till din lösning (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview fören klar IoT genom att göra det möjligt för dig att interagera med enhetens funktioner utan att du behöver känna till den underliggande enhets implementeringen. Den här snabb starten visar hur du använder Java för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten behöver du Java SE 8 på din utvecklings dator. Du måste också installera maven 3.

Mer information om hur du kommer igång med dessa finns i [förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) i Microsoft Azure IoT-enhetens SDK för Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för din hubb (Anmärkning för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

I den här snabb starten använder du en exempel miljö sensor som är skriven i Java som IoT Plug and Play-enhet. Följande instruktioner visar hur du installerar och kör enheten:

1. Öppna ett terminalfönster i valfri katalog. Kör följande kommando för att klona [Azure IoT-exemplen för Java](https://github.com/Azure-Samples/azure-iot-samples-java) GitHub-lagringsplatsen till den här platsen:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Det här terminalfönstret används nu som din _enhets_ Terminal. Gå till mappen för den klonade lagrings platsen och navigera till mappen **/Azure-IoT-samples-Java/Digital-Twin/samples/Device/JdkSample** . Installera de bibliotek som krävs och bygg det simulerade enhets programmet genom att köra följande kommando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurera _enhets anslutnings strängen_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Kör följande kommando för att köra exemplet från mappen Device.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Du ser meddelanden som säger att enheten är ansluten, utför olika installations steg och väntar på tjänst uppdateringar, följt av telemetri loggar. Detta anger att enheten nu är redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg. Stäng inte den här terminalen, du behöver den senare för att bekräfta att tjänst exemplen också fungerade.

## <a name="run-the-sample-solution"></a>Kör exempel lösningen

I den här snabb starten använder du en exempel-IoT-lösning i Java för att interagera med exempel enheten.

1. Öppna ett annat terminalfönster (det här är din _tjänsts_ Terminal). Gå till mappen för den klonade lagrings platsen och navigera till mappen **/Azure-IoT-samples-java\digital-twin\Samples\service\JdkSample** .

1. Installera de bibliotek som krävs och bygg tjänst exemplet genom att köra följande kommando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurera _IoT Hub-anslutningssträngen_ och _enhets-ID: t_ så att tjänsten kan ansluta till båda dessa:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Läsa en egenskap

1. När du anslöt _enheten_ i terminalen var ett av utdatafilerna följande meddelande för att ange onlinestatus. `state` Egenskapen, som används för att ange om enheten är online, är _True_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Gå till _tjänsten_ Terminal och Använd följande kommando för att köra tjänst exemplet för att läsa enhets information:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Rulla till _service_ `environmentalSensor` komponenten i utdata från tjänsten. Du ser att `state` egenskapen har rapporter ATS som _Sant_:
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

1. Gå till _tjänsten_ Terminal och ange följande variabler för att definiera vilken egenskap som ska uppdateras:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Använd följande kommando för att köra tjänst exemplet för att uppdatera egenskapen:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. I _tjänstens Terminal-_ utdata visas den uppdaterade enhets informationen. Bläddra till `environmentalSensor` komponenten om du vill se det nya värdet för ljus styrka på 42.

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

1. Gå till _enhetens_ Terminal. du ser att enheten har tagit emot uppdateringen:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Gå tillbaka till _tjänsten_ Terminal och kör kommandot nedan för att hämta enhets informationen igen för att bekräfta att egenskapen har uppdaterats.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. I _tjänstens_ Terminal-utdata, under `environmentalSensor` komponenten, ser du att det uppdaterade värdet för ljus styrka har rapporter ATS. Obs: det kan ta en stund innan enheten har slutfört uppdateringen. Du kan upprepa det här steget tills enheten faktiskt har bearbetat egenskaps uppdateringen.
    
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

1. Gå till _tjänsten_ Terminal och ange följande variabler för att definiera vilket kommando som ska anropas:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Använd följande kommando för att köra tjänst exemplet för att anropa kommandot:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. Utdata i _tjänstens_ Terminal ska visa följande bekräftelse:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Gå till _enhetens_ Terminal. du ser att kommandot har bekräftats:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-lösning. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Anvisningar: ansluta till och interagera med en enhet](howto-develop-solution.md)
