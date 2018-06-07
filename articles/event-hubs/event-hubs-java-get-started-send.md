---
title: Skicka händelser till Azure Event Hubs använder Java | Microsoft Docs
description: Kom igång skickar till Händelsehubbar som använder Java
services: event-hubs
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 05/30/2018
ms.author: sethm
ms.openlocfilehash: 6d3bf0b8ac5c5bdc7bf3deda21e800fe3cc6be2e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626419"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Skicka händelser till Azure Event Hubs använder Java

Händelsehubbar är en mycket skalbar införandet system som kan mata in miljontals händelser per sekund, aktivera ett program för att bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. När uppgifterna väl samlats i en händelsehubb kan du omvandla och lagra data med hjälp av en leverantör av realtidsanalys eller lagringskluster.

Mer information finns i [översikt av Händelsehubbar][Event Hubs overview].

Den här kursen visar hur du skickar händelser till en händelsehubb med hjälp av ett konsolprogram i Java. För att ta emot händelser med hjälp av värd för händelsebearbetning Java-bibliotek finns [i den här artikeln](event-hubs-java-get-started-receive-eph.md), eller klicka på det mottagande språket i den vänstra tabellen i innehållet.

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här kursen behöver du följande krav:

* Java development environment. Den här kursen använder [Eclipse](https://www.eclipse.org/).
* Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto][] innan du börjar.

Koden i den här kursen är baserad på den [SimpleSend GitHub exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), som du kan pröva för att se hela fungerande program.

## <a name="send-events-to-event-hubs"></a>Skicka händelser till Händelsehubbar

Java-klientbibliotek för Händelsehubbar är tillgänglig för användning i Maven-projekt från den [Maven centrallager](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Du kan referera till det här biblioteket med följande beroende deklarationen i projektfilen Maven. Den aktuella versionen är 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
```

För olika typer av versionsmiljöer du explicit kan hämta de senaste utgivna JAR-filerna från de [Maven centrallager](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

En enkel händelse utgivare och importera den *com.microsoft.azure.eventhubs* paketet för Händelsehubbar klientklasser och *com.microsoft.azure.servicebus* paketet för verktygsklasser som vanliga undantag som delas med Azure Service Bus-klientprogramvara. 

### <a name="declare-the-send-class"></a>Deklarera klassen skicka

För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Klassen namnet `Send`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.PartitionSender;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Send {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>Skapa anslutningssträng

Använd klassen ConnectionStringBuilder för att konstruera ett strängvärde för anslutning till skickar till Händelsehubbar klient-instans. Ersätt platshållarna med värdena som du fick när du skapade hubben namnområde och händelse:

```java
   final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
      .setNamespaceName("----NamespaceName-----")
      .setEventHubName("----EventHubName-----")
      .setSasKeyName("-----SharedAccessSignatureKeyName-----")
      .setSasKey("---SharedAccessSignatureKey----");
```

### <a name="send-events"></a>Skicka händelser

Skapa en enkel händelse genom att omvandla en sträng i UTF-8 byte kodningen. Sedan skapar en ny instans av Händelsehubbar klienten från anslutningssträngen och skicka meddelandet:   

```java 
byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
EventData sendEvent = new EventData(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Ta emot händelser med hjälp av EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Översikt av händelsehubbar][Event Hubs overview]
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

