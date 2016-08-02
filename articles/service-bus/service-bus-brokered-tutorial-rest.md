<properties 
    pageTitle="REST-självstudiekurs om asynkrona meddelanden i Service Bus | Microsoft Azure"
    description="REST-självstudiekurs om asynkrona meddelanden."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/03/2016"
    ms.author="sethm" />

# REST-självstudiekurs om asynkrona meddelanden i Service Bus

Den här kursen visar hur du skapar en grundläggande REST-baserad Azure Service Bus-kö och -ämne/-prenumeration.

## Steg 1: Skapa ett namnområde

Det första steget är att skapa ett namnområde för tjänsten och hämta en nyckel till [signatur för delad åtkomst](service-bus-sas-overview.md) (SAS). Ett namnområde för tjänsten ger en programgräns för varje program som exponeras via Service Bus. SAS-nyckeln genereras automatiskt av systemet när ett namnområde för tjänsten har skapats. Kombinationen av namnområdet för tjänsten och SAS-nyckeln ger en referens för Service Bus som används för att tillåta åtkomst till ett program.

### Skapa ett namnområde och få en SAS-nyckel

1. Du skapar ett namnområde för tjänsten i den [klassiska Azure-portalen][]. På den vänstra sidan klickar du på **Service Bus** och sedan på **Skapa**. Skriv ett namn för namnområdet och klicka på kryssmarkeringen.

1. Klicka på namnet på namnområdet som du skapade i föregående steg i den [klassiska Azure-portalen][] huvudfönster.

1. Klicka på fliken **Konfigurera**.

1. I avsnittet för **nyckelgenerator för delad åtkomst** skriver du ned den **primärnyckel** som är associerad med principen **RootManageSharedAccessKey**. Du kan även kopiera den till Urklipp. Du använder det här värdet senare i självstudiekursen.

## Skapa en konsolklient

Service Bus-köer gör att du kan lagra meddelanden i en kö av typen först in, först ut. Med ämnen och prenumerationer implementeras ett publicera-/prenumereramönster. Du skapar ett ämne och sedan en eller flera prenumerationer som är kopplade till detta ämne. När meddelanden skickas ut till ämnet, skickas de omedelbart ut till detta ämnes prenumeranter.

Koden i den här självstudiekursen gör följande.

- Den använder namnområdet för tjänsten och [SAS-nyckeln](service-bus-sas-overview.md) (signatur för delad åtkomst) för att ge åtkomst till dina namnområdesresurser för Service Bus.

- Den skapar en kö, skickar ett meddelande till den och läser meddelandet från den.

- Den skapar ett ämne, en prenumeration på detta ämne och skickar och läser meddelandet från prenumerationen.

- Den hämtar all information om kön, ämnet och prenumerationen, inklusive prenumerationsregler, från Service Bus.

- Den tar bort resurserna för kön, ämnet och prenumerationen.

Eftersom tjänsten är en webbtjänst i REST-format, ingår det inga särskilda typer. Hela utväxlingen innehåller nämligen strängar. Detta innebär att Visual Studio-projektet inte får ha några referenser till Service Bus-bibliotek.

När du har fått namnområdet för tjänsten och autentiseringsuppgifterna i det första steget, ska du sedan gå vidare med att skapa ett grundläggande konsolprogram i Visual Studio.

### Skapa ett konsolprogram

1. Starta Visual Studio som administratör genom att högerklicka på programmet i **Start**-menyn och sedan klicka på **Kör som administratör**.

1. Skapa ett nytt konsolprogramsprojekt. Klicka på **Arkiv**-menyn och på **Nytt**. Klicka sedan på **Projekt**. I dialogrutan **Nytt projekt** klickar du på **Visual C#** (om **Visual C#** inte visas tittar du under **Andra språk**). Sedan väljer du mallen **Konsolprogram**  och ger den namnet **Microsoft.ServiceBus.Samples**. Använd den förvalda platsen. Klicka på **OK** för att skapa projektet.

1. Se till att `using`-uttrycken visas på följande sätt i Program.cs:

    ```
    using System;
    using System.Globalization;
    using System.IO;
    using System.Net;
    using System.Security.Cryptography;
    using System.Text;
    using System.Xml;
    ```

1. Om så krävs byter du namn på namnområdet för programmet från Visual Studios förvalda namn till `Microsoft.ServiceBus.Samples`.

1. Lägg till följande globala variabler i `Program`-klassen:
    
    ```
    static string serviceNamespace;
    static string baseAddress;
    static string token;
    const string sbHostName = "servicebus.windows.net";
    ```

