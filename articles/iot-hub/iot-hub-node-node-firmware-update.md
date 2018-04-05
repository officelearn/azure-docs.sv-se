---
title: Enheten firmware-uppdatering med Azure IoT Hub (nod) | Microsoft Docs
description: Hur du använder hantering av enheter på Azure IoT Hub för att initiera en firmware-uppdatering för enheten. Du kan använda Azure IoT-SDK för Node.js för att implementera en simulerad enhetsapp och en service-appen som utlöser firmware-uppdatering.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: e8ef556793534c9eb8300ee262a4b134960f862a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Använd enhetshantering för att starta en enhet på en firmware-uppdatering (nod/nod)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

I den [Kom igång med enhetshantering] [ lnk-dm-getstarted] självstudiekursen du sett hur du använder den [enheten dubbla] [ lnk-devtwin] och [direkt metoder] [ lnk-c2dmethod] primitiver att starta om en enhet via fjärranslutning. Den här kursen använder samma IoT-hubb primitiver och vägledning och visar hur du gör en slutpunkt till slutpunkt simulerade firmware-uppdatering.  Det här mönstret används i uppdatering av inbyggd hanteringsprogramvara för Intel modern enhet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en Node.js-konsolprogram som anropar metoden firmwareUpdate direkt i appen simulerade enheten via din IoT-hubb.
* Skapa en simulerad enhetsapp som implementerar en **firmwareUpdate** direkt metod. Den här metoden initierar en process i flera steg som väntar på att ladda ned avbildningen inbyggd programvara, laddar ned avbildningen av inbyggd programvara och slutligen använder inbyggd avbildningen. Under varje steg i uppdateringen använder enheten rapporterade egenskaperna för att rapportera förlopp.

I slutet av den här kursen har du två Node.js-konsolappar:

**dmpatterns_fwupdate_service.js**, som anropar en direkt metod i appen simulerade enheten visar svaret och regelbundet (varje 500ms) visar den uppdaterade rapporterade egenskaper.

**dmpatterns_fwupdate_device.js**, som ansluter till din IoT-hubb med enhetens identitet skapade tidigare, tar emot en firmwareUpdate direkt metod, körs via en process som flera tillstånd för att simulera en firmware-uppdatering inklusive: väntar på avbildningen hämta hämta den nya avbildningen och slutligen avbildningen används.

För att kunna genomföra den här kursen behöver du följande:

* Node.js-version 4.0.x eller senare <br/>  [Förbered din utvecklingsmiljö] [ lnk-dev-setup] beskriver hur du installerar Node.js för den här självstudiekursen på Windows- eller Linux.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

Följ den [Kom igång med enhetshantering](iot-hub-node-node-device-management-get-started.md) artikel för att skapa din IoT-hubb och hämta anslutningssträngen för IoT-hubb.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Utlös en fjärransluten firmware-uppdatering på enheten med en direkt metod
I det här avsnittet skapar du en Node.js-konsolprogram som initierar en fjärransluten firmware-uppdatering på en enhet. Appen använder direkta metoden för att initiera uppdateringen och använder enheten dubbla frågor för att hämta status för aktiva firmware-uppdatering med jämna mellanrum.

1. Skapa en tom mapp som kallas **triggerfwupdateondevice**.  I den **triggerfwupdateondevice** mapp, skapa en package.json-fil med följande kommando vid en kommandotolk.  Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. Vid en kommandotolk i den **triggerfwupdateondevice** mapp, kör följande kommando för att installera den **azure iot hub** paketet:
   
    ```
    npm install azure-iothub --save
    ```
3. Med hjälp av en textredigerare, skapa en **dmpatterns_getstarted_service.js** filen i den **triggerfwupdateondevice** mapp.
4. Lägg till följande 'krävs, instruktioner i början av den **dmpatterns_getstarted_service.js** fil:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Lägg till följande variabeldeklarationer och ersätta platshållarvärdena:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Lägg till följande funktion att söka efter och visa värdet för firmwareUpdate rapporterade egenskapen.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Lägg till följande funktion för att anropa metoden firmwareUpdate för att starta om målenheten:
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Slutligen lägger du till följande funktion kod för att starta ordning för firmware-uppdatering och starta regelbundet visas rapporterade egenskaper:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Spara och Stäng den **dmpatterns_fwupdate_service.js** fil.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. I Kommandotolken i den **manageddevice** mapp, kör följande kommando för att börja lyssna efter omstart direkta metoden.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. I Kommandotolken i den **triggerfwupdateondevice** mapp, kör följande kommando för att utlösa remote omstart och fråga för enheten dubbla att söka efter senaste omstart tid.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Svaret från enheten till den direkta metoden i konsolen visas.

## <a name="next-steps"></a>Nästa steg
I den här kursen används direkt metod för att utlösa en fjärransluten firmware-uppdatering på en enhet och används egenskaperna rapporterade för att följa förloppet för firmware-uppdatering.

Information om hur du utökar din IoT-lösningen och schema metodanrop på flera enheter finns i [schema och broadcast jobb] [ lnk-tutorial-jobs] kursen.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
