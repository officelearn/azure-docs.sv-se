---
title: Geo-haveriberedskap för Azure Service Bus | Microsoft-dokument
description: Så här använder du geografiska regioner för att redundans och utföra haveriberedskap i Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259583"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Geo-haveriberedskap för Azure Service Bus

När hela Azure-regioner eller datacenter (om inga [tillgänglighetszoner](../availability-zones/az-overview.md) används) upplever driftstopp är det viktigt för databearbetning att fortsätta att fungera i en annan region eller datacenter. *Geo-disaster recovery* är därför en viktig funktion för alla företag. Azure Service Bus stöder återställning av geo-haveri på namnområdesnivå.

Funktionen För återställning av geokatastrofer är globalt tillgänglig för Service Bus Premium SKU. 

>[!NOTE]
> Geo-Disaster recovery säkerställer för närvarande endast att metadata (köer, ämnen, prenumerationer, filter) kopieras över från det primära namnområdet till sekundärt namnområde när de paras ihop.

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att notera skillnaden mellan "avbrott" och "katastrofer". 

Ett *avbrott* är tillfällig otillgänglighet för Azure Service Bus och kan påverka vissa komponenter i tjänsten, till exempel ett meddelandearkiv eller till och med hela datacentret. Men när problemet är åtgärdat blir Service Bus tillgängligt igen. Vanligtvis orsakar ett avbrott inte förlust av meddelanden eller andra data. Ett exempel på ett sådant avbrott kan vara ett strömavbrott i datacentret. Vissa avbrott är bara korta anslutningsförluster på grund av tillfälliga problem eller nätverksproblem. 

En *katastrof* definieras som permanent eller långsiktig förlust av ett Service Bus-kluster, Azure-region eller datacenter. Regionen eller datacentret kanske eller kanske inte blir tillgängliga igen eller kan vara nere i timmar eller dagar. Exempel på sådana katastrofer är brand, översvämningar eller jordbävning. En katastrof som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. I de flesta fall bör det dock inte finnas någon dataförlust och meddelanden kan återställas när datacentret är säkerhetskopierat.

