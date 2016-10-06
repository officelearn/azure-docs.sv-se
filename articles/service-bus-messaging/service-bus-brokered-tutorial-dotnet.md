<properties 
    pageTitle=".Net-självstudiekurs om asynkrona meddelanden i Service Bus | Microsoft Azure"
    description=".Net-självstudiekurs om asynkrona meddelanden"
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />


# .Net-självstudiekurs om asynkrona meddelanden i Service Bus

Med Azure Service Bus får du två omfattande meddelandelösningar. I en av dem används en centraliserad tjänst för vidarebefordran som körs i molnet och har stöd för olika transportprotokoll och webbtjänststandarder, däribland SOAP WS-* och REST. Klienten behöver ingen direkt anslutning till den lokala tjänsten och behöver inte heller veta var den finns. Den lokala tjänsten behöver inte ha några öppna ingående portar i brandväggen.

Den andra meddelandelösningen har funktioner för asynkrona meddelanden. Dessa kan betraktas som asynkrona eller frikopplade meddelandefunktioner som har stöd för publicering och prenumeration, temporal frikoppling och scenarier för belastningsutjämning med hjälp av meddelandeinfrastruktur för Service Bus. Frikopplad kommunikation har många fördelar: klienter och servrar kan till exempel ansluta efter behov och utföra åtgärder på ett asynkront sätt.

Den här självstudiekursen är avsedd att ge dig en översikt och praktisk erfarenhet av köer, som är en av huvudkomponenterna av asynkrona meddelanden i Service Bus. När du har gått igenom de olika avsnitten i självstudiekursen har du en app som fyller på en lista med meddelanden, skapar en kö och skickar meddelanden till den kön. Slutligen tar appen emot och visar meddelanden från kön och rensar sedan dess resurser och avslutas. Mer information om hur du skapar en app som använder Service Bus finns i [självstudiekursen för den vidarebefordrande meddelandetjänsten i Service Bus](../service-bus-relay/service-bus-relay-tutorial.md).

## Introduktion och nödvändiga komponenter

Köer erbjuder FIFO-leverans (”first in, first out”) av meddelanden till en eller flera konkurrerande konsumenter. FIFO betyder att meddelanden vanligtvis förväntas tas emot och bearbetas av mottagarna i den ordning som de lagts till i kön, och varje meddelande tas bara emot och bearbetas av en meddelandekonsument. En stor fördel med köer är *temporal frikoppling* av appkomponenter. Producenter och konsumenter behöver med andra ord inte skicka och ta emot meddelanden på samma gång, eftersom meddelanden lagras varaktigt i kön. En relaterad fördel är *belastningsutjämning*, vilket gör att producenter och konsumenter kan skicka och ta emot meddelanden med olika hastigheter.

Nedan följer några administrativa och nödvändiga steg som du bör följa innan du påbörjar självstudiekursen. Det första steget är att skapa ett namnområde för tjänsten samt hämta en nyckel till signatur för delad åtkomst (SAS). Ett namnområde ger en appgräns för varje app som exponeras via Service Bus. SAS-nyckeln genereras automatiskt av systemet när ett namnområde för tjänsten har skapats. Kombinationen av namnområdet för tjänsten och SAS-nyckeln ger en referens som Service Bus använder för att tillåta åtkomst till en app.

### Skapa ett namnområde för tjänsten och få en SAS-nyckel

