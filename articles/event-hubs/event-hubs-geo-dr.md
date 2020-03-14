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
ms.openlocfilehash: 40db6e9f429569bc19641aa5f0f371f287db7b18
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281475"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs - geohaveriberedskap 

När hela Azure-regioner eller data Center (om inga [tillgänglighets zoner](../availability-zones/az-overview.md) används) upplever drift stopp, är det viktigt att data bearbetningen fortsätter att fungera i en annan region eller data Center. Därför är *geo-haveri beredskap* och *geo-replikering* viktiga funktioner för alla företag. Azure Event Hubs stöder både geo-haveriberedskap och geo-replikering på namnområdesnivå. 

> [!NOTE]
> Funktionen för geo-katastrof återställning är bara tillgänglig för [standard-och dedikerade SKU: er](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att Observera skillnaden mellan ”avbrott” och ”katastrofer”. Ett *avbrott* är tillfälligt otillgängligt för Azure Event Hubs och kan påverka vissa komponenter i tjänsten, t. ex. ett meddelande arkiv eller till och med hela data centret. Men när problemet är löst, blir Event Hubs tillgänglig igen. Ett avbrott medför normalt förlusten av meddelanden eller andra data. Ett exempel på sådana ett avbrott kan vara ett strömavbrott i datacentret. Vissa avbrott kan endast kort anslutning förluster på grund av problem med tillfälliga eller. 

En *katastrof* definieras som en permanent eller mer långsiktig förlust av ett Event Hubs-kluster, Azure-region eller data Center. Den region eller datacenter kan eller kan inte bli tillgänglig igen, eller kanske inte körs i timmar eller dagar. Exempel på sådana katastrofer är fire, överbelasta eller jordbävning. Problem som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. Men i de flesta fall bör det finnas inga data går förlorade och meddelanden kan återställas när datacentret är säkerhetskopiera.

Funktionen Geo-disaster recovery i Azure Event Hubs är en lösning för haveriberedskap. Koncept och arbetsflödet som beskrivs i den här artikeln gäller katastrofscenarier och inte tillfälliga eller tillfälliga avbrott. En detaljerad beskrivning av haveri beredskap i Microsoft Azure finns i [den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Funktionen disaster recovery implementerar metadata katastrofåterställning och förlitar sig på primära och sekundära disaster recovery-namnområden. 

Funktionen för geo-katastrof återställning är endast tillgänglig för [standard-och dedikerade SKU: er](https://azure.microsoft.com/pricing/details/event-hubs/) . Du behöver inte göra några ändringar av anslutningen sträng, eftersom anslutningen görs via ett alias.

I den här artikeln används följande termer:

-  *Alias*: namnet på en katastrof återställnings konfiguration som du ställer in. Aliaset som innehåller en enda stabil anslutningssträng för fullständigt kvalificerade domännamn (FQDN). Program använder den här anslutningssträngen för alias för att ansluta till ett namnområde. 

-  *Primär/sekundär namnrymd*: de namn områden som motsvarar aliaset. Det primära namnområdet är ”aktiv” och tar emot meddelanden (det kan vara ett namnområde för befintliga eller nya). Det sekundära namnområdet är ”passiva” och ta emot inte meddelanden. Metadata mellan båda är synkroniserade, så att båda sömlöst kan godkänna meddelanden utan program kod eller anslutningen sträng ändringar. För att säkerställa att endast aktiva namnområdet tar emot meddelanden, måste du använda detta alias. 

-  *Metadata*: entiteter som händelse hubbar och konsument grupper; och deras egenskaper för tjänsten som är associerad med namn området. Observera att endast entiteter och deras inställningar replikeras automatiskt. Meddelanden och händelser som replikeras inte. 

-  *Redundans*: processen att aktivera det sekundära namn området.

## <a name="supported-namespace-pairs"></a>Namn rymds par som stöds
Följande kombinationer av primära och sekundära namn rymder stöds:  

| Primär namnrymd | Sekundär namnrymd | Stöds | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Ja | 
| Standard | Dedikerad | Ja | 
| Dedikerad | Dedikerad | Ja | 
| Dedikerad | Standard | Nej | 

> [!NOTE]
> Det går inte att para ihop namn områden som finns i samma dedicerade kluster. Du kan para ihop namn områden som finns i separata kluster. 

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

[Exemplet på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) visar hur du konfigurerar och initierar en redundansväxling. Detta exempel visar följande begrepp:

- Inställningar som krävs i Azure Active Directory för att använda Azure Resource Manager med Event Hubs. 
- Steg som krävs för att köra exempelkoden. 
- Skicka och ta emot från det aktuella primära namnområdet. 

## <a name="considerations"></a>Överväganden

Observera följande överväganden att tänka på med den här versionen:

1. Enligt design replikeras Event Hubs geo-haveri beredskap inte replikerar data, och därför kan du inte återanvända det gamla förskjutning svärdet för din primära händelsehubben på den sekundära händelsehubben. Vi rekommenderar att du startar om din händelse mottagare med något av följande:

- *EventPosition. FromStart ()* – om du vill läsa alla data på den sekundära händelsehubben.
- *EventPosition. FromEnd ()* – om du vill läsa alla nya data från tiden för anslutningen till den sekundära händelsehubben.
- *EventPosition. FromEnqueuedTime (datetime)* – om du vill läsa alla data som tas emot i den sekundära händelsehubben från ett visst datum och en specifik tidpunkt.

2. I redundans planeringen, bör du också faktorn tid. Exempelvis kan kan du förlora anslutningen längre än 15 till 20 minuter du välja att påbörja redundans. 
 
3. Det faktum att inga data replikeras innebär att för närvarande aktiva sessioner inte replikeras. Dessutom fungerar inte dubblettidentifiering och schemalagda meddelanden. Nya sessioner, schemalagda meddelanden och nya dubbletter ska fungera. 

4. Att redundansväxla en komplex distribuerad infrastruktur bör återställas [minst en](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) gång. 

5. Synkronisera enheter kan ta lite tid, cirka 50 – 100 entiteter per minut.

## <a name="availability-zones"></a>Tillgänglighetszoner 

Event Hubs standard-SKU: n stöder [Tillgänglighetszoner](../availability-zones/az-overview.md), vilket ger felisolerade platser inom en Azure-region. 

> [!NOTE]
> Tillgänglighetszoner stöd för Azure Event Hubs standard är bara tillgängligt i [Azure-regioner](../availability-zones/az-overview.md#services-support-by-region) där tillgänglighets zoner finns.

Du kan aktivera Tillgänglighetszoner på nya namnområden, med hjälp av Azure portal. Event Hubs stöder inte migreringen av befintliga namnområden. Du kan inte inaktivera redundans när du har aktiverat i namnområdet.

![3][]

## <a name="next-steps"></a>Nästa steg

* [Exemplet på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) går igenom ett enkelt arbets flöde som skapar en geo-par och initierar en redundansväxling för ett haveri beredskaps scenario.
* I [referensen REST API](/rest/api/eventhub/disasterrecoveryconfigs) beskrivs API: er för att utföra den geo-haveri återställnings konfigurationen.

Besök följande länkar för mer utförlig information om Event Hubs:

- Kom igång med händelsehubbar
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
