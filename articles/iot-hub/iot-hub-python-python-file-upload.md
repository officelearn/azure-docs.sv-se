---
title: Ladda upp filer från enheter till Azure IoT Hub med Python | Microsoft Docs
description: Hur du överför filer från en enhet till molnet med Azure IoT-enhetens SDK för Python. Överförda filer lagras i en Azure storage blob-behållare.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: kgremban
ms.openlocfilehash: 295f96258b2f5d6612ae7c5f86c9f360232111f6
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507849"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Ladda upp filer från din enhet till molnet med IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här artikeln visar hur du använder den [filen ladda upp funktionerna i IoT Hub](iot-hub-devguide-file-upload.md) att överföra en fil till [Azure blobblagring](../storage/index.yml). Självstudien visar hur du:

- På ett säkert sätt ange en lagringsbehållare för att ladda upp en fil.
- Använda Python-klienten för att ladda upp en fil via din IoT-hubb.

Den [skicka telemetri till IoT Hub](quickstart-send-telemetry-python.md) snabbstarten visar grundläggande enhet-till-moln-meddelandefunktioner för IoT Hub. Men i vissa fall kan inte du enkelt mappa enheterna skickar till relativt liten enhet-till-moln-meddelanden som IoT-hubb tar emot data. När du behöver upland filer från en enhet, kan du fortfarande använda säkerheten och pålitligheten för IoT Hub.

> [!NOTE]
> IoT Hub Python SDK stöder för närvarande endast överföra teckenbaserade filer som **.txt** filer.

I slutet av den här kursen kan du köra Python-konsolapp:

* **FileUpload.py**, som överför en fil till storage med hjälp av Python-enhets-SDK.

> [!NOTE]
> IoT-hubb har stöd för många enhetsplattformar och språk (inklusive C, .NET, Javascript, Python och Java) via SDK: er för Azure IoT-enheter. Referera till den [Azure IoT Developer Center] stegvisa instruktioner om hur du ansluter din enhet till Azure IoT Hub.

För att kunna genomföra den här kursen behöver du följande:

* [Python 2.x eller 3.x][lnk-python-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)][lnk-install-pip].
* Om du använder Windows OS installerar du [Visual C++ redistributable package][lnk-visual-c-redist] så att du kan använda native-DLL:er från Python.
* Ett aktivt Azure-konto. Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.
* En IoT-hubb i ditt Azure-konto med en enhetsidentitet för att testa filuppladdningsfunktionen. 

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en app för enheter

I det här avsnittet skapar du enhetsapp för att ladda upp en fil till IoT hub.

1. I Kommandotolken, kör du följande kommando för att installera den **azure-iothub-device-client** paketet:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Använd en textredigerare och skapa en testfil som du överför till blob storage. 

    > [!NOTE]
    > IoT Hub Python SDK stöder för närvarande endast överföra teckenbaserade filer som **.txt** filer.

1. Använd en textredigerare och skapa en **FileUpload.py** filen i arbetsmappen.

1. Lägg till följande `import` instruktioner och variabler i början av den **FileUpload.py** fil. 

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

1. I din fil ersätter `[Device Connection String]` med anslutningssträngen för din IoT hub-enhet. Ersätt `[Full path to file]` med sökvägen till Testfilen som du skapade eller alla filer på enheten som du vill ladda upp. Ersätt `[File name for storage]` med namnet som du vill ge till din fil när paketet har överförts till blob storage. 

1. Skapa ett motanrop för den **upload_blob** funktionen:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. Lägg till följande kod för att ansluta klienten och ladda upp filen. Även innehålla de `main` rutinen:

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


## <a name="run-the-application"></a>Köra programmet

Nu är du redo att köra programmet.

1. Kör följande kommando i Kommandotolken i arbetsmappen:

    ```cmd/sh
    python FileUpload.py
    ```

1. I följande skärmbild visas utdata från den **FileUpload** app:

    ![Utdata från app för simulerade enheter](./media/iot-hub-python-python-file-upload/1.png)

1. Du kan använda portalen för att visa den överförda filen i storage-behållare som du har konfigurerat:

    ![Uppladdad fil](./media/iot-hub-python-python-file-upload/2.png)


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
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
