---
title: Överföra filer från enheter till Azure IoT-hubb med Python | Microsoft Docs
description: Hur du överför filer från en enhet till molnet med Azure IoT-enhet SDK för Python. Överförda filer lagras i ett Azure storage blob-behållaren.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: kgremban
ms.openlocfilehash: 7f64783f5e1c79436b671ef98f30f5e3594b94e6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Ladda upp filer från enheten till molnet med IoT-hubb

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här kursen följer hur du använder den [filen överför funktionerna i IoT-hubb](iot-hub-devguide-file-upload.md) att överföra en fil till [Azure-blobblagring](../storage/index.yml). I kursen får du veta hur till:

- Ange en lagringsbehållare på ett säkert sätt för att överföra en fil.
- Använda Python-klienten för att överföra en fil via din IoT-hubb.

Den [Kom igång med IoT-hubb](iot-hub-node-node-getstarted.md) självstudiekursen visas de grundläggande enhet till moln meddelanden funktionerna i IoT-hubb. Men i vissa fall kan du enkelt mappa data enheterna skickar till relativt liten enhet till moln meddelanden som accepterar IoT-hubb. När du behöver upland filer från en enhet kan du fortfarande använda säkerheten och pålitligheten för IoT-hubb.

> [!NOTE]
> IoT-hubb Python SDK stöder för närvarande endast överföra teckenbaserade filer som **.txt** filer.

I slutet av den här kursen kan du köra Python-konsolapp:

* **FileUpload.py**, som överför en fil till lagring med hjälp av SDK för Python-enhet.

> [!NOTE]
> IoT-hubb stöder många vilka plattformar och språk (inklusive C, .NET, Javascript, Python och Java) via SDK för Azure IoT-enhet. Referera till den [Azure IoT Developer Center] stegvisa instruktioner om hur du ansluter enheten till Azure IoT Hub.

För att kunna genomföra den här kursen behöver du följande:

* [Python 2.x eller 3.x][lnk-python-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)][lnk-install-pip].
* Om du använder Windows OS installerar du [Visual C++ redistributable package][lnk-visual-c-redist] så att du kan använda native-DLL:er från Python.
* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)


[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>Överför en fil från en enhetsapp

I det här avsnittet skapar du enhetsapp för att överföra en fil till IoT-hubb.

1. Vid en kommandotolk, kör du följande kommando för att installera den **azure-iothub-enhet-klient** paketet:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Med hjälp av en textredigerare, skapa en **FileUpload.py** filer i arbetsmappen.

1. Lägg till följande `import` instruktioner och variabler i början av den **FileUpload.py** fil. Ersätt `deviceConnectionString` med anslutningssträngen för din IoT hub-enhet:

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name on storage after upload]"
    ```

1. Skapa ett återanrop för den **upload_blob** funktionen:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. Lägg till följande kod för att ansluta klienten och ladda upp filen. Även innehålla de `main` rutin:

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

1. Spara och Stäng den **UploadFile.py** fil.

1. Kopiera en exempeltextfil till arbetsmappen och byta namn på den `sample.txt`.

    > [!NOTE]
    > IoT-hubb Python SDK stöder för närvarande endast överföra teckenbaserade filer som **.txt** filer.


## <a name="run-the-application"></a>Köra programmet

Du är nu redo att köra programmet.

1. Kör följande kommando vid en kommandotolk i arbetsmappen:

    ```cmd/sh
    python FileUpload.py
    ```

1. Följande skärmbild visar utdata från den **FileUpload** app:

    ![Utdata från simulerade enheten app](./media/iot-hub-python-python-file-upload/1.png)

1. Du kan använda portalen för att visa den överförda filen i vilken lagringsbehållare som du har konfigurerat:

    ![Överförda filen](./media/iot-hub-python-python-file-upload/2.png)


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
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
