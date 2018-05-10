---
title: Moln till enhet meddelanden med Azure IoT Hub (.NET) | Microsoft Docs
description: Hur du skickar meddelanden moln till enhet till en enhet från en Azure IoT-hubb med Azure IoT-SDK för .NET. Du ändrar en enhetsapp för att ta emot meddelanden moln till enhet och ändra en backend-app för att skicka meddelanden moln till enhet.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: a31c05ed-6ec0-40f3-99ab-8fdd28b1a89a
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: b867976c637cdd4dd9b696382103c63f1af2e8b3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Skicka meddelanden från moln till enheten med IoT Hub (.NET)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduktion
Azure IoT Hub är en helt hanterad tjänst som hjälper till att aktivera tillförlitlig och säker dubbelriktad kommunikation mellan miljoner enheter och en lösning tillbaka avslutas. Den [Kom igång med IoT-hubb] kursen visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och code en enhetsapp som skickar meddelanden från enhet till moln.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här kursen bygger på [Kom igång med IoT-hubb]. Den visar hur till:

* Skicka meddelanden moln till enhet på en enhet till IoT-hubb från din lösningens serverdel.
* Ta emot meddelanden moln till enhet på en enhet.
* Begära leverans bekräftelse från din lösningens serverdel (*feedback*) för meddelanden som skickas till en enhet från IoT-hubb.

Du hittar mer information om moln till enhet meddelanden i den [IoT-hubb Utvecklarhandbok][IoT Hub developer guide - C2D].

I slutet av den här kursen kan du köra två .NET konsolappar:

* **SimulatedDevice**, en modifierad version av app som skapats i [Kom igång med IoT-hubb], som ansluter till din IoT-hubb och tar emot meddelanden moln till enhet.
* **SendCloudToDevice**, som skickar ett moln till enhet-meddelande till appen enheten via IoT-hubb och tar emot dess leverans bekräftelse.

> [!NOTE]
> IoT-hubben har SDK stöd för många vilka plattformar och språk (inklusive C, Java och Javascript) via [Azure IoT-enhet SDK]. Stegvisa instruktioner om hur du ansluter enheten till den här självstudiekursen kod och vanligtvis Azure IoT Hub finns i [IoT-hubb Utvecklarhandbok].
> 
> 

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

## <a name="receive-messages-in-the-device-app"></a>Ta emot meddelanden i appen enhet
I det här avsnittet ska du ändra appen för enheter som du skapade i [Kom igång med IoT-hubb] ta emot meddelanden moln till enhet från IoT-hubben.

1. I Visual Studio, i den **SimulatedDevice** projekt, Lägg till följande metod för att den **programmet** klass.
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
   
                await deviceClient.CompleteAsync(receivedMessage);
            }
        }
   
    Den `ReceiveAsync` metoden returnerar asynkront det mottagna meddelandet vid den tidpunkt då den tas emot av enheten. Den returnerar *null* efter en specifiable tidsgräns (i det här fallet används standardvärdet på en minut). När appen tar emot en *null*, den ska fortsätta att vänta på att nya meddelanden. Det här kravet är orsaken till det `if (receivedMessage == null) continue` rad.
   
    Anropet till `CompleteAsync()` meddelar IoT-hubb att meddelandet har bearbetats. Meddelandet kan tas bort på ett säkert sätt från enheten kön. Om något hände som förhindrade att appen enheten slutföra bearbetningen av meddelandet ger IoT-hubb den igen. Sedan är det viktigt att meddelandebehandling logiken i appen enheten är *idempotent*, så att ta emot samma meddelande flera gånger ger samma resultat. Ett program kan också tillfälligt Avbryt ett meddelande som resulterar i IoT-hubb behålla meddelandet i kön för framtida användning. Eller programmet kan avvisa ett meddelande som tar permanent bort meddelandet från kön. Mer information om livscykeln för moln-till-enhetsmeddelande finns i [IoT-hubb Utvecklarhandbok][IoT Hub developer guide - C2D].
   
   > [!NOTE]
   > När du använder HTTPS i stället för MQTT eller AMQP som transport, den `ReceiveAsync` metoden returnerar omedelbart. Stöds mönstret för moln till enhet meddelanden med HTTPS är periodvis anslutna enheter som kontrollerar för meddelanden sällan (mindre än var 25: e minut). Utfärda flera HTTPS får resultat i IoT-hubb begränsning av begäranden. Mer information om skillnaderna mellan MQTT, AMQP och HTTPS-stöd och IoT-hubb begränsning finns på [IoT-hubb Utvecklarhandbok][IoT Hub developer guide - C2D].
   > 
   > 
2. Lägg till följande metod i den **Main** metod, precis innan den `Console.ReadLine()` rad:
   
        ReceiveC2dAsync();

