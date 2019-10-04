---
title: Lägga till en riktig enhet till ett Azure IoT Central-program | Microsoft Docs
description: Lägg till en riktig enhet till Azure IoT Central-programmet i egenskap av operatör.
author: sandeeppujar
ms.author: sandeepu
ms.date: 08/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 96b8090bd0e178be53cb49f42438951645def5d9
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960501"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Självstudier: Lägga till en riktig enhet till Azure IoT Central-programmet

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Den här självstudien visar hur du lägger till och konfigurerar en riktig enhet till Microsoft Azure IoT Central-programmet. 
_* du behöver inte din egen externa enhet för att slutföra den här kursen. Den "riktiga enheten" skapas som ett kod program som körs i kommando rads miljön._ 

Den här självstudien består av två delar:

* Först lär du dig att som operatör lägga till och konfigurera en riktig enhet i Azure IoT Central-programmet. I slutet av den här delen hämtar du en anslutningssträng som används i den andra delen.
* Sedan lär du dig, som enhetsutvecklare, om koden i din riktiga enhet. Du lägger till anslutningssträngen från den första delen i exempelkoden.



I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en ny, riktig enhet
> * Konfigurera den riktiga enheten
> * Hämta anslutningssträngen för den riktiga enheten från programmet
> * Förstå hur klientkod mappas till programmet
> * Konfigurera klientkod för den riktiga enheten

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör byggaren skapa Azure IoT Central-programmet genom att slutföra åtminstone den första självstudien för byggare: [Definiera en ny enhetstyp](tutorial-define-device-type.md) (obligatoriskt)

