<properties
    pageTitle="Komma igång med Service Bus-köer | Microsoft Azure"
    description="Så här skriver du ett C#-konsolprogram för meddelandetjänsten i Service Bus"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/23/2016"
    ms.author="jotaub;sethm"/>


# Komma igång med Service Bus-köer

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## Detta kommer att utföras

I de här självstudierna kommer vi att gå igenom följande:

1. Skapa ett Service Bus-namnområde med Azure Portal.

2. Skapa en Service Bus-meddelandekö med Azure Portal.

3. Skriv ett konsolprogram för att skicka ett meddelande.

4. Skriv ett konsolprogram för att ta emot meddelanden.

## Krav

1. [Visual Studio 2013 eller Visual Studio 2015](http://www.visualstudio.com). Exemplen i de här självstudierna använder Visual Studio 2015.

2. En Azure-prenumeration.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 1. Skapa ett namnområde med Azure Portal

Om du redan har skapat ett Service Bus-namnområde går du vidare till avsnittet [Skapa en kö med hjälp av Azure Portal](#2-create-a-queue-using-the-azure-portal).

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 2. Skapa en kö med hjälp av Azure Portal

Om du redan har skapat en Service Bus-kö går du vidare till avsnittet [Skicka meddelanden till kön](#3-send-messages-to-the-queue).

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## 3. Skicka meddelanden till kön

Om du vill skicka meddelanden till kön skriver du ett C#-konsolprogram med Visual Studio.

### Skapa ett konsolprogram

1. Starta Visual Studio och skapa ett konsolprogram.

### Lägga till Service Bus-NuGet-paketet

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.

2. Klicka på fliken **Bläddra** och sök sedan efter ”Microsoft Azure Service Bus” och markera posten **Microsoft Azure Service Bus**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.

    ![Välj ett NuGet-paket][nuget-pkg]

### Skriva kod för att skicka ett meddelande till kön

1. Lägg till följande användningsinstruktion högst upp i filen Program.cs.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Lägg till följande kod till `Main`-metoden, ange **connectionString**-variabeln som den anslutningssträng som erhölls när namnområdet skapades och ange **queueName** som det könamn som användes när kön skapades.

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```

    Så här bör din Program.cs se ut.

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;

    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                client.Send(message);
            }
        }
    }
    ```
  
3. Kör programmet och kontrollera Azure Portal. Klicka på namnet på din kö i namnområdet på bladet **Översikt**. Observera att värdet för **Antal aktiva meddelanden** nu bör vara 1.
    
      ![Antal meddelanden][queue-message]
    
## 4. Ta emot meddelanden från kön

1. Skapa ett nytt konsolprogram och lägg till en referens till Service Bus NuGet-paketet, liknande det tidigare sändningsprogrammet.

2. Lägg till följande `using`-instruktion högst upp i filen Program.cs.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Lägg till följande kod till `Main`-metoden, ange **connectionString**-variabeln som den anslutningssträng som erhölls när namnområdet skapades och ange **queueName** som det könamn som användes när kön skapades.

    ```
    var connectionString = "";
    var queueName = "samplequeue";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
  
    Console.ReadLine();
    ```

    Så här bör din Program.cs-fil se ut:

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;
  
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
  
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
  
          Console.ReadLine();
        }
      }
    }
    ```
  
4. Kör programmet och kontrollera portalen. Observera att värdet för **Kölängd** nu bör vara 0.

    ![Kölängd][queue-message-receive]
  
Grattis! Nu har du skapat en kö, skickat ett meddelande och tagit emot ett meddelande.

## Nästa steg

Kolla in våra [GitHub-databaser med exempel](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) som visar några av de mer avancerade funktionerna i meddelandetjänsten i Azure Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->

[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples


<!--HONumber=Sep16_HO4-->


