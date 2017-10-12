---
title: "Översikt över prisnivåer för Azure Service Bus Premium- och Standard-meddelanden | Microsoft Docs"
description: "Service Bus Premium- och Standard-meddelandenivåer"
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: 
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: darosa;sethm
ms.openlocfilehash: a0c169e0580468e83a07c077f8c60e83d3fb52f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus Premium- och Standard-meddelandenivåer

Service Bus-meddelanden, inklusive entiteter som köer och ämnen, kombinerar meddelandefunktioner för företag med utförlig publicerings-/prenumerationssemantik i molnskala. Service Bus-meddelanden används som kommunikationsryggrad i flera avancerade molnlösningar.

*Premium*-nivån av Service Bus-meddelanden är ett svar på vanliga kundönskemål gällande skala, prestanda och tillgänglighet för verksamhetskritiska program. Även om funktionsuppsättningarna är snudd på identiska är dessa två nivåer av Service Bus-meddelanden utformade för att passa olika användningsfall.

En del övergripande skillnader visas i tabellen nedan.

| Premium | Standard |
| --- | --- |
| Högt genomflöde |Variabelt genomflöde |
| Förutsägbar prestanda |Variabel svarstid |
| Fast prissättning |Variabla priser – betala per användning |
| Möjlighet att skala arbetsbelastningen uppåt och nedåt |Saknas |
| Meddelandestorlek upp till 1 MB |Meddelandestorlek upp till 256 kB |

**Service Bus Premium-meddelanden** ger resursisolering på processor- och minnesnivån så att varje kunds arbetsbelastning körs i isolering. Den här resursbehållaren kallas för en *meddelandefunktionsenhet*. Varje Premium-namnområde allokeras minst en meddelandefunktionsenhet. Du kan köpa 1, 2 eller 4 meddelandefunktionsenheter för varje Service Bus Premium-namnområde. En enda arbetsbelastning eller enhet kan spänna över flera meddelandefunktionsenheter, och antalet meddelandefunktionsenheter kan ändras när du vill, även om faktureringen är per 24 timmar eller daglig taxa. Resultatet är förutsägbara och repeterbara prestanda för Service Bus-lösningen.

Prestanda är inte bara mer förutsägbara och tillgängliga, utan de är snabbare också. Service Bus Premium-meddelanden bygger på lagringsmotorn som introducerades i [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Med Premium-meddelanden är topprestandan mycket snabbare än på standardnivån.

## <a name="premium-messaging-technical-differences"></a>Premium-meddelanden – tekniska skillnader

I följande avsnitt diskuteras några skillnader mellan Premium- och Standard-meddelandenivåerna.

### <a name="partitioned-queues-and-topics"></a>Partitionerade köer och ämnen

Partitionerade köer och ämnen stöds i Premium – meddelandefunktion. Entiteterna är faktiskt alltid partitionerade (och går inte att inaktivera). Men Premium-partitionerade köer och ämnen fungerar inte på samma sätt som på Standard-nivån av Service Bus-meddelanden. Premium-meddelanden använder inte SQL som ett datalager och har inte längre den konkurrens om resurser som förknippas med en delad plattform. Därför är partitionering inte nödvändigt för att förbättra prestanda. Dessutom har antalet partitioner ändrats från 16 partitioner i Standard-meddelanden till 2 partitioner i Premium. Med två partitioner garanteras tillgänglighet och är ett mer passande antal för Premium-körningsmiljön. 

Med premiumfunktioner för meddelandehantering kan du ange storleken på en entitet med [MaxSizeInMegabytes](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxsizeinmegabytes#Microsoft_ServiceBus_Messaging_QueueDescription_MaxSizeInMegabytes), där storleken delas lika mellan de två partitionerna, till skillnad från [standardpartitionerade entiteter](service-bus-partitioning.md#standard), där den totala storleken är 16 gånger den angivna storleken. 

Mer information om partitionering finns i [Partitionerade köer och ämnen](service-bus-partitioning.md).

### <a name="express-entities"></a>Expressenheter

Eftersom meddelandehanteringen på premiumnivå körs i en helt isolerad körningsmiljö, stöds inte längre expressenheter i premiumnamnområden. Mer information om expressfunktionen finns i egenskapen [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Om du har kod som körs med meddelandehantering på standardnivå och vill portera den till premiumnivån kontrollerar du att egenskapen [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) har värdet **false** (standardvärdet).

## <a name="get-started-with-premium-messaging"></a>Kom igång med Premium-meddelandetjänster

Det är enkelt att komma igång med Premium-meddelandetjänster och processen liknar den för Standard-meddelandetjänster. Börja med att [skapa ett namnområde](service-bus-create-namespace-portal.md). Se till att du väljer **Premium** under **Prisnivå**.

![create-premium-namespace][create-premium-namespace]

Du kan också skapa [Premium-namnområden med hjälp av Azure Resource Manager-mallar](https://azure.microsoft.com/en-us/resources/templates/101-servicebus-pn-ar/).


## <a name="next-steps"></a>Nästa steg

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

* [Introduktion till Azure Service Bus Premium-meddelanden (blogginlägg)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introduktion till Azure Service Bus Premium-meddelanden (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Översikt över Service Bus-meddelanden](service-bus-messaging-overview.md)
* [Använd Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
