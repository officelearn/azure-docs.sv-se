---
title: "Skicka händelser till Azure Event Hubs använder Java | Microsoft Docs"
description: "Kom igång skickar till Händelsehubbar som använder Java"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 5c8c24e1f168be4b46ccfdb1d0c268866fc8ff7d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Skicka händelser till Azure Event Hubs använder Java

## <a name="introduction"></a>Introduktion
Händelsehubbar är en mycket skalbar införandet system som kan mata in miljontals händelser per sekund, aktivera ett program för att bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. När uppgifterna väl samlats i en händelsehubb kan du omvandla och lagra data med hjälp av en leverantör av realtidsanalys eller lagringskluster.

Mer information finns i [översikt av Händelsehubbar][Event Hubs overview].

Den här kursen visar hur du skickar händelser till en händelsehubb med hjälp av ett konsolprogram i Java. För att ta emot händelser med hjälp av värd för händelsebearbetning Java-bibliotek finns [i den här artikeln](event-hubs-java-get-started-receive-eph.md), eller klicka på det mottagande språket i den vänstra tabellen i innehållet.

För att kunna slutföra den här kursen behöver du följande:

* Java development environment. Den här självstudiekursen förutsätter vi att [Eclipse](https://www.eclipse.org/).
* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett kostnadsfritt konto på bara några minuter. Mer information om den <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">kostnadsfria utvärderingsversionen av Azure</a>.

## <a name="send-messages-to-event-hubs"></a>Skicka meddelanden till Event Hubs
Java-klientbibliotek för Händelsehubbar är tillgänglig för användning i Maven-projekt från den [Maven centrallager](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Du kan referera till det här biblioteket med följande beroende deklarationen i projektfilen Maven:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

För olika typer av versionsmiljöer du explicit kan hämta de senaste utgivna JAR-filerna från de [Maven centrallager](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

En enkel händelse utgivare och importera den *com.microsoft.azure.eventhubs* paketet för Händelsehubbar klientklasser och *com.microsoft.azure.servicebus* paketet för verktygsklasser som vanliga undantag som delas med Azure Service Bus-klientprogramvara. 

För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Klassen namnet `Send`.     

```java
import java.io.IOException;
import java.nio.charset.*;
import java.util.*;

import com.microsoft.azure.eventhubs.*;

public class Send
{
    public static void main(String[] args) 
            throws EventHubException, IOException
    {
```

Ersätt namnen på namnområde och händelsen hubb med de värden som används när du skapade händelsehubben.

```java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Skapa sedan en enda händelse genom att omvandla en sträng i UTF-8 byte kodningen. Sedan skapar en ny instans av Händelsehubbar klienten från anslutningssträngen och skicka meddelandet.   

```java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    
    // close the client at the end of your program
    ehClient.closeSync();
    }
}

``` 

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Ta emot händelser med hjälp av EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Översikt av händelsehubbar][Event Hubs overview]
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
