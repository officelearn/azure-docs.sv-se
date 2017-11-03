---
title: Azure IoT-hubb direkt metoder (nod) | Microsoft Docs
description: "Hur du använder Azure IoT Hub direkt metoder. Du kan använda Azure IoT-SDK för Node.js för att implementera en simulerad enhetsapp som innehåller en direkt metod och en service-appen som anropar metoden direkt."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a73c25724a239e56c3ea62a8452bb7c3a2b51be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-on-your-iot-device-with-nodejs"></a>Använd direkt metoderna på din IoT-enhet med Node.js
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

I slutet av den här kursen har du två Node.js-konsolappar:

* **CallMethodOnDevice.js**, som anropar en metod i appen simulerade enheten och visar svaret.
* **SimulatedDevice.js**, som ansluter till din IoT-hubb med enhetens identitet skapade tidigare och svarar på metoden som anropades av molnet.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda apparna så att de kan köras på enheter och på lösningens backend-servrar.
> 
> 

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet skapar du en Node.js-konsolprogram som svarar på en metod som anropas av molnet.

1. Skapa en ny tom mapp med namnet **simulateddevice**. I mappen **simulateddevice** skapar du en package.json-fil med hjälp av följande kommando i Kommandotolken. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. I Kommandotolken i mappen **simulateddevice** kör du följande kommando för att installera SDK-paketet **azure-iot-device** och paketet **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Med hjälp av en textredigerare skapar du en ny **SimulatedDevice.js**-fil i mappen **simulateddevice**.
4. Lägg till följande `require`-instruktioner i början av **SimulatedDevice.js**-filen:
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Lägg till en **connectionString** variabel och använda den för att skapa en **DeviceClient** instans. Ersätt **{enheten anslutningssträngen}** med enhetsanslutningen sträng som du genererade i den *skapa en enhetsidentitet* avsnitt:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Lägg till följande funktion för att implementera metoden på enheten:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Öppna anslutning till din IoT-hubb och starta initiera lyssnaren för metoden:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Spara och stäng filen **SimulatedDevice.js**.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskod, bör du implementera försök principer (till exempel anslutning försök), enligt förslaget i MSDN-artikel [hantering av tillfälliga fel][lnk-transient-faults].
> 
> 

## <a name="call-a-method-on-a-device"></a>Anropa en metod på en enhet
I det här avsnittet skapar du en Node.js-konsolprogram som anropar en metod i appen simulerade enheten och sedan visar svaret.

1. Skapa en ny tom mapp som kallas **callmethodondevice**. I den **callmethodondevice** mapp, skapa en package.json-fil med följande kommando vid en kommandotolk. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. Vid en kommandotolk i den **callmethodondevice** mapp, kör följande kommando för att installera den **azure iothub** paketet:
   
    ```
    npm install azure-iothub --save
    ```
3. Med hjälp av en textredigerare, skapa en **CallMethodOnDevice.js** filen i den **callmethodondevice** mapp.
4. Lägg till följande `require` instruktioner i början av den **CallMethodOnDevice.js** fil:
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    ```
5. Lägg till följande variabeldeklaration och ersätt platshållarvärdet med IoT Hub-anslutningssträngen för din hubb:
   
    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. Skapa klient för att öppna anslutningen till din IoT-hubb.
   
    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. Lägg till följande funktion att anropa metoden enhet och skriva ut svaret från enheten till konsolen:
   
    ```
    var methodParams = {
        methodName: methodName,
        payload: 'hello world',
        timeoutInSeconds: 30
    };
   
    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. Spara och Stäng den **CallMethodOnDevice.js** fil.

## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. Vid en kommandotolk i den **simulateddevice** mapp, kör följande kommando för att påbörja avlyssning för metodanrop från din IoT-hubb:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. Vid en kommandotolk i den **callmethodondevice** mapp, kör följande kommando för att börja övervaka din IoT-hubb:
   
    ```
    node CallMethodOnDevice.js 
    ```
   
    ![][8]
3. Enheten ta hänsyn till metoden genom att skriva ut meddelandet och programmet anropa metoden visas svaret från enheten visas:
   
    ![][9]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Denna enhetsidentitet använde för att aktivera appen simulerade enheten att ta hänsyn till metoder som anropas av molnet. Du kan också skapat en app som anropar metoder på enheten och visar svaret från enheten. 

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Kom igång med IoT-hubb]
* [Schema-jobb på flera enheter][lnk-devguide-jobs]

Information om hur du utökar din IoT-lösningen och schema metodanrop på flera enheter finns i [schema och broadcast jobb] [ lnk-tutorial-jobs] kursen.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-node-node-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-node-node-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Kom igång med IoT-hubb]: iot-hub-node-node-getstarted.md
