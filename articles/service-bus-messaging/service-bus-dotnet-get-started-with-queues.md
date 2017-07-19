---
title: "Komma igång med Azure Service Bus-köer | Microsoft Docs"
description: "Skriv ett C#-konsolprogram som använder meddelandeköer i Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 02d0ce093bc42cffa4f3993826c61c8aeca4d033
ms.contentlocale: sv-se
ms.lasthandoff: 07/01/2017


---
# <a name="get-started-with-service-bus-queues"></a>Komma igång med Service Bus-köer
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>Detta kommer att utföras
Den här självstudien omfattar följande steg:

1. Skapa ett Service Bus-namnområde med Azure Portal.
2. Skapa en Service Bus-kö med Azure Portal.
3. Skriv ett konsolprogram för att skicka ett meddelande.
4. Skriv ett konsolprogram som hämtar meddelandet som skickades i föregående steg.

## <a name="prerequisites"></a>Krav
1. [Visual Studio 2015 eller senare](http://www.visualstudio.com). I exemplen i den här självstudiekursen används Visual Studio 2017.
2. En Azure-prenumeration.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Skapa ett namnområde med Azure Portal
Om du redan har skapat ett namnområde för Service Bus-meddelanden går du vidare till avsnittet [Skapa en kö med hjälp av Azure Portal](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Skapa en kö med hjälp av Azure Portal
Om du redan har skapat en Service Bus-kö går du vidare till avsnittet [Skicka meddelanden till kön](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Skicka meddelanden till kön
Vi skriver ett C#-konsolprogram med Visual Studio för att skicka meddelanden till kön.

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio och skapa ett nytt projekt: **Konsolprogram (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Lägga till Service Bus-NuGet-paketet
1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
2. Klicka på fliken **Bläddra**, sök efter **Microsoft Azure Service Bus** och markera posten **WindowsAzure.ServiceBus**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.
   
    ![Välj ett NuGet-paket][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Skriva kod för att skicka ett meddelande till kön
1. Lägg till följande `using`-instruktion högst upp i filen Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Lägg till följande kod i metoden `Main`. Ställ in variabeln `connectionString` på den anslutningssträng du fick när du skapade namnområdet, och ställ in `queueName` på namnet du använde när du skapade kön.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Så här bör filen Program.cs se ut:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Kör programmet och kontrollera Azure Portal: klicka på köns namn på bladet **Översikt** för namnområdet. Bladet **Grundläggande** för kön visas. Observera att värdet för **Antal aktiva meddelanden** nu bör vara 1. Varje gång du kör sändningsprogrammet utan att hämta meddelanden ökar detta värde med 1. Observera också att den aktuella storleken för kön ökar varje gång programmet lägger till ett meddelande i kön.
   
      ![Meddelandestorlek][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Ta emot meddelanden från kön

1. Om du vill ta emot de meddelanden du nyss skickade skapar du ett nytt konsolprogram och lägger till en referens till Service Bus NuGet-paketet, ungefär som i det tidigare sändningsprogrammet.
2. Lägg till följande `using`-instruktion högst upp i filen Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Lägg till följande kod i metoden `Main`. Ställ in variabeln `connectionString` på den anslutningssträng du fick när du skapade namnområdet, och ställ in `queueName` på namnet du använde när du skapade kön.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Så här bör din Program.cs-fil se ut:
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. Kör programmet och kontrollera portalen igen. Observera att värdena för **Antal aktiva meddelanden** och **Aktuell** nu är 0.
   
    ![Kölängd][queue-message-receive]

Grattis! Nu har du skapat en kö, skickat ett meddelande och tagit emot ett meddelande.

## <a name="next-steps"></a>Nästa steg

Kolla in våra [GitHub-databaser med exempel](https://github.com/Azure/azure-service-bus/tree/master/samples) som visar några av de mer avancerade funktionerna i meddelandetjänsten i Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

