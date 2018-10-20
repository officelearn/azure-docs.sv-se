---
title: Skicka händelser till Azure Event Hubs med .NET Framework| Microsoft Docs
description: Komma igång med att skicka händelser till Event Hubs med .NET Framework
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: ae5d89aab4ce1bd599ed9a50dc46336f8a96a2f5
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456240"
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Skicka händelser till Azure Event Hubs med .NET Framework
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. Detaljerad översikt över Event Hubs finns i [översikt av Händelsehubbar](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudien visar hur du skickar händelser till en händelsehubb med ett konsolprogram som skrivits i C# med .NET Framework. 

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här självstudien, finns följande förhandskrav:

* [Microsoft Visual Studio 2017 eller senare](http://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett namnområde för Event Hubs och en händelsehubb
Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [i den här artikeln](event-hubs-create.md), fortsätter sedan enligt följande steg i den här självstudien.

## <a name="create-a-console-application"></a>Skapa ett konsolprogram

I Visual Studio skapar du ett nytt Visual C#-skrivbordsapprojekt med hjälp av projektmallen **Konsolprogram**. Namnge projektet **Avsändare**.
   
![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Högerklicka på projektet **Avsändare** i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**. 
2. Klicka på **Bläddra**-fliken och sök sedan efter `WindowsAzure.ServiceBus`. Klicka på **Installera** och godkänn användningsvillkoren. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio laddar ned, installerar och lägger till en referens till [Azure Service Bus-bibliotekets NuGet-paket](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

## <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till event hub

1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
      ```csharp
      using System.Threading;
      using Microsoft.ServiceBus.Messaging;
      ```
2. Lägg till följande fält i klassen **Program**, och ersätt platshållarvärdena med namnet på den händelsehubb du skapade i föregående avsnitt samt anslutningssträngen på namnområdesnivå som du sparat tidigare.
   
        ```csharp
        static string eventHubName = "Your Event Hub name";
        static string connectionString = "namespace connection string";
        ```
3. Lägg till följande metod i klassen **Program**:
   
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
   
      Med den här metoden skickas kontinuerligt händelser till din händelsehubb med en fördröjning på 200 ms.
4. Slutligen lägger du till följande rader till **Main**-metoden:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Kör programmet och kontrollera att det inte finns några fel.
  
Grattis! Du har nu skickat meddelanden till en händelsehubb.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skickat meddelanden till en händelsehubb med hjälp av .NET Framework. Läs hur du tar emot händelser från en händelsehubb med hjälp av .NET Framework i [ta emot händelser från event hub - .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