Det första steget är att skapa ett namnområde för tjänsten och hämta en nyckel till [signatur för delad åtkomst](../service-bus/service-bus-sas-overview.md) (SAS). Ett namnområde ger en appgräns för varje app som exponeras via Service Bus. SAS-nyckeln genereras automatiskt av systemet när ett namnområde för tjänsten har skapats. Kombinationen av namnområdet för tjänsten och SAS-nyckeln ger en referens för Service Bus som används för att tillåta åtkomst till ett program.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Nästa steg är att skapa ett Visual Studio-projekt och skriva två hjälpfunktioner som läser in en kommaavgränsad lista med meddelanden i ett starkt typbestämt .NET-[listobjekt](https://msdn.microsoft.com/library/6sh2ey19.aspx) i [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .

### Skapa ett Visual Studio-projekt

1. Öppna Visual Studio som administratör genom att högerklicka på programmet i Start-menyn och klicka på **Kör som administratör**.

1. Skapa ett nytt konsolappsrojekt. Klicka på **Arkivmenyn**, välj **Nytt** och klicka sedan på **Projekt**. I dialogrutan **Nytt projekt** klickar du på **Visual C#** (om **Visual C#** inte visas tittar du under **Andra språk**). Sedan klickar du på mallen **Konsolapp** och ger den namnet **QueueSample**. Använd standardinställningen **Plats**. Klicka på **OK** för att skapa projektet.

1. Använd pakethanteraren NuGet för att lägga till Service Bus-bibliotek i projektet:
    1. Högerklicka på projektet **QueueSample** i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
    2. I dialogrutan **Hantera Nuget-paket** klickar du på fliken **Bläddra** och söker efter **Azure Service Bus**. Sedan klickar du på **Installera**.
<br />
1. Dubbelklicka på filen Program.cs i Solution Explorer för att öppna den i Visual Studio-redigeraren. Ändra namnet på namnområdet från standardnamnet `QueueSample` till `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Ändra `using`-instruktionerna så som visas i följande kod:

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```

1. Skapa en textfil med namnet Data.csv och kopiera följande kommaavgränsade text.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Spara och stäng filen Data.csv och kom ihåg platsen där du sparade den.

1. Högerklicka på projektets namn (i det här fallet **QueueSample**) i Solution Explorer. Klicka på **Lägg till** och sedan på **Befintligt objekt**.

1. Bläddra till filen Data.csv som du skapade i steg 6. Klicka på filen och sedan på **Lägg till**. Se till att **Alla filer (*.*)** har valts i listan över filtyper.

### Skapa en metod som parsar en lista med meddelanden

1. I `Program`-klassen före `Main()`-metoden deklarerar du två variabler: en av typen **DataTable**, som ska innehålla listan över meddelanden i Data.csv. Den andra ska vara av typen listobjekt, starkt typbestämd till [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Den senare är listan över asynkrona meddelanden som används i följande steg i självstudiekursen.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```

1. Utanför `Main()` definierar du en `ParseCSV()`-metod som parsar listan över meddelanden i Data.csv och läser in meddelanden till en [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx)-tabell så som det visas här. Metoden returnerar ett **DataTable**-objekt.

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;
    
                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }
    
                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }
    
        return tableIssues;
    }
    ```

1. I `Main()`-metoden lägger du till en instruktion som anropar `ParseCSVFile()`-metoden:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
    
    }
    ```

### Skapa en metod som läser in listan med meddelanden

1. Utanför `Main()` definierar du en `GenerateMessages()`-metod som tar det **DataTable**-objekt som returnerades av `ParseCSVFile()` och läser in tabellen i en starkt typbestämd lista över asynkrona meddelanden. Metoden returnerar sedan **listobjektet** som i följande exempel. 

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();
    
        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```

1. I `Main()`, direkt efter anropet till `ParseCSVFile()`, lägger du till en instruktion som anropar `GenerateMessages()`-metoden med returvärdet från `ParseCSVFile()` som ett argument:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### Skaffa autentiseringsuppgifter för användare

1. Skapa först tre globala strängvariabler som ska innehålla värdena. Deklarera variablerna direkt efter de tidigare variabeldeklarationerna. Exempel:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. Skapa sedan en funktion som tar emot och lagrar namnområdet för tjänsten och SAS-nyckeln. Lägg till den här metoden utanför `Main()`. Några exempel: 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. I `Main()`, direkt efter anropet till `GenerateMessages()`, lägger du till en instruktion som anropar `CollectUserInput()`-metoden:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
        
        // Collect user input
        CollectUserInput();
    }
    ```

### Skapa lösningen

Från menyn **Skapa** i Visual Studio klickar du på **Skapa lösning** eller trycker på **Ctrl + Skift + B** för att bekräfta att det arbete du gjort hittills är korrekt.

## Skapa autentiseringsuppgifter för hantering

I det här steget definierar du vilka hanteringsåtgärder du ska använda för att skapa autentiseringsuppgifter för signatur för delad åtkomst (SAS) med vilka appen auktoriseras.

1. För att den här självstudiekursen ska bli tydligare placeras alla köåtgärder i en annan metod. Skapa en asynkron `Queue()`-metod i `Program`-klassen efter `Main()`-metoden. Några exempel:
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. Nästa steg är att skapa SAS-autentiseringsuppgifter med ett [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx)-objekt. I den här metoden används SAS-nyckelnamnet och värdet som hämtades i `CollectUserInput()`-metoden. Lägg till följande kod i `Queue()`-metoden:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. Skapa ett nytt objekt för hantering av namnområde med en URI som innehåller namnet på namnområdet och autentiseringsuppgifterna för hantering från det förra steget som argument. Lägg till den här koden direkt efter koden som lagts till i det förra steget. Ersätt `<yourNamespace>` med namnet på namnområdet för tjänsten:
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### Exempel

Nu bör koden se ut ungefär som följer:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## Skicka meddelanden till kön

I det här steget skapar du en kö och skickar meddelandena som finns i listan över asynkrona meddelanden till kön.

### Skapa en kö och skicka meddelanden till den

1. Skapa först kön. Kalla den till exempel `myQueue` och deklarera den direkt efter hanteringsåtgärderna som du lade till i `Queue()`-metoden i det senaste steget:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. I `Queue()`-metoden skapar du ett objekt för meddelandefabrik med en nyligen skapad Service Bus-URI som argument. Lägg till följande kod direkt efter hanteringsåtgärderna som du lade till i det senaste steget. Ersätt `<yourNamespace>` med namnet på namnområdet för tjänsten:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

1. Skapa sedan köobjektet med [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)-klassen. Lägg till följande kod direkt efter den du lade till i det senaste steget:

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```

