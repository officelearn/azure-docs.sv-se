---
title: Självstudie – uppdatera butikssortimentet med publicera/avpublicera kanaler och ämnesfilter med Azure-portalen | Microsoft Docs
description: I den här självstudien får du lära dig hur du skickar och tar emot meddelanden från ett ämne och en prenumeration och hur du lägger till och använder filterregler med .NET
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.author: sethm
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 654cb09621837c360deccecb7778c5d467592dd1
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124222"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Självstudie: Uppdatera lagret med Azure-portalen och ämnen/prenumerationer

Azure Service Bus är en meddelandetjänst i molnet för flera klienter som skickar information mellan program och tjänst. Asynkrona åtgärder ger dig en flexibel, asynkron meddelandetjänst med funktioner för strukturerade meddelanden enligt FIFO-metoden (först-in-först-ut) och funktioner för publicering/prenumeration. Den här kursen visar hur du använder Service Bus-ämnen och prenumerationer i ett scenario med butikslager, med publicera/prenumerera kanaler med Azure-portalen och .NET.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Service Bus-ämne och en eller flera prenumerationer på det ämnet med Azure-portalen
> * Lägg till ämnesfilter med .NET-kod
> * Skapa två meddelanden med olika innehåll
> * Skicka meddelandena och verifiera att de anlände i de förväntade prenumerationerna
> * Ta emot meddelanden från prenumerationerna

Ett exempel på det här scenariot är en uppdatering av lagersortimentet för flera butiker. I det här scenariot, får varje butik eller uppsättning butiker meddelanden för att uppdatera sina sortiment. Den här självstudien visar hur du implementerar det här scenariot med prenumerationer och filter. Först skapar du ett ämne med 3 prenumerationer, lägger till några regler och filter och skickar och tar sedan emot meddelanden från ämnet och prenumerationerna.

