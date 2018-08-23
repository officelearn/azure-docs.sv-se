---
title: Kom igång med Azure IoT Hub identitets- och modulen modultvilling (Node.js) | Microsoft Docs
description: Lär dig mer om att skapa modulen identitet och uppdatera modultvilling med IoT SDK för Node.js.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: fa77e117b8045be4ef0566e388c4e8df08c95fe2
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42056750"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>Kom igång med IoT Hub identitets- och modulen modultvilling med hjälp av Node.js-serverdel och Node.js-enhets-

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Medan Azure IoT Hub-enhetsidentiteten och enhetstvillingen gör att serverdelsprogrammet kan konfigurera en enhet och ger synlighet för enhetstillståndet tillhandahåller en modulidentitet och en modultvilling dessa funktioner för enskilda komponenter i en enhet. På kompatibla enheter med flera komponenter som operativsystembaserade enheter eller enheter med inbyggd programvara finns isolerad konfiguration och villkor för varje komponent.

I slutet av den här självstudien har du två Node.js-appar:

* **CreateIdentities**, som skapar en enhetsidentitet, en modulidentitet och en associerad säkerhetsnyckel för att ansluta enheten och modulklienterna.
* **UpdateModuleTwinReportedProperties**, som skickar uppdaterade rapporterade egenskaper för modultvillingen till din IoT Hub.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda programmen så att de kan köras på enheter och på lösningens backend-server.

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)
* En IoT-hubb.
* Installera senast [Node.js SDK](https://github.com/Azure/azure-iot-sdk-node).


Nu har du skapat din IoT Hub och du har värdnamnet och IoT Hub-anslutningssträngen som du behöver för att slutföra resten av den här kursen.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Skapa en enhetsidentitet och en modul-identitet i IoT Hub

I det här avsnittet skapar du en Node.js-app som skapar en enhetsidentitet och en modul-identitet i identitetsregistret i IoT hub. Enheter och moduler kan inte ansluta till IoT Hub utan en post i identitetsregistret. Mer information finns i avsnittet om identitetsregistret i [utvecklarhandboken för IoT Hub][lnk-devguide-identity]. När du kör den här konsolappen, genereras ett unikt ID och en unik nyckel för både enheten och modulen. Enheten och modulen använder dessa värden för att identifiera sig vid överföring av enhet-till-moln-meddelanden till IoT Hub. ID:n är skiftlägeskänsliga.

1.  Skapa en katalog för att lagra din kod.
2. Kör först i katalogen **npm init -y** att skapa en tom package.json med standardvärden. Det här är projektfilen för din kod.
3. Kör **npm-installationsprogrammet -S azure-iothub@modules-preview**  att installera service SDK inuti den **node_modules** underkatalog. 

    > [!NOTE] 
    > Underkatalog namnet node_modules använder word-modulen för att beskriva ”ett nod-bibliotek”. Den här termen har inget samband med IoT Hub-moduler.

4. Skapa följande .js-fil i katalogen. Anropa den **add.js**. Kopiera och klistra in dina hub-anslutningssträngen och namnet på händelsehubben.

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

Den här appen skapar en enhetsidentitet med ID **myFirstDevice** och en modul identitet med ID **myFirstModule** under enhet **myFirstDevice**. (Om modul-ID:t redan finns i identitetsregistret, hämtar koden bara den befintliga modulinformationen.) Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade modulappen för att ansluta till din IoT Hub.

5. Kör det här med hjälp av noden add.js. Det ger dig en anslutningssträng för din enhetsidentitet och en annan för din modul-identitet.

    > [!NOTE]
    > IoT Hub-identitetsregistret lagrar enhets- och modulidentiteter endast för att skydda åtkomsten till IoT Hub. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Det finns ingen aktiverad/inaktiverad flagga för modulidentiteter. Mer information finns i [utvecklarhandboken för IoT Hub][lnk-devguide-identity].

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Uppdatera modultvillingen med hjälp av Node.js-enhets-SDK

I det här avsnittet skapar du ett Node.js-appen på din simulerade enhet som uppdaterar modultvillingen rapporterade egenskaper.

1. **Hämta modulens anslutningssträng** – nu om du loggar in på [Azure-portalen][lnk-portal]. Gå till din IoT Hub och klicka på IoT-enheter. Leta rätt på myFirstDevice och öppna den, så ser du att myFirstModule har skapats. Kopiera modulens anslutningssträng. Den behövs i nästa steg.

    ![Information om Azure-portalmodulen][15]

2. Ett liknande sätt som du gjorde i ovanstående steg, skapa en katalog för enhetskoden och Använd NPM för att initiera den och installera enhets-SDK (**npm-installationsprogrammet -S azure-iot-device-amqp@modules-preview** ).

    > [!NOTE]
    > Npm install-kommandot kan känna långsam. Ha tålamod, det dra nedåt massor av kod från paketdatabasen.

    > [!NOTE] 
    > Om du ser ett meddelande om att npm fel! registret fel parsa json, det är säkert att ignorera. Om du ser ett meddelande om att npm fel! registret fel parsa json, det är säkert att ignorera.

3. Skapa en fil med namnet twin.js. Kopiera och klistra in din modul-ID-sträng.

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

2. Nu kan köra det här kommandot **noden twin.js**.

    ```
    F:\temp\module_twin>node twin.js
    client opened
    twin contents:
    { reported: { update: [Function: update], '$version': 1 },
      desired: { '$version': 1 } }
    new desired properties received:
    {"$version":1}
    twin state reported
    ```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Connecting your device][lnk-device-management] (Komma igång med enhetshantering)
* [Komma igång med IoT Edge][lnk-iot-edge]


<!-- Images. -->
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/