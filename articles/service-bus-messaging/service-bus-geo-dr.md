---
title: Azure Service Bus geohaveriberedskap | Microsoft Docs
description: Hur du använder geografiska regioner för redundans och utföra katastrofåterställning i Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: a0581ef43e8a3c02126612a21122db559a941370
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311223"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus Geo-haveriberedskap

När hela Azure-regioner eller Datacenter (om ingen [tillgänglighetszoner](../availability-zones/az-overview.md) används) drabbas, det är viktigt för att bearbeta till fortsätter att fungera i en annan region eller datacenter. Därför *geohaveriberedskap* är en viktig funktion för vilket företag som helst. Azure Service Bus stöder geo-haveriberedskap på namnområdesnivå.

Geo-disaster recovery-funktionen är globalt tillgänglig för Service Bus Premium-SKU. 

>[!NOTE]
> GEO-haveriberedskap säkerställer för närvarande endast att metadata (köer, ämnen, prenumerationer, filter) kopieras från den primära namnrymden till sekundärt namnområde tillsammans.

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att Observera skillnaden mellan ”avbrott” och ”katastrofer”. 

En *avbrott* är Azure Service Bus är tillfälligt otillgänglig och kan påverka vissa komponenter av tjänsten, till exempel ett meddelandearkiv eller även hela datacentret. Men när problemet är löst, blir Service Bus tillgänglig igen. Ett avbrott medför normalt förlusten av meddelanden eller andra data. Ett exempel på sådana ett avbrott kan vara ett strömavbrott i datacentret. Vissa avbrott kan endast kort anslutning förluster på grund av problem med tillfälliga eller. 

En *haveriberedskap* definieras som permanenta eller mer långsiktiga förlusten av en Service Bus-kluster, Azure-region eller datacenter. Den region eller datacenter kan eller kan inte bli tillgänglig igen, eller kanske inte körs i timmar eller dagar. Exempel på sådana katastrofer är fire, överbelasta eller jordbävning. Problem som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. Men i de flesta fall bör det finnas inga data går förlorade och meddelanden kan återställas när datacentret är säkerhetskopiera.

