---
title: Återställning av geokatastrofer – Azure-händelsehubbar| Microsoft-dokument
description: Så här använder du geografiska regioner för att redundans och utföra haveriberedskap i Azure Event Hubs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281475"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs - Återställning av geokatastrofer 

När hela Azure-regioner eller datacenter (om inga [tillgänglighetszoner](../availability-zones/az-overview.md) används) upplever driftstopp är det viktigt för databearbetning att fortsätta att fungera i en annan region eller datacenter. *Geo-disaster recovery* och *Geo-replication* är därför viktiga funktioner för alla företag. Azure Event Hubs stöder både geo-haveriberedskap och geo-replikering, på namnområdesnivå. 

> [!NOTE]
> Funktionen För återställning av geokatastrofer är endast tillgänglig för [standard- och dedikerade SKU:er](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att notera skillnaden mellan "avbrott" och "katastrofer". Ett *avbrott* är tillfällig otillgänglighet för Azure Event Hubs och kan påverka vissa komponenter i tjänsten, till exempel ett meddelandearkiv eller till och med hela datacentret. Men när problemet har åtgärdats blir Event Hubs tillgängligt igen. Vanligtvis orsakar ett avbrott inte förlust av meddelanden eller andra data. Ett exempel på ett sådant avbrott kan vara ett strömavbrott i datacentret. Vissa avbrott är bara korta anslutningsförluster på grund av tillfälliga problem eller nätverksproblem. 

En *katastrof* definieras som permanent eller långsiktig förlust av ett Event Hubs-kluster, Azure-region eller datacenter. Regionen eller datacentret kanske eller kanske inte blir tillgängliga igen eller kan vara nere i timmar eller dagar. Exempel på sådana katastrofer är brand, översvämningar eller jordbävning. En katastrof som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. I de flesta fall bör det dock inte finnas någon dataförlust och meddelanden kan återställas när datacentret är säkerhetskopierat.

Funktionen Geo-disaster recovery i Azure Event Hubs är en lösning för haveriberedskap. Begreppen och arbetsflödet som beskrivs i den här artikeln gäller katastrofscenarier och inte tillfälliga eller tillfälliga avbrott. En detaljerad diskussion om haveriberedskap i Microsoft Azure finns i [den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Funktionen för haveriberedskap implementerar metadatakatastrofåterställning och är beroende av primära och sekundära namnområden för haveriberedskap. 

Funktionen För återställning av geokatastrofer är endast tillgänglig för [standard- och dedikerade SKU:er.](https://azure.microsoft.com/pricing/details/event-hubs/) Du behöver inte göra några ändringar i anslutningssträngen eftersom anslutningen görs via ett alias.

Följande termer används i den här artikeln:

-  *Alias*: Namnet på en konfiguration för haveriberedskap som du har konfigurerat. Aliaset innehåller en FQDN-anslutningssträng (Full Qualified Domain Name). Program använder den här aliasanslutningssträngen för att ansluta till ett namnområde. 

-  *Primärt/sekundärt namnområde*: De namnområden som motsvarar aliaset. Det primära namnområdet är "aktivt" och tar emot meddelanden (detta kan vara ett befintligt eller nytt namnområde). Det sekundära namnområdet är "passivt" och tar inte emot meddelanden. Metadata mellan båda är synkroniserade, så båda kan sömlöst acceptera meddelanden utan programkod eller ändringar av anslutningssträngen. Om du vill vara säkra på att endast det aktiva namnområdet tar emot meddelanden måste du använda aliaset. 

-  *Metadata*: Entiteter som händelsehubbar och konsumentgrupper. och deras egenskaper för tjänsten som är associerade med namnområdet. Observera att endast entiteter och deras inställningar replikeras automatiskt. Meddelanden och händelser replikeras inte. 

-  *Redundans*: Processen att aktivera det sekundära namnområdet.

## <a name="supported-namespace-pairs"></a>Namnområdespar som stöds
Följande kombinationer av primära och sekundära namnområden stöds:  

| Primärt namnområde | Sekundärt namnområde | Stöds | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Ja | 
| Standard | Dedikerad | Ja | 
| Dedikerad | Dedikerad | Ja | 
| Dedikerad | Standard | Inga | 

> [!NOTE]
> Du kan inte para ihop namnområden som finns i samma dedikerade kluster. Du kan para ihop namnområden som finns i separata kluster. 

## <a name="setup-and-failover-flow"></a>Installations- och redundansflöde

Följande avsnitt är en översikt över redundansprocessen och förklarar hur du ställer in den första redundansen. 

![1][]

### <a name="setup"></a>Installation

Du skapar eller använder först ett befintligt primärt namnområde och ett nytt sekundärt namnområde och parar sedan ihop de två. Den här parkopplingen ger dig ett alias som du kan använda för att ansluta. Eftersom du använder ett alias behöver du inte ändra anslutningssträngar. Endast nya namnområden kan läggas till i redundanskopplingen. Slutligen bör du lägga till en del övervakning för att upptäcka om en redundans är nödvändig. I de flesta fall är tjänsten en del av ett stort ekosystem, vilket innebär att automatiska redundans sällan är möjliga, eftersom mycket ofta redundans måste utföras i synk med det återstående delsystemet eller infrastrukturen.

### <a name="example"></a>Exempel

I ett exempel på det här scenariot bör du tänka på en kassalösning (POS) som avger meddelanden eller händelser. Event Hubs skickar dessa händelser till någon mappnings- eller omformateringslösning, som sedan vidarebefordrar mappade data till ett annat system för vidare bearbetning. Då kan alla dessa system finnas i samma Azure-region. Beslutet om när och vilken del som ska växlas över beror på dataflödet i infrastrukturen. 

Du kan automatisera redundans antingen med övervakningssystem eller med specialbyggda övervakningslösningar. Men sådan automatisering tar extra planering och arbete, vilket är utanför ramen för denna artikel.

### <a name="failover-flow"></a>Redundansflöde

Om du startar redundansen krävs två steg:

1. Om ett annat avbrott inträffar vill du kunna redundans igen. Ställ därför in ett annat passivt namnområde och uppdatera parkopplingen. 

2. Hämta meddelanden från det tidigare primära namnområdet när det är tillgängligt igen. Därefter använder du det namnområdet för vanliga meddelanden utanför konfigurationen för geoåterställning eller ta bort det gamla primära namnområdet.

> [!NOTE]
> Endast misslyckas framåt semantik stöds. I det här fallet växlar du över och parar sedan ihop med ett nytt namnområde. Det går inte att gå tillbaka. till exempel i ett SQL-kluster. 

![2][]

## <a name="management"></a>Hantering

Om du gjorde ett misstag; Till exempel parat ihop fel regioner under den första installationen, kan du bryta ihopkopplingen av de två namnområdena när som helst. Om du vill använda de parade namnområdena som vanliga namnområden tar du bort aliaset.

## <a name="samples"></a>Exempel

[Exemplet på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) visar hur du ställer in och initierar en redundans. Det här exemplet visar följande begrepp:

- Inställningar som krävs i Azure Active Directory för att använda Azure Resource Manager med eventhubbar. 
- Steg som krävs för att köra exempelkoden. 
- Skicka och ta emot från det aktuella primära namnområdet. 

## <a name="considerations"></a>Överväganden

Observera följande överväganden att tänka på med den här versionen:

1. Händelsehubbars geo-haveriberedskap replikerar inte data, och därför kan du inte återanvända det gamla förskjutningsvärdet för din primära händelsehubb på din sekundära händelsehubb. Vi rekommenderar att du startar om händelsemottagaren med något av följande:

- *EventPosition.FromStart()* - Om du vill läsa alla data på din sekundära händelsehubb.
- *EventPosition.FromEnd()* - Om du vill läsa alla nya data från tidpunkten för anslutningen till din sekundära händelsehubb.
- *EventPosition.FromEnqueuedTime(dateTime)* - Om du vill läsa alla data som tas emot i din sekundära händelsehubben från ett visst datum och en viss tid.

2. I din redundansplanering bör du också överväga tidsfaktorn. Om du till exempel förlorar anslutningen i längre tid än 15 till 20 minuter kan du välja att initiera redundansen. 
 
3. Det faktum att inga data replikeras innebär att aktiva sessioner inte replikeras. Dessutom kanske dubblettidentifiering och schemalagda meddelanden inte fungerar. Nya sessioner, schemalagda meddelanden och nya dubbletter fungerar. 

4. Om det inte går över en komplex [distribuerad](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) infrastruktur bör du repetera minst en gång. 

5. Synkroniseringsentiteter kan ta lite tid, cirka 50-100 entiteter per minut.

## <a name="availability-zones"></a>Tillgänglighetszoner 

Event Hubs Standard SKU stöder [tillgänglighetszoner](../availability-zones/az-overview.md)och tillhandahåller felosolerade platser i en Azure-region. 

> [!NOTE]
> Stöd för tillgänglighetszoner för Azure Event Hubs Standard är endast tillgängligt i [Azure-regioner](../availability-zones/az-overview.md#services-support-by-region) där tillgänglighetszoner finns.

Du kan aktivera tillgänglighetszoner endast på nya namnområden med hjälp av Azure-portalen. Event Hubs stöder inte migrering av befintliga namnområden. Du kan inte inaktivera zonredundans när du har aktiverat den på namnområdet.

![3][]

## <a name="next-steps"></a>Nästa steg

* [Exemplet på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) går igenom ett enkelt arbetsflöde som skapar en geo-parkoppling och initierar en redundans för ett katastrofåterställningsscenario.
* [REST API-referensen](/rest/api/eventhub/disasterrecoveryconfigs) beskriver API:er för att utföra konfigurationen för återställning av geokatastrofer.

Besök följande länkar för mer utförlig information om Event Hubs:

- Kom igång med händelsehubbar
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