![ämne](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa de här självstudierna ska du kontrollera att du har installerat:

- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](http://www.visualstudio.com/vs) eller senare.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), version 2.0 eller senare.

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus-ämnen och prenumerationer

Varje [prenumeration på ett ämne](service-bus-messaging-overview.md#topics) får en kopia av varje meddelande. Ämnen är fullständigt protokollmässigt och semantiskt kompatibla med Service Bus-köer. Service Bus-ämnen stöder en mängd olika urvalsregler med filtervillkor, med valfria åtgärder som anger eller ändrar meddelandeegenskaperna. Varje gång en regel matchar så genererar den ett meddelande. Mer information om regler, filter och åtgärder, finns på den här [länken](topic-filters.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Gå först till [Azure-portalen][Azure portal] och logga in med din Azure-prenumeration. Det första steget är att skapa ett Service Bus-namnområde av typen **Meddelanden**.

## <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus

Ett namnområde för Service Bus-meddelanden innehåller en unik omfattningscontainer som refereras till av dess [fullständigt kvalificerade domännamn][], där du skapar en eller fler köer, ämnen och prenumerationer. I följande exempel skapas ett namnområde för Service Bus-meddelanden i en ny eller befintlig [resursgrupp](/azure/azure-resource-manager/resource-group-portal):

1. I det vänstra navigeringsfältet i portalen, klickar du på **+ Skapa en resurs**, därefter klickar du på **Enterprise-integration** och sedan på **Service Bus**.
2. I dialogrutan **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.
3. När du har kontrollerat att namnet för namnområdet är tillgängligt, väljer du prisnivå (Standard eller Premium).
4. I fältet **Prenumeration** väljer du en Azure-prenumeration för vilken du vill skapa namnområdet.
5. I fältet **Resursgrupp** väljer du en befintlig resursgrupp där namnområdet ska finnas eller skapar en ny.      
6. I **Plats** väljer du land eller region där namnområdet ska finnas.
7. Klicka på **Skapa**. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.

  ![namnområde](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>Hämta autentiseringsuppgifterna för hantering

Om du skapar ett nytt namnområde så genereras en automatiskt en ursprunglig regel för signatur för delad åtkomst (SAS) med ett kopplat par med primära och sekundära nycklar som ger fullständig kontroll över alla namnområdets aspekter. Gör så här om du vill kopiera den ursprungliga regeln:

1. Klicka på **Alla resurser**, sedan klickar du på den nyligen skapade namnrymden.
2. I namnområdesfönstret, klickar du på **Principer för delad åtkomst**.
3. I fönstret **Principer för delad åtkomst**, klickar du på **RootManageSharedAccessKey**.
4. I fönstret **Princip: RootManageSharedAccessKey**, klickar du på knappen **Kopiera** bredvid **Primär anslutningssträng** för att kopiera anslutningssträngen till dina urklipp för senare användning. Klistra in det här värdet i Anteckningar eller på en tillfällig plats.

    ![connection-string][connection-string]
5. Upprepa föregående steg, kopiera och klistra in värdet för **Primärnyckeln** till en tillfällig plats för senare användning.

## <a name="create-a-topic-and-subscriptions"></a>Skapa ett ämne och en prenumeration

Du skapar ett Service Bus-ämne genom att ange det namnområde som du vill skapa det under. Följande exempel visar hur du skapar ett ämne på portalen:

1. I det vänstra navigeringsfönstret i portalen klickar du på **Service Bus** (om du inte ser **Service Bus** klickar du på **Alla tjänster**).
2. Klicka på det namnområde där du vill skapa ämnet.
3. I namnområdesfönstret, klickar du på **Ämnen** och därefter går du till fönstret **Köer** och klickar på **+ Ämnen**.
4. Ange ämnet **Namn** kön och lämna de andra värdena med sina standardvärden.
5. Klicka på **Skapa** längst ned i fönstret.
6. Anteckna ämnets namn.
7. Välj det avsnitt som du nyss skapade.
8. Klicka på **+ Prenumeration**, ange prenumerationens namn **S1** och lämna alla andra värden som standard.
9. Upprepa föregående steg två gånger till och skapa prenumerationer med namnet **S2** och **S3**.

## <a name="create-filter-rules-on-subscriptions"></a>Skapa filterregler för prenumerationer

När namnområdet och ämnet/sprenumerationerna har etablerats och du har de nödvändiga behörigheterna, är du redo att skapa filterregler för prenumerationerna och sedan skicka och ta emot meddelanden. Du kan granska koden i [den här GitHub-exempelmappen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/GettingStarted\BasicSendReceiveTutorialwithFilters).

### <a name="send-and-receive-messages"></a>Skicka och ta emot meddelanden

Om du vill köra koden gör du följande:

1. I en kommandotolk eller PowerShell-prompt, klonar du [Service Bus GitHub-lagringsplatsen](https://github.com/Azure/azure-service-bus/) genom att utfärda följande kommando:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Gå till exempelmappen `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters`.

3. Hämta anslutningssträngen som du kopierade till Anteckningar i avsnittet [Hämta autentiseringsuppgifter för hantering](#obtain-the-management-credentials) i den här självstudien. Du behöver också namnet på det ämne som du skapade i föregående avsnitt.

4. Skriv följande kommando i kommandotolken:

   ```shell
   dotnet build
   ```

5. Gå till mappen `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0`.

6. Skriv följande kommando för att köra programmet. Se till att ersätta `myConnectionString` med värdet som du tidigare fick och `myTopicName` med namnet på det ämne som du skapade:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Följ instruktionerna i konsolen för att välja filterskapande först. En del av att skapa filter är att ta bort standardfiltren. När du använder PowerShell eller CLI, behöver du inte ta bort standardfiltret, men om du gör detta i koden, måste du ta bort dem. Konsolkommandona 1 och 3 hjälper dig att hantera filter för de prenumerationer som du skapade tidigare:

   - Kör 1: för att ta bort standardfiltren.
   - Kör 2: för att lägga till dina egna filter.
   - Kör 3: för att alternativt ta bort dina egna filter. Obeservera att det här inte återskapar standardfiltren.

    ![Visar resultat av 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Efter att filtret har skapats, kan du skicka meddelanden. Tryck på 4 och notera 10 meddelanden som skickas till ämnet:

    ![Skicka utdata](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Tryck på 5 och se de meddelanden som tas emot. Om du inte fick tillbaka 10 meddelanden, trycker du på m för att visa menyn och tryck sedan på 5 igen.

    ![Ta emot utdata](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs, tar du bort namnområdet och kön. Om du vill göra det, väljer du resurserna i portalen och klickar på **Ta bort**.

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Det här avsnittet innehåller mer information om vad exempelkoden gör.

### <a name="get-connection-string-and-topic"></a>Hämta anslutningssträngen och ämnet

Koden deklarerar först en uppsättning variabler som driver den återstående körningen av programmet.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

Anslutningssträngen och ämnets namn skickas in via kommandoradsparametrar som det visas och därefter läses de i `Main()`-metoden:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Ta bort standardfiltren

När du skapar en prenumeration, skapar Service Bus ett standardfilter per prenumeration. Det här filtret låter dig ta emot alla meddelanden som skickas till ämnet. Om du vill använda anpassade filter kan du ta bort standardfiltret som visas i följande kod:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Skapa filter

Följande kod lägger till anpassade filter som definierats i den självstudien:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Ta bort dina anpassade skapade filter

Om du vill ta bort alla filter för din prenumeration, visar följande kod hur du gör detta:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Skicka meddelanden

Att skicka meddelanden till ett ämne liknar att skicka meddelanden till en kö. Det här exemplet visar hur du skickar meddelanden med hjälp av en uppgiftslista och asynkron bearbetning:

```csharp
public async Task SendMessages()
{
    try
    {
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Ta emot meddelanden

Meddelanden tas emot igen via en uppgiftslista och koden använder batchbearbetning. Du kan skicka och ta emot med batchbearbetning, men det här exemplet visar bara hur du tar emot i batch. I verkligheten, skulle du inte avbryta loopen, utan fortsätta den och ställa in ett högre tidsspann, till exempel en minut. Mottagningsanropet till den asynkrona meddelandekön hålls öppen under denna tid och om meddelanden anländer så returneras de omedelbart och ett nytt mottagningsanrop utfärdas. Det här konceptet kallas *lång avsökning*. Att använda mottagningspumpen som du ser i [snabbstarten](service-bus-quickstart-portal.md) och i flera andra exempel på lagringsplatsen, är ett mer typiskt alternativ.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }

    await receiver.CloseAsync();
}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien, etablerade du resurser med hjälp av Azure-portalen och därefter skickade du och tog emot meddelanden från ett Service Bus-ämne och dess prenumerationer. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Service Bus-ämne och en eller flera prenumerationer på det ämnet med Azure-portalen
> * Lägg till ämnesfilter med .NET-kod
> * Skapa två meddelanden med olika innehåll
> * Skicka meddelandena och verifiera att de anlände i de förväntade prenumerationerna
> * Ta emot meddelanden från prenumerationerna

Om du vill ha fler exempel på att skicka och ta emot meddelanden, kan du komma igång med [Service Bus-exempel på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Gå vidare till nästa självstudie för att läsa mer om att använda Service Bus-funktionerna publicera/prenumerera.

> [!div class="nextstepaction"]
> [Uppdatera lagerplatsen med PowerShell och ämnen/prenumerationer](service-bus-tutorial-topics-subscriptions-powershell.md)

[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fullständigt kvalificerade domännamn]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png