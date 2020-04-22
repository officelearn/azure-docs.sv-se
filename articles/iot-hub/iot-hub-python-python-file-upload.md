---
title: Ladda upp filer från enheter till Azure IoT Hub med Python | Microsoft-dokument
description: Så här laddar du upp filer från en enhet till molnet med Azure IoT-enheten SDK för Python. Överförda filer lagras i en Azure storage blob-behållare.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 47fb7c615389e24322450ed1785aa7da9ec50db6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759689"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Ladda upp filer från enheten till molnet med IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här artikeln visar hur du använder [filöverföringsfunktionerna i IoT Hub](iot-hub-devguide-file-upload.md) för att ladda upp en fil till [Azure blob storage](../storage/index.yml). Självstudien visar hur du:

* På ett säkert sätt tillhandahålla en lagringsbehållare för att ladda upp en fil.

* Använd Python-klienten för att ladda upp en fil via din IoT-hubb.

[Snabbstarten Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md) visar den grundläggande funktionen för ioT-hubben för enheter till molnet. I vissa fall kan du dock inte enkelt mappa de data som dina enheter skickar till de relativt små meddelanden från enhet till moln som IoT Hub accepterar. När du behöver upland-filer från en enhet kan du fortfarande använda säkerheten och tillförlitligheten hos IoT Hub.

I slutet av den här självstudien kör du Python-konsolappen:

* **FileUpload.py**, som laddar upp en fil till lagring med Python Device SDK.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en enhetsapp

I det här avsnittet skapar du enhetsappen för att ladda upp en fil till IoT-hubben.

1. Kör följande kommando för att installera **azure-iot-device-paketet** i kommandotolken. Du använder det här paketet för att koordinera filöverföringen med din IoT-hubb.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Kör följande kommando för att installera [**azure.storage.blob-paketet**](https://pypi.org/project/azure-storage-blob/) i kommandotolken. Du använder det här paketet för att utföra filöverföringen.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Skapa en testfil som du överför till blob-lagring.

1. Skapa en **FileUpload.py-fil** i arbetsmappen med hjälp av en textredigerare.

1. Lägg till `import` följande satser och variabler i början av **FileUpload.py** filen.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. Ersätt `[Device Connection String]` med anslutningssträngen för IoT-hubbenheten i filen. Ersätt `[Full path to local file]` med sökvägen till testfilen som du skapade eller någon fil på enheten som du vill ladda upp.

1. Skapa en funktion för att överföra filen till blob storage:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Den här funktionen tolkar *den blob_info* struktur som skickas in i den för att skapa en URL som används för att initiera en [azure.storage.blob.BlobClient](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python). Sedan överför filen till Azure blob storage med den här klienten.

1. Lägg till följande kod för att ansluta klienten och ladda upp filen:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Den här koden skapar en asynkron **IoTHubDeviceClient** och använder följande API:er för att hantera filöverföringen med din IoT-hubb:

    * **get_storage_info_for_blob** får information från din IoT-hubb om det länkade lagringskonto som du skapade tidigare. Den här informationen omfattar värdnamn, behållarnamn, blobnamn och en SAS-token. Lagringsinformationen skickas till **store_blob** -funktionen (skapad i föregående steg), så **BlobClient** i den funktionen kan autentisera med Azure-lagring. Metoden **get_storage_info_for_blob** returnerar också en correlation_id som används i **notify_blob_upload_status** metoden. Den correlation_id är IoT Hub sätt att markera vilken blob du arbetar med.

    * **notify_blob_upload_status** meddelar IoT Hub om statusen för din blob-lagringsåtgärd. Du skickar det correlation_id som erhållits genom **get_storage_info_for_blob** metoden. Den används av IoT Hub för att meddela alla tjänster som kanske lyssnar efter ett meddelande om statusen för filöverföringsuppgiften.

1. Spara och **UploadFile.py** stäng UploadFile.py-filen.

## <a name="run-the-application"></a>Köra appen

Nu är du redo att köra programmet.

1. Kör följande kommando i en kommandotolk i arbetsmappen:

    ```cmd/sh
    python FileUpload.py
    ```

2. Följande skärmbild visar utdata från **FileUpload-appen:**

    ![Utdata från appen simulerad enhet](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. Du kan använda portalen för att visa den uppladdade filen i lagringsbehållaren som du konfigurerade:

    ![Laddad fil](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder filöverföringsfunktionerna i IoT Hub för att förenkla filuppladdningar från enheter. Du kan fortsätta att utforska IoT-hubbfunktioner och -scenarier med följande artiklar:

* [Skapa ett IoT-nav programmässigt](iot-hub-rm-template-powershell.md)

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)

Läs mer om Azure Blob Storage med följande länkar:

* [Azure Blob Storage-dokumentation](https://docs.microsoft.com/azure/storage/blobs/)

* [Azure Blob Storage för Python API-dokumentation](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)
