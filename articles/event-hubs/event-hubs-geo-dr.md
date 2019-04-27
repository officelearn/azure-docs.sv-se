---
title: Geohaveriberedskap – Azure Event Hubs | Microsoft Docs
description: Hur du använder geografiska regioner för redundans och utföra katastrofåterställning i Azure Event Hubs
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 56077d018c1ae62809d51fc66d7f5aff93fb4c02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821845"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs - geohaveriberedskap 

När hela Azure-regioner eller Datacenter (om ingen [tillgänglighetszoner](../availability-zones/az-overview.md) används) drabbas, det är viktigt för att bearbeta till fortsätter att fungera i en annan region eller datacenter. Därför *geohaveriberedskap* och *Geo-replikering* är viktiga funktioner för vilket företag som helst. Azure Event Hubs stöder både geo-haveriberedskap och geo-replikering på namnområdesnivå. 

Geo-disaster recovery-funktionen är globalt tillgänglig för Event Hubs Standard-SKU.

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att Observera skillnaden mellan ”avbrott” och ”katastrofer”. En *avbrott* är Azure Event Hubs är tillfälligt otillgänglig och kan påverka vissa komponenter av tjänsten, till exempel ett meddelandearkiv eller även hela datacentret. Men när problemet är löst, blir Event Hubs tillgänglig igen. Ett avbrott medför normalt förlusten av meddelanden eller andra data. Ett exempel på sådana ett avbrott kan vara ett strömavbrott i datacentret. Vissa avbrott kan endast kort anslutning förluster på grund av problem med tillfälliga eller. 

En *haveriberedskap* definieras som permanenta eller mer långsiktiga förlusten av en Event Hubs-kluster, Azure-region eller datacenter. Den region eller datacenter kan eller kan inte bli tillgänglig igen, eller kanske inte körs i timmar eller dagar. Exempel på sådana katastrofer är fire, överbelasta eller jordbävning. Problem som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. Men i de flesta fall bör det finnas inga data går förlorade och meddelanden kan återställas när datacentret är säkerhetskopiera.

Funktionen Geo-disaster recovery i Azure Event Hubs är en lösning för haveriberedskap. Koncept och arbetsflödet som beskrivs i den här artikeln gäller katastrofscenarier och inte tillfälliga eller tillfälliga avbrott. En detaljerad beskrivning av haveriberedskap i Microsoft Azure finns i [i den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Funktionen disaster recovery implementerar metadata katastrofåterställning och förlitar sig på primära och sekundära disaster recovery-namnområden. Observera att Geo-disaster recovery-funktionen är tillgänglig för den [Standard-SKU](https://azure.microsoft.com/pricing/details/event-hubs/) endast. Du behöver inte göra några ändringar av anslutningen sträng, eftersom anslutningen görs via ett alias.

I den här artikeln används följande termer:

-  *Alias*: Namnet på en haveriberedskapskonfiguration som du har konfigurerat. Aliaset som innehåller en enda stabil anslutningssträng för fullständigt kvalificerade domännamn (FQDN). Program använder den här anslutningssträngen för alias för att ansluta till ett namnområde. 

-  *Primära och sekundära namnområdet*: Namnområden som motsvarar aliaset. Det primära namnområdet är ”aktiv” och tar emot meddelanden (det kan vara ett namnområde för befintliga eller nya). Det sekundära namnområdet är ”passiva” och ta emot inte meddelanden. Metadata mellan båda är synkroniserade, så att båda sömlöst kan godkänna meddelanden utan program kod eller anslutningen sträng ändringar. För att säkerställa att endast aktiva namnområdet tar emot meddelanden, måste du använda detta alias. 

-  *Metadata*: Entiteter, till exempel händelsehubbar och konsumentgrupper; och deras egenskaper för tjänsten som är associerade med namnområdet. Observera att endast entiteter och deras inställningar replikeras automatiskt. Meddelanden och händelser som replikeras inte. 

-  *Redundans*: Att aktivera det sekundära namnområdet.

## <a name="setup-and-failover-flow"></a>Installation och redundans flöde

Följande avsnitt är en översikt över failover-processen och förklarar hur du ställer in inledande växling vid fel. 

![1][]

### <a name="setup"></a>Konfiguration

Du först skapa eller använda en befintlig primär namnrymd och ett nytt sekundärt namnområde och sedan koppla ihop två. Den här parkoppling ger dig ett alias som du kan använda för att ansluta. Eftersom du använder ett alias, behöver du inte ändra anslutningssträngar. Endast nya namnområden kan läggas till dina redundans länkning. Slutligen bör du lägga till viss övervakning för att identifiera om en redundansväxling är nödvändigt. I de flesta fall tjänsten är en del av ett omfattande ekosystem, vilket automatisk växling vid fel är sällan möjligt, som ofta redundansväxling måste utföras synkroniserade med återstående undersystem eller infrastruktur.

### <a name="example"></a>Exempel

Överväg en återställningspunkt för försäljning (POS)-lösning som genererar meddelanden eller händelser i ett exempel på det här scenariot. Händelsehubbar skickar dessa händelser till vissa kartor och formatera om lösningen, som sedan vidarebefordrar mappade data till ett annat system för vidare bearbetning. I det här läget att alla dessa system kan finnas i samma Azure-region. Beslutet om när och vilken del att växla över beror på flödet av data i din infrastruktur. 

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

## <a name="samples"></a>Exempel

Den [i GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) visar hur du ställer in och initiera redundans. Detta exempel visar följande begrepp:

- Inställningar som krävs i Azure Active Directory för att använda Azure Resource Manager med Event Hubs. 
- Steg som krävs för att köra exempelkoden. 
- Skicka och ta emot från det aktuella primära namnområdet. 

## <a name="considerations"></a>Överväganden

Observera följande överväganden att tänka på med den här versionen:

1. I redundans planeringen, bör du också faktorn tid. Exempelvis kan kan du förlora anslutningen längre än 15 till 20 minuter du välja att påbörja redundans. 
 
2. Det faktum att inga data replikeras innebär att för närvarande aktiva sessioner inte replikeras. Dessutom fungerar inte dubblettidentifiering och schemalagda meddelanden. Nya sessioner, schemalagda meddelanden och nya dubbletter ska fungera. 

3. Redundansväxla en infrastruktur för komplexa distribuerade ska vara [testas](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) minst en gång. 

4. Synkronisera enheter kan ta lite tid, cirka 50 – 100 entiteter per minut.

## <a name="availability-zones"></a>Tillgänglighetszoner 

Har stöd för Event Hubs Standard-SKU [Tillgänglighetszoner](../availability-zones/az-overview.md), vilket ger felisolerade platser inom en Azure-region. 

> [!NOTE]
> Tillgänglighetszoner support för Azure Event Hubs Standard ingår endast i [Azure-regioner](../availability-zones/az-overview.md#services-support-by-region) där tillgänglighetszoner finns.

Du kan aktivera Tillgänglighetszoner på nya namnområden, med hjälp av Azure portal. Event Hubs stöder inte migreringen av befintliga namnområden. Du kan inte inaktivera redundans när du har aktiverat i namnområdet.

![3][]

## <a name="next-steps"></a>Nästa steg

* Den [i GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) går igenom ett enkelt arbetsflöde som skapar ett geo-par och initierar en redundansväxling för en katastrofåterställning.
* Den [REST API-referens](/rest/api/eventhub/disasterrecoveryconfigs) beskriver API: er för att utföra Geo-katastrofberedskapskonfigurationen.

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png