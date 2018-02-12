---
title: "Azure Service Bus Geo-katastrofåterställning | Microsoft Docs"
description: "Hur du använder geografiska regioner för redundans och utföra katastrofåterställning i Azure Service Bus"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 9cbeff82f7a237c813ea91cd83e9273cad934991
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus Geo-katastrofåterställning

När hela Azure-regioner eller Datacenter (om inget [tillgänglighet zoner](../availability-zones/az-overview.md) används) eventuellt driftstopp, det är viktigt för databearbetning fortsätta att arbeta i en annan region eller datacenter. Därför *Geo-återställning* och *georeplikering* är viktiga funktioner för företag. Azure Service Bus stöder både geo-återställning och geo-replikering på namnområdesnivån. 

Funktionen Geo disaster recovery finns globalt för Service Bus Premium-SKU. 

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att Observera skillnaden mellan ”avbrott” och ”katastrofer”. En *avbrott* är temporär otillgänglighet Azure Service Bus och kan påverka vissa komponenter av tjänsten, till exempel ett meddelandearkiv eller även hela datacentret. Men när problemet är åtgärdat blir Service Bus tillgänglig igen. Normalt orsakar avbrott inte meddelanden eller andra data förlorade. Ett exempel på sådana avbrott kan vara ett strömavbrott i datacentret. Vissa avbrott är endast kort anslutning förluster på grund av problem med övergående eller nätverket. 

En *katastrofåterställning* definieras som permanenta eller långsiktig förlust av en Service Bus-kluster, Azure-region eller datacenter. Region eller datacenter kan eller kan inte bli tillgänglig igen eller kan vara avstängd för timmar eller dagar. Exempel på sådana katastrofer är brand, överbelasta eller jordbävning. En katastrof som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. Men i de flesta fall bör det finnas ingen dataförlust och meddelanden som kan återställas när Datacenter är säkerhetskopiera.

