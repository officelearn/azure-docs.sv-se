---
title: Moln-till-enhet-meddelanden med Azure IoT Hub (.NET) | Microsoft-dokument
description: Skicka meddelanden från molnet till enheten till en enhet från en Azure IoT-hubb med Azure IoT-SDK:er för .NET. Du ändrar en enhetsapp för att ta emot meddelanden från molnet till enheten och ändrar en backend-app för att skicka meddelanden från molnet till enheten.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 7805b9b3f000b2bc2e45272ab9ff469d5711e581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110210"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Skicka meddelanden från molnet till din enhet med IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals enheter och en lösningsbakdel. Snabbstarten [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md) visar hur du skapar en IoT-hubb, etablerar en enhetsidentitet i den och kodar en enhetsapp som skickar meddelanden från enhet till moln.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md). Den visar hur du utför följande uppgifter:

* Från din lösningsbaksida skickar du meddelanden från molnet till enheten till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från din lösningsbaksida, begär leveransbekräftelse *(feedback)* för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om meddelanden från molnet till enheten i [D2C- och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två .NET-konsolappar.

* **Simuleradenhet**. Den här appen ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten. Den här appen är en modifierad version av appen som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Den här appen skickar ett meddelande från molnet till enheten till enhetsappen via IoT Hub och tar sedan emot leveransbekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhetsplattformar och -språk, inklusive C, Java, Python och Javascript, via [Azure IoT-enhetSDK: er](iot-hub-devguide-sdks.md). Stegvisa instruktioner om hur du ansluter enheten till den här självstudiens kod och i allmänhet till Azure IoT Hub finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Krav

* Visual Studio

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Ta emot meddelanden i enhetsappen

I det här avsnittet ändrar du enhetsappen som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md) för att ta emot meddelanden från molnet till enheten från IoT-hubben.

1. Lägg till följande **SimulatedDevice** metod i klassen **Program** i Visual Studio i Visual Studio.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Lägg till följande metod i **huvudmetoden,** precis före raden: `Console.ReadLine()`

   ```csharp
   ReceiveC2dAsync();
   ```

Metoden `ReceiveAsync` returnerar asynkront det mottagna meddelandet när det tas emot av enheten. Den returnerar *null* efter en specifierbar timeout-period. I det här exemplet används standardvärdet på en minut. När appen får ett *null*bör den fortsätta att vänta på nya meddelanden. Detta krav är orsaken `if (receivedMessage == null) continue` till raden.

Anropet `CompleteAsync()` för att meddela IoT Hub att meddelandet har bearbetats. Meddelandet kan tas bort från enhetskön på ett säkert sätt. Om något hände som hindrade enhetsappen från att slutföra bearbetningen av meddelandet levererar IoT Hub det igen. Meddelandebearbetningslogiken i enhetsappen måste vara *idempotent*, så att samma resultat ger samma resultat om du tar emot samma meddelande flera gånger.

