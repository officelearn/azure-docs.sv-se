---
title: Ladda upp filer från enheter till Azure IoT Hub med nod | Microsoft-dokument
description: Så här laddar du upp filer från en enhet till molnet med Azure IoT-enheten SDK för Node.js. Överförda filer lagras i en Azure storage blob-behållare.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt
ms.openlocfilehash: af9743233a61e8e6d816b362d35e6a38735df35b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732250"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Ladda upp filer från enheten till molnet med IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här självstudien bygger på koden i [send cloud-to-device-meddelanden med IoT Hub-självstudiekurs](iot-hub-node-node-c2d.md) för att visa hur du använder [filöverföringsfunktionerna i IoT Hub](iot-hub-devguide-file-upload.md) för att ladda upp en fil till Azure [blob storage](../storage/index.yml). Självstudien visar hur du:

* Ge en enhet en Azure blob URI på ett säkert sätt för att ladda upp en fil.

* Använd IoT Hub-filöverföringsmeddelandena för att utlösa bearbetning av filen i appryggen.

[Snabbstarten Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) visar den grundläggande funktionen för ioT-hubben för enheter till molnet. I vissa fall kan du dock inte enkelt mappa de data som dina enheter skickar till de relativt små meddelanden från enhet till moln som IoT Hub accepterar. Ett exempel:

* Stora filer som innehåller bilder
* Videoklipp
* Vibrationsdata som provtas med hög frekvens
* Någon form av förbehandlade data.

Dessa filer är vanligtvis batchbearbetning i molnet med hjälp av verktyg som [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) stacken. När du behöver upland-filer från en enhet kan du fortfarande använda säkerheten och tillförlitligheten hos IoT Hub.

I slutet av den här självstudien kör du två Node.js-konsolappar:

* **SimulatedDevice.js**, som laddar upp en fil till lagring med hjälp av en SAS URI som tillhandahålls av din IoT-hubb.

* **ReadFileUploadNotification.js**, som tar emot filuppladdningsmeddelanden från din IoT-hubb.

> [!NOTE]
> IoT Hub stöder många enhetsplattformar och -språk (inklusive C, .NET, Javascript, Python och Java) via Azure IoT-enhetSDK:er. Se [Azure IoT Developer Center] för steg-för-steg-instruktioner om hur du ansluter din enhet till Azure IoT Hub.

## <a name="prerequisites"></a>Krav

* Node.js version 10.0.x eller senare. [Förbered utvecklingsmiljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här självstudien på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/pricing/free-trial/) bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en enhetsapp

I det här avsnittet skapar du enhetsappen för att ladda upp en fil till IoT-hubben.

1. Skapa en tom mapp med namnet ```simulateddevice```.  Skapa en package.json-fil i mappen ```simulateddevice``` genom att köra följande kommando i kommandotolken.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. Installera **azure-iot-device** Device SDK-paketet och **azure-iot-device-mqtt**-paketet genom att köra följande kommando i kommandotolken i mappen ```simulateddevice```:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Skapa en **SimulatedDevice.js**-fil i mappen ```simulateddevice``` med hjälp av en textredigerare.

4. Lägg till följande ```require```-instruktioner i början av **SimulatedDevice.js**-filen:

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Lägg till en `deviceconnectionstring`-variabel och använd den för att skapa en **Client**-instans.  Ersätt `{deviceconnectionstring}` med namnet på den enhet som du skapade i avsnittet *Skapa en IoT Hub:*

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > För enkelhetens skull ingår anslutningssträngen i koden: detta är inte en rekommenderad metod och beroende på ditt användningsfall och arkitektur kanske du vill överväga säkrare sätt att lagra denna hemlighet.

6. Lägg till följande kod för att ansluta klienten:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Skapa en motringning och använd **uploadToBlob-funktionen** för att ladda upp filen.

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

8. Spara och stäng filen **SimulatedDevice.js**.

9. Kopiera en bildfil `simulateddevice` till mappen `myimage.png`och byt namn på den .

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en serverningstjänst för att ta emot meddelanden om filöverföring från IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). För att ta emot meddelanden om filöverföring behöver tjänsten behörigheten **för tjänsten ansluter.** Som standard skapas varje IoT-hubb med en princip med namnet delad åtkomst med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Ta emot ett meddelande om filöverföring

I det här avsnittet skapar du en Node.js-konsolapp som tar emot meddelanden om filöverföring från IoT Hub.

Du kan använda **iothubowner-anslutningssträngen** från IoT Hub för att slutföra det här avsnittet. Anslutningssträngen i [Azure-portalen](https://portal.azure.com/) på **principbladet För delad åtkomst** hittar du.

1. Skapa en tom mapp med namnet ```fileuploadnotification```.  Skapa en package.json-fil i mappen ```fileuploadnotification``` genom att köra följande kommando i kommandotolken.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. I kommandotolken ```fileuploadnotification``` i mappen kör du följande kommando för att installera **Azure-iothub** SDK-paketet:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Skapa en **FileUploadNotification.js-fil** i `fileuploadnotification` mappen med hjälp av en textredigerare.

4. Lägg till `require` följande satser i början av **fileuploadNotification.js-filen:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Lägg till en `iothubconnectionstring`-variabel och använd den för att skapa en **Client**-instans.  Ersätt `{iothubconnectionstring}` platshållarvärdet med anslutningssträngen för IoT-hubb som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > För enkelhetens skull ingår anslutningssträngen i koden: detta är inte en rekommenderad metod och beroende på ditt användningsfall och arkitektur kanske du vill överväga säkrare sätt att lagra denna hemlighet.

6. Lägg till följande kod för att ansluta klienten:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Öppna klienten och använd funktionen **getFileNotificationReceiver** för att få statusuppdateringar.

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

8. Spara och stäng **fileuploadNotification.js-filen.**

## <a name="run-the-applications"></a>Köra programmen

Du är nu redo att köra programmen.

Kör följande kommando `fileuploadnotification` i en kommandotolk i mappen:

```cmd/sh
node FileUploadNotification.js
```

Kör följande kommando `simulateddevice` i en kommandotolk i mappen:

```cmd/sh
node SimulatedDevice.js
```

Följande skärmbild visar utdata från **appen Simulerad effekt:**

![Utdata från appen simulerad enhet](./media/iot-hub-node-node-file-upload/simulated-device.png)

Följande skärmbild visar utdata från **fileuploadnotification-appen:**

![Utdata från appen läsa-fil-ladda upp meddelanden](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Du kan använda portalen för att visa den uppladdade filen i lagringsbehållaren som du konfigurerade:

![Laddad fil](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder filöverföringsfunktionerna i IoT Hub för att förenkla filuppladdningar från enheter. Du kan fortsätta att utforska IoT-hubbfunktioner och -scenarier med följande artiklar:

* [Skapa ett IoT-nav programmässigt](iot-hub-rm-template-powershell.md)

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)
