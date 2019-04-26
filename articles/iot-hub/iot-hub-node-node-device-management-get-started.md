---
title: Kom igång med enhetshantering i Azure IoT Hub (Node) | Microsoft Docs
description: Hur du använder IoT Hub-enhetshantering för att initiera en fjärransluten enhet omstart. Du kan använda Azure IoT SDK för Node.js för att implementera en simulerad enhetsapp som innehåller en direkt metod och en service-app som anropar direktmetoden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.openlocfilehash: 15166d3943bc72a2eeff3580cefdd264ecaba61d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346287"
---
# <a name="get-started-with-device-management-node"></a>Kom igång med enhetshantering (Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd den [Azure-portalen](https://portal.azure.com) att skapa en IoT Hub och skapa en enhetsidentitet i IoT hub.

* Skapa en simulerad enhetsapp som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.

* Skapa en Node.js-konsolapp som anropar metoden omstart direkt i den simulerade enhetsappen via din IoT-hubb.

I slutet av den här självstudien har du två Node.js-konsolappar:

* **dmpatterns_getstarted_device.js**, som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare, tar emot en direkt metod för omstart, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.

* **dmpatterns_getstarted_service.js**, som anropar en direkt metod i den simulerade enhetsappen visar svaret och visar det uppdaterade rapporterade egenskaper.

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 4.0.x eller senare. [Förbereda utvecklingsmiljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här självstudiekursen i Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet ska utföra du följande steg:

* Skapa en Node.js-konsolapp som svarar på en direkt metod som anropas via molnet

* Utlösa en simulerad enhet-omstart

* Använd de rapporterade egenskaperna för att aktivera enhetstvillingfrågor att identifiera enheter och när de senast startades om

1. Skapa en tom mapp med namnet **manageddevice**.  I mappen **manageddevice** skapar du en package.json-fil med hjälp av följande kommando i Kommandotolken.  Acceptera alla standardvärden:
      
    ```
    npm init
    ```

2. I Kommandotolken i den **manageddevice** mapp, kör följande kommando för att installera den **azure-iot-device** SDK-paketet och **azure-iot-device-mqtt** paket:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Använd en textredigerare och skapa en **dmpatterns_getstarted_device.js** fil i den **manageddevice** mapp.

4. Lägg till följande ”require”-instruktioner i början av den **dmpatterns_getstarted_device.js** fil:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**.  Ersätt anslutningssträngen med enhetens anslutningssträng.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Lägg till följande funktion för att implementera direkt metod på enheten
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Öppna anslutningen till IoT-hubben och starta lyssnaren för direkt metod:

   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Spara och Stäng den **dmpatterns_getstarted_device.js** fil.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff) vilket rekommenderas i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärromstart på enheten med en direkt metod

I det här avsnittet skapar du en Node.js-konsolapp som initierar en fjärromstart på en enhet med en direkt metod. Appen använder enhetstvillingfrågor för att identifiera den senaste Omstartstid för enheten.

1. Skapa en tom mapp med namnet **triggerrebootondevice**. I den **triggerrebootondevice** mappen skapar du en package.json-fil med följande kommando i Kommandotolken. Acceptera alla standardvärden:
   
    ```
    npm init
    ```

2. I Kommandotolken i den **triggerrebootondevice** mapp, kör följande kommando för att installera den **azure-iothub** SDK-paketet och **azure-iot-device-mqtt** paket:
   
    ```
    npm install azure-iothub --save
    ```

3. Använd en textredigerare och skapa en **dmpatterns_getstarted_service.js** fil i den **triggerrebootondevice** mapp.

4. Lägg till följande ”require”-instruktioner i början av den **dmpatterns_getstarted_service.js** fil:

  
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Lägg till följande variabeldeklarationer och Ersätt platshållarvärdena:

   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Lägg till följande funktion för att anropa enhetsmetoden för att starta om målenheten:
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Lägg till följande funktion för att fråga efter enheten och få Omstartstid för senaste:
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Lägg till följande kod för att anropa funktionerna som utlöser direkt metod för omstart och fråga för Omstartstid för senaste:

   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Spara och Stäng den **dmpatterns_getstarted_service.js** fil.

## <a name="run-the-apps"></a>Köra apparna

Nu är det dags att köra apparna.

1. I Kommandotolken i den **manageddevice** mapp, kör följande kommando för att börja lyssna efter omstart direkt metod.

   
    ```
    node dmpatterns_getstarted_device.js
    ```

2. I Kommandotolken i den **triggerrebootondevice** mapp, kör följande kommando för att utlösa fjärromstart och fråga för enhetstvillingen för att hitta senaste omstart tid.

   
    ```
    node dmpatterns_getstarted_service.js
    ```

3. Du kan se svaret från enheten till den direkta metoden i konsolen.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]