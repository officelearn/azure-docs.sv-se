---
title: Meddelanden från molnet till enheten med Azure IoT Hub (.NET) | Microsoft Docs
description: Hur du skickar meddelanden från moln till enhet till en enhet från Azure IoT hub med Azure IoT SDK för .NET. Du kan ändra en enhetsapp för att ta emot meddelanden från moln till enhet och ändra en backend-app för att skicka meddelanden från moln-till-enhet.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: e744ffe9eb6e58c9226802f0196cb5acf1427bdf
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047727"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Skicka meddelanden från molnet till enheten med IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduktion

Azure IoT Hub är en helt hanterad tjänst som hjälper dig att aktivera pålitlig och säker dubbelriktad kommunikation mellan miljontals enheter och tillhandahåller serverdelen. [Skicka telemetri från en enhet till en IoT-hubb... ](quickstart-send-telemetry-dotnet.md) visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och koda en enhetsapp som skickar meddelanden från enheten till molnet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på snabbstarten [skickar telemetri från en enhet till IoT hub... ](quickstart-send-telemetry-dotnet.md). Den visar hur du utför följande steg:

* Skicka meddelanden från moln till enhet på en enhet via IoT Hub från lösningens backend-servrar.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från lösningens backend-servrar, begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om meddelanden från molnet till enheten i [D2C och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här kursen kan du köra två .NET-konsolappar:

* **SimulatedDevice**, en modifierad version av appen som skapats i [skickar telemetri från en enhet till IoT hub... ](quickstart-send-telemetry-dotnet.md), som ansluter till din IoT hub och tar emot meddelanden från molnet till enheten.

* **SendCloudToDevice**, som skickar ett moln-till-enhet-meddelande till app för enheter via IoT Hub och sedan ta emot dess leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via [SDK: er för Azure IoT-enheter](iot-hub-devguide-sdks.md). Stegvisa instruktioner om hur du ansluter enheten till den här självstudien kod och vanligen på Azure IoT Hub finns i den [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).
> 

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2017

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

## <a name="receive-messages-in-the-device-app"></a>Ta emot meddelanden i enhetsappen

I det här avsnittet ska du ändra app för enheter som du skapade i [skickar telemetri från en enhet till IoT hub... ](quickstart-send-telemetry-dotnet.md) att ta emot meddelanden från moln till enhet från IoT hub.

1. I Visual Studio i den **SimulatedDevice** projektet, Lägg till följande metod för att den **programmet** klass.

   ```csharp   
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

   Den `ReceiveAsync` returnerar metoden asynkront det mottagna meddelandet vid den tidpunkt då den tas emot av enheten. Den returnerar *null* efter en specifiable tidsgränsen (i det här fallet används standardvärdet på en minut). När appen tar emot en *null*, det bör fortsätta att vänta tills nya meddelanden. Det här kravet är orsaken till den `if (receivedMessage == null) continue` rad.
   
    Anropet till `CompleteAsync()` meddelar IoT Hub att meddelandet har behandlats. Meddelandet kan tas bort på ett säkert sätt från enheten kön. Om något hände som förhindrade att enhetsappen du har slutfört bearbetningen av meddelandet IoT-hubb levererar den igen. Sedan är det viktigt meddelandet bearbetning av logik i enhetsappen är *idempotenta*, så att ta emot samma meddelande flera gånger ger samma resultat. 
    
    Ett program kan också tillfälligt att avbryta pågående ett meddelande som leder till IoT hub behåller meddelandet i kön för framtida användning. Eller programmet kan avvisa ett meddelande som tar permanent bort meddelandet från kön. Läs mer om livscykeln för moln-till-enhetsmeddelande [D2C och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).
   
   > [!NOTE]
   > När du använder HTTPS i stället för MQTT eller AMQP som transport, den `ReceiveAsync` metoden returnerar omedelbart. Mönstret stöds för meddelanden från molnet till enheten med HTTPS är periodvis anslutna enheter som kontrollerar meddelanden sällan (mindre än var 25: e minut). Utfärda mer HTTPS tar emot resultaten i IoT Hub begränsning av förfrågningar. Mer information om skillnaderna mellan MQTT-, AMQP- och HTTPS-stöd och IoT Hub-begränsning finns [D2C och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).
   > 
   > 
2. Lägg till följande metod i den **Main** metod, precis innan den `Console.ReadLine()` rad:
   
   ``` csharp   
   ReceiveC2dAsync();
   ```

## <a name="send-a-cloud-to-device-message"></a>Skicka ett moln-till-enhet-meddelande
I det här avsnittet ska skriva du en .NET-konsolapp som skickar meddelanden från molnet till enheten till enhetsappen.

1. Skapa ett Visual C# Desktop-App-projekt i den aktuella lösningen i Visual Studio med hjälp av den **konsolprogram** projektmall. Ge projektet namnet **SendCloudToDevice**.
   
   ![Nytt projekt i Visual Studio](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. Högerklicka på lösningen i Solution Explorer och klicka sedan på **hantera NuGet-paket för lösningen...** . 
   
    Den här åtgärden öppnar den **hantera NuGet-paket** fönster.

3. Sök efter **Microsoft.Azure.Devices**, klickar du på **installera**, och Godkänn användningsvillkoren. 
   
    Detta hämtar, installerar och lägger till en referens till den [Azure IoT service SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

4. Lägg till följande `using`-instruktion högst upp i filen **Program.cs**:

   ``` csharp   
   using Microsoft.Azure.Devices;
   ```

5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT hub-anslutningssträngen från [skickar telemetri från en enhet till IoT hub... ](quickstart-send-telemetry-dotnet.md):

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Lägg till följande metod i klassen **Program**:
   
   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new 
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Den här metoden skickar ett nytt moln-till-enhet-meddelande till enheten med ID, `myFirstDevice`. Ändra den här parametern endast om du har ändrat den från den som används i [skickar telemetri från en enhet till IoT hub... ](quickstart-send-telemetry-dotnet.md).

7. Slutligen lägger du till följande rader till **Main**-metoden:

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. Från Visual Studio högerklickar du på din lösning och välj **ange Startprojekt...** . Välj **flera Startprojekt**och välj sedan den **starta** åtgärd för **ReadDeviceToCloudMessages**, **SimulatedDevice**, och **SendCloudToDevice**.

9. Tryck på **F5**. Alla tre program ska börja. Välj den **SendCloudToDevice** windows och tryck på **RETUR**. Du bör se meddelandet tas emot av app för enheter.
   
   ![App mottagande meddelande](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Ta emot leveransen feedback

Det är möjligt att begäran leverans (eller upphör att gälla) bekräftelser från IoT Hub för varje moln-till-enhet-meddelande. Det här alternativet kan lösningens serverdel underrättar enkelt logik för återförsök eller kompensation. Läs mer om molnet till enhet feedback [D2C och C2D-meddelanden med IoT Hub](iot-hub-devguide-messaging.md).

I det här avsnittet ska du ändra den **SendCloudToDevice** app att begära feedback och tar emot det från IoT hub.

1. I Visual Studio i den **SendCloudToDevice** projektet, Lägg till följande metod för att den **programmet** klass.

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

    Observera att det här mönstret receive är samma som används för att ta emot meddelanden från moln till enhet från enhetsappen.

2. Lägg till följande metod i den **Main** metoden rätt efter den `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` rad:
   
   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. Om du vill begära feedback för leverans av moln-till-enhet-meddelande, måste du ange en egenskap i den **SendCloudToDeviceMessageAsync** metod. Lägg till följande rad direkt efter den `var commandMessage = new Message(...);` rad:
   
   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Kör appar genom att trycka på **F5**. Du bör se alla tre program startar. Välj den **SendCloudToDevice** windows och tryck på **RETUR**. Du bör se meddelandet tas emot av app för enheter och efter några sekunder feedback meddelandet tas emot av din **SendCloudToDevice** program.
   
   ![App mottagande meddelande](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Den här självstudien implementerar inte någon återförsöksprincip sätt. I produktionskoden bör du implementera principer för omförsök (till exempel exponentiell backoff) vilket rekommenderas i MSDN-artikeln [hantering av tillfälliga fel](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx).
> 

## <a name="next-steps"></a>Nästa steg

I den här anvisningen lärde du dig att skicka och ta emot meddelanden från molnet till enheten. 

Exempel på fullständiga lösningar för slutpunkt till slutpunkt som använder IoT Hub finns i [Azure IoT lösningsacceleratorn för fjärrövervakning](https://docs.microsoft.com/azure/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns det [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).