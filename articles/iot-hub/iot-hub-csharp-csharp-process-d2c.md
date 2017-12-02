---
title: Vidarebefordra meddelanden med Azure IoT Hub (.Net) | Microsoft Docs
description: "Så här bearbeta meddelanden för Azure IoT Hub-enhet till moln genom att använda regler för Routning och anpassade slutpunkter för att skicka meddelanden till andra backend-tjänster."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: d8fed08aa22577574b30b360ec164daf592ed456
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="routing-messages-with-iot-hub-net"></a>Vidarebefordra meddelanden med IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Den här kursen bygger på den [Kom igång med IoT-hubb] kursen. Självstudier:

* Visar hur du använder regler för routning för att skicka meddelanden från enhet till moln i ett enkelt och konfigurationsbaserade sätt.
* Visar hur du isolera interaktiva meddelanden som kräver omedelbar åtgärd från lösningens serverdel för vidare bearbetning. En enhet kan till exempel skicka larmmeddelandet som utlöser lägga till ett ärende i ett CRM-system. Däremot-datapunkt meddelanden, till exempel temperatur telemetri, mata in en analytics-motorn.

I slutet av den här kursen kan köra du tre .NET konsolappar:

* **SimulatedDevice**, en modifierad version av app som skapats i den [Kom igång med IoT-hubb] självstudiekursen skickar meddelanden från enhet till moln datapunkt varje sekund och interaktiva enhet till moln meddelanden var 10: e sekund.
* **ReadDeviceToCloudMessages** som visar den icke-kritiska telemetri som skickats av din enhetsapp.
* **ReadCriticalQueue** tar bort viktiga meddelanden som skickas av din enhetsapp från en Service Bus-kö. Den här kön är kopplad till IoT-hubben.

> [!NOTE]
> IoT-hubben har SDK stöd för många vilka plattformar och språk, inklusive C, Java och JavaScript. Mer information om hur du ersätter den simulerade enheten i den här självstudiekursen med en fysisk enhet, finns det [Azure IoT Developer Center].

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. <br/>Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter.

Vi rekommenderar också läsa mer om [Azure Storage] och [Azure Service Bus].

## <a name="send-interactive-messages"></a>Skicka interaktiva meddelanden

Ändra appen för enheter som du skapade i den [Kom igång med IoT-hubb] kursen att ibland skicka interaktiva meddelanden.

I Visual Studio, i den **SimulatedDevice** projektet genom att ersätta den `SendDeviceToCloudMessagesAsync` metoden med följande kod:

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            if (rand.NextDouble() > 0.5)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else 
            {
                messageString = "This is a storage message";
                levelValue = "storage";
            }
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

Den här metoden lägger slumpmässigt till egenskapen `"level": "critical"` och `"level": "storage"` på meddelanden som skickas av enheten, som simulerar ett meddelande som kräver omedelbar åtgärd som programmet backend- eller som behöver lagras permanent. Programmet Överför denna information i egenskaperna för meddelandet i stället för i meddelandetexten, så att IoT-hubb kan vidarebefordra meddelandet till rätt meddelandets mål.

> [!NOTE]
> Du kan använda meddelandeegenskaper skicka meddelanden för olika scenarier, inklusive kall sökväg bearbetning, förutom hot sökväg exemplet som visas här.

> [!NOTE]
> Vi rekommenderar starkt att du använder en återförsöksprincip som exponentiell backoff enligt förslaget i MSDN-artikel [hantering av tillfälliga fel].

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>Skicka meddelanden till en kö i din IoT-hubb

I det här avsnittet får du:

* Skapa en Service Bus-kö.
* Anslut den till din IoT-hubb.
* Konfigurera din IoT-hubb för att skicka meddelanden till kön baserat på förekomsten av en egenskap i meddelandet.

Mer information om hur du bearbeta meddelanden från Service Bus-köer finns [Kom igång med köer][Service Bus queue].

1. Skapa en Service Bus-kö som beskrivs i [Kom igång med köer][Service Bus queue]. Kön måste vara i samma prenumeration och region som din IoT-hubb. Anteckna namnet på namnområdet och kön.

    > [!NOTE]
    > Service Bus-köer och ämnen som används som IoT-hubbslutpunkter inte får ha **sessioner** eller **dubblettidentifiering** aktiverat. Om något av dessa alternativ är aktiverade ändpunkt som **inte åtkomlig** i Azure-portalen.

2. Öppna din IoT-hubb i Azure-portalen och klicka på **slutpunkter**.
    
    ![Slutpunkter i IoT-hubb][30]

3. I den **slutpunkter** bladet, klickar du på **Lägg till** längst upp för att lägga till kön till din IoT-hubb. Namnet på slutpunkten **CriticalQueue** och markera med hjälp av listrutorna **Service Bus-kö**, Service Bus-namnrymd som kön finns och namnet på din kö. När du är klar klickar du på **spara** längst ned.
    
    ![Lägga till en slutpunkt][31]
    
