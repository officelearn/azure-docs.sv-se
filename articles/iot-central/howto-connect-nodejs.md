---
title: Anslut ett allmänt Node. js-klientprogram till Azure IoT Central | Microsoft Docs
description: Som enhets utvecklare, hur du ansluter en generisk Node. js-enhet till ditt Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 06/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3b73344a233182fe8366795cfa111b706c6d06ac
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876227"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Ansluta ett allmänt klient program till ditt Azure IoT Central-program (Node. js)

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver hur, som en enhets utvecklare, för att ansluta ett allmänt Node. js-program som representerar en riktig enhet till ditt Microsoft Azure IoT Central-program.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

1. Ett Azure IoT Central-program. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
1. En utvecklings dator med [Node. js](https://nodejs.org/) version 4.0.0 eller senare installerad. Du kan köra `node --version` på kommando raden för att kontrol lera din version. Node.js är tillgängligt för många olika operativsystem.

## <a name="create-a-device-template"></a>Skapa en enhets mall

I ditt Azure IoT Central-program behöver du en enhets mall med följande mått, enhets egenskaper, inställningar och kommandon:

### <a name="telemetry-measurements"></a>Mått för telemetri

Lägg till följande telemetri på sidan **mått** :

| Visningsnamn | Fältnamn  | Enheter | Min | Max | Decimaler |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatur  | temperatur | f     | 60  | 110 | 0              |
| Fuktighet     | luftfuktighet    | %     | 0   | 100 | 0              |
| Tryck     | tryck    | 101,3   | 80  | 110 | 0              |

> [!NOTE]
> Data typen för måttet telemetri är ett flytt ALS nummer.

Ange fält namn exakt som de visas i tabellen i enhets mal len. Om fält namnen inte matchar egenskaps namnen i motsvarande enhets kod, kan inte telemetri visas i programmet.

### <a name="state-measurements"></a>Tillstånds mått

Lägg till följande tillstånd på sidan **mått** :

| Visningsnamn | Fältnamn  | Värde 1 | Visningsnamn | Värde 2 | Visningsnamn |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Fläktläge     | fläktläge     | 1       | Körs      | 0       | Stoppad      |

> [!NOTE]
> Data typen för tillstånds måttet är sträng.

Ange fält namn exakt som de visas i tabellen i enhets mal len. Om fält namnen inte matchar egenskaps namnen i motsvarande enhets kod, kan inte tillstånd visas i programmet.

### <a name="event-measurements"></a>Händelse mätningar

Lägg till följande händelse på sidan **mått** :

| Visningsnamn | Fältnamn  | severity |
| ------------ | ----------- | -------- |
| Överhettning  | overheat    | Fel    |

> [!NOTE]
> Data typen för händelse mätningen är sträng.

### <a name="location-measurements"></a>Plats mått

Lägg till följande plats mått på sidan **mått** :

| Visningsnamn | Fältnamn  |
| ------------ | ----------- |
| Location     | location    |

Data typen plats mätning består av två flytt ALS nummer för longitud och latitud, och ett valfritt flytt ALS nummer för höjd.

Ange fält namn exakt som de visas i tabellen i enhets mal len. Om fält namnen inte matchar egenskaps namnen i motsvarande enhets kod, kan platsen inte visas i programmet.

### <a name="device-properties"></a>Enhetsegenskaper

Lägg till följande enhets egenskaper på sidan **Egenskaper** :

| Visningsnamn        | Fältnamn        | Datatyp |
| ------------------- | ----------------- | --------- |
| Serienummer       | serieNummer      | text      |
| Enhets tillverkare | tillverkare      | text      |

Ange fält namnen exakt som de visas i tabellen i enhets mal len. Om fält namnen inte matchar egenskaps namnen i motsvarande enhets kod, kan inte egenskaperna visas i programmet.

### <a name="settings"></a>Inställningar

Lägg till följande **nummer** inställningar på sidan **Inställningar** :

| Visningsnamn    | Fältnamn     | Enheter | Decimaler | Min | Max  | Inledande |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Fläkt hastighet       | fanSpeed       | varvtal   | 0        | 0   | 3000 | 0       |
| Ange temperatur | angeTemperatur | f     | 0        | 20  | 200  | 80      |

Ange fält namnet exakt som det visas i tabellen i enhets mal len. Om fält namnen inte matchar egenskaps namnen i motsvarande enhets kod kan enheten inte ta emot inställning svärdet.

### <a name="commands"></a>Kommandon

Lägg till följande kommando på sidan **kommandon** :

| Visningsnamn    | Fältnamn     | Standardvärde för tidsgräns | Datatyp |
| --------------- | -------------- | --------------- | --------- |
| Tids       | tids      | 30              | nummer    |

Lägg till följande ingångs fält i nedräknings kommandot:

| Visningsnamn    | Fältnamn     | Datatyp | Value |
| --------------- | -------------- | --------- | ----- |
| Räkna från      | countFrom      | nummer    | 10    |

Ange fält namn exakt som de visas i tabellerna i enhets mal len. Om fält namnen inte matchar egenskaps namnen i motsvarande enhets kod, kan enheten inte behandla kommandot.

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I ditt Azure IoT Central-program lägger du till en riktig enhet i enhets mal len som du skapade i föregående avsnitt.

Följ sedan anvisningarna i självstudien Lägg till en enhet för att [skapa en anslutnings sträng för den riktiga enheten](tutorial-add-device.md#generate-connection-string). Du använder den här anslutnings strängen i följande avsnitt:

### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Följande steg visar hur du skapar ett klient program som implementerar den riktiga enheten som du har lagt till i programmet. Här är det Node. js-programmet som representerar den riktiga enheten. 

1. Skapa en mapp med namnet `connected-air-conditioner-adv` på datorn. Navigera till mappen i din kommando rads miljö.

1. Starta Node. js-projektet genom att köra följande kommandon:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Skapa en fil med namnet **connectedAirConditionerAdv. js** i `connected-air-conditioner-adv` mappen.

1. Lägg till följande `require` -instruktioner i början av filen **connectedAirConditionerAdv. js** :

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Lägg till följande variabeldeklarationer i filen:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var locLong = -122.1215;
    var locLat = 47.6740;
    var client = clientFromConnectionString(connectionString);
    ```

    Uppdatera plats hållaren `{your device connection string}` med [enhets anslutnings strängen](tutorial-add-device.md#generate-connection-string). I det här exemplet initierar `targetTemperature` du till noll, du kan använda den aktuella läsningen från enheten eller ett värde från enheten.

1. Om du vill skicka mått för telemetri, tillstånd, händelser och platser till ditt Azure IoT Central-program lägger du till följande funktion i filen:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
        });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
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

1. Lägg till följande definition för att definiera inställningarna som enheten svarar på:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Lägg till följande i filen om du vill hantera uppdaterade inställningar från ditt Azure IoT Central-program:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
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
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Lägg till följande kod för att hantera ett nedräknings kommando som skickas från IoT Central-programmet:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }

            doCountdown();
          });
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

        // Create handler for countdown command
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Köra Node. js-programmet

Kör följande kommando i kommando rads miljön:

```cmd/sh
node connectedAirConditionerAdv.js
```

Som operatör i ditt Azure IoT Central-program för din riktiga enhet kan du:

* Visa telemetri på sidan **mått** :

    ![Visa telemetrin](media/howto-connect-nodejs/viewtelemetry.png)

* Visa platsen på sidan **mått** :

    ![Visa plats mått](media/howto-connect-nodejs/viewlocation.png)

* Visa de enhets egenskaps värden som skickas från enheten på sidan **Egenskaper** . Enhetens egenskaps paneler uppdateras när enheten ansluter:

    ![Visa enhets egenskaper](media/howto-connect-nodejs/viewproperties.png)

* Ange Fläktens hastighet och mål temperatur på sidan **Inställningar** :

    ![Ange fläkt hastighet](media/howto-connect-nodejs/setfanspeed.png)

* Anropa kommandot nedräkning från kommando sidan:

    ![Kommandot anropa nedräkning](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en allmän Node. js-klient till ditt Azure IoT Central-program, är det föreslagna nästa steg att lära dig hur du [konfigurerar en anpassad enhets mall](howto-set-up-template.md) för din egen IoT-enhet.
