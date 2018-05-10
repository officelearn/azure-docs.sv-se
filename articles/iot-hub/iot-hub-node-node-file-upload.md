---
title: Överföra filer från enheter till Azure IoT-hubb med noden | Microsoft Docs
description: Hur du överför filer från en enhet till molnet med Azure IoT-enhet SDK för Node.js. Överförda filer lagras i ett Azure storage blob-behållaren.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: b28a02462fe7a5a7f831102b3707fe03f84342ad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Ladda upp filer från enheten till molnet med IoT-hubb

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här kursen bygger på koden i den [meddelanden moln till enhet med IoT-hubben](iot-hub-node-node-c2d.md) vägledning till hur du använder den [filen överför funktionerna i IoT-hubb](iot-hub-devguide-file-upload.md) att överföra en fil till [Azure blob lagring](../storage/index.yml). I kursen får du veta hur till:

- Ange en enhet på ett säkert sätt med en Azure blob-URI för att överföra en fil.
- Använd IoT-hubb filen överför meddelanden för att utlösa bearbetning av filen i din app-serverdelen.

Den [Kom igång med IoT-hubb](iot-hub-node-node-getstarted.md) självstudiekursen visas de grundläggande enhet till moln meddelanden funktionerna i IoT-hubb. Men i vissa fall kan du enkelt mappa data enheterna skickar till relativt liten enhet till moln meddelanden som accepterar IoT-hubb. Exempel:

* Stora filer som innehåller bilder
* Videoklipp
* Vibration data samplas vid hög frekvens
* Någon form av förbearbetade data.

Dessa filer finns vanligtvis i molnet med hjälp av verktyg som bearbetas i batch [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) stacken. När du behöver upland filer från en enhet kan du fortfarande använda säkerheten och pålitligheten för IoT-hubb.

I slutet av den här kursen kan du köra två Node.js-konsolappar:

* **SimulatedDevice.js**, som överför en fil till lagring med hjälp av en SAS-URI som tillhandahålls av din IoT-hubb.
* **ReadFileUploadNotification.js**, som tar emot filen överföra meddelanden från din IoT-hubb.

> [!NOTE]
> IoT-hubb stöder många vilka plattformar och språk (inklusive C, .NET, Javascript, Python och Java) via SDK för Azure IoT-enhet. Referera till den [Azure IoT Developer Center] stegvisa instruktioner om hur du ansluter enheten till Azure IoT Hub.

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Överför en fil från en enhetsapp

I det här avsnittet skapar du enhetsapp för att överföra en fil till IoT-hubb.

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

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Lägg till en ```deviceconnectionstring```-variabel och använd den för att skapa en **Client**-instans.  Ersätt ```{deviceconnectionstring}``` med namnet på den enhet som du skapade i den _skapar en IoT-hubb_ avsnitt:

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > För enkelhetens skull anslutningssträngen ingår i koden: Detta rekommenderas inte och beroende på dina användningsfall och arkitektur du kanske vill säkrare sätt att lagra den här hemligheten.

1. Lägg till följande kod för att ansluta klienten:

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Skapa ett återanrop och använda den **uploadToBlob** funktion för att ladda upp filen.

    ```nodejs
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

## <a name="receive-a-file-upload-notification"></a>Ta emot ett meddelande om överföringen

I det här avsnittet skapar du en Node.js-konsolprogram som tar emot filen överföra meddelanden från IoT-hubb.

Du kan använda den **iothubowner** anslutningssträng från IoT-hubb för att slutföra det här avsnittet. Du hittar anslutningssträngen i den [Azure-portalen](https://portal.azure.com/) på den **princip för delad åtkomst** bladet.

1. Skapa en tom mapp med namnet ```fileuploadnotification```.  Skapa en package.json-fil i mappen ```fileuploadnotification``` genom att köra följande kommando i kommandotolken.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

1. Vid en kommandotolk i den ```fileuploadnotification``` mapp, kör följande kommando för att installera den **azure iothub** SDK-paketet:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Med hjälp av en textredigerare, skapa en **FileUploadNotification.js** filen i den ```fileuploadnotification``` mapp.

1. Lägg till följande ```require``` instruktioner i början av den **FileUploadNotification.js** fil:

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Lägg till en ```iothubconnectionstring```-variabel och använd den för att skapa en **Client**-instans.  Ersätt ```{iothubconnectionstring}``` med anslutningssträngen till IoT-hubb som du skapade i den _skapar en IoT-hubb_ avsnitt:

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > För enkelhetens skull anslutningssträngen ingår i koden: Detta rekommenderas inte och beroende på dina användningsfall och arkitektur du kanske vill säkrare sätt att lagra den här hemligheten.

1. Lägg till följande kod för att ansluta klienten:

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Öppna klienten och använda den **getFileNotificationReceiver** funktion för att ta emot uppdateringar.

    ```nodejs
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

Vid en kommandotolk i den `fileuploadnotification` mapp, kör du följande kommando:

```cmd/sh
node FileUploadNotification.js
```

Vid en kommandotolk i den `simulateddevice` mapp, kör du följande kommando:

```cmd/sh
node SimulatedDevice.js
```

Följande skärmbild visar utdata från den **SimulatedDevice** app:

![Utdata från simulerade enheten app](./media/iot-hub-node-node-file-upload/simulated-device.png)

Följande skärmbild visar utdata från den **FileUploadNotification** app:

![Utdata från Läs-filen-överför-meddelandeprogram](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Du kan använda portalen för att visa den överförda filen i vilken lagringsbehållare som du har konfigurerat:

![Överförda filen](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Nästa steg

I kursen får du har lärt dig hur du använder filen överför funktionerna i IoT-hubb för att förenkla filöverföringar från enheter. Du kan fortsätta att utforska IoT-hubb funktioner och scenarier i följande artiklar:

* [Skapa en IoT-hubb programmässigt][lnk-create-hub]
* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT-SDK][lnk-sdks]

<!-- Links -->
[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
