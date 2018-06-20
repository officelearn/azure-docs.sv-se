---
title: Lägga till en riktig enhet till ett Azure IoT Central-program | Microsoft Docs
description: Lägg till en riktig enhet till Azure IoT Central-programmet i egenskap av operatör.
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: dd68b65825c9c22453e0191d42a0fcce3b65ca64
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236094"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Självstudie: Lägga till en riktig enhet till Azure IoT Central-programmet

Den här självstudien visar hur du lägger till och konfigurerar en riktig enhet till Microsoft Azure IoT Central-programmet.

Den här självstudien består av två delar:

1. Först lär du dig att som operatör lägga till och konfigurera en riktig enhet i Azure IoT Central-programmet. I slutet av den här delen hämtar du en anslutningssträng som används i den andra delen.
2. Sedan lär du dig, som enhetsutvecklare, om koden i din riktiga enhet. Du lägger till anslutningssträngen från den första delen i exempelkoden.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Lägga till en ny, riktig enhet
> * Konfigurera den nya enheten
> * Hämta anslutningssträngen för den riktiga enheten från programmet
> * Förstå hur klientkod mappas till programmet
> * Konfigurera klientkod för den riktiga enheten

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar bör byggaren skapa Azure IoT Central-programmet genom att slutföra åtminstone den första självstudien för byggare:

* [Definiera en ny enhetstyp](tutorial-define-device-type.md) (obligatoriskt)
* [Konfigurera regler och åtgärder för din enhet](tutorial-configure-rules.md) (valfritt)
* [Anpassa operatörsvyer](tutorial-customize-operator.md) (valfritt)

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

För att lägga till en riktig enhet i ditt program använder du enhetsmallen **Ansluten luftkonditioneringsenhet** som du skapade i självstudien [Definiera en ny enhetstyp](tutorial-define-device-type.md).

1. Om du vill lägga till en ny enhet som operatör väljer du **Device Explorer** i den vänstra navigeringsmenyn:

   ![Sidan Device Explorer visar ansluten luftkonditioneringsenhet](media/tutorial-add-device/explorer.png)

   **Device Explorer** visar enhetsmallen **Ansluten luftkonditioneringsenhet** som skapades automatiskt när byggaren skapade enhetsmallen.

2. För att börja ansluta en riktig, ansluten luftkonditioneringsenhet väljer du **Ny** och sedan **Riktig**:

   ![Börja lägga till en ny, riktig, ansluten luftkonditioneringsenhet](media/tutorial-add-device/newreal.png)

