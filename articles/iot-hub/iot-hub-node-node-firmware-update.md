---
title: Enheten firmware-uppdatering med Azure IoT Hub (Node) | Microsoft Docs
description: Hur du använder enhetshantering i Azure IoT Hub för att initiera en firmware-uppdatering för enheten. Du kan använda Azure IoT SDK för Node.js för att implementera en simulerad enhetsapp och en service-app som utlöser uppdateringen av inbyggd programvara.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: 0cd8c019cf9a65e0e72227ba99c1995a45ed4067
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452438"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Använda enhetshantering för att initiera en enhet på en firmware-uppdatering (noden/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

I den [Kom igång med enhetshantering] [ lnk-dm-getstarted] självstudier, såg du hur du använder den [enhetstvillingen] [ lnk-devtwin] och [direkta metoder ] [ lnk-c2dmethod] primitiver att starta om en enhet via fjärranslutning. Den här självstudien använder samma IoT Hub-primitiver och ger vägledning och visar hur du gör en slutpunkt till slutpunkt simulerade firmware-uppdatering.  Det här mönstret används i update-implementeringen av inbyggd programvara för Intel Edison enheten exemplet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en Node.js-konsolapp som anropar metoden firmwareUpdate direkt i den simulerade enhetsappen via din IoT-hubb.
* Skapa en simulerad enhetsapp som implementerar en **firmwareUpdate** direkt metod. Den här metoden initierar en process i flera steg som väntar på att ladda ned avbildningen för inbyggd programvara, laddar ned avbildningen för inbyggd programvara och slutligen gäller avbildningen för inbyggd programvara. Enheten använder de rapporterade egenskaperna under varje steg av uppdateringen för att informera om förloppet.

I slutet av den här självstudien har du två Node.js-konsolappar:

**dmpatterns_fwupdate_service.js**, som anropar en direkt metod i den simulerade enhetsappen visar svaret och regelbundet (varje 500ms) visar det uppdaterade rapporterade egenskaper.

**dmpatterns_fwupdate_device.js**, som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare, får en direkt metod firmwareUpdate, körs via en process som har flera tillstånd att simulera en firmware update inklusive: väntar på avbildningen ladda ned att ladda ned den nya avbildningen och slutligen avbildningen används.

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 4.0.x eller senare <br/>  [Förbereda utvecklingsmiljön] [ lnk-dev-setup] beskriver hur du installerar Node.js för den här självstudiekursen i Windows eller Linux.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

Följ den [Kom igång med enhetshantering](iot-hub-node-node-device-management-get-started.md) artikeln om du vill skapa din IoT-hubb och få din IoT Hub-anslutningssträngen.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Utlösa en fjärransluten firmware-uppdatering på enheten med en direkt metod
I det här avsnittet skapar du en Node.js-konsolapp som initierar en fjärransluten firmware-uppdatering på en enhet. Appen använder en direkt metod för att initiera uppdateringen och använder enhetstvillingfrågor för att regelbundet hämta status för aktiva firmware-uppdatering.

1. Skapa en tom mapp med namnet **triggerfwupdateondevice**.  I den **triggerfwupdateondevice** mappen skapar du en package.json-fil med följande kommando i Kommandotolken.  Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. I Kommandotolken i den **triggerfwupdateondevice** mapp, kör följande kommando för att installera den **azure-iot-hub** paketet:
   
    ```
    npm install azure-iothub --save
    ```
3. Använd en textredigerare och skapa en **dmpatterns_getstarted_service.js** fil i den **triggerfwupdateondevice** mapp.
4. Lägg till följande ”require”-instruktioner i början av den **dmpatterns_getstarted_service.js** fil:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Lägg till följande variabeldeklarationer och Ersätt platshållarvärdena:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Lägg till följande funktion att hitta och visa värdet för firmwareUpdate rapporterade egenskap.
   
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
8. Slutligen lägger du till följande funktion till kod för att starta uppdateringssekvensen av inbyggd programvara och starta som regelbundet visar rapporterade egenskaper:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Spara och Stäng den **dmpatterns_fwupdate_service.js** fil.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. I Kommandotolken i den **manageddevice** mapp, kör följande kommando för att börja lyssna efter omstart direkt metod.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. I Kommandotolken i den **triggerfwupdateondevice** mapp, kör följande kommando för att utlösa fjärromstart och fråga för enhetstvillingen för att hitta senaste omstart tid.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Du kan se svaret från enheten till den direkta metoden i konsolen.

## <a name="next-steps"></a>Nästa steg
I den här självstudien används en direkt metod för att utlösa en fjärransluten firmware-uppdatering på en enhet och används de rapporterade egenskaperna för att följa förloppet för uppdatering av inbyggd programvara.

Läs hur du utökar din IoT-lösning och schema anropar på flera enheter i den [schema och sändningsjobb] [ lnk-tutorial-jobs] självstudien.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