Funktionen Geo-disaster recovery i Azure Service Bus är en lösning för haveriberedskap. Koncept och arbetsflödet som beskrivs i den här artikeln gäller katastrofscenarier och inte tillfälliga eller tillfälliga avbrott. En detaljerad beskrivning av haveriberedskap i Microsoft Azure finns i [i den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Funktionen disaster recovery implementerar metadata katastrofåterställning och förlitar sig på primära och sekundära disaster recovery-namnområden. Observera att Geo-disaster recovery-funktionen är tillgänglig för den [Premium-SKU](service-bus-premium-messaging.md) endast. Du behöver inte göra några ändringar av anslutningen sträng, eftersom anslutningen görs via ett alias.

I den här artikeln används följande termer:

-  *Alias*: Namnet på en haveriberedskapskonfiguration som du har konfigurerat. Aliaset som innehåller en enda stabil anslutningssträng för fullständigt kvalificerade domännamn (FQDN). Program använder den här anslutningssträngen för alias för att ansluta till ett namnområde. Med ett alias garanterar att anslutningssträngen är oförändrade när redundansen utlöses.

-  *Primära och sekundära namnområdet*: Namnområden som motsvarar aliaset. Det primära namnområdet är ”aktiv” och tar emot meddelanden (det kan vara ett namnområde för befintliga eller nya). Det sekundära namnområdet är ”passiva” och ta emot inte meddelanden. Metadata mellan båda är synkroniserade, så att båda sömlöst kan godkänna meddelanden utan program kod eller anslutningen sträng ändringar. För att säkerställa att endast aktiva namnområdet tar emot meddelanden, måste du använda detta alias. 

-  *Metadata*: Entiteter som köer, ämnen och prenumerationer; och deras egenskaper för tjänsten som är associerade med namnområdet. Observera att endast entiteter och deras inställningar replikeras automatiskt. Meddelanden replikeras inte.

-  *Redundans*: Att aktivera det sekundära namnområdet.

## <a name="setup"></a>Konfiguration

Följande avsnitt är en översikt över att konfigurera länkning av lagringspooler mellan namnområden.

![1][]

Installationen är följande-

1. Etablera en ***primära*** Service Bus Premium-Namespace.

2. Etablera en ***sekundära*** Service Bus Premium-Namespace i en region *skiljer sig från där det primära namnområdet har etablerats*. Detta hjälper att tillåta felisolering i olika datacenterregioner.

3. Skapa länkning av lagringspooler mellan primärt namnområde och sekundära namnområdet för att hämta den ***alias***.

4. Använd den ***alias*** hämtades i steg 3 att ansluta dina klientprogram att Geo-DR aktiverat primärt namnområde. Aliaset pekar till en början på det primära namnområdet.

5. [Valfritt] Lägg till viss övervakning för att identifiera om en redundansväxling är nödvändigt.

## <a name="failover-flow"></a>Redundansflöde

Redundans utlöses manuellt av kunden (antingen uttryckligen via ett kommando eller klienten som ägs av affärslogik som utlöser kommandot) och aldrig av Azure. Detta ger kunden fullständig ägarskap och synlighet för matchning av avbrott på Azures stamnät.

![4][]

Efter redundansen utlöses-

1. Den ***alias*** anslutningssträngen uppdateras för att peka på det sekundära Premium-namnområdet.

2. Klienter (sändare och mottagare) ansluta automatiskt till det sekundära namnområdet.

3. Den befintliga länkning av lagringspooler mellan primära och sekundära premium-namnområde har brutits.

När redundansen initieras-

1. Om något annat avbrott inträffar, som du vill kunna redundansväxla igen. Därför kan ställa in en annan passiva namnrymd och uppdatera kopplingen. 

2. Hämta meddelanden från det tidigare versionen primärt namnområdet när den är tillgänglig igen. Använd det här namnområdet för regelbundna meddelanden utanför din konfiguration för geo-återställning efter det eller ta bort det gamla primära namnområdet.

> [!NOTE]
> Endast misslyckas vidarebefordra semantik stöds. I det här scenariot du växlar över och sedan koppla igen med ett nytt namnområde. Det går inte att återställas. till exempel i en SQL-kluster. 

Du kan automatisera redundans med övervakningssystem eller med anpassade övervakningslösningar. Sådana automation tar dock extra planering och arbete som ligger utanför omfånget för den här artikeln.

![2][]

## <a name="management"></a>Hantering

Om du har gjort ett misstag; till exempel du länkade fel regioner under den första konfigurationen, du kan bryta länkningen av de två namnområdena när som helst. Om du vill använda den kopplade namnområden som vanlig namnområden tar du bort den.

## <a name="use-existing-namespace-as-alias"></a>Använd befintligt namnområde som alias

Om du har ett scenario där du inte kan ändra anslutningar av producenter och konsumenter, kan du återanvända namnet på ditt namnområde som aliasnamnet. Se den [exempelkoden på GitHub här](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Exempel

Den [exemplen på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) visar hur du ställer in och initiera redundans. De här exemplen visar följande begrepp:

- En .NET-exempel och inställningar som krävs i Azure Active Directory för att använda Azure Resource Manager med Service Bus för att konfigurera och aktivera Geo-haveriberedskap.
- Steg som krävs för att köra exempelkoden.
- Hur du använder ett befintligt namnområde som ett alias.
- Steg för att du kan också aktivera Geo-haveriberedskap via PowerShell eller CLI.
- [Skicka och ta emot](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) från det primära eller sekundära namnområdet med hjälp av alias.

## <a name="considerations"></a>Överväganden

Observera följande överväganden att tänka på med den här versionen:

1. I redundans planeringen, bör du också faktorn tid. Exempelvis kan kan du förlora anslutningen längre än 15 till 20 minuter du välja att påbörja redundans.

2. Det faktum att inga data replikeras innebär att för närvarande aktiva sessioner inte replikeras. Dessutom fungerar inte dubblettidentifiering och schemalagda meddelanden. Nya sessioner, nya schemalagda meddelanden och nya dubbletter ska fungera. 

3. Redundansväxla en infrastruktur för komplexa distribuerade ska vara [testas](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) minst en gång.

4. Synkronisera enheter kan ta lite tid, cirka 50 – 100 entiteter per minut. Prenumerationer och regler räknas också som entiteter.

## <a name="availability-zones"></a>Tillgänglighetszoner

Service Bus Premium-SKU: N stöder också [Tillgänglighetszoner](../availability-zones/az-overview.md), vilket ger felisolerade platser inom en Azure-region.

> [!NOTE]
> Tillgänglighetszoner-support för Azure Service Bus Premium är bara tillgängliga i [Azure-regioner](../availability-zones/az-overview.md#services-support-by-region) där tillgänglighetszoner finns.

Du kan aktivera Tillgänglighetszoner på nya namnområden, med hjälp av Azure portal. Service Bus stöder inte migreringen av befintliga namnområden. Du kan inte inaktivera redundans när du har aktiverat i namnområdet.

![3][]

## <a name="next-steps"></a>Nästa steg

- Se Geo-haveriberedskap [REST API-referensen här](/rest/api/servicebus/disasterrecoveryconfigs).
- Kör Geo-haveriberedskap [i GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Se Geo-haveriberedskap [exemplet som skickar meddelanden till ett alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Om du vill veta mer om Service Bus-meddelanden, finns i följande artiklar:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST-API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png