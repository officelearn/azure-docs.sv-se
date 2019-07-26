---
title: Ladda upp filer från enheter till Azure IoT Hub med .NET | Microsoft Docs
description: Ladda upp filer från en enhet till molnet med hjälp av Azure IoT Device SDK för .NET. Överförda filer lagras i en BLOB-behållare för Azure Storage.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: f1001df52b3bbb54f3b872f23276957fa01a7da5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403194"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Ladda upp filer från enheten till molnet med IoT Hub med .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här självstudien bygger på koden i [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-csharp-csharp-c2d.md) själv studie kurs som visar hur du använder fil överförings funktionerna i IoT Hub. Det visar hur du:

* Tillhandahålla en enhet på ett säkert sätt en Azure Blob-URI för att ladda upp en fil.

* Använd IoT Hub fil överförings meddelanden för att utlösa bearbetning av filen i Server delen av din app.

[Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-dotnet.md) snabb start och [skicka meddelanden från molnet till enheten med IoT Hub](iot-hub-csharp-csharp-c2d.md) själv studie kursen Visa de grundläggande meddelande funktionerna från enhet till moln och meddelanden från moln till enhet i IoT Hub. I självstudien [Konfigurera meddelanderoutning med IoT Hub](tutorial-routing.md) beskrivs ett tillförlitligt sätt att lagra meddelanden från enheten till molnet i Azure Blob Storage. I vissa fall kan du dock inte enkelt mappa de data som enheterna skickar till de relativt små enhets-till-moln-meddelanden som IoT Hub accepterar. Exempel:

* Stora filer som innehåller bilder
* Videor
* Exempel på vibrations data med hög frekvens
* Någon form av förbearbetade data

De här filerna bearbetas vanligt vis i molnet med hjälp av verktyg som [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) -stacken. När du behöver ladda upp filer från en enhet kan du fortfarande använda säkerhet och tillförlitlighet för IoT Hub.

I slutet av den här självstudien kör du två .NET-konsol program:

* **SimulatedDevice**, en modifierad version av appen som skapats i [skicka meddelanden från molnet till enheten med IoT Hub](iot-hub-csharp-csharp-c2d.md) själv studie kursen. Den här appen laddar upp en fil till lagring med hjälp av en SAS-URI från IoT Hub.

* **ReadFileUploadNotification**, som tar emot fil överförings meddelanden från din IoT Hub.

> [!NOTE]
> IoT Hub stöder många enhets plattformar och språk (inklusive C, Java och Java Script) via SDK: er för Azure IoT-enheter. I [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) hittar du stegvisa instruktioner för hur du ansluter enheten till Azure IoT Hub.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en enhets app

I det här avsnittet ändrar du den enhets app som du skapade i [skicka meddelanden från molnet till enheten med IoT Hub](iot-hub-csharp-csharp-c2d.md) för att ta emot meddelanden från molnet till enheten från IoT Hub.

1. I Visual Studio högerklickar du på projektet **SimulatedDevice** , klickar på **Lägg till**och klickar sedan på **befintligt objekt**. Navigera till en avbildnings fil och ta med den i projektet. I den här självstudien förutsätter vi att avbildningen heter `image.jpg`.

1. Högerklicka på avbildningen och klicka sedan på **Egenskaper**. Kontrol lera att kopiera **till utdata-katalogen** är inställt på **Kopiera alltid**.

    ![Visa var du uppdaterar avbildnings egenskapen för kopiera till utdatakatalogen](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. I **program.cs** -filen lägger du till följande-instruktioner överst i filen:

    ```csharp
    using System.IO;
    ```

1. Lägg till följande metod i klassen **Program**:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    `UploadToBlobAsync` Metoden tar i fil namnet och Stream-källan för filen som ska överföras och hanterar överföringen till lagringen. I konsol programmet visas hur lång tid det tar att ladda upp filen.

1. Lägg till följande metod i **main** -metoden, precis före `Console.ReadLine()` raden:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> För enkelhetens skull implementerar den här självstudien inte någon princip för återförsök. I produktions koden bör du implementera principer för omförsök (till exempel exponentiell backoff), enligt förslag i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en backend-tjänst för att ta emot meddelanden om fil överföring från IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md). Om du vill ta emot meddelanden om fil överföring behöver tjänsten **tjänst anslutnings** behörighet. Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Ta emot ett meddelande om fil uppladdning

I det här avsnittet skriver du en .NET-konsol app som tar emot meddelanden om fil överförings meddelanden från IoT Hub.

1. I den aktuella Visual Studio-lösningen skapar du ett C# visuellt Windows-projekt med hjälp av projekt mal len **konsol program** . Ge projektet namnet **ReadFileUploadNotification**.

    ![Nytt projekt i Visual Studio](./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png)

2. I Solution Explorer högerklickar du på projektet **ReadFileUploadNotification** och klickar sedan på **Hantera NuGet-paket...** .

3. I fönstret **NuGet Package Manager** söker du efter **Microsoft. Azure.** Devices, klickar på **Installera**och godkänner användnings villkoren.

    Den här åtgärden hämtar, installerar och lägger till en referens till [Azure IoT service SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Devices/) i **ReadFileUploadNotification** -projektet.

4. I **program.cs** -filen lägger du till följande-instruktioner överst i filen:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

5. Lägg till följande fält i klassen **Program**. Ersätt placeholder-värdet med IoT Hub-anslutningssträngen som du kopierade tidigare i [Hämta IoT Hub](#get-the-iot-hub-connection-string)-anslutningssträngen: `{iot hub connection string}`

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

6. Lägg till följande metod i klassen **Program**:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Observera att det här mottagnings mönstret är samma som används för att ta emot meddelanden från molnet till enheten från Device-appen.

7. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Köra programmen

Du är nu redo att köra programmen.

1. Högerklicka på din lösning i Visual Studio och välj **Ange start projekt**. Välj **flera start projekt**och välj sedan **Start** åtgärden för **ReadFileUploadNotification** och **SimulatedDevice**.

2. Tryck på **F5**. Båda programmen ska starta. Du bör se att uppladdningen är klar i en enda konsol app och meddelandet överförings meddelande som tagits emot av den andra konsolen. Du kan använda [Azure Portal](https://portal.azure.com/) -eller Visual Studio-Server Explorer för att söka efter förekomsten av den överförda filen i ditt Azure Storage-konto.

    ![Skärm bild som visar skärmen utdata](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder fil överförings funktionerna i IoT Hub för att förenkla fil överföringar från enheter. Du kan fortsätta att utforska funktionerna och scenarierna i IoT Hub med följande artiklar:

* [Skapa en IoT Hub program mässigt](iot-hub-rm-template-powershell.md)

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Distribuera AI till gräns enheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
