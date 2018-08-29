---
title: Skicka händelser till Azure Event Hubs med Java | Microsoft Docs
description: Kom igång med att skicka till Event Hubs med Java
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 08/27/2018
ms.author: shvija
ms.openlocfilehash: f67982eda60a8fdfdf0d50785827c513275fd202
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124763"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Skicka händelser till Azure Event Hubs med Java

Händelsehubbar är en mycket skalbar inmatning system som kan mata in miljontals händelser per sekund, aktivera ett program för att bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. När samlats in i en händelsehubb, kan du omvandla och lagra dem med valfri leverantör av realtidsanalys eller lagringskluster.

Mer information finns i den [översikt av Händelsehubbar][Event Hubs overview].

Den här självstudiekursen visar hur du skickar händelser till en händelsehubb med hjälp av ett konsolprogram i Java. Om du vill ta emot händelser med Java Event Processor Host-biblioteket, se [i den här artikeln](event-hubs-java-get-started-receive-eph.md), eller klicka på ditt mottagarspråk i den vänstra innehållsförteckningen.

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudien behöver du följande krav:

* En Java-utvecklingsmiljön. Den här självstudien används [Eclipse](https://www.eclipse.org/).
* Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.

Koden i den här självstudien är baserad på den [SimpleSend GitHub-exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), som du kan kontrollera för att se fullständiga fungerande program.

## <a name="send-events-to-event-hubs"></a>Skicka händelser till Event Hubs

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

### <a name="declare-the-send-class"></a>Deklarera Send-klassen

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

### <a name="how-messages-are-routed-to-eventhub-partitions"></a>Hur meddelanden skickas till EventHub-partitioner

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

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Ta emot händelser med EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Översikt av händelsehubbar][Event Hubs overview]
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