Installera [Node. js](https://nodejs.org/) version 8.0.0 eller senare på utvecklings datorn. Du kan köra `node --version` på kommando raden för att kontrol lera din version. Node.js är tillgängligt för många olika operativsystem.

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

För att lägga till en riktig enhet i ditt program använder du enhetsmallen **Ansluten luftkonditioneringsenhet** som du skapade i självstudien [Definiera en ny enhetstyp](tutorial-define-device-type.md).

1. Om du vill lägga till en ny enhet som en operatör väljer du **enheter** i den vänstra navigerings menyn:

   ![Sidan Device Explorer visar ansluten luftkonditioneringsenhet](media/tutorial-add-device/explorer.png)

   I **Device Explorer** visas enhets mal len för den **anslutna luft konditioneringen** och en simulerad enhet. När du skapar en enhets mall skapar IoT Central automatiskt en simulerad enhet.

2. Observera att den **anslutna Luft Konditionerings** enhets mal len är den som är vald i **Device Explorer**. Om du vill börja ansluta en riktig luftkonditionerings enhet som använder den här mallen väljer du **+** , sedan **verklig**:

   ![Börja lägga till en ny, riktig, ansluten luftkonditioneringsenhet](media/tutorial-add-device/newreal.png)

3. Ange ditt egna **enhets-ID** (bör vara i gemener) eller Använd det föreslagna värdet. Du kan också ange ett **enhets namn** för den nya enheten och välja **skapa**.

   ![Byt namn på enheten](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Konfigurera en riktig enhet

Den riktiga enheten skapas utifrån enhetsmallen **Ansluten luftkonditioneringsenhet**. Du kan använda **Inställningar** för att konfigurera enheten och ange egenskapsvärden för att registrera information om enheten.

1. På sidan **Inställningar** är statusen för inställningen **Ange temperatur** **ingen uppdatering**. Den behåller det här tillståndet tills den riktiga enheten ansluter till programmet och bekräftar att den har agerat på inställningen.

    ![Inställningar visar synkronisering](media/tutorial-add-device/settingssyncing.png)

2. På sidan **Egenskaper** för din nya, riktiga enhet är både plats för tjänsten och senaste service datum redigerbara egenskaper. Fälten serienummer och version av inbyggd programvara är tomma tills enheten ansluts till programmet. Dessa skrivskyddade värden skickas från enheten och kan inte redige ras.

    ![Enhetsegenskaper för den riktiga enheten](media/tutorial-add-device/setproperties1.png)

3. Du kan visa sidorna **Mått**, **Regler** och **Instrumentpanelen** för den riktiga enheten.

## <a name="prepare-the-client-code"></a>Förbereda klientkoden

Exempel koden i den här artikeln är skriven i [Node. js](https://nodejs.org/) och visar tillräckligt med kod för en enhet för att:

* Anslut till Azure IoT Central-programmet.
* Skicka temperaturtelemetri som en ansluten luftkonditioneringsenhet.
* Skicka enhetsegenskaper till Azure IoT Central-programmet.
* Svara på en operatör som använder inställningen **Ange temperatur**.
* Hantera Echo-kommandot från Azure IoT Central-programmet.

Artiklarna som anges i avsnittet [Nästa steg](#next-steps) innehåller fler fullständiga exempel och visar andra programmeringsspråk. Mer information om hur enheter ansluter till Azure IoT Central finns i artikeln om [enhetsanslutning](concepts-connectivity.md).

Följande steg visar hur du förbereder [Node.js](https://nodejs.org/)-exemplet:

### <a name="get-the-device-connection-information"></a>Hämta information om enhets anslutning

1. Anslutningssträngen för en enhetsinstans i ditt program genereras från enhetsinformation som tillhandahålls av IoT Central.

   På enhetsskärmen för den riktiga, anslutna luftkonditioneringsenheten väljer du **Anslut**.

   ![Enhetssidan visar informationslänk för att visa anslutning](media/tutorial-add-device/connectionlink.png)

1. På sidan **enhets anslutning** noterar du **omfångs-ID**, **enhets-ID** och **primär nyckel** värden. Du kommer att använda dessa värden senare i den här självstudien.

   ![Information om anslutningen](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Förbereda Node. js-projektet

1. Skapa en mapp med namnet `connectedairconditioner` på din utvecklings dator.

1. I kommandoradsmiljön går du till mappen `connectedairconditioner` som du skapade.

1. Kör följande kommando och acceptera alla standardvärden för att initiera Node.js-projektet:

    ```cmd/sh
    npm init
      ```

1. Kör följande kommando för att installera de nödvändiga paketen:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Använd en textredigerare för att skapa en fil med namnet **ConnectedAirConditioner.js** i mappen `connectedairconditioner`.

1. Lägg till följande `require`-instruktioner i början av filen **ConnectedAirConditioner.js**:

    ```javascript
    'use strict';

    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Lägg till följande variabel deklarationer i filen. Ersätt plats hållarna `{your Scope ID}`, `{your Device ID}` och `{your Primary Key}` med den enhets anslutnings information som du antecknade tidigare:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    ```

1. Spara de ändringar som du gjort hittills, men låt filen vara öppen.

## <a name="review-client-code"></a>Granska klient koden

I föregående avsnitt skapade du ett Skeleton Node. js-projekt för ett enhets program som ansluter till ditt Azure IoT Central-program. Nästa steg är att lägga till kod till:

* Anslut till Azure IoT Central-programmet.
* Skicka telemetri till Azure IoT Central-programmet.
* Skicka enhetsegenskaper till Azure IoT Central-programmet.
* Ta emot inställningar från Azure IoT Central-programmet.
* Hantera Echo-kommandot från Azure IoT Central-programmet.

1. För att skicka temperaturtelemetri till Azure IoT Central-programmet lägger du till följande kod i filen **ConnectedAirConditioner.js**:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Namnet på det fält i JSON som du skickar måste matcha namnet på det fält som du angav för temperaturtelemetri i enhetsmallen. I det här exemplet är fältnamnet **temperatur**.

1. För att skicka enhetsegenskaperna såsom **firmwareVersion** och **serialNumber** lägger du till följande definition:

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) =>
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. För att definiera inställningar som enheten stöder, till exempel **angeTemperatur**, lägger du till följande definition:

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

1. Hantera inställningar som skickas från Azure IoT Central genom att lägga till följande funktion som hittar och kör rätt enhetskod:

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

    Den här funktionen:

    * Söker efter utskick av en önskad egenskap från Azure IoT Central.
    * Hittar rätt funktion att anropa för att hantera inställningsändringen.
    * Skickar tillbaka en bekräftelse till ditt Azure IoT Central-program.

1. För att svara på ett kommando såsom **echo** från ditt Azure IoT Central-program lägger du till följande definition:

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
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
        hubClient.onDeviceMethod('echo', onCommandEcho);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };
    ```

1. Registrera och Anslut enheten till ditt IoT Central-program:

    ```javascript
    // Start the device (connect it to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.assignedHub);
        console.log('deviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

1. Spara ändringarna som du har gjort.

## <a name="run-the-client-code"></a>Kör klient koden

Nu kan du köra klient koden och se hur den interagerar med ditt IoT Central-program:

1. Kör exemplet genom att ange följande kommando i kommandoradsmiljön:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Kontrollera att du befinner dig i mappen `connectedairconditioner` när du kör det här kommandot.

1. Programmet skriver utdata till konsolen:

   ![Utdata för klientprogram](media/tutorial-add-device/output.png)

1. Efter ca 30 sekunder visas telemetrin på sidan **Mått** för enheten:

   ![Riktig telemetri](media/tutorial-add-device/realtelemetry.png)

1. På sidan **Inställningar** ser du att inställningen nu är synkroniserad. När enheten först anslöt fick den inställningsvärdet och bekräftade ändringen:

   ![Inställningen har synkroniserats](media/tutorial-add-device/settingsynced.png)

1. På sidan **Inställningar** anger du enhetens temperatur till **95** och väljer **Uppdatera enhet**. Exempelprogrammet tar emot och bearbetar den här ändringen:

   ![Ta emot och bearbeta inställning](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Det finns två meddelanden om inställningsuppdatering. Ett när statusen `pending` skickas och ett när statusen `completed` status skickas.

1. På sidan **Mått** ser du att enheten skickar högre temperaturvärden:

    ![Temperaturtelemetri är nu högre](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="nextstepaction"]
> * Lägga till en ny, riktig enhet
> * Konfigurera den nya enheten
> * Hämta anslutningssträngen för den riktiga enheten från programmet
> * Förstå hur klientkod mappas till programmet
> * Konfigurera klientkod för den riktiga enheten

Nu när du har anslutit en riktig enhet till ditt Azure IoT Central-program, finns det några förslag på nästa steg.

Som operatör lär du dig att:

* [Hantera dina enheter](howto-manage-devices.md)
* [Använda enhetsuppsättningar](howto-use-device-sets.md)
* [Skapa anpassade analyser](howto-use-device-sets.md)

Som enhetsutvecklare lär du dig att:

* [Förbereda och ansluta en DevKit-enhet (C)](howto-connect-devkit.md)
* [Förbereda och ansluta en Raspberry Pi (python)](howto-connect-raspberry-pi-python.md)
* [Förbereda och ansluta en Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Förbereda och ansluta en Windows 10 IoT Core-enhetC#()](howto-connect-windowsiotcore.md)
* [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)
