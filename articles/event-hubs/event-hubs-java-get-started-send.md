---
title: Skicka händelser till Azure Event Hubs använder Java | Microsoft Docs
description: Kom igång skickar till Händelsehubbar som använder Java
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.openlocfilehash: 5dd0c88dab9ff4b7073a9acf6872b4c3ff085586
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Skicka händelser till Azure Event Hubs använder Java

Händelsehubbar är en mycket skalbar införandet system som kan mata in miljontals händelser per sekund, aktivera ett program för att bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. När uppgifterna väl samlats i en händelsehubb kan du omvandla och lagra data med hjälp av en leverantör av realtidsanalys eller lagringskluster.

Mer information finns i [översikt av Händelsehubbar][Event Hubs overview].

Den här kursen visar hur du skickar händelser till en händelsehubb med hjälp av ett konsolprogram i Java. För att ta emot händelser med hjälp av värd för händelsebearbetning Java-bibliotek finns [i den här artikeln](event-hubs-java-get-started-receive-eph.md), eller klicka på det mottagande språket i den vänstra tabellen i innehållet.

För att kunna slutföra den här kursen behöver du följande:

* Java development environment. Den här självstudiekursen förutsätter vi att [Eclipse](https://www.eclipse.org/).
* Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto][] innan du börjar.

Koden i den här kursen är baserad på den [skicka GitHub exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/Send), som du kan pröva för att se hela fungerande program.

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

Skapa sedan en enda händelse genom att omvandla en sträng i UTF-8 byte kodningen. Sedan skapar en ny instans av Händelsehubbar klienten från anslutningssträngen och skicka meddelandet.   

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

