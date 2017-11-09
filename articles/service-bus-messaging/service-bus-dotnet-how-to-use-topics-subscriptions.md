---
title: "Kom igång med ämnen och prenumerationer i Azure Service Bus | Microsoft Docs"
description: "Skriv ett C#-konsolprogram som använder meddelandeämnen och prenumerationer i Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 79573909e02a9743c732f8c1dd48c53966df3f0c
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="get-started-with-service-bus-topics"></a>Kom igång med Service Bus-ämnen

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här självstudien omfattar följande steg:

1. Skapa ett Service Bus-namnområde med Azure Portal.
2. Skapa ett Service Bus-ämne med Azure Portal.
3. Skapa en Service Bus-prenumeration på ämnet med Azure Portal.
4. Skriv ett konsolprogram för att skicka ett meddelande till ämnet.
5. Skriv ett konsolprogram för att ta emot meddelandet från prenumerationen.

## <a name="prerequisites"></a>Krav

1. [Visual Studio 2015 eller senare](http://www.visualstudio.com). I exemplen i den här självstudiekursen används Visual Studio 2017.
2. En Azure-prenumeration.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Skapa ett namnområde med Azure Portal

Om du redan har skapat ett namnområde för Service Bus-meddelanden går du vidare till avsnittet [Skapa ett ämne med Azure Portal](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Skapa ett ämne med Azure Portal

1. Logga in på [Azure portal][azure-portal].
2. I det vänstra navigeringsfönstret i portalen klickar du på **Service Bus** (om du inte ser **Service Bus** klickar du på **Fler tjänster** eller på **Alla resurser**).
3. Klicka på det namnområde där du vill skapa ämnet. Bladet med namnområdesöversikten visas:
   
    ![Skapa ett ämne][createtopic1]
4. På bladet **Service Bus-namnområde** klickar du på **Ämnen** och sedan på **Lägg till ämne**.
   
    ![Välja ämnen][createtopic2]
5. Ange ett namn för ämnet och avmarkera alternativet **Aktivera partitionering**. Lämna standardvärdena för de andra alternativen.
   
    ![Välj ny][createtopic3]
6. Klicka på **Skapa** längst ned på bladet.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Skapa en prenumeration på ämnet

1. Klicka på det namnområde du skapade i steg 1 i fönstret med portalresurser, och klicka sedan på namnet på det ämne du skapade i steg 2.
2. Klicka på plustecknet bredvid **Prenumeration** längst upp i översiktsfönstret för att lägga till en prenumeration på det här ämnet.

    ![Skapa en prenumeration][createtopic4]

3. Ange ett namn för prenumerationen. Lämna standardvärdena för de andra alternativen.

## <a name="4-send-messages-to-the-topic"></a>4. Skicka meddelanden till ämnet

För att kunna skicka meddelanden till ämnet skriver vi ett C#-konsolprogram med Visual Studio.

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio och skapa ett nytt projekt: **Konsolprogram (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Lägga till Service Bus-NuGet-paketet

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
2. Klicka på fliken **Bläddra**, sök efter **WindowsAzure.ServiceBus** och markera posten **WindowsAzure.ServiceBus**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.
   
    ![Välj ett NuGet-paket][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-topic"></a>Skriv kod för att skicka ett meddelande till ämnet

1. Lägg till följande `using`-instruktion högst upp i filen Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Lägg till följande kod i metoden `Main`. Ställ in variabeln `connectionString` på den anslutningssträng du fick när du skapade namnområdet, och ställ in `topicName` på namnet du använde när du skapade ämnet.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    client.Send(message);

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

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

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                client.Send(message);
                
                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Kör programmet och kontrollera Azure Portal: klicka på ämnets namn på bladet **Översikt** för namnområdet. Bladet **Grundläggande** för ämnet visas. Lägg märke till att värdet **Antal meddelanden** nu ska vara 1 för prenumerationerna som visas längst ned på bladet. Varje gång du kör sändningsprogrammet utan att hämta meddelanden (beskrivs i nästa avsnitt) ökar detta värde med 1. Observera också att ämnets aktuella storlek ökar värdet **Aktuell** på bladet **Grundläggande** varje gång programmet lägger till ett meddelande i ämnet/prenumerationen.
   
      ![Meddelandestorlek][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Ta emot meddelanden från prenumerationen

1. Om du vill ta emot de meddelanden du nyss skickade skapar du ett nytt konsolprogram och lägger till en referens till Service Bus NuGet-paketet, ungefär som i det tidigare sändningsprogrammet.
2. Lägg till följande `using`-instruktion högst upp i filen Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Lägg till följande kod i metoden `Main`. Ställ in variabeln `connectionString` på den anslutningssträng du fick när du skapade namnområdet, och ställ in `topicName` på namnet du använde när du skapade ämnet. Se till att du ersätter `<your subscription name>` med namnet på prenumerationen som du skapade i steg 3. 
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
4. Kör programmet och kontrollera portalen igen. Observera att värdena för **Antal meddelanden** och **Aktuell** nu är 0.
   
    ![Ämnets längd][topic-message-receive]

Grattis! Du har nu skapat ett ämne och en prenumeration, skickat ett meddelande och tagit emot meddelandet.

## <a name="next-steps"></a>Nästa steg

Kolla in våra [GitHub-databaser med exempel](https://github.com/Azure/azure-service-bus/tree/master/samples) som visar några av de mer avancerade funktionerna i meddelandetjänsten i Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
