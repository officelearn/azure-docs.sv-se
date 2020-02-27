---
title: Självstudie – ansluta en generisk Node. js-klient app till Azure IoT Central | Microsoft Docs
description: Den här självstudien visar hur du, som enhets utvecklare, ansluter en enhet som kör en Node. js-klient till ditt Azure IoT Central-program. Du skapar en enhets mall genom att importera en enhets kapacitets modell och lägga till vyer som gör att du kan interagera med en ansluten enhet
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624545"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Självstudie: skapa och Anslut ett Node. js-klientprogram till ditt Azure IoT Central-program (Node. js)

Den här självstudien visar hur du, som enhets utvecklare, ansluter ett Node. js-klientprogram till ditt Azure IoT Central-program. Node. js-programmet simulerar beteendet hos en riktig enhet. Du använder en _enhets funktions modell_ för en miljö sensor för att skapa en _enhets mall_ i IoT Central. Du lägger till vyer i enhets mal len så att du kan visualisera enhetens telemetri, hantera enhets egenskaper och använda kommandon för att kontrol lera dina enheter.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Importera en enhets kapacitets modell för att skapa en enhets mall.
> * Lägg till standard-och anpassade vyer i en enhets mall.
> * Publicera en enhets mall och Lägg till en riktig enhet i IoT Central programmet.
> * Skapa och kör Node. js-enhets koden och se hur den ansluter till ditt IoT Central-program.
> * Visa den simulerade telemetri som enheten skickar.
> * Använd en vy för att hantera enhets egenskaper.
> * Anropa kommandon för att kontrol lera enheten.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här artikeln:

