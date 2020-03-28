---
title: Självstudiekurs - Anslut en allmän Node.js-klientapp till Azure IoT Central | Microsoft-dokument
description: Den här självstudien visar hur du som enhetsutvecklare ansluter en enhet som kör en Node.js-klientapp till ditt Azure IoT Central-program. Du skapar en enhetsmall genom att importera en enhetskapacitetsmodell och lägga till vyer som gör att du kan interagera med en ansluten enhet
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77624545"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Självstudiekurs: Skapa och ansluta ett Node.js-klientprogram till ditt Azure IoT Central-program (Node.js)

Den här självstudien visar hur du som enhetsutvecklare ansluter ett Node.js-klientprogram till ditt Azure IoT Central-program. Node.js-programmet simulerar beteendet hos en riktig enhet. Du använder en _exempelenhetsfunktionsmodell_ för en miljösensorenhet för att skapa en _enhetsmall_ i IoT Central. Du lägger till vyer i enhetsmallen så att du kan visualisera enhetstelemetri, hantera enhetsegenskaper och använda kommandon för att styra dina enheter.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Importera en enhetsfunktionsmodell för att skapa en enhetsmall.
> * Lägg till standardvyer och anpassade vyer i en enhetsmall.
> * Publicera en enhetsmall och lägg till en riktig enhet i ditt IoT Central-program.
> * Skapa och kör nod.js-enhetskoden och se den ansluta till ditt IoT Central-program.
> * Visa den simulerade telemetri som enheten skickar.
> * Använd en vy för att hantera enhetsegenskaper.
> * Anropa kommandon för att styra enheten.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra stegen i den här artikeln:

* Ett Azure IoT Central-program som skapats med hjälp av **Custom application **template. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* En utvecklingsmaskin med [Node.js](https://nodejs.org/) version 10.0.0 eller senare installerad. Du kan `node --version` köra på kommandoraden för att kontrollera din version. Node.js är tillgängligt för många olika operativsystem. Instruktionerna i den här självstudien förutsätter att du kör **nodkommandot** i Kommandotolken i Windows. Du kan använda Node.js på en mängd olika operativsystem.

## <a name="create-a-device-template"></a>Skapa en enhetsmall

Skapa en `environmental-sensor` mapp som anropas på den lokala datorn.

Ladda ner [miljösensor kapacitet modell](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) JSON `environmental-sensor` fil och spara den i mappen.

Använd en textredigerare för `{YOUR_COMPANY_NAME_HERE}` att ersätta de `EnvironmentalSensorInline.capabilitymodel.json` två instanserna med ditt företagsnamn i filen som du hämtade.

Skapa en enhetsmall som heter *Miljösensor* i azure IoT Central-programmet genom att importera `EnvironmentalSensorInline.capabilitymodel.json` enhetsfunktionsmodellfilen:

![Enhetsmall med standardmodell för importerad enhetskapacitet](./media/tutorial-connect-device/device-template.png)

Enhetskapacitetsmodellen innehåller två gränssnitt: **standardgränssnittet för enhetsinformation** och det anpassade **miljösensorgränssnittet.** **Gränssnittet för miljösensor** definierar följande funktioner:

| Typ | Visningsnamn | Beskrivning |
| ---- | ------------ | ----------- |
| Egenskap | Enhetstillstånd     | Enhetens tillstånd. Två tillstånd online/offline är tillgängliga. |
| Egenskap | Kundens namn    | Namnet på den kund som för närvarande använder enheten. |
| Egenskap | Ljusstyrka | Ljusstyrkan för lampan på enheten. Kan anges som 1 (hög), 2 (medium), 3 (låg). |
| Telemetri | Temperatur | Aktuell temperatur på enheten. |
| Telemetri | Fuktighet    | Aktuell luftfuktighet på enheten. |
| Kommando | Blinka          | Börja blinka lysdioden för givet tidsintervall. |
| Kommando | Turnon         | Slå på LED-lampan på enheten. |
| Kommando | Avfarten        | Stäng av LED-lampan på enheten. |
| Kommando | rundiagnostics (rundiagnostics) | Det här kommandot startar en diagnostikkörning. |

Om du vill anpassa hur egenskapen **Enhetstillstånd** visas i i ditt IoT Central-program väljer du **Anpassa** i enhetsmallen. Expandera **enhetstillståndsposten,** ange _Online_ som **sant namn** och _Offline_ som **falskt namn**. Spara sedan ändringarna:

![Anpassa enhetsmallen](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Skapa vyer

Med vyer kan du interagera med enheter som är anslutna till ditt IoT Central-program. Du kan till exempel ha vyer som visar telemetri, vyer som visar egenskaper och vyer som gör att du kan redigera skrivbara och molnegenskaper. Vyer är en del av en enhetsmall.

Om du vill lägga till några standardvyer i **mallen Miljösensorenhet** navigerar du till enhetsmallen, väljer **Vyer**och väljer panelen **Generera standardvyer.** Kontrollera att **Översikt** och **Om** är **På**och välj sedan **Generera standardinstrumentpanelsvyer**. Nu har du definierat två standardvyer i mallen.

**Gränssnittet för miljösensor** innehåller två skrivbara egenskaper - **Kundnamn** och **ljusstyrka**. Om du vill skapa en vy kan du använda för att redigera dessa egenskaper:

1. Välj **Vyer** och välj sedan panelen **Redigera enhet och molndata.**

1. Ange _egenskaper_ som formulärnamn.

1. Välj egenskaperna **Ljusstyrka** och **Kundnamn.** Välj sedan **Lägg till avsnitt**.

1. Spara ändringarna.

![Lägga till en vy för att aktivera egenskapsredigering](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Publicera mallen

Innan du lägger till en enhet i ditt IoT Central-program som använder mallen **miljösensorenhet** måste du publicera mallen.

Välj **Publicera**i enhetsmallen . Välj **Publicera**på panelen som visar de ändringar som ska publiceras .

Om du vill kontrollera att mallen är klar att användas navigerar du till sidan **Enheter** i ditt IoT Central-program. Avsnittet **Enheter** visar en lista över de publicerade enheterna i programmet:

![Publicerade mallar på sidan enheter](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I ditt Azure IoT Central-program lägger du till en riktig enhet i enhetsmallen som du skapade i föregående avsnitt:

1. På sidan **Enheter** väljer du mallen **miljösensorenhet.**

1. Välj **+ Nytt**.

1. Kontrollera att **simulerad** är **Av**. Välj sedan **Skapa**.

Klicka på enhetens namn och välj sedan **Anslut**. Anteckna enhetsanslutningsinformationen på sidan **Enhetsanslutning** - **ID-scope,** **Enhets-ID**och **Primärnyckel**. Du behöver dessa värden när du skapar enhetskoden:

![Information om enhetsanslutning](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Följande steg visar hur du skapar ett Node.js-klientprogram som implementerar den verkliga enheten som du har lagt till i programmet. Det här nod.js-programmet simulerar beteendet hos en riktig enhet.

1. I kommandoradsmiljön navigerar `environmental-sensor` du till mappen som du skapade tidigare.

1. Om du vill initiera node.js-projektet och installera de nödvändiga beroendena kör du `npm init`följande kommandon – accepterar alla standardalternativ när du kör:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Skapa en fil som heter **environmentalSensor.js** i `environmental-sensor` mappen.

1. Lägg till `require` följande satser i början av **filen environmentalSensor.js:**

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Lägg till följande variabeldeklarationer i filen:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    var ledOn = true;
    ```

    Uppdatera platshållarna `{your Scope ID}` `{your Device ID}`och `{your Primary Key}` med de värden som du har noterat tidigare. I det här exemplet `targetTemperature` initierar du till noll, du kan använda den aktuella avläsningen från enheten eller ett värde från enhetstvillingen.

1. Om du vill skicka telemetri till ditt Azure IoT Central-program lägger du till följande funktion i filen:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Om du vill skicka enhetsegenskaper till ditt Azure IoT Central-program lägger du till följande funktion i filen:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Om du vill definiera och hantera de skrivbara egenskaper som enheten svarar på lägger du till följande kod:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
      }, 5000);
      }
    };

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. Lägg till följande kod för att hantera kommandon som skickats från IoT Central-programmet:

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
    function turnOn(request, response) {
      console.log('Received synchronous call to turn on LED');
      if(!ledOn){
        console.log('Turning on the LED');
        ledOn = true;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle LED turn off command
    function turnOff(request, response) {
      console.log('Received synchronous call to turn off LED');
      if(ledOn){
        console.log('Turning off the LED');
        ledOn = false;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
        }
      });
    }
    ```

1. Lägg till följande kod för att slutföra anslutningen till Azure IoT Central och koppla samman funktionerna i klientkoden:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Kör Node.js-programmet

Om du vill starta enhetsklientprogrammet kör du följande kommando i kommandoradsmiljön:

```cmd/sh
node environmentalSensor.js
```

Du kan se enheten ansluter till ditt Azure IoT Central-program och börjar skicka telemetri:

![Kör klientprogrammet](media/tutorial-connect-device/run-application.png)

Som operatör i ditt Azure IoT Central-program kan du:

* Visa den telemetri som enheten skickar på sidan **Översikt:**

    ![Visa telemetrin](media/tutorial-connect-device/view-telemetry.png)

* Uppdatera egenskapsvärden för skrivbara egenskaper på sidan **Egenskaper:**

    ![Uppdatera egenskaper](media/tutorial-connect-device/update-properties.png)

    ![Enhet för uppdatera egenskaper](media/tutorial-connect-device/update-properties-device.png)

* Anropa kommandona från sidan **Kommandon:**

    ![Kommandot Ring blink](media/tutorial-connect-device/call-command.png)

    ![Anropa blinkkommandoenhet](media/tutorial-connect-device/call-command-device.png)

* Visa enhetsegenskaperna på sidan **Om:**

    ![Visa egenskaper](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om enhetskapacitetsmodeller och hur du skapar egna enhetsmallar fortsätter du till instruktioner:

> [!div class="nextstepaction"]
> [Definiera en ny IoT-enhetstyp](./howto-set-up-template.md)
