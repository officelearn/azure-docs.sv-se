---
title: Meddelanden från moln till enhet med Azure IoT Hub (.NET) | Microsoft Docs
description: 'Skicka meddelanden från moln till enhet till en enhet från en Azure IoT Hub med Azure IoT-SDK: er för .NET. Du ändrar en enhets app för att ta emot meddelanden från molnet till enheten och ändra en backend-app för att skicka meddelanden från molnet till enheten.'
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 5a7cb4ecde599a76b2d42cf874420d9cbcfda3d5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402642"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Skicka meddelanden från molnet till din enhet med IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduktion

Azure IoT Hub är en fullständigt hanterad tjänst som hjälper till att möjliggöra tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals enheter och Server delen av lösningen. Snabb starten [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-dotnet.md) visar hur du skapar en IoT-hubb, etablerar en enhets identitet i den och kodar en enhets app som skickar enhet till molnet-meddelanden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md). Det visar hur du utför följande steg:

* Skicka meddelanden från moln till enhet från Server delen av lösningen till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från Server delen av lösningen kan du begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om moln-till-enhet-meddelanden i [D2C-och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två .NET-konsol program.

* **SimulatedDevice**, en modifierad version av appen som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **SendCloudToDevice**, som skickar ett meddelande från moln till enhet till appen device via IoT Hub, och sedan får leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhets plattformar och språk (inklusive C, Java och Java Script) via SDK: er för [Azure IoT-enheter](iot-hub-devguide-sdks.md). Stegvisa instruktioner för hur du ansluter din enhet till den här själv studie kursen och i allmänhet till Azure IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).
>

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="receive-messages-in-the-device-app"></a>Ta emot meddelanden i enhets appen

I det här avsnittet ändrar du enhets appen som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md) för att ta emot meddelanden från molnet till enheten från IoT Hub.

1. I Visual Studio, i **SimulatedDevice** -projektet, lägger du till följande metod i **program** klassen.

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

   `ReceiveAsync` Metoden returnerar det mottagna meddelandet asynkront vid den tidpunkt då enheten tas emot av enheten. Den returnerar *Null* efter en specificerad timeout-period (i det här fallet används standardvärdet på en minut). När appen får ett *Null*-värde ska den fortsätta att vänta på nya meddelanden. Det här kravet är orsaken till `if (receivedMessage == null) continue` raden.

    Anropet till `CompleteAsync()` meddelar IoT Hub att meddelandet har bearbetats. Meddelandet kan tas bort på ett säkert sätt från enhets kön. Om något hände som hindrade appen från att slutföra bearbetningen av meddelandet, IoT Hub leverera det igen. Det är sedan viktigt att meddelande bearbetnings logiken i enhets appen är *idempotenta*, så att den tar emot samma meddelande flera gånger, vilket ger samma resultat. 

    Ett program kan också tillfälligt överge ett meddelande, vilket resulterar i IoT Hub som bevarar meddelandet i kön för framtida konsumtion. Eller också kan programmet avvisa ett meddelande som permanent tar bort meddelandet från kön. Mer information om livs cykeln för moln-till-enhet-meddelanden finns i [D2C-och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > När du `ReceiveAsync` använder https i stället för MQTT eller AMQP som transport, returnerar metoden omedelbart. Det mönster som stöds för meddelanden från moln till enhet med HTTPS är tillfälligt anslutna enheter som söker efter meddelanden som inte förekommer ofta (mindre än var 25: e minut). Om du skickar mer HTTPS får du resultat i IoT Hub begränsning av begär Anden. Mer information om skillnaderna mellan MQTT, AMQP och HTTPS-stöd och IoT Hub begränsning finns i [D2C och C2D Messaging med IoT Hub](iot-hub-devguide-messaging.md).
   >

2. Lägg till följande metod i **main** -metoden, precis före `Console.ReadLine()` raden:

   ```csharp
   ReceiveC2dAsync();
   ```

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en backend-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md). För att skicka meddelanden från molnet till enheten måste tjänsten ha behörighet för **tjänst anslutning** . Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från moln till enhet