Geo-disaster recovery-funktion i Azure Service Bus är en lösning för katastrofåterställning. Koncept och arbetsflödet som beskrivs i den här artikeln gäller katastrofåterställning scenarier och inte tillfälligt eller tillfälliga avbrott. En detaljerad beskrivning av katastrofåterställning i Microsoft Azure finns [i den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Funktionen disaster recovery implementerar metadata katastrofåterställning och bygger på primära och sekundära disaster recovery-namnområden. Observera att Geo disaster recovery-funktionen är tillgänglig för den [Premium-SKU](service-bus-premium-messaging.md) endast. Du behöver inte göra ändringar anslutning sträng som anslutningen görs via ett alias.

Följande villkor används i den här artikeln:

-  *Alias*: namn för någon haveriberedskapskonfiguration som du skapat. Aliaset som innehåller en enda stabil anslutningssträng för fullständigt kvalificerade domännamn (FQDN). Program använda anslutningssträng med detta alias för att ansluta till ett namnområde. 

-  *Primära och sekundära namnområdet*: de namnområden som motsvarar aliaset. Det primära namnområdet är ”aktiv” och tar emot meddelanden (det kan vara en befintlig eller ny namnområde). Sekundär namnområdet är ”passiva” och ta emot inte meddelanden. Metadata mellan de båda är synkroniserade, så att både sömlöst kan godkänna meddelanden utan någon kod eller ett anslutningsfel sträng ändringar i programmet. För att säkerställa att endast aktiva namnområdet tar emot meddelanden, måste du använda detta alias. 

-  *Metadata*: entiteter som köer, ämnen, prenumerationer, och deras egenskaper som är associerade med namnområdet för tjänsten. Observera att endast enheter och deras inställningar replikeras automatiskt. Meddelanden replikeras inte. 

-  *Redundans*: att aktivera sekundär namnområdet.

## <a name="setup-and-failover-flow"></a>Installationen och växling vid fel

Följande avsnitt är en översikt över failover-processen och förklarar hur du ställer in inledande växling vid fel. 

![1][]

### <a name="setup"></a>Konfiguration

Du först skapa eller använda en befintlig primär namnrymd och ett nytt sekundära namnområde och sedan koppla två. Länkning av den här får du ett alias som du kan använda för att ansluta. Eftersom du använder ett alias, behöver du inte ändra anslutningssträngar. Endast nya namnområden kan läggas till redundans para ihop. Slutligen bör du lägga till vissa övervakning för att identifiera om det krävs en växling vid fel. I de flesta fall tjänsten är en del av ett stort ekosystem, vilket automatisk redundans är sällan möjliga som ofta växling vid fel måste utföras synkroniserad med återstående undersystemet eller infrastruktur.

### <a name="example"></a>Exempel

Överväga en lösning för platsen för försäljning (POS) som skickar meddelanden eller händelser i ett exempel på det här scenariot. Service Bus skickar händelser till vissa mappnings- eller formatera om lösning som sedan vidarebefordrar mappade data till ett annat system för vidare bearbetning. Alla dessa system kan då finnas i samma Azure-region. Beslut om när och vilken del att växla över beror på dataflödet i din infrastruktur. 

Du kan automatisera redundans med övervakningssystem, eller med specialbyggt övervakar lösningar. Sådana automation tar dock extra planerings- och arbetet, vilket ligger utanför omfånget för den här artikeln.

### <a name="failover-flow"></a>Redundansflöde

Om du påbörja redundans krävs två steg:

1. Om en annan avbrott inträffar som du vill kunna redundansväxla igen. Därför måste ställa in en annan passiva namnrymd och uppdatera kopplingen. 

2. Hämta meddelanden från tidigare primära namnområde när den är tillgänglig igen. Använd namnutrymmet för regelbundna meddelanden utanför inställningarna geo-återställning efter det, eller ta bort det gamla primära namnområdet.

> [!NOTE]
> Endast misslyckas vidarebefordra semantik stöds. I det här scenariot växla över och sedan koppla igen med ett nytt namnområde. Det går inte att återställas. till exempel i en SQL-kluster. 

![2][]

## <a name="management"></a>Hantering

Om du har gjort ett misstag; till exempel du paras ihop fel regioner under den första konfigurationen, du kan dela länkar de två namnområdena när som helst. Om du vill använda parad namnområden som regelbundet namnområden tar du bort den.

## <a name="use-existing-namespace-as-alias"></a>Använd befintligt namnområde som alias

Om du har ett scenario där du inte kan ändra anslutningar för producenter och konsumenter, kan du återanvända namnet på ditt namnområde som aliasnamnet. Finns det [exempelkoden på GitHub här](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Exempel

Den [exemplen på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) visar hur du ställer in och initiera redundans. De här exemplen visar följande:

- Inställningar som krävs i Azure Active Directory för att använda Azure Resource Manager med Service Bus. 
- Steg som krävs för att köra exempelkoden. 
- Skicka och ta emot från det aktuella primära namnområdet. 
- Hur du använder ett befintligt namnområde som alias.

## <a name="considerations"></a>Överväganden

Observera följande viktiga aspekter att tänka på med den här versionen:

1. Du bör också övervägas tid faktorn under växling vid fel planeringen. Till exempel om du förlorar anslutningen längre än 15 till 20 minuter kan du välja att påbörja redundans. 
 
2. Det faktum att inga data replikeras innebär att för tillfället aktiva sessioner inte replikeras. Dessutom kanske dubblettidentifiering och schemalagda meddelanden inte fungerar. Nya sessioner, nya schemalagda meddelanden och nya dubbletter ska fungera. 

3. Misslyckande en infrastruktur för komplexa distribuerade ska vara [testas](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) minst en gång. 

4. Synkronisera enheter kan ta lite tid, ungefär 50 – 100 entiteter per minut. Prenumerationer och regler räknas också som entiteter. 

## <a name="next-steps"></a>Nästa steg

- Geo-återställning finns [här REST API-referensen](/rest/api/servicebus/disasterrecoveryconfigs).
- Kör Geo-återställning [på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Geo-återställning finns [exempel som skickar meddelanden till ett alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande artiklar:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST-API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
