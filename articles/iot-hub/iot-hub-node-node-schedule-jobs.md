---
title: Schemalägg jobb med Azure IoT Hub (Node) | Microsoft Docs
description: Så här schemalägger du ett Azure IoT Hub-jobb att anropa en direkt metod på flera enheter. Du kan använda Azure IoT SDK för Node.js för att implementera appar för simulerade enheter och en tjänstapp för att köra jobbet.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: juanpere
ms.openlocfilehash: 2161cd12f8bdb6aa3018d81a7864816a97ed122a
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187758"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Schemalägg och Sänd jobb (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub är en helt hanterad tjänst som gör att en backend-app för att skapa och spåra jobb som schemalägger och uppdatera miljontals enheter.  Jobb kan användas för följande åtgärder:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direktmetoder

Den övergripande ett jobb omsluter på sådana åtgärder och spårar förloppet för körning mot en uppsättning enheter, som definieras av en enhet twin fråga.  En backend-app kan till exempel använda ett jobb för att anropa en metod för omstart på 10 000 enheter, som angetts av en enhet twin fråga och schemalagda vid ett senare tillfälle.  Programmet kan sedan spåra förloppet som var och en av dessa enheter tar emot och körningsmetod omstart.

Mer information om var och en av de här funktionerna i de här artiklarna:

* Enhetstvillingen och egenskaper: [Kom igång med enhetstvillingar] [ lnk-get-started-twin] och [självstudie: Så här använder du tvillingegenskaper][lnk-twin-props]
* Direkta metoder: [IoT Hub developer guide - direkta metoder] [ lnk-dev-methods] och [självstudie: direkta metoder][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en simulerad enhetsapp för Node.js som har en direkt metod, vilket gör att **lockDoor**, som kan anropas av lösningens serverdel.
* Skapa en Node.js-konsolapp som anropar den **lockDoor** direkt metod i den simulerade enhetsappen med hjälp av ett jobb och uppdateringar önskade egenskaper med hjälp av ett enhetsjobb.

I slutet av den här självstudien har du två Node.js-appar:

**simDevice.js**, som ansluter till IoT hub med enhetsidentiteten och tar emot en **lockDoor** direkt metod.

**scheduleJobService.js**, som anropar en direkt metod i den simulerade enhetsappen och uppdaterar enhetstvillingen önskade egenskaper med hjälp av ett jobb.

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 4.0.x eller senare <br/>  [Förbereda utvecklingsmiljön] [ lnk-dev-setup] beskriver hur du installerar Node.js för den här självstudiekursen i Windows eller Linux.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet skapar du en Node.js-konsolapp som svarar på en direkt metod som anropas av moln, vilket utlöser en simulerad **lockDoor** metod.

1. Skapa en ny tom mapp med namnet **simDevice**.  I den **simDevice** mappen skapar du en package.json-fil med följande kommando i Kommandotolken.  Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. I Kommandotolken i den **simDevice** mapp, kör följande kommando för att installera den **azure-iot-device** SDK-paketet och **azure-iot-device-mqtt** paketet:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Använd en textredigerare och skapa en ny **simDevice.js** fil i den **simDevice** mapp.
4. Lägg till följande ”require”-instruktioner i början av den **simDevice.js** fil:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Lägg till följande funktion för att hantera den **lockDoor** metod.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Lägg till följande kod för att registrera hanteraren för den **lockDoor** metod.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. Spara och Stäng den **simDevice.js** fil.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Schemalägga jobb för att anropa en direkt metod och uppdatera egenskaperna för en enhetstvilling
I det här avsnittet skapar du en Node.js-konsolapp som initierar en fjärransluten **lockDoor** med hjälp av en direkt metod på en enhet och uppdatera den enhetstvillingen egenskaper.

1. Skapa en ny tom mapp med namnet **scheduleJobService**.  I den **scheduleJobService** mappen skapar du en package.json-fil med följande kommando i Kommandotolken.  Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. I Kommandotolken i den **scheduleJobService** mapp, kör följande kommando för att installera den **azure-iothub** SDK-paketet och **azure-iot-device-mqtt** paket:
   
    ```
    npm install azure-iothub uuid --save
    ```
3. Använd en textredigerare och skapa en ny **scheduleJobService.js** fil i den **scheduleJobService** mapp.
4. Lägg till följande ”require”-instruktioner i början av den **dmpatterns_gscheduleJobServiceetstarted_service.js** fil:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. Lägg till följande variabeldeklarationer och Ersätt platshållarvärdena:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. Lägg till följande funktion som används för att övervaka körning av jobbet:
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```
7. Lägg till följande kod för att schemalägga jobb som anropar enhetsmetoden:
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
8. Lägg till följande kod för att schemalägga jobb för att uppdatera enhetstvillingen:
   
    ```
    var twinPatch = {
       etag: '*', 
       properties: {
           desired: {
               building: '43', 
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
9. Spara och Stäng den **scheduleJobService.js** fil.

## <a name="run-the-applications"></a>Köra programmen
Nu är det dags att köra programmen.

1. I Kommandotolken i den **simDevice** mapp, kör följande kommando för att börja lyssna efter omstart direkt metod.
   
    ```
    node simDevice.js
    ```
2. I Kommandotolken i den **scheduleJobService** mapp, kör följande kommando för att utlösa jobb för att låsa dörren och uppdatera läsningen
   
    ```
    node scheduleJobService.js
    ```
3. Du kan se svaret från enheten till den direkta metoden i konsolen.

## <a name="next-steps"></a>Nästa steg
I den här självstudien använde du ett jobb för att schemalägga en direkt metod att en enhet och varje uppdatering av enheten tvillingegenskaper.

Om du vill fortsätta att komma igång med IoT Hub och enhetshanteringsmönster som via luften firmware-uppdatering, se:

[Självstudie: Hur du gör en firmware-uppdatering][lnk-fwupdate]

Om du vill komma igång med IoT Hub går [komma igång med Azure IoT Edge][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: tutorial-device-twins.md
[lnk-c2d-methods]: quickstart-control-device-node.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: tutorial-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