1. Klistra in följande kod inuti `Main()`:

    ```
    Console.Write("Enter your service namespace: ");
    serviceNamespace = Console.ReadLine();
    
    Console.Write("Enter your SAS key: ");
    string SASKey = Console.ReadLine();
    
    baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
    try
    {
        token = GetSASToken("RootManageSharedAccessKey", SASKey);
    
        string queueName = "Queue" + Guid.NewGuid().ToString();
    
        // Create and put a message in the queue
        CreateQueue(queueName, token);
        SendMessage(queueName, "msg1");
        string msg = ReceiveAndDeleteMessage(queueName);
    
        string topicName = "Topic" + Guid.NewGuid().ToString();
        string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
        CreateTopic(topicName);
        CreateSubscription(topicName, subscriptionName);
        SendMessage(topicName, "msg2");
    
        Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));
    
        // Get an Atom feed with all the queues in the namespace
        Console.WriteLine(GetResources("$Resources/Queues"));
    
        // Get an Atom feed with all the topics in the namespace
        Console.WriteLine(GetResources("$Resources/Topics"));
    
        // Get an Atom feed with all the subscriptions for the topic we just created
        Console.WriteLine(GetResources(topicName + "/Subscriptions"));
    
        // Get an Atom feed with all the rules for the topic and subscription we just created
        Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));
    
        // Delete the queue we created
        DeleteResource(queueName);
    
        // Delete the topic we created
        DeleteResource(topicName);
    
        // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
        Console.WriteLine(GetResources("$Resources/Topics"));
    
        // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
        Console.WriteLine(GetResources("$Resources/Queues"));
    }
    catch (WebException we)
    {
        using (HttpWebResponse response = we.Response as HttpWebResponse)
        {
            if (response != null)
            {
                Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
            }
            else
            {
                Console.WriteLine(we.ToString());
            }
        }
    }
    
    Console.WriteLine("\nPress ENTER to exit.");
    Console.ReadLine();
    ```

## Skapa autentiseringsuppgifter för hantering

Nästa steg är att skriva en metod som bearbetar det namnområde och den SAS-nyckel som du angav i föregående steg och som returnerar en SAS-token. I det här exemplet skapas en SAS-token som är giltig i en timme.

### Skapa en GetSASToken()-metod

Klistra in följande kod efter `Main()`-metoden i `Program`-klassen:

```
private static string GetSASToken(string SASKeyName, string SASKeyValue)
{
  TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
  string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
  string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
  HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

  string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
  string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
      WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
  return sasToken;
}
```
## Skapa kön

Nästa steg är att skriva en metod som använder kommandot HTTP PUT i REST-format för att skapa en kö.

Klistra in följande kod direkt efter den `GetSASToken()`-kod som du lade till i det förra steget:

