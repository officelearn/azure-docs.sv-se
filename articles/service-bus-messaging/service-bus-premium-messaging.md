---
title: Azure Service Bus premium och standardnivåer
description: I den här artikeln beskrivs standard- och premiumnivåer för Azure Service Bus. Jämför dessa nivåer och ger tekniska skillnader.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774568"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus Premium- och Standard-meddelandenivåer

Service Bus-meddelanden, inklusive entiteter som köer och ämnen, kombinerar meddelandefunktioner för företag med utförlig publicerings-/prenumerationssemantik i molnskala. Service Bus-meddelanden används som kommunikationsryggrad i flera avancerade molnlösningar.

*Premium*-nivån av Service Bus-meddelanden är ett svar på vanliga kundönskemål gällande skala, prestanda och tillgänglighet för verksamhetskritiska program. Premium-nivån rekommenderas för användning i produktionsmiljö. Även om funktionsuppsättningarna är snudd på identiska är dessa två nivåer av Service Bus-meddelanden utformade för att passa olika användningsfall.

En del övergripande skillnader visas i tabellen nedan.

| Premium | Standard |
| --- | --- |
| Högt genomflöde |Variabelt genomflöde |
| Förutsägbar prestanda |Variabel svarstid |
| Fast prissättning |Variabla priser – betala per användning |
| Möjlighet att skala arbetsbelastningen uppåt och nedåt |Ej tillämpligt |
| Meddelandestorlek upp till 1 MB |Meddelandestorlek upp till 256 kB |

**Service Bus Premium-meddelanden** ger resursisolering på processor- och minnesnivån så att varje kunds arbetsbelastning körs i isolering. Den här resursbehållaren kallas *för en meddelandeenhet*. Varje Premium-namnområde allokeras minst en meddelandefunktionsenhet. Du kan köpa 1, 2, 4 eller 8 meddelandeenheter för varje Service Bus Premium-namnområde. En enda arbetsbelastning eller entitet kan sträcka sig över flera meddelandeenheter och antalet meddelandeenheter kan ändras efter behag. Resultatet är förutsägbara och repeterbara prestanda för Service Bus-lösningen.

Prestanda är inte bara mer förutsägbara och tillgängliga, utan de är snabbare också. Service Bus Premium Messaging bygger på lagringsmotorn som introducerades i [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Med Premium-meddelanden är topprestandan mycket snabbare än på standardnivån.

## <a name="premium-messaging-technical-differences"></a>Premium-meddelanden – tekniska skillnader

I följande avsnitt diskuteras några skillnader mellan Premium- och Standard-meddelandenivåerna.

### <a name="partitioned-queues-and-topics"></a>Partitionerade köer och ämnen

Det finns inget stöd för partitionerade köer och ämnen i Premium Messaging. Mer information om partitionering finns i [Partitionerade köer och ämnen](service-bus-partitioning.md).

### <a name="express-entities"></a>Expressenheter

Eftersom meddelandehanteringen på premiumnivå körs i en helt isolerad körningsmiljö, stöds inte längre expressenheter i premiumnamnområden. Mer information om expressfunktionen finns i egenskapen [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Om du har kod som körs med meddelandehantering på standardnivå och vill portera den till premiumnivån kontrollerar du att egenskapen [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) har värdet **false** (standardvärdet).

## <a name="premium-messaging-resource-usage"></a>Resursanvändning för Premium Messaging
I allmänhet kan alla åtgärder på en entitet orsaka CPU- och minnesanvändning. Här är några av dessa åtgärder: 

- Hanteringsåtgärder som CRUD-åtgärder (Skapa, Hämta, Uppdatera och Ta bort) i köer, ämnen och prenumerationer.
- Körningsåtgärder (skicka och ta emot meddelanden)
- Övervakning av åtgärder och varningar

Den extra CPU och minnesanvändning är inte prissatt dessutom dock. För Premium Messaging-nivån finns ett enda pris för meddelandeenheten.

Cpu- och minnesanvändningen spåras och visas för dig av följande skäl: 

- Ge insyn i systemets interna
- Förstå kapaciteten hos köpta resurser.
- Kapacitetsplanering som hjälper dig att bestämma dig för att skala upp/ner.

## <a name="messaging-unit---how-many-are-needed"></a>Meddelandeenhet - Hur många behövs?

När du etablerar ett Azure Service Bus Premium-namnområde måste antalet tilldelade meddelandeenheter anges. Dessa meddelandeenheter är dedikerade resurser som allokeras till namnområdet.

Antalet meddelandeenheter som allokerats till servicebusspremiernas namnområde kan **justeras dynamiskt** för att ta hänsyn till ändringen (ökning eller minskning) av arbetsbelastningar.

Det finns ett antal faktorer att ta hänsyn till när du bestämmer antalet meddelandeenheter för din arkitektur:

- Börja med ***1 eller 2 meddelandeenheter*** som tilldelats ditt namnområde.
- Studera mätvärdena för CPU-användning i [resursanvändningsmåtten](service-bus-metrics-azure-monitor.md#resource-usage-metrics) för ditt namnområde.
    - Om CPU-användningen är ***lägre än 20 %*** kanske du kan skala ***ned*** antalet meddelandeenheter som allokerats till ditt namnområde.
    - Om CPU-användningen är ***över 70 %*** kommer ditt program att dra nytta av ***att skala upp*** antalet meddelandeenheter som allokerats till ditt namnområde.

Processen att skala de resurser som allokerats till en Service Bus-namnområden kan automatiseras med hjälp av [Azure Automation Runbooks](../automation/automation-quickstart-create-runbook.md).

> [!NOTE]
> **Skalning** av resurser som allokerats till namnområdet kan vara antingen förebyggande eller reaktiv.
>
>  * **Förebyggande:** Om ytterligare arbetsbelastning förväntas (på grund av säsongsvariationer eller trender) kan du fortsätta att allokera fler meddelandeenheter till namnområdet innan arbetsbelastningarna träffar.
>
>  * **Reaktiv:** Om ytterligare arbetsbelastningar identifieras genom att studera resursanvändningsmåtten kan ytterligare resurser allokeras till namnområdet för att inkludera ökande efterfrågan.
>
> Faktureringsmätarena för Service Bus är varje timme. När det gäller uppskalning betalar du bara för de extra resurserna för de timmar som dessa användes.
>

## <a name="get-started-with-premium-messaging"></a>Kom igång med Premium-meddelandetjänster

Det är enkelt att komma igång med Premium-meddelandetjänster och processen liknar den för Standard-meddelandetjänster. Börja med att [skapa ett namnområde](service-bus-create-namespace-portal.md) i [Azure Portal](https://portal.azure.com). Se till att du väljer **Premium** under **Prisnivå**. Klicka på **Visa fullständiga prisuppgifter** om du vill se mer information om varje nivå.

![create-premium-namespace][create-premium-namespace]

Du kan också skapa [Premium-namnområden med hjälp av Azure Resource Manager-mallar](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Nästa steg

I följande länkar kan du lära dig mer om Service Bus-meddelanden:

* [Introduktion till Azure Service Bus Premium Messaging (blogginlägg)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introduktion till Azure Service Bus Premium-meddelanden (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Översikt över Service Bus-meddelanden](service-bus-messaging-overview.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
