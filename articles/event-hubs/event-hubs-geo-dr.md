---
title: Geo-haveri beredskap – Azure Event Hubs | Microsoft Docs
description: Använda geografiska regioner för att redundansväxla och utföra haveri beredskap i Azure Event Hubs
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
ms.openlocfilehash: 61318fbccdf92c6502aa8b2236d8b234cec67668
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209153"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs-geo-haveri beredskap 

När hela Azure-regioner eller data Center (om inga [tillgänglighets zoner](../availability-zones/az-overview.md) används) upplever drift stopp, är det viktigt att data bearbetningen fortsätter att fungera i en annan region eller data Center. Därför är *geo-haveri beredskap* och *geo-replikering* viktiga funktioner för alla företag. Azure Event Hubs stöder både geo-haveri återställning och geo-replikering på namn områdes nivå. 

> [!NOTE]
> Funktionen för geo-katastrof återställning är bara tillgänglig för [standard-och dedikerade SKU: er](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att notera skillnaden mellan "avbrott" och "katastrofer". Ett *avbrott* är tillfälligt otillgängligt för Azure Event Hubs och kan påverka vissa komponenter i tjänsten, t. ex. ett meddelande arkiv eller till och med hela data centret. När problemet har åtgärd ATS blir Event Hubs dock tillgängligt igen. Normalt orsakar ett avbrott inte att meddelanden eller andra data går förlorade. Ett exempel på ett sådant avbrott kan vara ett strömavbrott i data centret. Vissa avbrott är bara korta anslutnings förluster på grund av tillfälliga eller nätverks problem. 

En *katastrof* definieras som en permanent eller mer långsiktig förlust av ett Event Hubs-kluster, Azure-region eller data Center. Regionen eller data centret kan komma att bli otillgängliga igen, eller så kan det vara nere i timmar eller dagar. Exempel på sådana katastrofer är brand, översvämning eller jord bävning. En katastrof som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. I de flesta fall bör det dock inte finnas någon data förlust och meddelanden kan återställas när data centret har säkerhetskopierats.

Den geo-haverie återställnings funktionen i Azure Event Hubs är en katastrof återställnings lösning. Begreppen och arbets flödet som beskrivs i den här artikeln gäller katastrof scenarier och inte tillfälligt, eller tillfälliga avbrott. En detaljerad beskrivning av haveri beredskap i Microsoft Azure finns i [den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och villkor

Funktionen för haveri beredskap implementerar haveri beredskap för metadata och förlitar sig på de primära och sekundära återställnings namn områdena för haveri beredskap. 

Funktionen för geo-katastrof återställning är endast tillgänglig för [standard-och dedikerade SKU: er](https://azure.microsoft.com/pricing/details/event-hubs/) . Du behöver inte göra några ändringar i anslutnings strängen eftersom anslutningen görs via ett alias.

Följande villkor används i den här artikeln:

-  *Alias*: namnet på en katastrof återställnings konfiguration som du ställer in. Aliaset innehåller en enda stabil fullständigt kvalificerad domän namns anslutnings sträng (FQDN). Program använder den här Ali Aset-anslutningssträngen för att ansluta till ett namn område. 

-  *Primär/sekundär namnrymd*: de namn områden som motsvarar aliaset. Det primära namn området är "aktivt" och tar emot meddelanden (det kan vara ett befintligt eller nytt namn område). Det sekundära namn området är "passiv" och tar inte emot meddelanden. Metadata mellan båda är synkroniserade, så båda kan sömlöst acceptera meddelanden utan program kod eller anslutnings sträng ändringar. För att säkerställa att endast det aktiva namn området tar emot meddelanden måste du använda aliaset. 

-  *Metadata*: entiteter som händelse hubbar och konsument grupper; och deras egenskaper för tjänsten som är associerad med namn området. Observera att endast entiteter och deras inställningar replikeras automatiskt. Meddelanden och händelser replikeras inte. 

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

## <a name="setup-and-failover-flow"></a>Konfiguration och redundansväxla flöde

Följande avsnitt innehåller en översikt över redundansväxlingen och förklarar hur du ställer in den inledande redundansväxlingen. 

![1][]

### <a name="setup"></a>Installation

Först skapar du eller använder ett befintligt primärt namn område och ett nytt sekundärt namn område och kopplar sedan samman de två. Den här ihopparningen ger dig ett alias som du kan använda för att ansluta. Eftersom du använder ett alias behöver du inte ändra anslutnings strängarna. Det går bara att lägga till nya namn områden i ihopparningen för redundans. Slutligen bör du lägga till viss övervakning för att upptäcka om det behövs en redundansväxling. I de flesta fall är tjänsten en del av ett stort eko system, vilket innebär att automatisk redundans inte är möjlig, så ofta måste redundansen utföras i synkronisering med återstående del system eller infrastruktur.

### <a name="example"></a>Exempel

I ett exempel på det här scenariot bör du överväga en lösning för kassan (kassa) som avger antingen meddelanden eller händelser. Event Hubs skickar händelser till vissa mappnings-eller omformateras lösningar, som sedan vidarebefordrar mappade data till ett annat system för vidare bearbetning. Vid detta tillfälle kan alla dessa system finnas i samma Azure-region. Beslutet om när och vilken del som ska redundansväxla beror på data flödet i din infrastruktur. 

Du kan automatisera redundans antingen med övervaknings system eller med anpassade övervaknings lösningar. Sådan automatisering tar dock extra planering och arbete som ligger utanför den här artikelns räckvidd.

### <a name="failover-flow"></a>Flöde för växling vid fel

Om du initierar redundansväxlingen krävs två steg:

1. Om ett annat avbrott inträffar vill du kunna redundansväxla igen. Konfigurera därför ett annat passivt namn område och uppdatera ihopparningen. 

2. Hämta meddelanden från det tidigare primära namn området när det är tillgängligt igen. Efter det använder du det namn området för vanliga meddelanden utanför din geo-återställnings installation eller tar bort det gamla primära namn området.

> [!NOTE]
> Det finns bara stöd för att vidarebefordra semantik. I det här scenariot kan du växla över och sedan para ihop med ett nytt namn område. Det finns inte stöd för att återställa igen. till exempel i ett SQL-kluster. 

![2][]

## <a name="management"></a>Hantering

Om du har gjort ett misstag, Du kan till exempel para ihop fel regioner under den första installationen. du kan när som helst avbryta länkningen av de två namn områdena. Om du vill använda de kopplade namn rymderna som vanliga namn områden, tar du bort aliaset.

## <a name="samples"></a>Exempel

[Exemplet på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) visar hur du konfigurerar och initierar en redundansväxling. Det här exemplet demonstrerar följande begrepp:

- Inställningar som krävs i Azure Active Directory att använda Azure Resource Manager med Event Hubs. 
- Steg som krävs för att köra exempel koden. 
- Skicka och ta emot från aktuellt primärt namn område. 

## <a name="considerations"></a>Överväganden

Tänk på följande när du är i åtanke med den här versionen:

1. Enligt design replikeras Event Hubs geo-haveri beredskap inte replikerar data, och därför kan du inte återanvända det gamla förskjutning svärdet för din primära händelsehubben på den sekundära händelsehubben. Vi rekommenderar att du startar om din händelse mottagare med något av följande:

- *EventPosition. FromStart ()* – om du vill läsa alla data på den sekundära händelsehubben.
- *EventPosition. FromEnd ()* – om du vill läsa alla nya data från tiden för anslutningen till den sekundära händelsehubben.
- *EventPosition. FromEnqueuedTime (datetime)* – om du vill läsa alla data som tas emot i den sekundära händelsehubben från ett visst datum och en specifik tidpunkt.

2. I planeringen av redundansväxling bör du även överväga tids faktorn. Om du till exempel förlorar anslutningen i mer än 15 till 20 minuter kan du välja att initiera redundansväxlingen. 
 
3. Det faktum att inga data replikeras innebär att aktuella aktiva sessioner inte replikeras. Dessutom kanske inte dubblettidentifiering och schemalagda meddelanden fungerar. Nya sessioner, schemalagda meddelanden och nya dubbletter kommer att fungera. 

4. Att redundansväxla en komplex distribuerad infrastruktur bör återställas [minst en](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) gång. 

5. Synkronisering av entiteter kan ta lite tid, ungefär 50-100 entiteter per minut.

## <a name="availability-zones"></a>Tillgänglighetszoner 

Event Hubs standard-SKU: n stöder [Tillgänglighetszoner](../availability-zones/az-overview.md), vilket ger felisolerade platser inom en Azure-region. 

> [!NOTE]
> Tillgänglighetszoner stöd för Azure Event Hubs standard är bara tillgängligt i [Azure-regioner](../availability-zones/az-region.md) där tillgänglighets zoner finns.

Du kan bara aktivera Tillgänglighetszoner på nya namn områden med hjälp av Azure Portal. Event Hubs stöder inte migrering av befintliga namn rymder. Du kan inte inaktivera zon redundans när du har aktiverat den i namn området.

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