1. Lägg till en kod som körs i en loop genom listan med asynkrona meddelanden som du skapade tidigare och skickar dem till kön. Lägg till följande kod direkt efter `CreateQueueClient()`-instruktionen i det förra steget:
    
    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## Ta emot meddelanden från kön

I det här steget hämtar du listan över meddelanden från kön som du skapade i det förra steget.

### Skapa en mottagare och ta emot meddelanden från kön

I `Queue()`-metoden itererar du igenom kön och tar emot meddelanden med [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx)-metoden, som skriver ut alla meddelanden till konsolen. Lägg till följande kod direkt efter den du lade till i det förra steget:

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Observera att `Thread.Sleep` bara används för att simulera bearbetningen av meddelandet. Du behöver förmodligen inte den i en riktig meddelandeapp.

### Avsluta kömetoden och rensa resurser

Lägg till följande kod direkt efter den förra för att rensa meddelandefabriken och köresurserna:

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### Anropa kömetoden

Det sista steget är att lägga till en instruktion som anropar  `Queue()`-metoden från `Main()`. Lägg till följande markerade kodrad i slutet av Main():
    
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
    
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
    
    // Add this call
    Queue();
}
```

### Exempel

Följande kod innehåller hela **QueueSample**-appen.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## Skapa och kör QueueSample-appen

Nu när du har slutfört ovanstående steg kan du skapa och köra **QueueSample**-appen.

### Skapa QueueSample-appen

I menyn **Skapa** i Visual Studio klickar du på **Skapa lösning** eller trycker på **CTRL+SKIFT+B**. Om det uppstår fel kontrollerar du att koden är korrekt baserad på det fullständiga exemplet som visas i slutet av det förra steget.

## Nästa steg

I den här självstudiekursen visades hur du skapar en klientapp och en tjänst för Service Bus med funktioner för asynkrona meddelanden i Service Bus. Information om [Service Bus Relay](service-bus-messaging-overview.md#Relayed-messaging) finns i [självstudiekursen för den vidarebefordrande meddelandetjänsten i Service Bus](../service-bus-relay/service-bus-relay-tutorial.md).

I följande avsnitt kan du lära dig mer om [Service Bus](https://azure.microsoft.com/services/service-bus/).

- [Översikt över meddelandetjänsten i Service Bus](service-bus-messaging-overview.md)
- [Grunderna i Service Bus](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus-arkitektur](../service-bus/service-bus-architecture.md)




<!--HONumber=Sep16_HO4-->


