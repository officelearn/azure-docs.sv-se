---
title: Självstudie – uppdatera butikssortimentet med publicera/avpublicera kanaler och ämnesfilter med Azure CLI | Microsoft Docs
description: I den här självstudien får du lära dig hur du skickar och tar emot meddelanden från ett ämne och en prenumeration och hur du lägger till och använder filterregler med Azure CLI
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.author: sethm
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 42f0781de5412310ecb5326f0384268aba9c53dd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651677"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Självstudie: Uppdatera lagret med CLI och ämnen/prenumerationer

Azure Service Bus är en meddelandetjänst i molnet för flera klienter som skickar information mellan program och tjänst. Asynkrona åtgärder ger dig en flexibel, asynkron meddelandetjänst med funktioner för strukturerade meddelanden enligt FIFO-metoden (först-in-först-ut) och funktioner för publicering/prenumeration. Den här kursen visar hur du använder Service Bus-ämnen och prenumerationer i ett scenario med butikslager, med publicera/prenumerera kanaler med Azure CLI och Java.

I den här guiden får du lära dig hur man:
> [!div class="checklist"]
> * Skapa ett Service Bus-ämne och en eller flera prenumerationer på det ämnet med Azure CLI
> * Lägg till ämnesfilter med Azure CLI
> * Skapa två meddelanden med olika innehåll
> * Skicka meddelandena och verifiera att de anlände i de förväntade prenumerationerna
> * Ta emot meddelanden från prenumerationerna

Ett exempel på det här scenariot är en uppdatering av lagersortimentet för flera butiker. I det här scenariot, får varje butik eller uppsättning butiker meddelanden för att uppdatera sina sortiment. Den här självstudien visar hur du implementerar det här scenariot med prenumerationer och filter. Först skapar du ett ämne med 3 prenumerationer, lägger till några regler och filter och skickar och tar sedan emot meddelanden från ämnet och prenumerationerna.

![ämne](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][] konto innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna utveckla Service Bus-appar med Java, måste du ha följande installerat:

- [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), senaste versionen.
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org), version 3.0 eller senare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus-ämnen och prenumerationer

Varje [prenumeration på ett ämne](service-bus-messaging-overview.md#topics) får en kopia av varje meddelande. Ämnen är fullständigt protokollmässigt och semantiskt kompatibla med Service Bus-köer. Service Bus-ämnen stöder en mängd olika urvalsregler med filtervillkor, med valfria åtgärder som anger eller ändrar meddelandeegenskaperna. Varje gång en regel matchar så genererar den ett meddelande. Mer information om regler, filter och åtgärder, finns på den här [länken](topic-filters.md).

## <a name="log-in-to-azure"></a>Logga in på Azure

När CLI har installerats, öppnar du en kommandotolk och utfärdar följande kommandon för att logga in på Azure. De här stegen är inte nödvändiga om du använder Cloud Shell:

1. Om du använder Azure CLI lokalt, kör du följande kommando för att logga in på Azure. Det här inloggningssteget behövs inte om du kör de här kommandona i Cloud Shell:

   ```azurecli-interactive
   az login
   ```

2. Ange den aktuella prenumerationskontexten till den Azure-prenumeration du vill använda:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>Använd CLI för att etablera resurser

Utfärda följande kommandon för att etablera Service Bus-resurser. Tänk på att ersätta alla platshållare med lämpliga värden:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Efter att det sista kommandot körts, kopierar och klistrar du in anslutningssträngen och det könamn som du valde till en tillfällig plats, till exempel Anteckningar. Du behöver den i nästa steg.

## <a name="create-filter-rules-on-subscriptions"></a>Skapa filterregler för prenumerationer

När namnområdet och ämnet/sprenumerationerna har etablerats och du har de nödvändiga behörigheterna, är du redo att skapa filterregler för prenumerationerna och sedan skicka och ta emot meddelanden. Du kan granska koden i [den här GitHub-exempelmappen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/quickstarts-and-tutorials/tutorial-topics-subscriptions-filters-java/src/main/java/com/microsoft/azure/).

## <a name="send-and-receive-messages"></a>Skicka och ta emot meddelanden

1. Se till att Cloud Shell är öppet och visar Bash-prompten.

2. Klona [Service Bus GitHub-lagringsplatsen](https://github.com/Azure/azure-service-bus/) genom att utfärda följande kommando:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Navigera till exempelmappen `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java`. Observera att kommandona i Bash-gränssnittet är skiftlägeskänsliga och avgränsare måste vara snedstreck.

3. Utfärda följande kommando för att skapa programmet:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Utfärda följande kommando för att köra programmet. Se till att ersätta platshållarna med den anslutningssträng och det ämnesnamn som du fick i föregående steg:

   ```shell
  java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Observera hur 10 meddelanden skickas till ämnet och därefter tas emot från de enskilda prenumerationerna:

   ![programmets utdata](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Rensa resurser

Kör följande kommando för att ta bort resursgruppen, namnområdet och alla relaterade resurser:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Det här avsnittet innehåller mer information om vad exempelkoden gör.

### <a name="get-connection-string-and-queue"></a>Hämta anslutningssträngen och kön

Koden deklarerar först en uppsättning variabler som driver den återstående körningen av programmet:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";    
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

Anslutningssträngen och ämnesnamnet är de enda värden som läggs till via kommandoradsparametrar och skickas till `main()`. Den faktiska kodkörningen utlöses i `run()`-metoden och skickar och tar därefter emot meddelanden från ämnet:

```java
public static void main(String[] args) {
        TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
        // Send sample messages.
        this.sendMessagesToTopic();

        // Receive messages from subscriptions.
        this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Skapa ämnesklient för att skicka meddelanden

För att skicka och ta emot meddelanden, skapar `sendMessagesToTopic()`-metoden skapar en instans av ämnesklienten som använder anslutningssträngen och ämnesnamnet och därefter anropar en annan metod som skickar meddelandena:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
         // Create client for the topic.
        TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

        // Create a message sender from the topic client.

        System.out.printf("\nSending orders to topic.\n");

        // Now we can start sending orders.
        CompletableFuture.allOf(
                SendOrders(topicClient,Store[0]),
                SendOrders(topicClient,Store[1]),
                SendOrders(topicClient,Store[2]),
                SendOrders(topicClient,Store[3]),
                SendOrders(topicClient,Store[4]),
                SendOrders(topicClient,Store[5]),
                SendOrders(topicClient,Store[6]),
                SendOrders(topicClient,Store[7]),
                SendOrders(topicClient,Store[8]),
                SendOrders(topicClient,Store[9])                
        ).join();

        System.out.printf("\nAll messages sent.\n");
    }

     public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));         
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8)); 
            // We always set the Sent to field
            message.setTo(store);    
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both. 
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});
                        
            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());            
            topicClient.sendAsync(message);
        }
               
        return new CompletableFuture().completedFuture(null);         
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Ta emot meddelanden från de enskilda prenumerationerna

