---
title: Geo-haveri beredskap – Azure Event Hubs | Microsoft Docs
description: Använda geografiska regioner för att redundansväxla och utföra haveri beredskap i Azure Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 6dd2385a6f6e61136a1284171532aedd70a9cc96
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608358"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs-geo-haveri beredskap 
När hela Azure-regioner eller data Center (om inga [tillgänglighets zoner](../availability-zones/az-overview.md) används) upplever drift stopp, är det viktigt att data bearbetningen fortsätter att fungera i en annan region eller data Center. Därför är *geo-haveri beredskap* och *geo-replikering* viktiga funktioner för alla företag. Azure Event Hubs stöder både geo-haveri återställning och geo-replikering på namn områdes nivå. 

> [!NOTE]
> Funktionen för geo-katastrof återställning är bara tillgänglig för [standard-och dedikerade SKU: er](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att notera skillnaden mellan "avbrott" och "katastrofer". Ett **avbrott** är tillfälligt otillgängligt för Azure Event Hubs och kan påverka vissa komponenter i tjänsten, t. ex. ett meddelande arkiv eller till och med hela data centret. När problemet har åtgärd ATS blir Event Hubs dock tillgängligt igen. Normalt orsakar ett avbrott inte förlust av meddelanden eller andra data. Ett exempel på ett sådant avbrott kan vara ett strömavbrott i data centret. Vissa avbrott är bara korta anslutnings förluster på grund av tillfälliga eller nätverks problem. 

En *katastrof* definieras som en permanent eller mer långsiktig förlust av ett Event Hubs-kluster, Azure-region eller data Center. Regionen eller data centret kan komma att bli otillgängliga igen, eller så kan det vara nere i timmar eller dagar. Exempel på sådana katastrofer är brand, översvämning eller jord bävning. En katastrof som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. I de flesta fall bör det dock inte finnas någon data förlust och meddelanden kan återställas när data centret har säkerhetskopierats.

Den geo-haverie återställnings funktionen i Azure Event Hubs är en katastrof återställnings lösning. Begreppen och arbets flödet som beskrivs i den här artikeln gäller katastrof scenarier och inte tillfälligt, eller tillfälliga avbrott. En detaljerad beskrivning av haveri beredskap i Microsoft Azure finns i [den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och villkor

Funktionen för haveri beredskap implementerar haveri beredskap för metadata och förlitar sig på de primära och sekundära återställnings namn områdena för haveri beredskap. 

Funktionen för geo-katastrof återställning är endast tillgänglig för [standard-och dedikerade SKU: er](https://azure.microsoft.com/pricing/details/event-hubs/) . Du behöver inte göra några ändringar i anslutnings strängen eftersom anslutningen görs via ett alias.

Följande villkor används i den här artikeln:

-  *Alias*: namnet på en katastrof återställnings konfiguration som du ställer in. Aliaset innehåller en enda stabil fullständigt kvalificerad domän namns anslutnings sträng (FQDN). Program använder den här Ali Aset-anslutningssträngen för att ansluta till ett namn område. 

-  *Primär/sekundär namnrymd*: de namn områden som motsvarar aliaset. Det primära namn området är "aktivt" och tar emot meddelanden (kan vara ett befintligt eller nytt namn område). Det sekundära namn området är "passiv" och tar inte emot meddelanden. Metadata mellan båda är synkroniserade, så båda kan sömlöst acceptera meddelanden utan program kod eller anslutnings sträng ändringar. För att säkerställa att endast det aktiva namn området tar emot meddelanden måste du använda aliaset. 

-  *Metadata*: entiteter som händelse hubbar och konsument grupper; och deras egenskaper för tjänsten som är associerad med namn området. Endast entiteter och deras inställningar replikeras automatiskt. Meddelanden och händelser replikeras inte. 

-  *Redundans*: processen att aktivera det sekundära namn området.

## <a name="supported-namespace-pairs"></a>Namn rymds par som stöds
Följande kombinationer av primära och sekundära namn rymder stöds:  

| Primär namnrymd | Sekundär namnrymd | Stöds | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Yes | 
| Standard | Dedikerad | Yes | 
| Dedikerad | Dedikerad | Yes | 
| Dedikerad | Standard | No | 

> [!NOTE]
> Det går inte att para ihop namn områden som finns i samma dedicerade kluster. Du kan para ihop namn områden som finns i separata kluster. 

## <a name="setup-and-failover-flow"></a>Konfiguration och redundansväxla flöde

Följande avsnitt innehåller en översikt över redundansväxlingen och förklarar hur du ställer in den inledande redundansväxlingen. 

![1][]

### <a name="setup"></a>Konfiguration

Först skapar du eller använder ett befintligt primärt namn område och ett nytt sekundärt namn område och kopplar sedan samman de två. Den här ihopparningen ger dig ett alias som du kan använda för att ansluta. Eftersom du använder ett alias behöver du inte ändra anslutnings strängarna. Det går bara att lägga till nya namn områden i ihopparningen för redundans. Slutligen bör du lägga till viss övervakning för att upptäcka om det behövs en redundansväxling. I de flesta fall är tjänsten en del av ett stort eko system, vilket innebär att det inte går att använda automatiska redundanser, eftersom ofta redundans måste utföras i synkronisering med det återstående del systemet eller infrastrukturen.

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

Tänk på följande när du tänker på följande:

1. Enligt design replikeras Event Hubs geo-haveri beredskap inte replikerar data, och därför kan du inte återanvända det gamla förskjutning svärdet för din primära händelsehubben på den sekundära händelsehubben. Vi rekommenderar att du startar om din händelse mottagare med någon av följande metoder:

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

## <a name="private-endpoints"></a>Privata slut punkter
Det här avsnittet innehåller ytterligare information om hur du använder geo-haveri beredskap med namn områden som använder privata slut punkter. Information om hur du använder privata slut punkter med Event Hubs i allmänhet finns i [Konfigurera privata slut punkter](private-link-service.md).

### <a name="new-pairings"></a>Nya par
Om du försöker skapa en koppling mellan ett primärt namn område med en privat slut punkt och ett sekundärt namn område utan en privat slut punkt, kommer ihopparningen att Miss pare ras. Länkningen fungerar bara om både primärt och sekundärt namn område har privata slut punkter. Vi rekommenderar att du använder samma konfigurationer på de primära och sekundära namn områdena och i virtuella nätverk där privata slut punkter skapas.  

> [!NOTE]
> När du försöker para ihop det primära namn området med privat slut punkt och ett sekundärt namn område, kontrollerar validerings processen endast om det finns en privat slut punkt i det sekundära namn området. Den kontrollerar inte om slut punkten fungerar eller kommer att fungera efter en redundansväxling. Det är ditt ansvar att se till att det sekundära namn området med privat slut punkt fungerar som förväntat efter redundansväxlingen.
>
> Om du vill testa att konfigurationerna för privata slut punkter är samma på primära och sekundära namn områden, skickar du en Read-begäran (till exempel: [Hämta händelsehubben](/rest/api/eventhub/get-event-hub)) till det sekundära namn området utanför det virtuella nätverket och kontrollerar att du får ett fel meddelande från tjänsten.

### <a name="existing-pairings"></a>Befintliga länkningar
Om ihopparningen mellan det primära och sekundära namn området redan finns, kommer privat slut punkt att skapas på det primära namn området att Miss pare ras. Lös problemet genom att skapa en privat slut punkt på det sekundära namn området och skapa sedan en för det primära namn området.

> [!NOTE]
> Vi tillåter skrivskyddad åtkomst till det sekundära namn området, men uppdateringar av konfigurationer för privat slut punkt tillåts. 

### <a name="recommended-configuration"></a>Rekommenderad konfiguration
När du skapar en haveri beredskaps konfiguration för ditt program och Event Hubs namn områden måste du skapa privata slut punkter för både primära och sekundära Event Hubs namn områden mot virtuella nätverk som är värdar för både primära och sekundära instanser av programmet. 

Anta att du har två virtuella nätverk: VNET-1, VNET-2 och dessa primära och sekundära namn områden: EventHubs-Namespace1-Primary, EventHubs-Namespace2-Secondary. Du måste utföra följande steg: 

- På EventHubs-Namespace1-Primary skapar du två privata slut punkter som använder undernät från VNET-1 och VNET-2
- På EventHubs-Namespace2-sekundär skapar du två privata slut punkter som använder samma undernät från VNET-1 och VNET-2 

![Privata slut punkter och virtuella nätverk](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Fördelen med den här metoden är att redundansväxlingen kan ske i program lagret, oberoende av Event Hubs namn område. Beakta följande scenarier: 

**Redundans för program:** Här finns programmet inte i VNET-1, men kommer att flyttas till VNET-2. Eftersom både privata slut punkter har kon figurer ATS på både VNET-1 och VNET-2 för både primär och sekundär namnrymd, fungerar programmet bara. 

**Event Hubs namnrymd – endast redundans**: här igen eftersom både privata slut punkter har kon figurer ATS på båda virtuella nätverken för både primära och sekundära namn områden, fungerar programmet bara. 

> [!NOTE]
> Vägledning om geo-haveri beredskap för ett virtuellt nätverk finns i [Virtual Network verksamhets kontinuitet](../virtual-network/virtual-network-disaster-recovery-guidance.md).
 
## <a name="next-steps"></a>Nästa steg

* [Exemplet på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) går igenom ett enkelt arbets flöde som skapar en geo-par och initierar en redundansväxling för ett haveri beredskaps scenario.
* I [referensen REST API](/rest/api/eventhub/) beskrivs API: er för att utföra den geo-haveri återställnings konfigurationen.

Besök följande länkar för mer utförlig information om Event Hubs:

- Kom igång med händelsehubbar
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
