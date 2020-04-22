---
title: Ladda upp filer från enheter till Azure IoT Hub med .NET | Microsoft-dokument
description: Så här laddar du upp filer från en enhet till molnet med Azure IoT-enheten SDK för .NET. Överförda filer lagras i en Azure storage blob-behållare.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 14472e84d425bf03a3c6a0c2dc558d4b8225caec
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733403"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Ladda upp filer från enheten till molnet med IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här självstudien bygger på koden i [send cloud-to-device-meddelanden med IoT](iot-hub-csharp-csharp-c2d.md) Hub-självstudiekursen för att visa hur du använder filöverföringsfunktionerna i IoT Hub. Det visar dig hur du:

* Ge en enhet en Azure blob URI på ett säkert sätt för att ladda upp en fil.

* Använd IoT Hub-filöverföringsmeddelandena för att utlösa bearbetning av filen i appryggen.

Snabbstarten [Skicka telemetri från en enhet till en snabbstart för IoT-hubb](quickstart-send-telemetry-dotnet.md) och [Skicka meddelanden från molnet till enheten med IoT Hub-självstudien](iot-hub-csharp-csharp-c2d.md) visar den grundläggande funktionen för ioT-hubb- och moln-till-enhet-meddelanden i IoT Hub. Med självstudien [Konfigurera meddelanderoutning med IoT Hub](tutorial-routing.md) beskrivs ett sätt att lagra meddelanden från enhet till moln på ett tillförlitligt sätt i Microsoft Azure Blob-lagring. I vissa fall kan du dock inte enkelt mappa de data som dina enheter skickar till de relativt små meddelanden från enhet till moln som IoT Hub accepterar. Ett exempel:

* Stora filer som innehåller bilder

* Videoklipp

* Vibrationsdata som provtas med hög frekvens

* Någon form av förbehandlade data

Dessa filer är vanligtvis batchbearbetning i molnet med hjälp av verktyg som [Azure Data Factory](../data-factory/introduction.md) eller [Hadoop](../hdinsight/index.yml) stacken. När du behöver ladda upp filer från en enhet kan du fortfarande använda säkerhet och tillförlitlighet för IoT Hub.

I slutet av den här självstudien kör du två .NET-konsolappar:

* **Simuleradenhet**. Den här appen laddar upp en fil till lagring med hjälp av en SAS URI som tillhandahålls av din IoT-hubb. Det är en modifierad version av appen som skapats i [send cloud-to-device-meddelanden med IoT](iot-hub-csharp-csharp-c2d.md) Hub-självstudiekurs.

* **ReadFileUploadNotification**. Den här appen tar emot filuppladdningsmeddelanden från din IoT-hubb.

> [!NOTE]
> IoT Hub stöder många enhetsplattformar och -språk, inklusive C, Java, Python och Javascript, via Azure IoT-enhetSDK:er. Se [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) för steg-för-steg-instruktioner om hur du ansluter din enhet till Azure IoT Hub.

## <a name="prerequisites"></a>Krav

* Visual Studio

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en enhetsapp

I det här avsnittet ändrar du enhetsappen som du skapade i [Skicka meddelanden från molnet till enheten med IoT Hub](iot-hub-csharp-csharp-c2d.md) för att ta emot meddelanden från molnet till enheten från IoT-hubben.

1. Högerklicka på projektet **Simuleradenhet** i Visual Studio Solution Explorer och välj **Lägg till** > **befintligt objekt**. Hitta en bildfil och inkludera den i projektet. Den här självstudien `image.jpg`förutsätter att bilden heter .

1. Högerklicka på bilden och välj sedan **Egenskaper**. Kontrollera att **Kopiera till utdatakatalog** är inställt på **Kopiera alltid**.

    ![Visa var bildegenskapen ska uppdateras för Kopiera till utdatakatalog](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. Lägg till följande satser högst upp i filen i **Program.cs filen:**

    ```csharp
    using System.IO;
    ```

1. Lägg till följande metod i klassen **Program:**

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

    Metoden `UploadToBlobAsync` tar in filens filnamn och strömkälla som ska laddas upp och hanterar överföringen till lagring. Konsolappen visar hur tid det tar att ladda upp filen.

1. Lägg till följande rad i **huvudmetoden,** precis före: `Console.ReadLine()`

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> För enkelhetens skull implementerar den här självstudien inte någon policy för återförsök. I produktionskoden bör du implementera principer för återförsök, till exempel exponentiell backoff, som föreslås i [Transient felhantering](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en backend-tjänst för att ta emot meddelanden om filöverföring från IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md). För att ta emot meddelanden om filöverföring behöver tjänsten behörigheten **för tjänsten ansluter.** Som standard skapas varje IoT-hubb med en princip med namnet delad åtkomst med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Ta emot ett meddelande om filöverföring

I det här avsnittet skriver du en .NET-konsolapp som tar emot meddelanden om filöverföring från IoT Hub.

1. Välj **Arkiv** > **nytt** > **projekt**i den aktuella Visual Studio-lösningen . I **Skapa ett nytt projekt**väljer du Console App **(.NET Framework)** och väljer sedan **Nästa**.

1. Namnge projektet *ReadFileUploadNotification*. Under **Lösning**väljer du **Lägg till i lösning**. Välj **Skapa** för att skapa projektet.

    ![Konfigurera ReadFileUploadNotification-projektet i Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. Högerklicka på **ReadFileUploadNotification-projektet** i Solution Explorer och välj **Hantera NuGet-paket**.

1. I **NuGet Package Manager**väljer du **Bläddra**. Sök efter och välj **Microsoft.Azure.Devices**och välj sedan **Installera**.

    Det här steget hämtar, installerar och lägger till en referens till [Azure IoT-tjänsten SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Devices/) i **ReadFileUploadNotification-projektet.**

1. Lägg till följande programsats högst upp i filen i **Program.cs** filen för det här projektet:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt `{iot hub connection string}` platshållarvärdet med anslutningssträngen för IoT-hubb som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben:](#get-the-iot-hub-connection-string)

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Lägg till följande metod i klassen **Program:**

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

    Observera att det här mottagningsmönstret är detsamma som används för att ta emot meddelanden från enheten från enhetsappen.

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

1. Högerklicka på lösningen i Solutions Explorer och välj **Ange startprojekt**.

1. I **Common Properties** > **Startup Project**väljer du **Flera startprojekt**och väljer sedan åtgärden **Start** för **ReadFileUploadNotification** och **SimulatedDevice**. Spara ändringarna genom att välja **OK**.

1. Tryck på **F5**. Båda programmen ska starta. Du bör se uppladdningen i en konsolapp och meddelandet om uppladdning som tas emot av den andra konsolappen. Du kan använda [Azure-portalen](https://portal.azure.com/) eller Visual Studio Server Explorer för att kontrollera om den uppladdade filen finns i ditt Azure Storage-konto.

    ![Skärmbild som visar utdataskärmen](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder filöverföringsfunktionerna i IoT Hub för att förenkla filuppladdningar från enheter. Du kan fortsätta att utforska IoT Hub-funktioner och scenarier med följande artiklar:

* [Skapa ett IoT-nav programmässigt](iot-hub-rm-template-powershell.md)

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