> [!NOTE]
> Den här självstudiekursen implementerar inte några återförsöksprincip sätt. I produktionskod, bör du implementera försök principer (till exempel exponentiell backoff), enligt förslaget i MSDN-artikel [hantering av tillfälliga fel].
> 
> 

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande moln till enhet
I det här avsnittet kan du skriva en .NET-konsolapp som skickar moln till enhet meddelanden till appen med enheten.

1. Skapa ett Visual C#-Skrivbordsapprojekt-projekt i den aktuella Visual Studio-lösningen med hjälp av den **konsolprogram** projektmall. Namnge projektet **SendCloudToDevice**.
   
    ![Nytt projekt i Visual Studio][20]
2. Högerklicka på lösningen i Solution Explorer och klicka sedan på **hantera NuGet-paket för lösningen...** . 
   
    Den här åtgärden öppnar den **hantera NuGet-paket** fönster.
3. Sök efter **Microsoft.Azure.Devices**, klickar du på **installera**, och Godkänn användningsvillkoren. 
   
    Detta hämtar, installerar och lägger till en referens till den [Azure IoT service SDK NuGet-paketet].

4. Lägg till följande `using`-instruktion högst upp i filen **Program.cs**:
   
        using Microsoft.Azure.Devices;
5. Lägg till följande fält i klassen **Program**. Ersätt platshållaren värdet med anslutningssträngen från IoT-hubb [Kom igång med IoT-hubb]:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Lägg till följande metod i klassen **Program**:
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    Den här metoden skickar ett nytt meddelande moln till enhet till enheten med ID, `myFirstDevice`. Ändra den här parametern endast om den ändrats från den som används i [Kom igång med IoT-hubb].
7. Slutligen lägger du till följande rader till **Main**-metoden:
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. I Visual Studio högerklickar du på din lösning och välj **ange Startprojekt...** . Välj **flera Startprojekt**och välj den **starta** åtgärd för **ReadDeviceToCloudMessages**, **SimulatedDevice**, och **SendCloudToDevice**.
9. Tryck på **F5**. Alla tre program ska starta. Välj den **SendCloudToDevice** windows och tryck på **RETUR**. Du bör se meddelandet tas emot av enheten appen.
   
   ![Appen tar emot meddelandet][21]

## <a name="receive-delivery-feedback"></a>Ta emot leverans feedback
Det är möjligt att begäran leverans (eller sista) bekräftelser från IoT-hubb för varje moln till enhet-meddelande. Det här alternativet kan lösningens serverdel enkelt informera logik för återförsök eller ersättning. Mer information om moln till enhet feedback finns i [IoT-hubb Utvecklarhandbok][IoT Hub developer guide - C2D].

I det här avsnittet kan du ändra den **SendCloudToDevice** app att begära feedback och tar emot det från IoT-hubb.

1. I Visual Studio, i den **SendCloudToDevice** projekt, Lägg till följande metod för att den **programmet** klass.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();
   
                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }
   
    Observera att ta emot mönstret är det samma som används för att ta emot meddelanden moln till enhet från appen enhet.
2. Lägg till följande metod i den **Main** metod, rätt efter den `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` rad:
   
        ReceiveFeedbackAsync();
3. Om du vill begära feedback för leverans av meddelandet moln till enhet, måste du ange en egenskap i den **SendCloudToDeviceMessageAsync** metod. Lägg till följande rad direkt efter den `var commandMessage = new Message(...);` rad:
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. Kör appar genom att trycka på **F5**. Du bör se alla tre program startar. Välj den **SendCloudToDevice** windows och tryck på **RETUR**. Du bör se meddelandet tas emot av enheten appen och efter några sekunder feedback meddelandet tas emot av din **SendCloudToDevice** program.
   
   ![Appen tar emot meddelandet][22]

> [!NOTE]
> Den här självstudiekursen implementerar inte några återförsöksprincip sätt. I produktionskod, bör du implementera försök principer (till exempel exponentiell backoff), enligt förslaget i MSDN-artikel [hantering av tillfälliga fel].
> 
> 

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen beskrivs hur du skickar och tar emot meddelanden moln till enhet. 

Exempel på fullständiga lösningar för slutpunkt till slutpunkt med IoT-hubb finns [Azure IoT-Fjärrövervaknings lösningsaccelerator].

Mer information om hur du utvecklar lösningar med IoT-hubb finns i [IoT-hubb Utvecklarhandbok].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT service SDK NuGet-paketet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[hantering av tillfälliga fel]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md

[IoT-hubb Utvecklarhandbok]: iot-hub-devguide.md
[Kom igång med IoT-hubb]: iot-hub-csharp-csharp-getstarted.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure IoT-Fjärrövervaknings lösningsaccelerator]: https://docs.microsoft.com/azure/iot-suite/
[Azure IoT-enhet SDK]: iot-hub-devguide-sdks.md