---
title: Schemalägg jobb med Azure IoT Hub (Node) | Microsoft Docs
description: 'Schemalägga ett Azure IoT Hub-jobb för att anropa en direkt metod på flera enheter. Du kan använda Azure IoT SDK: erna för Node. js för att implementera de simulerade enhetens appar och en tjänst-app för att köra jobbet.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.openlocfilehash: a3a07f54d77577085951b2bccfa1213adef5b20d
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403911"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Schema-och sändnings jobb (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som gör det möjligt för en backend-app att skapa och spåra jobb som schemalägger och uppdaterar miljon tals enheter.  Jobb kan användas för följande åtgärder:

* Uppdatera önskade egenskaper
* Uppdatera Taggar
* Anropa direkt metoder

Ett jobb är konceptuellt, och spårar förloppet för körningen mot en uppsättning enheter som definieras av en enhets dubbla frågor.  En backend-app kan till exempel använda ett jobb för att anropa en metod för omstart på 10 000 enheter, som anges av en enhets dubbla frågor och schemaläggs vid ett senare tillfälle. Programmet kan sedan spåra förloppet när var och en av enheterna får och kör metoden starta om.

Lär dig mer om var och en av dessa funktioner i dessa artiklar:

* Enhetens dubbla och egenskaper: [Kom igång med enhets dubbla](iot-hub-node-node-twin-getstarted.md) och [Självstudier: Använda enhetens dubbla egenskaper](tutorial-device-twins.md)

* Direkta metoder: [Guide för IoT Hub utvecklare – direkta metoder](iot-hub-devguide-direct-methods.md) och [Självstudier: direkta metoder](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en Node. js-simulerad Device-app som har en direkt metod som aktiverar **lockDoor**, som kan anropas av lösningens Server del.

* Skapa en Node. js-konsol som anropar **lockDoor** Direct-metoden i den simulerade Device-appen med ett jobb och uppdaterar önskade egenskaper med ett enhets jobb.

I slutet av den här självstudien har du två Node. js-appar:

* **simDevice. js**, som ansluter till din IoT Hub med enhets identiteten och får en **lockDoor** Direct-metod.

* **scheduleJobService. js**, som anropar en direkt metod i den simulerade Device-appen och uppdaterar enhetens dubbla egenskaper med ett jobb.

För att kunna genomföra den här kursen behöver du följande:

* Node. js version 10.0. x eller senare för att [förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node. js för den här själv studie kursen på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Node. js-konsol som svarar på en direkt metod som anropas av molnet, vilket utlöser en simulerad **lockDoor** -metod.

1. Skapa en ny tom mapp med namnet **simDevice**.  I mappen **simDevice** skapar du en Package. JSON-fil med hjälp av följande kommando i kommando tolken.  Acceptera alla standardvärden:

   ```
   npm init
   ```

2. I kommando tolken i mappen **simDevice** kör du följande kommando för att installera paketet **Azure-IoT-Device** SDK och **Azure-IoT-Device-MQTT** :
   
   ```
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Skapa en ny **simDevice. js** -fil i mappen **simDevice** med hjälp av en text redigerare.

4. Lägg till följande "require"-instruktioner i början av filen **simDevice. js** :
   
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

6. Lägg till följande funktion för att hantera metoden **lockDoor** .
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

7. Lägg till följande kod för att registrera hanteraren för **lockDoor** -metoden.

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

8. Spara och Stäng filen **simDevice. js** .

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktions koden bör du implementera principer för omförsök (till exempel en exponentiell backoff), enligt förslag i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Schemalägg jobb för att anropa en direkt metod och uppdatera en enhets dubbla egenskaper

I det här avsnittet skapar du en Node. js-konsol som initierar en fjärran sluten **lockDoor** på en enhet med hjälp av en direkt metod och uppdaterar enhetens dubbla egenskaper.

1. Skapa en ny tom mapp med namnet **scheduleJobService**.  I mappen **scheduleJobService** skapar du en Package. JSON-fil med hjälp av följande kommando i kommando tolken.  Acceptera alla standardvärden:

    ```
    npm init
    ```

2. I kommando tolken i mappen **scheduleJobService** kör du följande kommando för att installera paketet **Azure-iothub** Device SDK och paketet **Azure-IoT-Device-MQTT** :
   
    ```
    npm install azure-iothub uuid --save
    ```

3. Skapa en ny **scheduleJobService. js** -fil i mappen **scheduleJobService** med hjälp av en text redigerare.

4. Lägg till följande "require"-instruktioner i början av filen **dmpatterns_gscheduleJobServiceetstarted_service. js** :
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Lägg till följande variabel deklarationer och ersätt plats hållarnas värden:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Lägg till följande funktion som används för att övervaka körningen av jobbet:
   
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

7. Lägg till följande kod för att schemalägga jobbet som anropar enhets metoden:
   
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

8. Lägg till följande kod för att schemalägga jobbet för att uppdatera enheten:
   
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

9. Spara och Stäng filen **scheduleJobService. js** .

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. I kommando tolken i mappen **simDevice** kör du följande kommando för att börja lyssna efter metoden starta om Direct.
   
    ```
    node simDevice.js
    ```

2. I kommando tolken i mappen **scheduleJobService** kör du följande kommando för att utlösa jobben för att låsa dörren och uppdatera den dubbla
   
    ```
    node scheduleJobService.js
    ```

3. Du ser enhets svaret till den direkta metoden i-konsolen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod till en enhet och uppdateringen av enhetens egenskaper.

Information [om hur du fortsätter att komma igång med IoT Hub-och enhets hanterings mönster, till exempel fjärran sluten av den inbyggda program varan Så här gör du en uppdatering](tutorial-firmware-update.md)av den inbyggda program varan.

För att fortsätta komma igång med IoT Hub, se [komma igång med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
