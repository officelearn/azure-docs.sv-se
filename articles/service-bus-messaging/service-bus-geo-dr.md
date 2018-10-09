---
title: Azure Service Bus geohaveriberedskap | Microsoft Docs
description: Hur du använder geografiska regioner för redundans och utföra katastrofåterställning i Azure Service Bus
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: spelluru
ms.openlocfilehash: 0436248dac2812c447d25de16a4ac6b45bd7248f
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855193"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus Geo-haveriberedskap

När hela Azure-regioner eller Datacenter (om ingen [tillgänglighetszoner](../availability-zones/az-overview.md) används) drabbas, det är viktigt för att bearbeta till fortsätter att fungera i en annan region eller datacenter. Därför *geohaveriberedskap* och *Geo-replikering* är viktiga funktioner för vilket företag som helst. Azure Service Bus stöder både geo-haveriberedskap och geo-replikering på namnområdesnivå. 

Geo-disaster recovery-funktionen är globalt tillgänglig för Service Bus Premium-SKU. 

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att Observera skillnaden mellan ”avbrott” och ”katastrofer”. En *avbrott* är Azure Service Bus är tillfälligt otillgänglig och kan påverka vissa komponenter av tjänsten, till exempel ett meddelandearkiv eller även hela datacentret. Men när problemet är löst, blir Service Bus tillgänglig igen. Ett avbrott medför normalt förlusten av meddelanden eller andra data. Ett exempel på sådana ett avbrott kan vara ett strömavbrott i datacentret. Vissa avbrott kan endast kort anslutning förluster på grund av problem med tillfälliga eller. 

En *haveriberedskap* definieras som permanenta eller mer långsiktiga förlusten av en Service Bus-kluster, Azure-region eller datacenter. Den region eller datacenter kan eller kan inte bli tillgänglig igen, eller kanske inte körs i timmar eller dagar. Exempel på sådana katastrofer är fire, överbelasta eller jordbävning. Problem som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. Men i de flesta fall bör det finnas inga data går förlorade och meddelanden kan återställas när datacentret är säkerhetskopiera.

Funktionen Geo-disaster recovery i Azure Service Bus är en lösning för haveriberedskap. Koncept och arbetsflödet som beskrivs i den här artikeln gäller katastrofscenarier och inte tillfälliga eller tillfälliga avbrott. En detaljerad beskrivning av haveriberedskap i Microsoft Azure finns i [i den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Funktionen disaster recovery implementerar metadata katastrofåterställning och förlitar sig på primära och sekundära disaster recovery-namnområden. Observera att Geo-disaster recovery-funktionen är tillgänglig för den [Premium-SKU](service-bus-premium-messaging.md) endast. Du behöver inte göra några ändringar av anslutningen sträng, eftersom anslutningen görs via ett alias.

I den här artikeln används följande termer:

-  *Alias*: namnet på en haveriberedskapskonfiguration som du har konfigurerat. Aliaset som innehåller en enda stabil anslutningssträng för fullständigt kvalificerade domännamn (FQDN). Program använder den här anslutningssträngen för alias för att ansluta till ett namnområde. 

-  *Primära och sekundära namnområdet*: namnområden som motsvarar aliaset. Det primära namnområdet är ”aktiv” och tar emot meddelanden (det kan vara ett namnområde för befintliga eller nya). Det sekundära namnområdet är ”passiva” och ta emot inte meddelanden. Metadata mellan båda är synkroniserade, så att båda sömlöst kan godkänna meddelanden utan program kod eller anslutningen sträng ändringar. För att säkerställa att endast aktiva namnområdet tar emot meddelanden, måste du använda detta alias. 

-  *Metadata*: entiteter som köer, ämnen och prenumerationer; och deras egenskaper för tjänsten som är associerade med namnområdet. Observera att endast entiteter och deras inställningar replikeras automatiskt. Meddelanden replikeras inte. 

-  *Redundans*: aktiveringsprocessen för det sekundära namnområdet.

## <a name="setup-and-failover-flow"></a>Installation och redundans flöde

Följande avsnitt är en översikt över failover-processen och förklarar hur du ställer in inledande växling vid fel. 

![1][]

### <a name="setup"></a>Konfiguration

Du först skapa eller använda en befintlig primär namnrymd och ett nytt sekundärt namnområde och sedan koppla ihop två. Den här parkoppling ger dig ett alias som du kan använda för att ansluta. Eftersom du använder ett alias, behöver du inte ändra anslutningssträngar. Endast nya namnområden kan läggas till dina redundans länkning. Slutligen bör du lägga till viss övervakning för att identifiera om en redundansväxling är nödvändigt. I de flesta fall tjänsten är en del av ett omfattande ekosystem, vilket automatisk växling vid fel är sällan möjligt, som ofta redundansväxling måste utföras synkroniserade med återstående undersystem eller infrastruktur.

### <a name="example"></a>Exempel

Överväg en återställningspunkt för försäljning (POS)-lösning som genererar meddelanden eller händelser i ett exempel på det här scenariot. Service Bus skickar händelser till vissa mappnings- eller formatera om lösningen, som sedan vidarebefordrar mappade data till ett annat system för vidare bearbetning. I det här läget att alla dessa system kan finnas i samma Azure-region. Beslutet om när och vilken del att växla över beror på flödet av data i din infrastruktur. 

Du kan automatisera redundans med övervakningssystem eller med anpassade övervakningslösningar. Sådana automation tar dock extra planering och arbete som ligger utanför omfånget för den här artikeln.

### <a name="failover-flow"></a>Redundansflöde

Om du påbörja redundans krävs två steg:

1. Om något annat avbrott inträffar, som du vill kunna redundansväxla igen. Därför kan ställa in en annan passiva namnrymd och uppdatera kopplingen. 

2. Hämta meddelanden från det tidigare versionen primärt namnområdet när den är tillgänglig igen. Använd det här namnområdet för regelbundna meddelanden utanför din konfiguration för geo-återställning efter det eller ta bort det gamla primära namnområdet.

> [!NOTE]
> Endast misslyckas vidarebefordra semantik stöds. I det här scenariot du växlar över och sedan koppla igen med ett nytt namnområde. Det går inte att återställas. till exempel i en SQL-kluster. 

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

## <a name="availability-zones-preview"></a>Tillgänglighetszoner (förhandsversion)

Service Bus Premium-SKU: N stöder också [Tillgänglighetszoner](../availability-zones/az-overview.md), vilket ger felisolerade platser inom en Azure-region. 

> [!NOTE]
> Förhandsversionen av Tillgänglighetszoner stöds bara i den **centrala USA**, **östra USA 2**, och **Frankrike, centrala** regioner.

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

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png