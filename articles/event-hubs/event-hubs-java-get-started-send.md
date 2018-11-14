---
title: Skicka händelser till Azure Event Hubs med Java | Microsoft Docs
description: Kom igång med att skicka till Event Hubs med Java
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 11/12/2018
ms.author: shvija
ms.openlocfilehash: 510f1a2bc23d14e1bb9e8e561b52936ae9d53685
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624547"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Skicka händelser till Azure Event Hubs med Java

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudiekursen visar hur du skickar händelser till en händelsehubb med hjälp av ett konsolprogram som skrivits i Java. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

* En Java-utvecklingsmiljön. Den här självstudien används [Eclipse](https://www.eclipse.org/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett namnområde för Event Hubs och en händelsehubb
Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md) och fortsätter sedan enligt följande steg i den här självstudien.

## <a name="add-reference-to-azure-event-hubs-library"></a>Lägg till referens till Azure Event Hubs-biblioteket

Java-klientbibliotek för Event Hubs är tillgängliga för användning i Maven-projekt från den [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Du kan referera till det här biblioteket med följande beroendedeklaration i Maven-projektfilen. Den aktuella versionen är 1.0.2:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

För olika typer av versionsmiljöer kan du uttryckligen hämta de senast utgivna JAR-filerna från den [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

För en enkel händelse utgivare och importera den *com.microsoft.azure.eventhubs* paketet för Event Hubs-klientklasser och *com.microsoft.azure.servicebus* Paketera för verktygsklasser som vanliga undantag som delas med Azure Service Bus meddelandeklient. 

## <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Namnge klassen `SimpleSend`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Skapa anslutningssträng

Använda klassen ConnectionStringBuilder för att konstruera ett Anslutningssträngens värde att skicka till Event Hubs klientinstans. Ersätt platshållarna med de värden du erhöll när du skapade namnområde och en händelsehubb:

```java
final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
        .setNamespaceName("Your Event Hubs namespace name")
        .setEventHubName("Your event hub")
        .setSasKeyName("Your policy name")
        .setSasKey("Your primary SAS key");
```

### <a name="send-events"></a>Skicka händelser

Skapa en enda händelse genom att omvandla en sträng till UTF-8 byte kodningen. Sedan skapar en ny instans för Event Hubs-klienten från anslutningssträngen och skicka meddelandet:   

```java 
String payload = "Message " + Integer.toString(i);
byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
EventData sendEvent = EventData.create(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

Skapa och kör programmet och kontrollera att det inte finns några fel.

Grattis! Du har nu skickat meddelanden till en händelsehubb.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Bilaga: Hur meddelanden skickas till EventHub-partitioner

Innan meddelanden hämtas av användare, har de publiceras till partitionerna först av utgivarna. När meddelanden har publicerats till event hub synkront med metoden sendSync() com.microsoft.azure.eventhubs.EventHubClient-objektet visas kan meddelandet skickas till en specifik partition eller distribueras till alla tillgängliga partitioner på ett sätt för resursallokering beroende på om partitionsnyckel anges eller inte.

När du anger en sträng som representerar Partitionsnyckeln ska nyckeln kodas för att avgöra vilken partition som ska skicka händelsen till.

När Partitionsnyckeln inte har angetts, sedan kommer meddelanden round robined till alla tillgängliga partitioner

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skickat meddelanden till en event hub med Java. Läs hur du tar emot händelser från en event hub med Java i [ta emot händelser från event hub - Java](event-hubs-java-get-started-receive-eph.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