4. Klicka på **vägar** i din IoT-hubb. Klicka på **Lägg till** längst upp på bladet för att skapa en regel för vidarebefordran som skickar meddelanden till kön du just lagt till. Välj **DeviceTelemetry** som datakällan. Ange `level="critical"` som villkor och välj den kö som du just har lagt till som en anpassad slutpunkt som routning regeln slutpunkt. När du är klar klickar du på **spara** längst ned.
    
    ![Lägga till en väg][32]
    
    Kontrollera återställningsplats vägen är inställd på **på**. Det här värdet är standardkonfigurationen för en IoT-hubb.
    
    ![Fallback-väg][33]

## <a name="read-from-the-queue-endpoint"></a>Läsa från kön slutpunkten

I det här avsnittet läsa meddelanden från kön slutpunkten.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Console App (.NET Framework)**. Namnge projektet **ReadCriticalQueue**.

2. I Solution Explorer högerklickar du på den **ReadCriticalQueue** projektet och klicka sedan på **hantera NuGet-paket**. Den här åtgärden visar den **NuGet Package Manager** fönster.

3. Sök efter **WindowsAzure.ServiceBus**, klickar du på **installera**, och Godkänn användningsvillkoren. Den här åtgärden hämtar, installerar och lägger till en referens till Azure Service Bus med dess beroenden.

4. Lägg till följande **med** instruktioner överst i den **Program.cs** fil:
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Slutligen lägger du till följande rader till den **Main** metod. Ersätt anslutningssträngen med **lyssna** behörigheter för kön:
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Köra programmen
Du är nu redo att köra programmen.

1. Högerklicka på lösningen i Visual Studio i Solution Explorer och markera **ange Startprojekt**. Välj **flera Startprojekt**och välj **starta** åtgärden för den **ReadDeviceToCloudMessages**, **SimulatedDevice**, och **ReadCriticalQueue** projekt.
2. Tryck på **F5** för att starta konsolen de tre appar. Den **ReadDeviceToCloudMessages** appen har endast icke-kritiska meddelanden som skickas från den **SimulatedDevice** program, och **ReadCriticalQueue** appen har endast kritiska meddelanden.
   
   ![Tre konsolappar][50]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Valfritt) Lägg till lagringsbehållaren i IoT-hubb och flöde meddelanden till den

I det här avsnittet, skapa ett lagringskonto, ansluta till din IoT-hubb och konfigurera din IoT-hubb för att skicka meddelanden till kontot utifrån förekomsten av en egenskap i meddelandet. Mer information om hur du hanterar lagring finns [komma igång med Azure Storage][Azure Storage].

 > [!NOTE]
   > Om du inte är begränsad till en **Endpoint**, du kan konfigurera den **StorageContainer** förutom den **CriticalQueue** och kör både simulatneously.

1. Skapa ett lagringskonto som beskrivs i [Azure Storage-dokumentation] [lnk-storage]. Anteckna namnet på kontot.

2. Öppna din IoT-hubb i Azure-portalen och klicka på **slutpunkter**.

3. I den **slutpunkter** bladet väljer den **CriticalQueue** slutpunkt och klicka på **ta bort**. Klicka på **Ja**, och klicka sedan på **Lägg till**. Namnet på slutpunkten **StorageContainer** och markera med hjälp av listrutorna **Azure Storage-behållare**, och skapa en **lagringskonto** och en **lagring behållaren**.  Anteckna namnen.  När du är klar klickar du på **OK** längst ned. 

 > [!NOTE]
   > Om du inte är begränsad till en **Endpoint**, du behöver inte ta bort den **CriticalQueue**.

4. Klicka på **vägar** i din IoT-hubb. Klicka på **Lägg till** längst upp på bladet för att skapa en regel för vidarebefordran som skickar meddelanden till kön du just lagt till. Välj **meddelanden** som datakällan. Ange `level="storage"` som villkor och välj **StorageContainer** som en anpassad slutpunkt som routning regeln slutpunkt. Klicka på **spara** längst ned.  

    Kontrollera återställningsplats vägen är inställd på **på**. Den här inställningen är standardkonfigurationen för en IoT-hubb.

1. Kontrollera att dina tidigare program körs fortfarande. 

1. I Azure-portalen går du till ditt lagringskonto under **Blob-tjänsten**, klicka på **Bläddra blobbar...** .  Välj din behållare, navigera till och klicka JSON-filen och på **hämta** att visa data.

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen beskrivs hur du ska skicka meddelanden från enhet till moln med hjälp av meddelandet routningsfunktionen för IoT-hubb.

Den [hur du skickar meddelanden moln till enhet med IoT-hubben] [ lnk-c2d] visar hur du kan skicka meddelanden till dina enheter från din lösningens serverdel.

Exempel på fullständiga lösningar för slutpunkt till slutpunkt med IoT-hubb finns [Azure IoT Suite][lnk-suite].

Mer information om hur du utvecklar lösningar med IoT-hubb finns i [IoT-hubb Utvecklarhandbok].

Läs mer om meddelanderoutning i IoT-hubb i [skicka och ta emot meddelanden med IoT-hubben][lnk-devguide-messaging].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/
[IoT-hubb Utvecklarhandbok]: iot-hub-devguide.md
[Kom igång med IoT-hubb]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot
[hantering av tillfälliga fel]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
