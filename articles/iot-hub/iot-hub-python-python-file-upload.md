---
title: Ladda upp filer från enheter till Azure IoT Hub med Python | Microsoft-dokument
description: Så här laddar du upp filer från en enhet till molnet med Azure IoT-enheten SDK för Python. Överförda filer lagras i en Azure storage blob-behållare.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110743"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Ladda upp filer från enheten till molnet med IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här artikeln visar hur du använder [filöverföringsfunktionerna i IoT Hub](iot-hub-devguide-file-upload.md) för att ladda upp en fil till [Azure blob storage](../storage/index.yml). Självstudien visar hur du:

* På ett säkert sätt tillhandahålla en lagringsbehållare för att ladda upp en fil.

* Använd Python-klienten för att ladda upp en fil via din IoT-hubb.

[Snabbstarten Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md) visar den grundläggande funktionen för ioT-hubben för enheter till molnet. I vissa fall kan du dock inte enkelt mappa de data som dina enheter skickar till de relativt små meddelanden från enhet till moln som IoT Hub accepterar. När du behöver upland-filer från en enhet kan du fortfarande använda säkerheten och tillförlitligheten hos IoT Hub.

> [!NOTE]
> IoT Hub Python SDK stöder för närvarande endast överföring av teckenbaserade filer som **TXT-filer.**

I slutet av den här självstudien kör du Python-konsolappen:

* **FileUpload.py**, som laddar upp en fil till lagring med Python Device SDK.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> Den här guiden använder den inaktuella V1 Python SDK, eftersom funktionen Filuppladdning ännu inte har implementerats i den nya V2 SDK.

## <a name="prerequisites"></a>Krav

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en enhetsapp

I det här avsnittet skapar du enhetsappen för att ladda upp en fil till IoT-hubben.

1. Kör följande kommando i kommandotolken för att installera **azure-iothub-device-client-paketet:**

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Med hjälp av en textredigerare skapar du en testfil som du ska ladda upp till blob-lagring.

    > [!NOTE]
    > IoT Hub Python SDK stöder för närvarande endast överföring av teckenbaserade filer som **TXT-filer.**

3. Skapa en **FileUpload.py-fil** i arbetsmappen med hjälp av en textredigerare.

4. Lägg till `import` följande satser och variabler i början av **FileUpload.py** filen. 

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

5. Ersätt `[Device Connection String]` med anslutningssträngen för IoT-hubbenheten i filen. Ersätt `[Full path to file]` med sökvägen till testfilen som du skapade eller någon fil på enheten som du vill ladda upp. Ersätt `[File name for storage]` med det namn som du vill ge filen när den har överförts till bloblagring. 

6. Skapa en motringning för **funktionen upload_blob:**

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Lägg till följande kod för att ansluta klienten och ladda upp filen. Inkludera även `main` rutinen:

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

8. Spara och **UploadFile.py** stäng UploadFile.py-filen.

## <a name="run-the-application"></a>Köra appen

Nu är du redo att köra programmet.

1. Kör följande kommando i en kommandotolk i arbetsmappen:

    ```cmd/sh
    python FileUpload.py
    ```

2. Följande skärmbild visar utdata från **FileUpload-appen:**

    ![Utdata från appen simulerad enhet](./media/iot-hub-python-python-file-upload/1.png)

3. Du kan använda portalen för att visa den uppladdade filen i lagringsbehållaren som du konfigurerade:

    ![Laddad fil](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder filöverföringsfunktionerna i IoT Hub för att förenkla filuppladdningar från enheter. Du kan fortsätta att utforska IoT-hubbfunktioner och -scenarier med följande artiklar:

* [Skapa ett IoT-nav programmässigt](iot-hub-rm-template-powershell.md)

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK:er](iot-hub-devguide-sdks.md)
