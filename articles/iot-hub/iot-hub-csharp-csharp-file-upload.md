---
title: "Överföra filer från enheter till Azure IoT-hubb med .NET | Microsoft Docs"
description: "Hur du överför filer från en enhet till molnet med Azure IoT-enhet SDK för .NET. Överförda filer lagras i ett Azure storage blob-behållaren."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: acc599c1fbe13b2739fc5480d6204f90e59b0242
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Ladda upp filer från enheten till molnet med IoT-hubben med hjälp av .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här kursen bygger på koden i den [meddelanden moln till enhet med IoT-hubben](iot-hub-csharp-csharp-c2d.md) vägledning till hur du kan använda funktionerna för överför filen för IoT-hubb. Den visar hur till:

- Ange en enhet på ett säkert sätt med en Azure blob-URI för att överföra en fil.
- Använd IoT-hubb filen överför meddelanden för att utlösa bearbetning av filen i din app-serverdelen.

Den [Kom igång med IoT-hubb](iot-hub-csharp-csharp-getstarted.md) och [meddelanden moln till enhet med IoT-hubben](iot-hub-csharp-csharp-c2d.md) självstudiekurser visar grundläggande enhet till moln och moln till enhet meddelandetjänsten funktioner i IoT-hubb. Den [meddelanden processen enhet till moln](iot-hub-csharp-csharp-process-d2c.md) självstudiekursen beskriver ett sätt att på ett tillförlitligt sätt lagra meddelanden från enhet till moln i Azure blob storage. Men i vissa fall kan du enkelt mappa data enheterna skickar till relativt liten enhet till moln meddelanden som accepterar IoT-hubb. Exempel:

* Stora filer som innehåller bilder
* Videoklipp
* Vibration data samplas vid hög frekvens
* Någon form av förbearbetade data

Dessa filer finns vanligtvis i molnet med hjälp av verktyg som bearbetas i batch [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.md) stacken. När du behöver överföra filer från en enhet kan du fortfarande använda säkerheten och pålitligheten för IoT-hubb.

I slutet av den här kursen kan du köra två .NET konsolappar:

* **SimulatedDevice**, en modifierad version av app som skapats i den [meddelanden moln till enhet med IoT-hubben](iot-hub-csharp-csharp-c2d.md) kursen. Den här appen överför en fil till lagring med hjälp av en SAS-URI som tillhandahålls av din IoT-hubb.
* **ReadFileUploadNotification**, som tar emot filen överföra meddelanden från din IoT-hubb.

> [!NOTE]
> IoT-hubb stöder många vilka plattformar och språk (inklusive C, Java och Javascript) via Azure IoT-enhet SDK: er. Referera till den [Azure IoT Developer Center] stegvisa instruktioner om hur du ansluter enheten till Azure IoT Hub.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Överför en fil från en enhetsapp

I det här avsnittet kan du ändra appen för enheter som du skapade i [meddelanden moln till enhet med IoT-hubben](iot-hub-csharp-csharp-c2d.md) ta emot meddelanden moln till enhet från IoT-hubben.

1. I Visual Studio högerklickar du på den **SimulatedDevice** projektet, klicka på **Lägg till**, och klicka sedan på **befintlig artikel**. Navigera till en bildfil och inkludera den i projektet. Den här självstudiekursen förutsätts bilden heter `image.jpg`.

1. Högerklicka på bilden och klicka sedan på **egenskaper**. Se till att **kopiera till utdatakatalog** är inställd på **Kopiera alltid**.

    ![][1]

1. I den **Program.cs** lägger du till följande uttryck överst i filen:

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

    Den `UploadToBlobAsync` metoden hämtar i filen namn och dataströmmen källan för filen ska överföras och hanterar överföringen till lagring. Appen konsolen visar den tid det tar för att överföra filen.

1. Lägg till följande metod i den **Main** metod, precis innan den `Console.ReadLine()` rad:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Den här självstudiekursen implementerar inte några återförsöksprincip sätt. I produktionskod, bör du implementera försök principer (till exempel exponentiell backoff), enligt förslaget i MSDN-artikel [hantering av tillfälliga fel].

## <a name="receive-a-file-upload-notification"></a>Ta emot ett meddelande om överföringen

I det här avsnittet kan du skriva en .NET-konsolapp som tar emot filen överföra meddelanden från IoT-hubb.

1. Skapa ett Visual C# Windows-projekt i den aktuella Visual Studio-lösningen med hjälp av den **konsolprogram** projektmall. Namnge projektet **ReadFileUploadNotification**.

    ![Nytt projekt i Visual Studio][2]

1. I Solution Explorer högerklickar du på den **ReadFileUploadNotification** projektet och klicka sedan på **hantera NuGet-paket...** .

1. I den **NuGet Package Manager** fönster, söka efter **Microsoft.Azure.Devices**, klickar du på **installera**, och Godkänn användningsvillkoren.

    Den här åtgärden hämtar, installerar och lägger till en referens till den [Azure IoT service SDK NuGet-paketet] i den **ReadFileUploadNotification** projekt.

1. I den **Program.cs** lägger du till följande uttryck överst i filen:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållaren värdet med anslutningssträngen från IoT-hubb [Kom igång med IoT-hubb]:

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

    Observera att ta emot mönstret är det samma som används för att ta emot meddelanden moln till enhet från appen enhet.

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

1. Högerklicka på lösningen i Visual Studio och välj **ange Startprojekt**. Välj **flera Startprojekt**och välj den **starta** åtgärd för **ReadFileUploadNotification** och **SimulatedDevice**.

1. Tryck på **F5**. Båda programmen ska starta. Du bör se överföringen slutförts i ett konsolprogram och överför meddelandet tas emot av andra konsolprogram. Du kan använda den [Azure-portalen] eller Visual Studio Server Explorer för att söka efter förekomst av den överförda filen i ditt Azure Storage-konto.

    ![][50]

## <a name="next-steps"></a>Nästa steg

I kursen får du har lärt dig hur du använder filen överför funktionerna i IoT-hubb för att förenkla filöverföringar från enheter. Du kan fortsätta att utforska IoT-hubb funktioner och scenarier i följande artiklar:

* [Skapa en IoT-hubb programmässigt][lnk-create-hub]
* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT-SDK][lnk-sdks]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Distribuera AI till enheter med Azure IoT kant][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png

<!-- Links -->

[Azure-portalen]: https://portal.azure.com/

[Azure IoT Developer Center]: http://www.azure.com/develop/iot

[hantering av tillfälliga fel]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure IoT service SDK NuGet-paketet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