* Ett Azure IoT Central-program som skapats med hjälp av mallen * * Custom program * *. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* En utvecklings dator med [Node. js](https://nodejs.org/) version 10.0.0 eller senare installerad. Du kan kontrol lera din version genom att köra `node --version` på kommando raden. Node.js är tillgängligt för många olika operativsystem. Anvisningarna i den här självstudien förutsätter att du kör **Node** -kommandot i kommando tolken i Windows. Du kan använda Node. js på flera olika operativ system.

## <a name="create-a-device-template"></a>Skapa en enhets mall

Skapa en mapp med namnet `environmental-sensor` på den lokala datorn.

Ladda ned JSON-filen för [miljö sensorns kapacitets modell](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) och spara den i mappen `environmental-sensor`.

Använd en text redigerare för att ersätta de två instanserna av `{YOUR_COMPANY_NAME_HERE}` med ditt företags namn i `EnvironmentalSensorInline.capabilitymodel.json` filen som du laddade ned.

I ditt Azure IoT Central-program skapar du en enhets mall som kallas *miljö sensor* genom att importera `EnvironmentalSensorInline.capabilitymodel.json` enhetens kapacitets modell fil:

![Enhets mal len med importerad enhets kapacitets modell](./media/tutorial-connect-device/device-template.png)

Enhetens kapacitets modell innehåller två gränssnitt: standard gränssnittet för **enhets information** och det anpassade **miljö sensor** gränssnittet. Gränssnittet för **miljö sensor** definierar följande funktioner:

| Typ | Visningsnamn | Description |
| ---- | ------------ | ----------- |
| Egenskap | Enhetstillstånd     | Enhetens status. Två tillstånd online/offline är tillgängligt. |
| Egenskap | Kund namn    | Namnet på kunden som har bearbetat enheten. |
| Egenskap | Ljus styrke nivå | Ljus styrke nivån för ljuset på enheten. Kan anges som 1 (hög), 2 (medel), 3 (låg). |
| Telemetri | Temperatur | Aktuell temperatur på enheten. |
| Telemetri | Fuktighet    | Aktuell fuktighet på enheten. |
| Kommando | Blink          | Börja blinka LYSDIODen för angivet tidsintervall. |
| Kommando | turnon         | Sätt på LAMPAn på enheten. |
| Kommando | turnoff        | Stäng av indikator lampan på enheten. |
| Kommando | rundiagnostics | Det här kommandot startar en diagnostisk körning. |

Om du vill anpassa hur egenskapen **enhets tillstånd** visas i IoT Central programmet väljer du **Anpassa** i enhets mal len. Expandera **enhets tillstånds** posten, ange _online_ som det **sanna namnet** och _offline_ som det **falska namnet**. Spara sedan ändringarna:

![Anpassa enhets mal len](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Skapa vyer

Med vyer kan du interagera med enheter som är anslutna till ditt IoT Central-program. Du kan till exempel ha vyer som visar telemetri, vyer som visar egenskaper och vyer som låter dig redigera skrivbara och moln egenskaper. Vyer ingår i en enhets mall.

Om du vill lägga till några standardvyer i din **miljö sensors** enhets mall, navigerar du till din enhets mall, väljer **vyer**och väljer sedan panelen **generera standardvyer** . Se till att **översikten** och **om** är **på**och välj sedan **skapa standard instrument panels visningar**. Nu har du två standardvyer som definierats i mallen.

**Miljö sensor** gränssnittet innehåller två skrivbara egenskaper – **kundens namn** och **ljus styrke nivå**. Om du vill skapa en vy kan du använda för att redigera dessa egenskaper:

1. Välj **vyer** och välj sedan panelen **Redigera enhet och moln data** .

1. Ange _Egenskaper_ som formulär namn.

1. Välj Egenskaper för **ljus styrke nivå** och **kundnamn** . Välj sedan **Lägg till avsnitt**.

1. Spara ändringarna.

![Lägga till en vy för att aktivera redigering av egenskaper](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Publicera mallen

Innan du lägger till en enhet till ditt IoT Central program som använder **miljösensorns** enhets mall måste du publicera mallen.

I enhets mal len väljer du **publicera**. Välj **publicera**i panelen som visar de ändringar som ska publiceras.

Kontrol lera att mallen är redo att användas genom att gå till sidan **enheter** i IoT Central programmet. Avsnittet **enheter** visar en lista över de publicerade enheterna i programmet:

![Publicerade mallar på sidan enheter](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I ditt Azure IoT Central-program lägger du till en riktig enhet i enhets mal len som du skapade i föregående avsnitt:

1. På sidan **enheter** väljer du mallen **miljö sensor** enhet.

1. Välj **+ ny**.

1. Se till att den **simulerade** är **avstängd**. Välj sedan **Skapa**.

Klicka på enhetens namn och välj sedan **Anslut**. Anteckna anslutnings informationen för enheten på sidan för **enhets anslutningens** ID- **omfång**, **enhets-ID**och **primär nyckel**. Du behöver dessa värden när du skapar din enhets kod:

![Information om enhets anslutning](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Följande steg visar hur du skapar ett Node. js-klientprogram som implementerar den riktiga enheten som du har lagt till i programmet. Detta Node. js-program simulerar beteendet hos en riktig enhet.

1. I din kommando rads miljö navigerar du till den `environmental-sensor` mapp som du skapade tidigare.

1. Initiera Node. js-projektet och installera de nödvändiga beroendena genom att köra följande kommandon – acceptera alla standard alternativ när du kör `npm init`:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Skapa en fil med namnet **environmentalSensor. js** i mappen `environmental-sensor`.

1. Lägg till följande `require`-instruktioner i början av filen **environmentalSensor. js** :

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

    Uppdatera plats hållarna `{your Scope ID}`, `{your Device ID}`och `{your Primary Key}` med de värden som du antecknade tidigare. I det här exemplet initierar du `targetTemperature` till noll, du kan använda den aktuella läsningen från enheten eller ett värde från enheten med dubbla.

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

1. Om du vill skicka enhets egenskaper till ditt Azure IoT Central-program lägger du till följande funktion i filen:

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

1. Lägg till följande kod för att hantera de kommandon som skickas från IoT Central-programmet:

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

## <a name="run-your-nodejs-application"></a>Köra Node. js-programmet

Starta enhets klient programmet genom att köra följande kommando i din kommando rads miljö:

```cmd/sh
node environmentalSensor.js
```

Du kan se att enheten ansluter till ditt Azure IoT Central-program och börjar skicka telemetri:

![Kör klient programmet](media/tutorial-connect-device/run-application.png)

Som operatör i ditt Azure IoT Central-program kan du:

* Visa telemetri som skickats av enheten på sidan **Översikt** :

    ![Visa telemetrin](media/tutorial-connect-device/view-telemetry.png)

* Uppdatera skrivbara egenskaps värden på sidan **Egenskaper** :

    ![Uppdatera egenskaper](media/tutorial-connect-device/update-properties.png)

    ![Uppdatera egenskaper enhet](media/tutorial-connect-device/update-properties-device.png)

* Anropa kommandona från **kommando** sidan:

    ![Anropa Blink-kommando](media/tutorial-connect-device/call-command.png)

    ![Anropa blinkande kommando enhet](media/tutorial-connect-device/call-command-device.png)

* Visa enhets egenskaperna på sidan **om** :

    ![Visa egenskaper](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om enhets kapacitets modeller och hur du skapar egna enhetsspecifika mallar, Fortsätt till instruktions guiden:

> [!div class="nextstepaction"]
> [Definiera en ny IoT-enhets typ](./howto-set-up-template.md)