Nu skriver du en .NET-konsol app som skickar meddelanden från molnet till enheten till Device-appen.

1. I den aktuella Visual Studio-lösningen högerklickar du på lösningen och väljer Lägg till > nytt projekt. Välj **Windows-skrivbordet** och sedan **konsol program (.NET Framework)** . Namnge projektet **SendCloudToDevice** och välj den senaste versionen av .NET Framework och välj sedan **OK** för att skapa projektet.

   ![Nytt projekt i Visual Studio](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. I Solution Explorer högerklickar du på lösningen och klickar sedan på **Hantera NuGet-paket för lösning...** .

   Den här åtgärden öppnar fönstret **Hantera NuGet-paket** .

3. Sök efter **Microsoft. Azure.** Devices, Välj fliken Bläddra. När du hittar paketet klickar du på **Installera**och godkänner användnings villkoren.

   Detta laddar ned, installerar och lägger till en referens till [Azure IoT service SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

4. Lägg till följande `using` -instruktion högst upp i **program.cs** -filen.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

5. Lägg till följande fält i klassen **Program**. Ersätt placeholder-värdet med IoT Hub-anslutningssträngen som du kopierade tidigare i [Hämta IoT Hub](#get-the-iot-hub-connection-string)-anslutningssträngen.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Lägg till följande metod i klassen **Program**. Ange enhets namnet på det du använde när du definierade enheten i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Den här metoden skickar ett nytt meddelande från moln till enhet till enheten med ID: t `myFirstDevice`. Ändra bara den här parametern om du har ändrat den från den som används i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-dotnet.md).

7. Lägg slutligen till följande rader i **main** -metoden.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. Högerklicka på din lösning i Visual Studio och välj **Ange start projekt...** . Välj **flera start projekt**och välj sedan **Start** åtgärden för **ReadDeviceToCloudMessages**, **SimulatedDevice**och **SendCloudToDevice**.

9. Tryck på **F5**. Alla tre programmen bör starta. Välj **SendCloudToDevice** -fönster och tryck på **RETUR**. Du bör se meddelandet som tas emot av Device-appen.

   ![Appen tar emot meddelande](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Få feedback om leverans

Det går att begära leverans (eller förfallo datum) bekräftelser från IoT Hub för varje moln-till-enhet-meddelande. Med det här alternativet kan lösningens Server del enkelt informera om återförsök eller kompensations logik. Mer information om feedback från moln till enhet finns i [D2C och C2D Messaging med IoT Hub](iot-hub-devguide-messaging.md).

I det här avsnittet ändrar du **SendCloudToDevice** -appen för att begära feedback och tar emot den från IoT-hubben.

1. I Visual Studio, i **SendCloudToDevice** -projektet, lägger du till följande metod i **program** klassen.

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

    Observera att det här mottagnings mönstret är samma som används för att ta emot meddelanden från molnet till enheten från Device-appen.

2. Lägg till följande metod i **main** -metoden, direkt efter `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` raden.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. Om du vill begära feedback för leverans av ditt meddelande från molnet till enheten måste du ange en egenskap i **SendCloudToDeviceMessageAsync** -metoden. Lägg till följande rad, direkt efter `var commandMessage = new Message(...);` raden.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Kör apparna genom att trycka på **F5**. Du bör se alla tre programmens start. Välj **SendCloudToDevice** -fönster och tryck på **RETUR**. Du bör se meddelandet som tas emot av Device-appen och efter några sekunder får du ett feedback-meddelande som tas emot av ditt **SendCloudToDevice** -program.

   ![Appen tar emot meddelande](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> För enkelhetens skull implementerar den här självstudien inte någon princip för återförsök. I produktions koden bör du implementera principer för omförsök (till exempel exponentiell backoff), enligt förslag i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Nästa steg

I den här instruktionen har du lärt dig hur du skickar och tar emot meddelanden från molnet till enheten.

Exempel på kompletta lösningar från slut punkt till slut punkt som använder IoT Hub finns i [Azure IoT Remote Monitoring Solution Accelerator](https://docs.microsoft.com/azure/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).
