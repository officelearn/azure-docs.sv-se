---
title: Ladda upp filer från enheter till Azure IoT Hub med .NET | Microsoft Docs
description: Hur du överför filer från en enhet till molnet med Azure IoT-enhetens SDK för .NET. Överförda filer lagras i en Azure storage blob-behållare.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: 677f0e0f17191feb560ac5e9bb72a058e385084d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185838"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Ladda upp filer från din enhet till molnet med IoT Hub med .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här självstudien bygger på koden i den [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-csharp-csharp-c2d.md) självstudien för att visa dig hur du använder filen ladda upp funktionerna i IoT Hub. Den visar hur du:

- Ange en enhet på ett säkert sätt med en Azure blob-URI: N för att ladda upp en fil.
- Använd IoT Hub filen ladda upp meddelanden för att utlösa fil i din app-serverdel.

Den [Kom igång med IoT Hub](quickstart-send-telemetry-dotnet.md) och [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-csharp-csharp-c2d.md) självstudiekurser visar de grundläggande funktionerna av enhet till moln och från moln till enhet meddelanden i IoT Hub. Den [processen enhet till moln-meddelanden](tutorial-routing.md) självstudien beskrivs ett sätt att pålitligt lagra meddelanden från enheten till molnet i Azure blob storage. Men i vissa fall kan inte du enkelt mappa enheterna skickar till relativt liten enhet-till-moln-meddelanden som IoT-hubb tar emot data. Exempel:

* Stora filer som innehåller bilder
* Videoklipp
* Vibrationer data samplas med hög frekvens
* Någon form av förbearbetade data

Dessa filer är vanligtvis bearbetas i molnet med hjälp av verktyg som [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) stack. När du behöver ladda upp filer från en enhet, kan du fortfarande använda säkerheten och pålitligheten för IoT Hub.

I slutet av den här kursen kan du köra två .NET-konsolappar:

* **SimulatedDevice**, en modifierad version av appen som skapats i den [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-csharp-csharp-c2d.md) självstudien. Den här appen överför en fil till storage med hjälp av en SAS-URI som tillhandahålls av din IoT-hubb.
* **ReadFileUploadNotification**, som tar emot filen ladda upp meddelanden från IoT hub.

> [!NOTE]
> IoT-hubb har stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via SDK: er för Azure IoT-enheter. Referera till den [Azure IoT Developer Center] stegvisa instruktioner om hur du ansluter din enhet till Azure IoT Hub.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en app för enheter

I det här avsnittet ska du ändra app för enheter som du skapade i [skicka meddelanden från moln till enhet med IoT Hub](iot-hub-csharp-csharp-c2d.md) att ta emot meddelanden från moln till enhet från IoT hub.

1. I Visual Studio högerklickar du på den **SimulatedDevice** projektet, klicka på **Lägg till**, och klicka sedan på **befintligt objekt**. Navigera till en bildfil och inkludera den i projektet. Den här självstudien förutsätter att avbildningen har namnet `image.jpg`.

1. Högerklicka på filen och klicka sedan på **egenskaper**. Se till att **kopiera till utdatakatalog** är inställd på **Kopiera alltid**.

    ![][1]

1. I den **Program.cs** Lägg till följande uttryck överst i filen:

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

    Den `UploadToBlobAsync` metoden tar in filkälla för namn och ström av filen som ska laddas upp och hanterar överföringen till lagring. Konsol-appen visar den tid det tar för att överföra filen.

1. Lägg till följande metod i den **Main** metod, precis innan den `Console.ReadLine()` rad:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Den här självstudien implementerar inte någon återförsöksprincip sätt. I produktionskoden bör du implementera principer för omförsök (till exempel exponentiell backoff) vilket rekommenderas i MSDN-artikeln [hantering av tillfälliga fel].

## <a name="receive-a-file-upload-notification"></a>Ta emot ett filuppladdningsmeddelande

I det här avsnittet ska skriva du en .NET-konsolapp som tar emot filen ladda upp meddelanden från IoT Hub.

1. Skapa ett Visual C# Windows-projekt i den aktuella lösningen i Visual Studio med hjälp av den **konsolprogram** projektmall. Ge projektet namnet **ReadFileUploadNotification**.

    ![Nytt projekt i Visual Studio][2]

1. I Solution Explorer högerklickar du på den **ReadFileUploadNotification** projektet och klicka sedan på **hantera NuGet-paket...** .

1. I den **NuGet-Pakethanteraren** letar **Microsoft.Azure.Devices**, klickar du på **installera**, och Godkänn användningsvillkoren.

    Den här åtgärden hämtar, installerar och lägger till en referens till den [Azure IoT service SDK NuGet-paketet] i den **ReadFileUploadNotification** projekt.

1. I den **Program.cs** Lägg till följande uttryck överst i filen:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT hub-anslutningssträngen från [komma igång med IoT Hub:

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Lägg till följande metod i klassen **Program**:

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
            Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Observera att det här mönstret receive är samma som används för att ta emot meddelanden från moln till enhet från enhetsappen.

1. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Köra programmen

Du är nu redo att köra programmen.

1. Högerklicka på din lösning i Visual Studio och välj **ange Startprojekt**. Välj **flera Startprojekt**och välj sedan den **starta** åtgärd för **ReadFileUploadNotification** och **SimulatedDevice**.

1. Tryck på **F5**. Båda programmen ska börja. Du bör se överförts i en konsolapp och ladda upp meddelandet tas emot av andra konsolappen. Du kan använda den [Azure-portalen] eller Visual Studio Server Explorer för att söka efter förekomst av den överförda filen i ditt Azure Storage-konto.

    ![][50]

## <a name="next-steps"></a>Nästa steg

I den här självstudien beskrivs hur du använder filen ladda upp funktionerna i IoT Hub för att förenkla filöverföringar från enheter. Du kan fortsätta att utforska IoT hub funktioner och scenarier i följande artiklar:

* [Skapa en IoT hub programmässigt][lnk-create-hub]
* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT SDK: er][lnk-sdks]

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png

<!-- Links -->

[Azure-portalen]: https://portal.azure.com/

[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot

[Hantering av tillfälliga fel]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure IoT service SDK NuGet-paketet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