3. Alternativt kan du byta namn på den nya enheten genom att välja enhetsnamnet och redigera värdet:

   ![Byt namn på enheten](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Konfigurera en riktig enhet

Den riktiga enheten skapas utifrån enhetsmallen **Ansluten luftkonditioneringsenhet**. Som byggare kan du använda **Inställningar** för att konfigurera enheten och ange egenskapsvärden för att registrera information om enheten.

1. På sidan **Inställningar** är statusen för inställningen **Ange temperatur** **ingen uppdatering**. Den behåller det här tillståndet tills den riktiga enheten ansluter och bekräftar att den har agerat på inställningen:

    ![Inställningar visar synkronisering](media/tutorial-add-device/settingssyncing.png)

2. På sidan **Egenskaper** för din nya, riktiga, anslutna luftkonditioneringsenhet anger du **Serienummer** till **rcac0010** och **Version av inbyggd programvara** till 9,75. Välj sedan **Spara**:

    ![Ange egenskaper för den riktiga enheten](media/tutorial-add-device/setproperties.png)

3. Som byggare kan du visa sidorna **Mått**, **Regler** och **Instrumentpanelen** för den riktiga enheten.

## <a name="get-connection-string-for-real-device-from-application"></a>Hämta anslutningssträngen för den riktiga enheten från programmet

En enhetsutvecklare måste bädda in *anslutningssträngen* för den riktiga enheten i den kod som körs på enheten. Anslutningssträngen gör att enheten kan ansluta säkert till Azure IoT Central-programmet. Varje enhetsinstans har en unik anslutningssträng. Följande steg visar hur du hittar anslutningssträngen för en enhetsinstans i programmet:

1. På skärmen **Enhet** för den riktiga, anslutna luftkonditioneringsenheten väljer du **Anslut den här enheten**:

    ![Enhetssidan visar informationslänk för att visa anslutning](media/tutorial-add-device/connectionlink.png)

2. På sidan **Anslut** kopierar du den **primära anslutningssträngen** och sparar den. Du kan använda det här värdet i den andra halvan av den här självstudien. En enhetsutvecklare använder det här värdet i klientprogrammet som körs på enheten:

    ![Strängvärden för anslutning](media/tutorial-add-device/connectionstring.png)

## <a name="prepare-the-client-code"></a>Förbereda klientkoden

Exempelkoden i den här artikeln är skriven i [Node.js](https://nodejs.org/) och visar precis nog med kod för att:

* Anslut som en enhet till Azure IoT Central-programmet.
* Skicka temperaturtelemetri som en ansluten luftkonditioneringsenhet.
* Svara på en operatör som använder inställningen **Ange temperatur**.

I artiklarna om hur du går tillväga som avsnittet [Nästa steg](#next-steps) hänvisar till finns mer fullständiga exempel och information om hur du använder andra programmeringsspråk. Mer information om hur enheter ansluter till Azure IoT Central finns i artikeln om [enhetsanslutning](concepts-connectivity.md).

Följande steg visar hur du förbereder [Node.js](https://nodejs.org/)-exemplet:

1. Installera [Node.js](https://nodejs.org/)-versionen 4.0.x eller senare på datorn. Node.js är tillgängligt för många olika operativsystem.

2. Skapa en mapp med namnet `connectedairconditioner` på datorn.

3. I kommandoradsmiljön går du till mappen `connectedairconditioner` som du skapade.

4. Kör följande kommando och acceptera alla standardvärden för att initiera Node.js-projektet:

   ```cmd/sh
   npm init
   ```

5. Kör följande kommando för att installera de nödvändiga paketen:

   ```cmd/sh
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

6. Använd en textredigerare för att skapa en fil med namnet **ConnectedAirConditioner.js** i mappen `connectedairconditioner`.

7. Lägg till följande `require`-instruktioner i början av filen **ConnectedAirConditioner.js**:

   ```javascript
   'use strict';

   var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
   var Message = require('azure-iot-device').Message;
   var ConnectionString = require('azure-iot-device').ConnectionString;
   ```

8. Lägg till följande variabeldeklarationer i filen:

   ```javascript
   var connectionString = '{your device connection string}';
   var targetTemperature = 0;
   var client = clientFromConnectionString(connectionString);
   ```

   > [!NOTE]
   > Du uppdaterar platshållaren `{your device connection string}` i ett senare steg.

9. Spara de ändringar som du gjort hittills, men låt filen vara öppen.

## <a name="understand-how-client-code-maps-to-the-application"></a>Förstå hur klientkod mappas till programmet

I det föregående avsnittet skapade du ett Node.js-stommeprojekt för ett program som ansluter till Azure IoT Central-programmet. I det här avsnittet lägger du till koden i:

* Anslut till Azure IoT Central-programmet.
* Skicka telemetri till Azure IoT Central-programmet.
* Ta emot inställningar från Azure IoT Central-programmet.

1. För att skicka temperaturtelemetri till Azure IoT Central-programmet lägger du till följande kod i filen **ConnectedAirConditioner.js**:

   ```javascript
   // Send device telemetry.
   function sendTelemetry() {
     var temperature = targetTemperature + (Math.random() * 15);
     var data = JSON.stringify({ temperature: temperature });
     var message = new Message(data);
     client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
       (err ? `; error: ${err.toString()}` : '') +
       (res ? `; status: ${res.constructor.name}` : '')));
   }
   ```

   Namnet på det fält i JSON som du skickar måste matcha namnet på det fält som du angav för temperaturtelemetri i enhetsmallen. I det här exemplet är fältnamnet **temperatur**.

2. För att definiera inställningar som enheten stöder, till exempel **angeTemperatur**, lägger du till följande definition:

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

3. Hantera inställningar som skickas från Azure IoT Central genom att lägga till följande funktion som hittar och kör rätt enhetskod:

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
    * Skickar tillbaka en bekräftelse till Azure IoT Central-programmet.

4. Lägg till följande kod för att slutföra anslutningen till Azure IoT Central och koppla samman funktionerna i klientkoden:

   ```javascript
   // Handle device connection to Azure IoT Central.
   var connectCallback = (err) => {
     if (err) {
       console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
     } else {
       console.log('Device successfully connected to Azure IoT Central');
        // Send telemetry measurements to Azure IoT Central every 1 second.
       setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
       client.getTwin((err, twin) => {
         if (err) {
           console.log(`Error getting device twin: ${err.toString()}`);
         } else {
           // Apply device settings and handle changes to device settings.
           handleSettings(twin);
         }
       });
     }
   };

   client.open(connectCallback);
   ```

5. Spara de ändringar som du gjort hittills, men låt filen vara öppen.

## <a name="configure-client-code-for-the-real-device"></a>Konfigurera klientkod för den riktiga enheten

<!-- Add the connection string to the sample code, build, and run -->
Om du vill konfigurera din klientkod för att ansluta till Azure IoT Central-programmet behöver du lägga till anslutningssträngen för den riktiga enheten som du angav tidigare i den här självstudien.

1. I filen **ConnectedAirConditioner.js** söker du efter följande kodrad:

   ```javascript
   var connectionString = '{your device connection string}';
   ```

2. Ersätt `{your device connection string}` med den riktiga enhetens anslutningssträng. Du angav anslutningssträngen i slutet av avsnittet om att hämta anslutningssträngen för den riktiga enheten från programmet.

3. Spara ändringarna i filen **ConnectedAirConditioner.js**.

4. Kör exemplet genom att ange följande kommando i kommandoradsmiljön:

   ```cmd/sh
   node ConnectedAirConditioner.js
   ```

   > [!NOTE]
   > Kontrollera att du befinner dig i mappen `connectedairconditioner` när du kör det här kommandot.

5. Programmet skriver utdata till konsolen:

   ![Utdata för klientprogram](media/tutorial-add-device/output.png)

6. Efter ca 30 sekunder visas telemetrin på sidan **Mått** för enheten:

   ![Riktig telemetri](media/tutorial-add-device/realtelemetry.png)

7. På sidan **Inställningar** ser du att inställningen nu är synkroniserad. När enheten först anslöt fick den inställningsvärdet och bekräftade ändringen:

   ![Inställningen har synkroniserats](media/tutorial-add-device/settingsynced.png)

8. På sidan **Inställningar** anger du enhetens temperatur till **95** och väljer **Uppdatera enhet**. Exempelprogrammet tar emot och bearbetar den här ändringen:

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

Nu när du har anslutit en riktig enhet till Azure IoT Central-programmet föreslår vi följande steg:

Som operatör lär du dig att:

* [Hantera dina enheter](howto-manage-devices.md)
* [Använda enhetsuppsättningar](howto-use-device-sets.md)
* [Skapa anpassade analyser](howto-create-analytics.md)

Som enhetsutvecklare lär du dig att:

* [Förbereda och ansluta ett DevKit](howto-connect-devkit.md)
* [Förbereda och ansluta en Raspberry Pi](howto-connect-raspberry-pi-python.md)
* [Ansluta en generisk Node.js-klient till Azure IoT Central-programmet](howto-connect-nodejs.md)