`receiveAllMessages()`-metoden anropar `receiveAllMessageFromSubscription()`-metoden, som därefter skapar en prenumerationsklient per anrop och tar emot meddelanden från de enskilda prenumerationerna:

```java
public void receiveAllMessages() throws Exception {     
    System.out.printf("\nStart Receiving Messages.\n");
    
    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2]) 
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {
        
        int receivedMessages = 0;

        // Create subscription client.
        IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

        // Create a receiver from the subscription client and receive all messages.
        System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

        while (true)
        {
            // This will make the connection wait for N seconds if new messages are available. 
            // If no additional messages come we close the connection. This can also be used to realize long polling.
            // In case of long polling you would obviously set it more to e.g. 60 seconds.
            IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
            if (receivedMessage != null)
            {
                if ( receivedMessage.getProperties() != null ) {                                                                                
                    System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));                                                                                          
                    
                    // Show the label modified by the rule action
                    if(receivedMessage.getLabel() != null)
                        System.out.printf("Label=%s\n", receivedMessage.getLabel());   
                }
                
                byte[] body = receivedMessage.getBody();
                Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
                System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());                          
                
                subscriptionClient.complete(receivedMessage.getLockToken());
                receivedMessages++;
            }
            else
            {
                // No more messages to receive.
                subscriptionClient.close();
                break;
            }
        }
        System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
        
        return new CompletableFuture().completedFuture(null);
}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien, etablerade du resurser med hjälp av Azure CLI och därefter skickade du och tog emot meddelanden från ett Service Bus-ämne och dess prenumerationer. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Service Bus-ämne och en eller flera prenumerationer på det ämnet med Azure-portalen
> * Lägg till ämnesfilter med .NET-kod
> * Skapa två meddelanden med olika innehåll
> * Skicka meddelandena och verifiera att de anlände i de förväntade prenumerationerna
> * Ta emot meddelanden från prenumerationerna

Om du vill ha fler exempel på att skicka och ta emot meddelanden, kan du komma igång med [Service Bus-exempel på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Gå vidare till nästa självstudie för att läsa mer om att använda Service Bus-funktionerna publicera/prenumerera.

> [!div class="nextstepaction"]
> [Uppdatera lagerplatsen med PowerShell och ämnen/prenumerationer](service-bus-tutorial-topics-subscriptions-portal.md)

[kostnadsfritt]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install Azure CLI 2.0]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create