Ett program kan också tillfälligt överge ett meddelande, vilket resulterar i att IoT-hubben behåller meddelandet i kön för framtida förbrukning. Eller så kan programmet avvisa ett meddelande som permanent tar bort meddelandet från kön. Mer information om livscykeln för meddelanden mellan molnet och enheten finns i [D2C- och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > När du använder HTTPS i stället för MQTT `ReceiveAsync` eller AMQP som transport returneras metoden omedelbart. Mönstret som stöds för meddelanden från molnet till enheten med HTTPS är periodvis anslutna enheter som söker efter meddelanden sällan (mindre än var 25:e minut). Att utfärda mer HTTPS tar emot resultat i IoT Hub begränsning av begäranden. Mer information om skillnaderna mellan stöd för MQTT, AMQP och HTTPS och Begränsning av IoT Hub finns i [D2C- och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en backend-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md). Om du vill skicka meddelanden från molnet till enheten behöver tjänsten behörigheten **för tjänsten ansluta.** Som standard skapas varje IoT-hubb med en princip med namnet delad åtkomst med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från molnet till enheten

Nu skriver du en .NET-konsolapp som skickar meddelanden från molnet till enheten till enhetsappen.

1. Välj **Arkiv** > **nytt** > **projekt**i den aktuella Visual Studio-lösningen . I **Skapa ett nytt projekt**väljer du Console App **(.NET Framework)** och väljer sedan **Nästa**.

1. Namnge projektet *SendCloudToDevice*. Under **Lösning**väljer du **Lägg till i lösning** och accepterar den senaste versionen av .NET Framework. Välj **Skapa** för att skapa projektet.

   ![Konfigurera ett nytt projekt i Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. Högerklicka på den nya lösningen i Solution Explorer och välj sedan **Hantera NuGet-paket**.

1. I **Hantera NuGet-paket**väljer du **Bläddra**och söker sedan efter och väljer **Microsoft.Azure.Devices**. Välj **Installera**.

   Det här steget hämtar, installerar och lägger till en referens till [Azure IoT-tjänsten SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. Lägg till `using` följande sats högst upp i **Program.cs-filen.**

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med anslutningssträngen för IoT-hubb som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Lägg till följande metod i klassen **Program**. Ange enhetsnamnet till det du använde när du definierade enheten i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Den här metoden skickar ett nytt meddelande från molnet till `myFirstDevice`enheten med ID:et . Ändra den här parametern endast om du har ändrat den från den som används i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md).

1. Lägg slutligen till följande **Main** rader i huvudmetoden.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. Högerklicka på lösningen i Solutions Explorer och välj **Ange startprojekt**.

1. I **Common Properties** > **Startup Project**väljer du Flera **startprojekt**och väljer sedan **åtgärden Start** för **ReadDeviceToCloudMessages**, **SimulatedDevice**och **SendCloudToDevice**. Spara ändringarna genom att välja **OK**.

1. Tryck på **F5**. Alla tre ansökningarna ska starta. Markera fönstren **SendCloudToDevice** och tryck på **Retur**. Du bör se meddelandet som tas emot av enhetsappen.

   ![Meddelande om appmottagning](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Få feedback på leverans

Det är möjligt att begära leverans-(eller utgångsdatum) bekräftelser från IoT Hub för varje moln-till-enhet-meddelande. Med det här alternativet kan lösningens baksida enkelt informera om återförsök eller kompensationslogik. Mer information om feedback från molnet till enheten finns i [D2C- och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

I det här avsnittet ändrar du **SendCloudToDevice-appen** för att begära feedback och ta emot den från IoT-hubben.

1. Lägg till följande metod i klassen **Program** i Visual Studio i **Visual** Studio.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Observera att det här mottagningsmönstret är detsamma som används för att ta emot meddelanden från enheten från enhetsappen.

1. Lägg till följande rad i **huvudmetoden** direkt efter `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Om du vill begära feedback för leverans av ditt meddelande från molnet till enheten måste du ange en egenskap i metoden **SendCloudToDeviceMessageAsync.** Lägg till följande rad, `var commandMessage = new Message(...);` direkt efter raden.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Kör apparna genom att trycka på **F5**. Du bör se alla tre program start. Markera fönstren **SendCloudToDevice** och tryck på **Retur**. Du bör se meddelandet som tas emot av enhetsappen och efter några sekunder det feedbackmeddelande som tas emot av ditt **SendCloudToDevice-program.**

   ![Meddelande om appmottagning](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> För enkelhetens skull implementerar den här självstudien inte någon princip för återförsök. I produktionskoden bör du implementera principer för återförsök, till exempel exponentiell backoff, som föreslås i [Transient felhantering](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Nästa steg

I det här meddelandet fick du lära dig hur du skickar och ta emot meddelanden från molnet till enheten.

Information om hur du ser exempel på kompletta heltäckande lösningar som använder IoT Hub finns i [Azure IoT Remote Monitoring solution accelerator](https://docs.microsoft.com/azure/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).
