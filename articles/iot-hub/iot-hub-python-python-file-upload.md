---
title: Ladda upp filer från enheter till Azure IoT Hub med python | Microsoft Docs
description: Ladda upp filer från en enhet till molnet med hjälp av Azure IoT-enhetens SDK för python. Överförda filer lagras i en BLOB-behållare för Azure Storage.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 37ea126952451eae81c548a11f70b9a258808d34
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147355"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Ladda upp filer från enheten till molnet med IoT Hub (python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här artikeln visar hur du använder [fil överförings funktionerna i IoT Hub](iot-hub-devguide-file-upload.md) för att ladda upp en fil till [Azure Blob Storage](../storage/index.yml). Självstudien visar hur du:

* Tillhandahålla en lagrings behållare på ett säkert sätt för att ladda upp en fil.

* Använd python-klienten för att ladda upp en fil via din IoT Hub.

[Genom att skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-python.md) -snabb start demonstreras de grundläggande meddelande funktionerna från enhet till moln i IoT Hub. I vissa fall kan du dock inte enkelt mappa de data som enheterna skickar till de relativt små enhets-till-moln-meddelanden som IoT Hub accepterar. När du behöver använda filer från en enhet kan du fortfarande använda säkerhet och tillförlitlighet för IoT Hub.

> [!NOTE]
> IoT Hub python SDK stöder för närvarande bara överföring av teckenbaserade filer, till exempel **txt** -filer.

I slutet av den här självstudien kör du python-konsol programmet:

* **FileUpload.py**, som laddar upp en fil till lagringen med python-enhets-SDK: n.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en enhets app

I det här avsnittet skapar du Device-appen för att ladda upp en fil till IoT Hub.

1. Kör följande kommando i kommando tolken för att installera paketet **Azure-iothub-Device-client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Med hjälp av en text redigerare skapar du en testfil som ska överföras till Blob Storage.

    > [!NOTE]
    > IoT Hub python SDK stöder för närvarande bara överföring av teckenbaserade filer, till exempel **txt** -filer.

3. Med hjälp av en text redigerare skapar du en **FileUpload.py** -fil i arbetsmappen.

4. Lägg till följande `import` -instruktioner och variabler i början av **FileUpload.py** -filen. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. Ersätt `[Device Connection String]` med anslutnings strängen för din IoT Hub-enhet i filen. Ersätt `[Full path to file]` med sökvägen till test filen som du skapade, eller en fil på enheten som du vill ladda upp. Ersätt `[File name for storage]` med det namn som du vill ge filen när den har laddats upp till Blob Storage. 

6. Skapa en motringning för **upload_blob** -funktionen:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Lägg till följande kod för att ansluta klienten och ladda upp filen. Ta även med `main` rutinen:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Spara och Stäng filen **UploadFile.py** .

## <a name="run-the-application"></a>Köra programmet

Nu är du redo att köra programmet.

1. Kör följande kommando i en kommando tolk i arbetsmappen:

    ```cmd/sh
    python FileUpload.py
    ```

2. Följande skärm bild visar utdata från **fileupload** -appen:

    ![Utdata från den simulerade enhets appen](./media/iot-hub-python-python-file-upload/1.png)

3. Du kan använda portalen för att visa den överförda filen i den lagrings behållare som du konfigurerade:

    ![Överförd fil](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder fil överförings funktionerna i IoT Hub för att förenkla fil överföringar från enheter. Du kan fortsätta att utforska funktionerna och scenarierna i IoT Hub med följande artiklar:

* [Skapa en IoT Hub program mässigt](iot-hub-rm-template-powershell.md)

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)