Funktionen Geo-disaster recovery i Azure Service Bus är en lösning för haveriberedskap. Begreppen och arbetsflödet som beskrivs i den här artikeln gäller katastrofscenarier och inte tillfälliga eller tillfälliga avbrott. En detaljerad diskussion om haveriberedskap i Microsoft Azure finns i [den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Funktionen för haveriberedskap implementerar metadatakatastrofåterställning och är beroende av primära och sekundära namnområden för haveriberedskap. Observera att funktionen för återställning av geokatastrofer endast är tillgänglig för [Premium SKU.](service-bus-premium-messaging.md) Du behöver inte göra några ändringar i anslutningssträngen eftersom anslutningen görs via ett alias.

Följande termer används i den här artikeln:

-  *Alias*: Namnet på en konfiguration för haveriberedskap som du har konfigurerat. Aliaset innehåller en FQDN-anslutningssträng (Full Qualified Domain Name). Program använder den här aliasanslutningssträngen för att ansluta till ett namnområde. Om du använder ett alias säkerställs att anslutningssträngen är oförändrad när redundansen utlöses.

-  *Primärt/sekundärt namnområde*: De namnområden som motsvarar aliaset. Det primära namnområdet är "aktivt" och tar emot meddelanden (detta kan vara ett befintligt eller nytt namnområde). Det sekundära namnområdet är "passivt" och tar inte emot meddelanden. Metadata mellan båda är synkroniserade, så båda kan sömlöst acceptera meddelanden utan programkod eller ändringar av anslutningssträngen. Om du vill vara säkra på att endast det aktiva namnområdet tar emot meddelanden måste du använda aliaset. 

-  *Metadata*: Entiteter som köer, ämnen och prenumerationer. och deras egenskaper för tjänsten som är associerade med namnområdet. Observera att endast entiteter och deras inställningar replikeras automatiskt. Meddelanden replikeras inte.

-  *Redundans*: Processen att aktivera det sekundära namnområdet.

## <a name="setup"></a>Installation

Följande avsnitt är en översikt för att ställa in parkoppling mellan namnområdena.

![1][]

Installationsprocessen är följande -

1. Etablera ett ***primary*** service bus premiumnamnområde.

2. Etablera ett ***sekundärt*** servicebusspremienamnområde i en annan region *än där det primära namnområdet etableras*. Detta kommer att bidra till att tillåta felisolering över olika datacenterregioner.

3. Skapa parkoppling mellan det primära namnområdet och sekundärt namnområde för att hämta ***aliaset***.

    >[!NOTE] 
    > Om du har [migrerat ditt Azure Service Bus Standard-namnområde till Azure Service Bus Premium](service-bus-migrate-standard-premium.md)måste du använda det befintliga aliaset (dvs. din servicebussstandardnamnområdesanslutningssträng) för att skapa konfigurationen för haveriberedskap via **PS/CLI-** eller **REST API**.
    >
    >
    > Detta beror på att ditt Azure Service Bus Standard-namnområdesanslutningssträng/DNS-namn under migreringen blir ett alias för ditt Azure Service Bus Premium-namnområde.
    >
    > Dina klientprogram måste använda det här aliaset (d.v.s. namnområdesanslutningssträngen för Azure Service Bus Standard) för att ansluta till premiumnamnområdet där parkopplingen av haveriberedskap har konfigurerats.
    >
    > Om du använder portalen för att konfigurera konfigurationen för haveriberedskap, kommer portalen att abstrahera den här varningsanklagningen från dig.


4. Använd ***det alias*** som erhålls i steg 3 för att ansluta klientprogrammen till det Geo-DR-aktiverade primära namnområdet. Inledningsvis pekar aliaset på det primära namnområdet.

5. [Valfritt] Lägg till lite övervakning för att identifiera om en redundans är nödvändig.

## <a name="failover-flow"></a>Redundansflöde

En redundans utlöses manuellt av kunden (antingen uttryckligen via ett kommando eller via klientägd affärslogik som utlöser kommandot) och aldrig av Azure. Detta ger kunden full ägarskap och synlighet för avbrottslösning på Azures stamnät.

![4][]

När redundansen har utlösts -

1. Aliasanslutningssträngen uppdateras så att den pekar på sekundärpremienamnområdet. ***alias***

2. Klienter (avsändare och mottagare) ansluter automatiskt till det sekundära namnområdet.

3. Den befintliga parkopplingen mellan primärt och sekundärt premiumnamnområde är bruten.

När redundansen väl har påbörjats -

1. Om ett annat avbrott inträffar vill du kunna växla över igen. Ställ därför in ett annat passivt namnområde och uppdatera parkopplingen. 

2. Hämta meddelanden från det tidigare primära namnområdet när det är tillgängligt igen. Därefter använder du det namnområdet för vanliga meddelanden utanför konfigurationen för geoåterställning eller ta bort det gamla primära namnområdet.

> [!NOTE]
> Endast misslyckas framåt semantik stöds. I det här fallet växlar du över och parar sedan ihop med ett nytt namnområde. Det går inte att gå tillbaka. till exempel i ett SQL-kluster. 

Du kan automatisera redundans antingen med övervakningssystem eller med specialbyggda övervakningslösningar. Men sådan automatisering tar extra planering och arbete, vilket är utanför ramen för denna artikel.

![2][]

## <a name="management"></a>Hantering

Om du gjorde ett misstag; Till exempel parat ihop fel regioner under den första installationen, kan du bryta ihopkopplingen av de två namnområdena när som helst. Om du vill använda de parade namnområdena som vanliga namnområden tar du bort aliaset.

## <a name="use-existing-namespace-as-alias"></a>Använda befintligt namnområde som alias

Om du har ett scenario där du inte kan ändra anslutningar för producenter och konsumenter kan du återanvända namnområdets namn som aliasnamn. Se [exempelkoden på GitHub här](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Exempel

Exemplen [på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) visar hur du ställer in och initierar en redundans. Dessa exempel visar följande begrepp:

- Ett .NET-exempel och inställningar som krävs i Azure Active Directory för att använda Azure Resource Manager med Service Bus för att konfigurera och aktivera återställning av geokatastrofer.
- Steg som krävs för att köra exempelkoden.
- Så här använder du ett befintligt namnområde som ett alias.
- Åtgärder för att alternativt aktivera geokatastrofåterställning via PowerShell eller CLI.
- [Skicka och ta emot](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) från det aktuella primära eller sekundära namnområdet med hjälp av aliaset.

## <a name="considerations"></a>Överväganden

Observera följande överväganden att tänka på med den här versionen:

1. I din redundansplanering bör du också överväga tidsfaktorn. Om du till exempel förlorar anslutningen i längre tid än 15 till 20 minuter kan du välja att initiera redundansen.

2. Det faktum att inga data replikeras innebär att aktiva sessioner inte replikeras. Dessutom kanske dubblettidentifiering och schemalagda meddelanden inte fungerar. Nya sessioner, nya schemalagda meddelanden och nya dubbletter fungerar. 

3. Om det inte går över en komplex [distribuerad](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) infrastruktur bör du repetera minst en gång.

4. Synkroniseringsentiteter kan ta lite tid, cirka 50-100 entiteter per minut. Prenumerationer och regler räknas också som entiteter.

## <a name="availability-zones"></a>Tillgänglighetszoner

Service Bus Premium SKU stöder också [tillgänglighetszoner](../availability-zones/az-overview.md), som tillhandahåller fel-isolerade platser inom en Azure-region.

> [!NOTE]
> Stöd för tillgänglighetszoner för Azure Service Bus Premium är endast tillgängligt i [Azure-regioner](../availability-zones/az-overview.md#services-support-by-region) där tillgänglighetszoner finns.

Du kan aktivera tillgänglighetszoner endast på nya namnområden med hjälp av Azure-portalen. Service Bus stöder inte migrering av befintliga namnområden. Du kan inte inaktivera zonredundans när du har aktiverat den på namnområdet.

![3][]

## <a name="next-steps"></a>Nästa steg

- Se [REST API-referensen för REST API](/rest/api/servicebus/disasterrecoveryconfigs)för geo-haverining här .
- Kör exemplet med geokatastrofåterställning [på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Se exemplet med återställning av [geokatastrofer som skickar meddelanden till ett alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Mer information om Service Bus-meddelanden finns i följande artiklar:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API för vila](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
