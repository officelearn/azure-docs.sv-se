---
title: "Kom igång med Azure IoT Hub enhetshantering (nod) | Microsoft Docs"
description: "Hur du använder IoT-hubb enhetshantering för att initiera en omstart av fjärranslutna enheter. Du kan använda Azure IoT-SDK för Node.js för att implementera en simulerad enhetsapp som innehåller en direkt metod och en service-appen som anropar metoden direkt."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: juanpere
ms.openlocfilehash: 0aeba8560de46ae2032304f133b66ad0a70f1354
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="get-started-with-device-management-node"></a>Komma igång med hantering av enheter (nod)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använda Azure portal för att skapa en IoT-hubb och skapa en enhetsidentitet i din IoT-hubb.
* Skapa en simulerad enhetsapp som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.
* Skapa en Node.js-konsolprogram som anropar metoden omstart direkt i appen simulerade enheten via din IoT-hubb.

I slutet av den här kursen har du två Node.js-konsolappar:

**dmpatterns_getstarted_device.js**, som ansluter till din IoT-hubb med enhetens identitet skapade tidigare, tar emot en direkt metod för omstart, simulerar en fysisk omstart och rapporterar tid för den senaste omstarten.

**dmpatterns_getstarted_service.js**, som anropar en direkt metod i appen simulerade enheten visar svaret och visar den uppdaterade rapporterade egenskaper.

För att kunna genomföra den här kursen behöver du följande:

* Node.js-version 4.0.x eller senare <br/>  [Förbered din utvecklingsmiljö] [ lnk-dev-setup] beskriver hur du installerar Node.js för den här självstudiekursen på Windows- eller Linux.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet kommer du att

* Skapa en Node.js-konsolapp som svarar på en direkt metod som anropas via molnet
* Utlös en simulerad enhet-omstart
* Använda egenskaperna rapporterade för att aktivera enheten dubbla frågor för att identifiera enheter och när de senaste startas om

1. Skapa en tom mapp med namnet **manageddevice**.  I mappen **manageddevice** skapar du en package.json-fil med hjälp av följande kommando i Kommandotolken.  Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. Vid en kommandotolk i den **manageddevice** mapp, kör följande kommando för att installera den **azure iot-enhet** enheten SDK-paketet och **azure-iot-enhet – mqtt** paketet:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Med hjälp av en textredigerare, skapa en **dmpatterns_getstarted_device.js** filen i den **manageddevice** mapp.
4. Lägg till följande 'krävs, instruktioner i början av den **dmpatterns_getstarted_device.js** fil:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**.  Ersätt anslutningssträngen med anslutningssträngen enhet.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Lägg till följande funktion för att implementera metoden direkt på enheten
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
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
7. Öppna anslutning till din IoT-hubb och starta lyssnaren direkta metoden:
   
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
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlös en fjärransluten omstart på enheten med en direkt metod
I det här avsnittet skapar du en Node.js-konsolprogram som initierar en fjärransluten omstart på en enhet med en direkt metod. Appen använder enheten dubbla frågor för att identifiera senast omstart för enheten.

1. Skapa en tom mapp som kallas **triggerrebootondevice**.  I den **triggerrebootondevice** mapp, skapa en package.json-fil med följande kommando vid en kommandotolk.  Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. Vid en kommandotolk i den **triggerrebootondevice** mapp, kör följande kommando för att installera den **azure iothub** enheten SDK-paketet och **azure-iot-enhet – mqtt** paketet:
   
    ```
    npm install azure-iothub --save
    ```
3. Med hjälp av en textredigerare, skapa en **dmpatterns_getstarted_service.js** filen i den **triggerrebootondevice** mapp.
4. Lägg till följande 'krävs, instruktioner i början av den **dmpatterns_getstarted_service.js** fil:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Lägg till följande variabeldeklarationer och ersätta platshållarvärdena:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. Lägg till följande funktion för att anropa metoden enheten för att starta om målenheten:
   
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
7. Lägg till följande funktion för att fråga efter enheten och få omstart senast:
   
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
8. Lägg till följande kod för att anropa funktionerna som utlöser direkt metod för omstart och fråga för omstart senast:
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Spara och Stäng den **dmpatterns_getstarted_service.js** fil.

## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. I Kommandotolken i den **manageddevice** mapp, kör följande kommando för att börja lyssna efter omstart direkta metoden.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. I Kommandotolken i den **triggerrebootondevice** mapp, kör följande kommando för att utlösa remote omstart och fråga för enheten dubbla att söka efter senaste omstart tid.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. Svaret från enheten till den direkta metoden i konsolen visas.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
