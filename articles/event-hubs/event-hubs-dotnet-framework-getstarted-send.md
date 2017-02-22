---
title: "Skicka händelser till Azure Event Hubs med .NET Framework| Microsoft Docs"
description: "Komma igång med att skicka händelser till Event Hubs med .NET Framework"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: c52f7055897ba8e851add431e5ab9c0defdb5bfc
ms.openlocfilehash: 29523e308e038904773582c73c1688f57e3c31e3


---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Skicka händelser till Azure Event Hubs med .NET Framework

## <a name="introduction"></a>Introduktion
Händelsehubbar är en tjänst som bearbetar stora mängder händelsedata (telemetri) från anslutna enheter och program. När du har samlat in data i händelsehubbar kan du lagra dem med ett lagringskluster eller omvandla dem med hjälp av en leverantör av realtidsanalys. Den här storskaliga händelseinsamlingen och bearbetningsfunktionen är en viktig komponent inom moderna programarkitekturer som t.ex. sakernas internet.

I den här kursen får du lära dig att skapa en händelsehubb i [Azure Portal](https://portal.azure.com). Du får också lära dig att skicka händelser till en händelsehubb med ett konsolprogram som skrivits i C# med .NET Framework. För att ta emot händelser med .NET Framework kan du läsa artikeln [Receive events using the .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md) (Ta emot händelser med .NET Framework) eller klicka på ditt mottagarspråk till vänster i innehållsförteckningen.

För att kunna genomföra den här kursen behöver du följande:

* [Microsoft Visual Studio](http://visualstudio.com)
* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett utan kostnad på ett par minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/free/).

## <a name="send-messages-to-event-hubs"></a>Skicka meddelanden till Event Hubs
I det här avsnittet skriver du en Windows-konsolapp som skickar händelser till din Event Hub.

1. I Visual Studio skapar du ett nytt Visual C#-skrivbordsapprojekt med hjälp av projektmallen **Konsolprogram**. Namnge projektet **Avsändare**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)
2. Högerklicka på lösningen i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**. 
3. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus`. Kontrollera att projektnamnet (**Avsändare**) har angetts i rutan **Versioner**. Klicka på **Installera** och godkänn användningsvillkoren. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio laddar ned, installerar och lägger till en referens till [Azure Service Bus-bibliotekets NuGet-paket](https://www.nuget.org/packages/WindowsAzure.ServiceBus).
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
5. Lägg till följande fält till **Program**-klassen, där du ersätter platshållarvärdena med namnet på den Event Hub du skapat i föregående avsnitt och anslutningssträngen på namnområdesnivå som du sparat tidigare.
   
    ```csharp
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```
6. Lägg till följande metod i klassen **Program**:
   
    ```csharp
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }
   
            Thread.Sleep(200);
        }
    }
    ```
   
    Den här metoden skickar kontinuerligt händelser till din Event Hub med en fördröjning på 200 ms.
7. Slutligen lägger du till följande rader till **Main**-metoden:
   
    ```csharp
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ett fungerande program som skapar en händelsehubb och skickar data kan du gå vidare till följande scenarier:

* [Ta emot händelser med hjälp av värd för händelsebearbetning](event-hubs-dotnet-framework-getstarted-receive-eph.md)
* [Referens för händelsebearbetningsvärd](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md




<!--HONumber=Feb17_HO1-->


