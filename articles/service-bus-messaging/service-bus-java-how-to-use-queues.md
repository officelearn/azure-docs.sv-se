---
title: Använda Azure Service Bus-köer med Java
description: I den här självstudien får du lära dig hur du skapar Java-program för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus-kö.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 184ffd39281ea27d8596bc37a9f89fd22acfb1ba
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732172"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Snabbstart: Använd Azure Service Bus-köer med Java för att skicka och ta emot meddelanden

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
I den här självstudien får du lära dig hur du skapar Java-program för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus-kö. 

> [!NOTE]
> Du hittar Java-exempel på GitHub i [azure-service-bus-databasen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

## <a name="prerequisites"></a>Krav
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [msdn-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Om du inte har en kö att arbeta med följer du stegen i [Azure-portalen för att skapa en](service-bus-quickstart-portal.md) köartikel för Service Bus för att skapa en kö.
    1. Läs snabb **översikten över** servicebussköer . **queues** 
    2. Skapa ett **servicebussnamnområde**. 
    3. Hämta **anslutningssträngen**.
    4. Skapa en **servicebusskö**.
3. Installera [Azure SDK för Java][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program så att det använder Service Bus
Kontrollera att du har installerat [Azure SDK för Java][Azure SDK for Java] innan du skapar det här exemplet. 

Om du använder Eclipse kan du installera [Azure Toolkit för Eclipse][Azure Toolkit for Eclipse] som innehåller Azure SDK för Java. Du kan sedan lägga till **Microsoft Azure-bibliotek för Java** i projektet. Om du använder IntelliJ läser du [Installera Azure Toolkit för IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 

![Lägga till Microsoft Azure-bibliotek för Java i ditt Eclipse-projekt](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Lägg till `import` följande satser högst upp i Java-filen:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Om du vill skicka meddelanden till en servicebusskö instansierar programmet ett **QueueClient-objekt** och skickar meddelanden asynkront. Följande kod visar hur du skickar ett meddelande för en kö som har skapats via portalen.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Meddelanden som skickas till och tas emot från Service Bus-köer är instanser av klassen [Meddelande.](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) Meddelandeobjekt har en uppsättning standardegenskaper (till exempel Etikett och TimeToLive), en ordlista som används för att lagra anpassade programspecifika egenskaper och en samling godtyckliga programdata. Ett program kan ställa in meddelandets brödtext genom att skicka ett serialiserbart objekt till konstruktorn för meddelandet, och lämplig serialiserare används sedan för att serialisera objektet. Alternativt kan du ge en **java. Io. InputStream-objekt.**


Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Det primära sättet att ta emot meddelanden från en kö är att använda ett **ServiceBusContract-objekt.** Mottagna meddelanden kan fungera i två olika lägen: **ReceiveAndDelete** och **PeekLock**.

När du använder **läget ReceiveAndDelete** är mottagning en enda skottåtgärd , det vill när Service Bus tar emot en läsbegäran för ett meddelande i en kö markeras meddelandet som förbrukat och returnerar det till programmet. **ReceiveAndDelete-läge** (som är standardläget) är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande i händelse av ett fel. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran.
Eftersom Service Bus har markerat meddelandet som förbrukat, då när programmet startas om och börjar konsumera meddelanden igen, har det missat meddelandet som förbrukades före kraschen.

I **PeekLock-läge** blir mottagning en tvåstegsåtgärd, vilket gör det möjligt att stödja program som inte tolererar meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning) slutförs den andra fasen av mottagningsprocessen genom att anropa **complete()** på det mottagna meddelandet. När Service Bus ser **hela()** anropet markeras meddelandet som förbrukat och tar bort det från kön. 

I följande exempel visas hur meddelanden kan tas emot och bearbetas med **PeekLock-läge** (inte standardläget). I exemplet nedan används motringningsmodellen med en registrerad meddelandehanterare `TestQueue`och bearbetar meddelanden när de kommer in i vår . Det här läget anropar **complete()** automatiskt när motringningen returnerar normalt och anropar **abandon()** om motringningen genererar ett undantag. 

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
        // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram inte kan bearbeta meddelandet av någon anledning kan det anropa metoden **abandon()** på klientobjektet med det mottagna meddelandets låstoken som erhållits via **getLockToken()**. Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en timeout som är associerad med ett meddelande som är låst i kön, och om programmet inte kan bearbeta meddelandet innan låstidsgränsen upphör att gälla (till exempel om programmet kraschar), låses Service Bus upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

I händelse av att programmet kraschar efter bearbetning av meddelandet men innan **den fullständiga()** begäran utfärdas, levereras meddelandet till programmet när det startas om. Det här kallas ofta för *At Least Once Processing*, det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av **getMessageId-metoden** för meddelandet, som förblir konstant över leveransförsök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett servicebussnamnområde och administrera meddelandeenheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar. 

## <a name="next-steps"></a>Efterföljande moment
Nu när du har lärt dig grunderna i servicebussköer läser du [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions] för mer information.

Mer information finns på [Java Developer Center](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