```
// Uses HTTP PUT to create the queue
private static string CreateQueue(string queueName, string token)
{
    // Create the URI of the new queue, note that this uses the HTTPS scheme
    string queueAddress = baseAddress + queueName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating queue {0}", queueAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                          <title type=""text"">" + queueName + @"</title>
                          <content type=""application/xml"">
                            <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                          </content>
                        </entry>";

    byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## Skicka ett meddelande till kön

I det här steget kan du lägga till en metod som använder kommandot HTTP POST i REST-format för att skicka ett meddelande till den kö som du skapade i det förra steget.

1. Klistra in följande kod direkt efter den `CreateQueue()`-kod som du lade till i det förra steget:

    ```
    // Sends a message to the "queueName" queue, given the name and the value to enqueue
    // Uses an HTTP POST request.
    private static void SendMessage(string queueName, string body)
    {
        string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
        Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
        WebClient webClient = new WebClient();
        webClient.Headers[HttpRequestHeader.Authorization] = token;
    
        webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));
    }
    ```

1. Standardegenskaperna för asynkrona meddelanden placeras i en HTTP-rubrik av typen `BrokerProperties`. De asynkrona egenskaperna måste serialiseras i JSON-format. Om du vill ange ett **TimeToLive**-värde på 30 sekunder och lägga till meddelandeetiketten ”M1” i meddelandet, ska du lägga till följande kod direkt före det `webClient.UploadData()`-anrop som visas i det förra exemplet:

    ```
    // Add brokered message properties "TimeToLive" and "Label"
    webClient.Headers.Add("BrokerProperties", "{ \"TimeToLive\":30, \"Label\":\"M1\"}");
    ```

    Observera att egenskaper för asynkrona meddelanden har lagts till och kommer att läggas till. Därför måste sändbegäran ange en API-version som har stöd för alla egenskaper för asynkrona meddelanden som är en del av begäran. Om den angivna API-versionen inte stöder en egenskap för asynkrona meddelanden, kommer den egenskapen att ignoreras.

1. Anpassade meddelandeegenskaper definieras som en uppsättning nyckelvärdespar. Alla anpassade egenskaper lagras i sin egen TPPT-rubrik. Om du vill lägga till de anpassade egenskaperna ”Prioritet” och ”Kund”, lägger du till följande kod direkt före det `webClient.UploadData()`-anrop som visas i det förra exemplet:

    ```
    // Add custom properties "Priority" and "Customer".
    webClient.Headers.Add("Priority", "High");
    webClient.Headers.Add("Customer", "12345");
    ```

## Ta emot och ta bort ett meddelande från kön

Nästa steg är att lägga till en metod som använder kommandot HTTP DELETE i REST-format för att ta emot och ta bort ett meddelande från kön.

Klistra in följande kod direkt efter den `SendMessage()`-kod som du lade till i det förra steget:

```
// Receives and deletes the next message from the given resource (queue, topic, or subscription)
// using the resourceName and an HTTP DELETE request
private static string ReceiveAndDeleteMessage(string resourceName)
{
    string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
    Console.WriteLine("\nRetrieving message from {0}", fullAddress);
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
    string responseStr = Encoding.UTF8.GetString(response);

    Console.WriteLine(responseStr);
    return responseStr;
}
```

## Skapa ett ämne och en prenumeration

Nästa steg är att skriva en metod som använder kommandot HTTP PUT i REST-format för att skapa ett ämne. Sedan kan du skriva en metod som skapar en prenumeration för detta ämne.

### Skapa ett ämne

Klistra in följande kod direkt efter den `ReceiveAndDeleteMessage()`-kod som du lade till i det förra steget:

```
// Using an HTTP PUT request.
private static string CreateTopic(string topicName)
{
    var topicAddress = baseAddress + topicName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating topic {0}", topicAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

### Skapa en prenumeration

Följande kod skapar en prenumeration på det ämne som du skapade i det förra steget. Lägg till följande kod direkt efter definitionen `CreateTopic()`:

```
private static string CreateSubscription(string topicName, string subscriptionName)
{
    var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## Hämta meddelanderesurser

I det här steget lägger du till kod som hämtar meddelandeegenskaperna och sedan tar bort de meddelanderesurser som du skapade i det förra steget.

### Hämta ett Atom-flöde med de angivna resurserna

Lägg till följande kod direkt efter den `CreateSubscription()`-metod som du lade till i det förra steget:

```
private static string GetResources(string resourceAddress)
{
    string fullAddress = baseAddress + resourceAddress;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;
    Console.WriteLine("\nGetting resources from {0}", fullAddress);
    return FormatXml(webClient.DownloadString(fullAddress));
}
```

### Ta bort meddelandeentiteter

Lägg till följande kod direkt efter den kod som du lade till i det förra steget:

```
private static string DeleteResource(string resourceName)
{
    string fullAddress = baseAddress + resourceName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nDeleting resource at {0}", fullAddress);
    byte[] response = webClient.UploadData(fullAddress, "DELETE", newbyte[0]);
    return Encoding.UTF8.GetString(response);
}
```

### Formatera Atom-flödet

`GetResources()`-metoden innehåller ett anrop till en `FormatXml()`-metod som formaterar om det hämtade Atom-flödet så att det blir lättare att läsa. Följande är definitionen av `FormatXml()`. Lägg till den här koden direkt efter den `DeleteResource()`-kod som du lade till i det förra avsnittet:

```
// Formats the XML string to be more human-readable; intended for display purposes
private static string FormatXml(string inputXml)
{
    XmlDocument document = new XmlDocument();
    document.Load(new StringReader(inputXml));

    StringBuilder builder = new StringBuilder();
    using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
    {
        writer.Formatting = Formatting.Indented;
        document.Save(writer);
    }

    return builder.ToString();
}
```

## Skapa och kör appen

Du kan nu skapa och köra appen. Från menyn **Skapa** i Visual Studio klickar du på **Skapa lösning** eller så trycker du på F6.

### Köra programmet

Om det inte finns några fel, trycker du på F5 för att köra programmet. Du anger namnområdet, SAS-nyckelnamnet och SAS-nyckelvärdet som du fick i det första steget när du uppmanas att göra det.

### Exempel

Följande exempel är den fullständiga koden, som den bör visas när du har följt alla steg i den här självstudiekursen.

```
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Security.Cryptography;
using System.Text;
using System.Xml;

namespace Microsoft.ServiceBus.Samples
{
    class Program
    {
        static string serviceNamespace;
        static string baseAddress;
        static string token;
        const string sbHostName = "servicebus.windows.net";

        static void Main(string[] args)
        {
            Console.Write("Enter your service namespace: ");
            serviceNamespace = Console.ReadLine();

            Console.Write("Enter your SAS key: ");
            string SASKey = Console.ReadLine();

            baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
            try
            {
                token = GetSASToken("RootManageSharedAccessKey", SASKey);

                string queueName = "Queue" + Guid.NewGuid().ToString();

                // Create and put a message in the queue
                CreateQueue(queueName, token);
                SendMessage(queueName, "msg1");
                string msg = ReceiveAndDeleteMessage(queueName);

                string topicName = "Topic" + Guid.NewGuid().ToString();
                string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
                CreateTopic(topicName);
                CreateSubscription(topicName, subscriptionName);
                SendMessage(topicName, "msg2");

                Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));

                // Get an Atom feed with all the queues in the namespace
                Console.WriteLine(GetResources("$Resources/Queues"));

                // Get an Atom feed with all the topics in the namespace
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the subscriptions for the topic we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions"));

                // Get an Atom feed with all the rules for the topic and subscription we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));

                // Delete the queue we created
                DeleteResource(queueName);

                // Delete the topic we created
                DeleteResource(topicName);

                // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Queues"));
            }
            catch (WebException we)
            {
                using (HttpWebResponse response = we.Response as HttpWebResponse)
                {
                    if (response != null)
                    {
                        Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
                    }
                    else
                    {
                        Console.WriteLine(we.ToString());
                    }
                }
            }

            Console.WriteLine("\nPress ENTER to exit.");
            Console.ReadLine();
        }

        private static string GetSASToken(string SASKeyName, string SASKeyValue)
        {
            TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
            string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
            string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
            HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

            string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
            string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
                WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
            return sasToken;
        }

        // Uses HTTP PUT to create the queue
        private static string CreateQueue(string queueName, string token)
        {
            // Create the URI of the new queue, note that this uses the HTTPS scheme
            string queueAddress = baseAddress + queueName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating queue {0}", queueAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + queueName + @"</title>
                                  <content type=""application/xml"">
                                    <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        // Sends a message to the "queueName" queue, given the name and the value to enqueue
        // Uses an HTTP POST request.
        private static void SendMessage(string queueName, string body)
        {
            string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
            Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            // Add brokered message properties “TimeToLive” and “Label”.
            webClient.Headers.Add("BrokerProperties", "{ \"TimeToLive\":30, \"Label\":\"M1\"}");
            // Add custom properties “Priority” and “Customer”.
            webClient.Headers.Add("Priority", "High");
            webClient.Headers.Add("Customer", "12345");
            webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));

        }

        // Receives and deletes the next message from the given resource (queue, topic, or subscription)
        // using the resourceName and an HTTP DELETE request.
        private static string ReceiveAndDeleteMessage(string resourceName)
        {
            string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
            Console.WriteLine("\nRetrieving message from {0}", fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            string responseStr = Encoding.UTF8.GetString(response);

            Console.WriteLine(responseStr);
            return responseStr;
        }

        // Using an HTTP PUT request.
        private static string CreateTopic(string topicName)
        {
            var topicAddress = baseAddress + topicName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating topic {0}", topicAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string CreateSubscription(string topicName, string subscriptionName)
        {
            var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string GetResources(string resourceAddress)
        {
            string fullAddress = baseAddress + resourceAddress;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            Console.WriteLine("\nGetting resources from {0}", fullAddress);
            return FormatXml(webClient.DownloadString(fullAddress));
        }

        private static string DeleteResource(string resourceName)
        {
            string fullAddress = baseAddress + resourceName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nDeleting resource at {0}", fullAddress);
            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            return Encoding.UTF8.GetString(response);
        }

        // Formats the XML string to be more human-readable; intended for display purposes
        private static string FormatXml(string inputXml)
        {
            XmlDocument document = new XmlDocument();
            document.Load(new StringReader(inputXml));

            StringBuilder builder = new StringBuilder();
            using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
            {
                writer.Formatting = Formatting.Indented;
                document.Save(writer);
            }

            return builder.ToString();
        }
    }
}
```

## Nästa steg

Läs följande artiklar om du vill lära dig mer:

- [Översikt över Service Bus-meddelanden](service-bus-messaging-overview.md)
- [Grunderna i Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Självstudiekurs i Service Bus Relay REST](service-bus-relay-rest-tutorial.md)

[klassiska Azure-portalen]: http://manage.windowsazure.com



<!--HONumber=Jun16_HO2-->


