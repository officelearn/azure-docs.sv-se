---
title: Börja med Azure IoT Hub-modulidentitet & modultvilling (Node.js)
description: Lär dig hur du skapar modulidentitet och uppdateringsmodultvilling med IoT SDK:er för Node.js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.custom: amqp
ms.openlocfilehash: 8e1599b1bd5db5e410e8bbd76fffbe0beb5f066e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732304"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>Komma igång med IoT Hub-modulidentitet och modultvilling (Node.js)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Medan Azure IoT Hub-enhetsidentitet och enhetstvilling gör det möjligt för backend-programmet att konfigurera en enhet och ger synlighet på enhetens villkor, tillhandahåller en modulidentitet och modultvilling dessa funktioner för enskilda komponenter på en enhet. På kompatibla enheter med flera komponenter som operativsystembaserade enheter eller enheter med inbyggd programvara finns isolerad konfiguration och villkor för varje komponent.

I slutet av den här självstudien har du två Node.js-appar:

* **CreateIdentities**, som skapar en enhetsidentitet, en modulidentitet och en associerad säkerhetsnyckel för att ansluta enheten och modulklienterna.

* **UpdateModuleTwinReportedProperties**, som skickar uppdaterade rapporterade egenskaper för modultvillingen till din IoT Hub.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda programmen så att de kan köras på enheter och på lösningens backend-server.

## <a name="prerequisites"></a>Krav

* Node.js version 10.0.x eller senare. [Förbered utvecklingsmiljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här självstudien på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/pricing/free-trial/) bara några minuter.)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Skapa en enhetsidentitet och en modulidentitet i IoT Hub

I det här avsnittet skapar du en Node.js-app som skapar en enhetsidentitet och en modulidentitet i identitetsregistret i IoT-hubben. Enheter och moduler kan inte ansluta till IoT Hub utan en post i identitetsregistret. Mer information finns i avsnittet "Identitetsregister" i [utvecklarhandboken för IoT Hub](iot-hub-devguide-identity-registry.md). När du kör den här konsolappen, genereras ett unikt ID och en unik nyckel för både enheten och modulen. Enheten och modulen använder dessa värden för att identifiera sig vid överföring av enhet-till-moln-meddelanden till IoT Hub. ID:n är skiftlägeskänsliga.

1. Skapa en katalog som innehåller koden.

2. Inuti katalogen körs först **npm init -y** för att skapa ett tomt package.json med standardvärden. Det här är projektfilen för koden.

3. Kör **npm installera -S\@azure-iothub moduler-förhandsvisning** för att installera tjänsten SDK i **node_modules** underkatalog.

    > [!NOTE]
    > Underkatalognamnet node_modules använder ordmodulen som betyder "ett nodbibliotek". Termen här har ingenting att göra med IoT Hub moduler.

4. Skapa följande .js-fil i katalogen. Kalla det **add.js**. Kopiera och klistra in hubbanslutningssträngen och hubbens namn.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Denna app skapar en enhetsidentitet med ID **myFirstDevice** och en modul identitet med ID **myFirstModule** under enheten **myFirstDevice**. (Om det finns ett modul-ID redan i identitetsregistret hämtar koden helt enkelt den befintliga modulinformationen.) Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade modulappen för att ansluta till din IoT Hub.

Kör detta med nod add.js. Det ger dig en anslutningssträng för din enhetsidentitet och en annan för din modulidentitet.

> [!NOTE]
> IoT Hub-identitetsregistret lagrar enhets- och modulidentiteter endast för att skydda åtkomsten till IoT Hub. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Det finns ingen aktiverad/inaktiverad flagga för modulidentiteter. Mer information finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Uppdatera modultvillingen med Node.js-enheten SDK

I det här avsnittet skapar du en Node.js-app på din simulerade enhet som uppdaterar modultvillingens rapporterade egenskaper.

1. **Hämta din modulanslutningssträng** – Logga in på [Azure-portalen](https://portal.azure.com/). Gå till din IoT Hub och klicka på IoT-enheter. Hitta myFirstDevice, öppna den och du ser myFirstModule skapades framgångsrikt. Kopiera modulens anslutningssträng. Den behövs i nästa steg.

   ![Information om Azure-portalmodulen](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. I likhet med du gjorde i steget ovan, skapa en katalog för din enhetskod och använda NPM för att initiera den och installera enheten SDK **(npm installera -S azure-iot-device-amqp\@moduler-förhandsvisning**).

   > [!NOTE]
   > Kommandot npm installera kan kännas långsamt. Ha tålamod, det drar ner massor av kod från paketet förvaret.

   > [!NOTE]
   > Om du ser ett fel som säger npm ERR! registerfel parsing json, är detta säkert att ignorera. Om du ser ett fel som säger npm ERR! registerfel parsing json, är detta säkert att ignorera.

3. Skapa en fil som heter twin.js. Kopiera och klistra in modulens identitetssträng.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Kör nu detta med **kommandonoden twin.js**.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   Du kommer då att se:

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>Nästa steg

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

* [Connecting your device](iot-hub-node-node-device-management-get-started.md) (Komma igång med enhetshantering)

* [Komma igång med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)