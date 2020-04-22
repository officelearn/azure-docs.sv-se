---
title: Schemalägga jobb med Azure IoT Hub (nod) | Microsoft-dokument
description: Så här schemalägger du ett Azure IoT Hub-jobb för att anropa en direkt metod på flera enheter. Du använder Azure IoT SDK:er för Node.js för att implementera de simulerade enhetsapparna och en tjänstapp för att köra jobbet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt
ms.openlocfilehash: d7f9ce37ad85d39388eea90af263f59ce312a6b8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732271"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Schemalägg och sända jobb (Nod.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som gör det möjligt för en backend-app för att skapa och spåra jobb som schemalägger och uppdaterar miljontals enheter.  Jobb kan användas för följande åtgärder:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direkta metoder

Begreppsmässigt radbryts en av dessa åtgärder och spårar körningens förlopp mot en uppsättning enheter, som definieras av en enhetstvillingfråga.  En backend-app kan till exempel använda ett jobb för att anropa en omstartsmetod på 10 000 enheter, som anges av en enhetstvillingfråga och schemalagd vid en framtida tidpunkt. Det programmet kan sedan spåra förloppet som var och en av dessa enheter ta emot och köra omstart metoden.

Läs mer om var och en av dessa funktioner i följande artiklar:

* Enhetstvilling och egenskaper: [Kom igång med enhetstvillingar](iot-hub-node-node-twin-getstarted.md) och [självstudiekurs: Så här använder du enhetstvillingegenskaper](tutorial-device-twins.md)

* Direkta metoder: [IoT Hub developer guide - direkta metoder](iot-hub-devguide-direct-methods.md) och [handledning: direkta metoder](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en nod.js-simulerad enhetsapp som har en direkt metod, vilket möjliggör **lockDoor**, som kan anropas av lösningens backend.

* Skapa en Node.js-konsolapp som anropar **metoden lockDoor** direct i den simulerade enhetsappen med hjälp av ett jobb och uppdaterar önskade egenskaper med hjälp av ett enhetsjobb.

I slutet av den här självstudien har du två Node.js-appar:

* **simDevice.js**, som ansluter till din IoT-hubb med enhetens identitet och tar emot en **lockDoor** direkt metod.

* **scheduleJobService.js**, som anropar en direkt metod i den simulerade enhetsappen och uppdaterar enhetstvillingens önskade egenskaper med hjälp av ett jobb.

## <a name="prerequisites"></a>Krav

* Node.js version 10.0.x eller senare. [Förbered utvecklingsmiljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här självstudien på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/pricing/free-trial/) bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Node.js-konsolapp som svarar på en direkt metod som anropas av molnet, vilket utlöser en simulerad **lockDoor-metod.**

1. Skapa en ny tom mapp med namnet **simDevice**.  Skapa en package.json-fil med följande kommando i kommandotolken i mappen **simDevice.**  Acceptera alla standardvärden:

   ```console
   npm init
   ```

2. Kör följande kommando i kommandotolken i **simDevice-mappen** för att installera **Azure-iot-device** Device SDK-paketet och **azure-iot-device-mqtt-paketet:**

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Skapa en ny **simDevice.js-fil** med hjälp av en textredigerare i **mappen simDevice.**

4. Lägg till följande "kräv" satser i början av **filen simDevice.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**. Ersätt `{yourDeviceConnectionString}` platshållarvärdet med den enhetsanslutningssträng som du kopierade tidigare.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Lägg till följande funktion för att hantera **lockDoor-metoden.**

    ```javascript
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

7. Lägg till följande kod för att registrera hanteraren för **lockDoor-metoden.**

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Spara och stäng **filen simDevice.js.**

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för återförsök (till exempel en exponentiell backoff), som föreslås i artikeln [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Schemalägga jobb för att anropa en direkt metod och uppdatera en enhetstvillings egenskaper

I det här avsnittet skapar du en Node.js-konsolapp som initierar ett **fjärrlåsDörr** på en enhet med en direkt metod och uppdaterar enhetstvillingens egenskaper.

1. Skapa en ny tom mapp med namnet **scheduleJobService**.  Skapa en package.json-fil med följande kommando i kommandotolken i mappen **scheduleJobService.**  Acceptera alla standardvärden:

    ```console
    npm init
    ```

2. Kör följande kommando i kommandotolken i mappen **scheduleJobService** för att installera **azure-iothub** Device SDK-paketet och **azure-iot-device-mqtt-paketet:**

    ```console
    npm install azure-iothub uuid --save
    ```

3. Skapa en ny **schemaJobService.js-fil** med hjälp av en textredigerare i mappen **scheduleJobService.**

4. Lägg till följande "kräv"-satser i början av **filen scheduleJobService.js:**

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Lägg till följande variabeldeklarationer. Ersätt `{iothubconnectionstring}` platshållarvärdet med värdet som du kopierade i [Hämta anslutningssträngen för IoT-hubb](#get-the-iot-hub-connection-string). Om du har registrerat en annan enhet än **myDeviceId**måste du ändra den i frågevillkoret.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Lägg till följande funktion som används för att övervaka körningen av jobbet:

    ```javascript
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

7. Lägg till följande kod för att schemalägga jobbet som anropar enhetsmetoden:
  
    ```javascript
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

8. Lägg till följande kod för att schemalägga jobbet för att uppdatera enhetstvillingen:

    ```javascript
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

9. Spara och stäng **filen scheduleJobService.js.**

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Vid kommandotolken i **simDevice-mappen** kör du följande kommando för att börja lyssna efter metoden starta om direkt.

    ```console
    node simDevice.js
    ```

2. I kommandotolken i **mappen scheduleJobService** kör du följande kommando för att utlösa jobben för att låsa dörren och uppdatera tvillingen

    ```console
    node scheduleJobService.js
    ```

3. Enhetssvaret på den direkta metoden och jobbstatusen i konsolen visas.

   Följande visar enhetens svar på den direkta metoden:

   ![Utdata för simulerad enhetsapp](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Följande visar jobben för schemaläggning av tjänster för direkt-metoden och enhets twin-uppdateringen och de jobb som körs till slutförande:

   ![Köra den simulerade enhetsappen](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod till en enhet och uppdateringen av enhetstvillingens egenskaper.

Information om hur du fortsätter att komma igång med IoT Hub och enhetshanteringsmönster som fjärröver uppdateringen av den inbyggda programvaran finns i [Självstudiekurs: Hur du gör en uppdatering av den inbyggda programvaran](tutorial-firmware-update.md).

Information om hur du fortsätter att komma igång med IoT Hub finns i [Komma igång med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
