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
ms.date: 03/08/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 5c326a025a4276ae9b1a777439ed6c728d3e7103
ms.lasthandoff: 03/09/2017


---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Skicka händelser till Azure Event Hubs med .NET Framework

## <a name="introduction"></a>Introduktion
Händelsehubbar är en tjänst som bearbetar stora mängder händelsedata (telemetri) från anslutna enheter och program. När du har samlat in data i händelsehubbar kan du lagra dem med ett lagringskluster eller omvandla dem med hjälp av en leverantör av realtidsanalys. Den här storskaliga händelseinsamlingen och bearbetningsfunktionen är en viktig komponent inom moderna programarkitekturer som t.ex. sakernas internet.

I den här kursen får du lära dig att skapa en händelsehubb i [Azure Portal](https://portal.azure.com). Du får också lära dig att skicka händelser till en händelsehubb med ett konsolprogram som skrivits i C# med .NET Framework. För att ta emot händelser med .NET Framework kan du läsa artikeln [Receive events using the .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md) (Ta emot händelser med .NET Framework) eller klicka på ditt mottagarspråk till vänster i innehållsförteckningen.

För att kunna genomföra den här kursen behöver du följande:

* [Microsoft Visual Studio 2015 eller senare](http://visualstudio.com). För skärmdumparna i de här självstudierna används Visual Studio 2017.
* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett utan kostnad på ett par minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett Event Hubs-namnområde och en Event Hub

Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs, och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med Event Hub. Om du vill skapa ett namnområde och en Event Hub följer du anvisningarna i [den här artikeln](event-hubs-create.md) och fortsätter sedan enligt följande steg.

## <a name="create-a-console-application"></a>Skapa ett konsolprogram
I det här avsnittet skriver du en Windows-konsolapp som skickar händelser till din Event Hub.

1. I Visual Studio skapar du ett nytt Visual C#-skrivbordsapprojekt med hjälp av projektmallen **Konsolprogram**. Namnge projektet **Avsändare**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)
2. Högerklicka på projektet **Avsändare** i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**. 
3. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus`. Klicka på **Installera** och godkänn användningsvillkoren. 
   
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
8. Kör programmet och kontrollera att det inte finns några fel.
  
Grattis! Du har nu skickat meddelanden till en Event Hub.

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


