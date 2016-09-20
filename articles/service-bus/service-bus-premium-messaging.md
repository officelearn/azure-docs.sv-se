<properties
    pageTitle="Översikt över prisnivåer för Service Bus Premium- och Standard-meddelanden | Microsoft Azure"
    description="Service Bus Premium- och Standard-meddelanden"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/22/2016"
    ms.author="darosa;sethm"/>

# Service Bus Premium- och Standard-meddelandenivåer 

Asynkrona Service Bus-meddelandetjänster, inklusive meddelandeenheter som köer och ämnen, kombinerar meddelandefunktioner för företag med utförlig publicerings-/ prenumerationssemantik i molnskala. Service Bus-meddelandetjänster används som kommunikationsryggrad i många avancerade molnlösningar.

*Premium*-nivån av Service Bus-meddelanden är ett svar på vanliga kundönskemål gällande skala, prestanda och tillgänglighet för verksamhetskritiska program. Även om funktionsuppsättningarna är snudd på identiska är dessa två nivåer av Service Bus-meddelanden utformade för att passa olika användningsfall.

En del övergripande skillnader visas i tabellen nedan.

| Premium                               | Standard                       |
|---------------------------------------|--------------------------------|
| Högt genomflöde                       | Variabelt genomflöde            |
| Förutsägbar prestanda               | Variabel svarstid               |
| Förutsägbara priser                   | Variabla priser – betala per användning |
| Möjlighet att skala arbetsbelastningen uppåt och nedåt | Saknas                            |
| Meddelandestorlek > 256 kB                  | Meddelandestorlek är 256 kB          |

**Service Bus Premium-meddelanden** ger resursisolering på processor- och minnesnivån så att varje kunds arbetsbelastning körs i isolering. Den här resursbehållaren kallas för en *meddelandefunktionsenhet*. Varje Premium-namnområde allokeras minst en meddelandefunktionsenhet. Du kan köpa 1, 2 eller 4 meddelandefunktionsenheter för varje Service Bus Premium-namnområde. En enda arbetsbelastning eller enhet kan spänna över flera meddelandefunktionsenheter, och antalet meddelandefunktionsenheter kan ändras när du vill, även om faktureringen är per 24 timmar eller daglig taxa. Resultatet är förutsägbara och repeterbara prestanda för Service Bus-lösningen.

Prestanda är inte bara mer förutsägbara och tillgängliga, utan de är snabbare också. Service Bus Premium-meddelanden bygger på lagringsmotorn som infördes i [Azure Händelsehubbar](https://azure.microsoft.com/services/event-hubs/). Med Premium-meddelanden är topprestanda mycket snabbare än med standardnivån.

## Premium-meddelanden – tekniska skillnader

Nedan följer några skillnader mellan Premium- och Standard-meddelandenivåerna.

### Partitionerade enheter

Det finns stöd för partitionerade enheter i Premium-meddelanden, men de fungerar inte på samma sätt som på Standard- och Basic-nivåerna av Service Bus-meddelanden. Premium-meddelanden använder inte SQL som ett datalager och har inte längre den konkurrens om resurser som förknippas med en delad plattform. Därför är partitionering inte nödvändigt. Dessutom har antalet partitioner ändrats från 16 partitioner i Standard-meddelanden till två partitioner i Premium. Med två partitioner garanteras tillgänglighet och är ett mer passande antal för Premium-körningsmiljön. Mer information om partitionering finns i [Partitionerade meddelandeentiteter](service-bus-partitioning.md).

### Expressenheter

Eftersom det körs på en helt isolerad körningsmiljö behövs inte längre expressenheter i Premium-meddelanden. Följaktligen stöds inte expressenheter i Premium-namnområden. Mer information om expressfunktionen finns på egenskapen [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx).

## Nästa steg

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

- [Introduktion till Azure Service Bus Premium-meddelanden (blogginlägg)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introduktion till Azure Service Bus Premium-meddelanden (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Översikt över meddelandetjänsten i Service Bus](service-bus-messaging-overview.md)
- [Översikt över arkitekturen i Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Använda Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)



<!--HONumber=sep16_HO1-->


