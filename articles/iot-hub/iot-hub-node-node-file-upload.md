---
title: Ladda upp filer från enheter till Azure IoT Hub med noden | Microsoft Docs
description: Hur du överför filer från en enhet till molnet med Azure IoT-enhetens SDK för Node.js. Överförda filer lagras i en Azure storage blob-behållare.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 8b502c7616ed889166e0c5e708fe7858bf884031
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011168"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Ladda upp filer från din enhet till molnet med IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här självstudien bygger på koden i den [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-node-node-c2d.md) självstudien för att visa dig hur du använder den [filen ladda upp funktionerna i IoT Hub](iot-hub-devguide-file-upload.md) att överföra en fil till [Azure-blob Storage](../storage/index.yml). Självstudien visar hur du:

- Ange en enhet på ett säkert sätt med en Azure blob-URI: N för att ladda upp en fil.
- Använd IoT Hub filen ladda upp meddelanden för att utlösa fil i din app-serverdel.

Den [Kom igång med IoT Hub](quickstart-send-telemetry-node.md) självstudiekursen demonstrerar de grundläggande meddelanden funktionerna med enheten till molnet i IoT Hub. Men i vissa fall kan inte du enkelt mappa enheterna skickar till relativt liten enhet-till-moln-meddelanden som IoT-hubb tar emot data. Exempel:

* Stora filer som innehåller bilder
* Videoklipp
* Vibrationer data samplas med hög frekvens
* Någon form av förbearbetade data.

Dessa filer är vanligtvis bearbetas i molnet med hjälp av verktyg som [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) stack. När du behöver upland filer från en enhet, kan du fortfarande använda säkerheten och pålitligheten för IoT Hub.

I slutet av den här kursen kan du köra två Node.js-konsolappar:

* **SimulatedDevice.js**, som överför en fil till storage med hjälp av en SAS-URI som tillhandahålls av din IoT-hubb.
* **ReadFileUploadNotification.js**, som tar emot filen ladda upp meddelanden från IoT hub.

> [!NOTE]
> IoT-hubb har stöd för många enhetsplattformar och språk (inklusive C, .NET, Javascript, Python och Java) via SDK: er för Azure IoT-enheter. Referera till den [Azure IoT Developer Center] stegvisa instruktioner om hur du ansluter din enhet till Azure IoT Hub.

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en app för enheter

I det här avsnittet skapar du enhetsapp för att ladda upp en fil till IoT hub.

1. Skapa en tom mapp med namnet ```simulateddevice```.  Skapa en package.json-fil i mappen ```simulateddevice``` genom att köra följande kommando i kommandotolken.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

1. Installera **azure-iot-device** Device SDK-paketet och **azure-iot-device-mqtt**-paketet genom att köra följande kommando i kommandotolken i mappen ```simulateddevice```:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Skapa en **SimulatedDevice.js**-fil i mappen ```simulateddevice``` med hjälp av en textredigerare.

1. Lägg till följande ```require```-instruktioner i början av **SimulatedDevice.js**-filen:

    ```javascript
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Lägg till en ```deviceconnectionstring```-variabel och använd den för att skapa en **Client**-instans.  Ersätt ```{deviceconnectionstring}``` med namnet på den enhet som du skapade i den _skapar en IoT Hub_ avsnittet:

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > För enkelhetens skull anslutningssträngen som ingår i koden: Detta är inte en rekommendation och beroende på användningsfall och arkitektur kan du överväga att säkrare sätt för att lagra den här hemligheten.

1. Lägg till följande kod för att ansluta klienten:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Skapa ett motanrop och Använd den **uploadToBlob** funktionen för att ladda upp filen.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);
    
        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

1. Spara och stäng filen **SimulatedDevice.js**.

1. Kopiera en fil till den `simulateddevice` mappen och Byt namn på den `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Ta emot ett filuppladdningsmeddelande

I det här avsnittet skapar du en Node.js-konsolapp som tar emot filen ladda upp meddelanden från IoT Hub.

Du kan använda den **iothubowner** anslutningssträngen från din IoT-hubb för att slutföra det här avsnittet. Du hittar anslutningssträngen i den [Azure-portalen](https://portal.azure.com/) på den **princip för delad åtkomst** bladet.

1. Skapa en tom mapp med namnet ```fileuploadnotification```.  Skapa en package.json-fil i mappen ```fileuploadnotification``` genom att köra följande kommando i kommandotolken.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

1. I Kommandotolken i den ```fileuploadnotification``` mapp, kör följande kommando för att installera den **azure-iothub** SDK-paketet:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Använd en textredigerare och skapa en **FileUploadNotification.js** fil i den ```fileuploadnotification``` mapp.

1. Lägg till följande ```require``` instruktioner i början av den **FileUploadNotification.js** fil:

    ```javascript
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Lägg till en ```iothubconnectionstring```-variabel och använd den för att skapa en **Client**-instans.  Ersätt ```{iothubconnectionstring}``` med anslutningssträngen till IoT-hubben som du skapade i den _skapar en IoT Hub_ avsnittet:

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > För enkelhetens skull anslutningssträngen som ingår i koden: Detta är inte en rekommendation och beroende på användningsfall och arkitektur kan du överväga att säkrare sätt för att lagra den här hemligheten.

1. Lägg till följande kod för att ansluta klienten:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Öppna klienten och använda den **getFileNotificationReceiver** funktionen för att få statusuppdateringar.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. Spara och Stäng den **FileUploadNotification.js** fil.

## <a name="run-the-applications"></a>Köra programmen

Du är nu redo att köra programmen.

I en kommandotolk i den `fileuploadnotification` mapp, kör du följande kommando:

```cmd/sh
node FileUploadNotification.js
```

I en kommandotolk i den `simulateddevice` mapp, kör du följande kommando:

```cmd/sh
node SimulatedDevice.js
```

I följande skärmbild visas utdata från den **SimulatedDevice** app:

![Utdata från app för simulerade enheter](./media/iot-hub-node-node-file-upload/simulated-device.png)

I följande skärmbild visas utdata från den **FileUploadNotification** app:

![Utdata från Läs –--filuppladdningsmeddelande app](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Du kan använda portalen för att visa den överförda filen i storage-behållare som du har konfigurerat:

![Uppladdad fil](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien beskrivs hur du använder filen ladda upp funktionerna i IoT Hub för att förenkla filöverföringar från enheter. Du kan fortsätta att utforska IoT hub funktioner och scenarier i följande artiklar:

* [Skapa en IoT hub programmässigt][lnk-create-hub]
* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT SDK: er][lnk-sdks]

<!-- Links -->
[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
