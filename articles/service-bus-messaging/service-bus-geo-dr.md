---
title: Azure Service Bus geo-haveri beredskap | Microsoft Docs
description: Hur du använder geografiska regioner för att redundansväxla och utföra haveri beredskap i Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356627"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geo-haveri beredskap

När hela Azure-regioner eller data Center (om inga [tillgänglighets zoner](../availability-zones/az-overview.md) används) upplever drift stopp, är det viktigt att data bearbetningen fortsätter att fungera i en annan region eller data Center. Därför är *geo-haveri beredskap* en viktig funktion för alla företag. Azure Service Bus stöder geo-katastrof återställning på namn områdes nivå.

Funktionen för geo-katastrof återställning är globalt tillgänglig för Service Bus Premium SKU. 

>[!NOTE]
> Geo-haveri beredskap garanterar för närvarande bara att metadata (köer, ämnen, prenumerationer och filter) kopieras från det primära namn området till ett sekundärt namn område vid koppling.

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att Observera skillnaden mellan ”avbrott” och ”katastrofer”. 

Ett *avbrott* är tillfälligt otillgängligt för Azure Service Bus och kan påverka vissa komponenter i tjänsten, t. ex. ett meddelande arkiv eller till och med hela data centret. När problemet har åtgärd ATS blir Service Bus dock tillgängligt igen. Ett avbrott medför normalt förlusten av meddelanden eller andra data. Ett exempel på sådana ett avbrott kan vara ett strömavbrott i datacentret. Vissa avbrott kan endast kort anslutning förluster på grund av problem med tillfälliga eller. 

En *katastrof* definieras som en permanent eller mer långsiktig förlust av ett Service Bus-kluster, Azure-region eller data Center. Den region eller datacenter kan eller kan inte bli tillgänglig igen, eller kanske inte körs i timmar eller dagar. Exempel på sådana katastrofer är fire, överbelasta eller jordbävning. Problem som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. Men i de flesta fall bör det finnas inga data går förlorade och meddelanden kan återställas när datacentret är säkerhetskopiera.

Funktionen för att återställa geo-haverier i Azure Service Bus är en lösning för katastrof återställning. Koncept och arbetsflödet som beskrivs i den här artikeln gäller katastrofscenarier och inte tillfälliga eller tillfälliga avbrott. En detaljerad beskrivning av haveri beredskap i Microsoft Azure finns i [den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Funktionen disaster recovery implementerar metadata katastrofåterställning och förlitar sig på primära och sekundära disaster recovery-namnområden. Observera att funktionen för geo-katastrof återställning endast är tillgänglig för [Premium-SKU: n](service-bus-premium-messaging.md) . Du behöver inte göra några ändringar av anslutningen sträng, eftersom anslutningen görs via ett alias.

I den här artikeln används följande termer:

-  *Alias*: namnet på en katastrof återställnings konfiguration som du ställer in. Aliaset som innehåller en enda stabil anslutningssträng för fullständigt kvalificerade domännamn (FQDN). Program använder den här anslutningssträngen för alias för att ansluta till ett namnområde. Genom att använda ett alias ser du till att anslutnings strängen är oförändrad När redundansväxlingen utlöses.

-  *Primär/sekundär namnrymd*: de namn områden som motsvarar aliaset. Det primära namnområdet är ”aktiv” och tar emot meddelanden (det kan vara ett namnområde för befintliga eller nya). Det sekundära namnområdet är ”passiva” och ta emot inte meddelanden. Metadata mellan båda är synkroniserade, så att båda sömlöst kan godkänna meddelanden utan program kod eller anslutningen sträng ändringar. För att säkerställa att endast aktiva namnområdet tar emot meddelanden, måste du använda detta alias. 

-  *Metadata*: entiteter som köer, ämnen och prenumerationer. och deras egenskaper för tjänsten som är associerad med namn området. Observera att endast entiteter och deras inställningar replikeras automatiskt. Meddelanden replikeras inte.

-  *Redundans*: processen att aktivera det sekundära namn området.

## <a name="setup"></a>Konfiguration

Följande avsnitt är en översikt över hur du ställer in koppling mellan namn områden.

![1][]

Installations processen är följande:

1. Etablera ett ***primärt*** Service Bus Premium-namnområde.

2. Etablera ett ***sekundärt*** Service Bus Premium-namnområde i en *annan region än den där det primära namn området etableras*. Detta gör det möjligt att isolera fel i olika data Center regioner.

3. Skapa koppling mellan det primära namn området och sekundär namnrymd för att hämta ***aliaset***.

    >[!NOTE] 
    > Om du har [migrerat Azure Service Bus standard namn området till Azure Service Bus Premium](service-bus-migrate-standard-premium.md)måste du använda det befintliga aliaset (t. ex. din Service Bus standard namn rymds anslutnings sträng) för att skapa haveri beredskap via **PS/CLI** eller **REST API**.
    >
    >
    > Detta beror på att när du migrerar Azure Service Bus standard namn områdets anslutnings sträng/DNS-namn till ett alias till ditt Azure Service Bus Premium-namnområde.
    >
    > Klient programmen måste använda det här aliaset (d.v.s. anslutnings strängen Azure Service Bus standard namn område) för att ansluta till Premium-namnområdet där haveri beredskap har kon figurer ATS.
    >
    > Om du använder portalen för att konfigurera haveri beredskaps konfigurationen kommer portalen att sammanställa detta villkor från dig.


4. Använd det ***alias*** som hämtades i steg 3 för att ansluta dina klient program till det geo-Dr-aktiverade primära namn området. Från början pekar alias till det primära namn området.

5. Valfritt Lägg till viss övervakning för att identifiera om en redundansväxling krävs.

## <a name="failover-flow"></a>Redundansflöde

En redundansväxling utlöses manuellt av kunden (antingen manuellt via ett kommando eller via den klient som ägs av företaget och som utlöser kommandot) och aldrig av Azure. Detta ger kunden fullständig ägande rätt och synlighet för avbrotts lösningar på Azures stamnät.

![4][]

När redundansväxlingen har utlösts –

1. ***Ali Asets*** anslutnings sträng uppdateras för att peka på det sekundära Premium-namnområdet.

2. Klienter (avsändare och mottagare) ansluter automatiskt till det sekundära namn området.

3. Det befintliga paret mellan primär och sekundär Premium-namnrymd är brutet.

När redundansväxlingen har initierats –

1. Om ett annat avbrott inträffar vill du kunna redundansväxla igen. Därför kan ställa in en annan passiva namnrymd och uppdatera kopplingen. 

2. Hämta meddelanden från det tidigare versionen primärt namnområdet när den är tillgänglig igen. Använd det här namnområdet för regelbundna meddelanden utanför din konfiguration för geo-återställning efter det eller ta bort det gamla primära namnområdet.

> [!NOTE]
> Endast misslyckas vidarebefordra semantik stöds. I det här scenariot du växlar över och sedan koppla igen med ett nytt namnområde. Det går inte att återställas. till exempel i en SQL-kluster. 

Du kan automatisera redundans med övervakningssystem eller med anpassade övervakningslösningar. Sådana automation tar dock extra planering och arbete som ligger utanför omfånget för den här artikeln.

![2][]

## <a name="management"></a>Hantering

Om du har gjort ett misstag; till exempel du länkade fel regioner under den första konfigurationen, du kan bryta länkningen av de två namnområdena när som helst. Om du vill använda den kopplade namnområden som vanlig namnområden tar du bort den.

## <a name="use-existing-namespace-as-alias"></a>Använd befintligt namn område som alias

Om du har ett scenario där du inte kan ändra anslutningarna till producenter och konsumenter kan du återanvända namn områdets namn som aliasnamn. Se [exempel koden på GitHub här](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Exempel

I [exemplen på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) visas hur du konfigurerar och initierar en redundansväxling. Dessa exempel demonstrerar följande begrepp:

- Ett .NET-exempel och inställningar som krävs i Azure Active Directory för att använda Azure Resource Manager med Service Bus för att konfigurera och aktivera geo-katastrof återställning.
- Steg som krävs för att köra exempelkoden.
- Använda ett befintligt namn område som ett alias.
- Steg för att aktivera geo-katastrof återställning via PowerShell eller CLI.
- [Skicka och ta emot](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) från det aktuella primära eller sekundära namn området med hjälp av aliaset.

## <a name="considerations"></a>Överväganden

Observera följande överväganden att tänka på med den här versionen:

1. I redundans planeringen, bör du också faktorn tid. Exempelvis kan kan du förlora anslutningen längre än 15 till 20 minuter du välja att påbörja redundans.

2. Det faktum att inga data replikeras innebär att för närvarande aktiva sessioner inte replikeras. Dessutom fungerar inte dubblettidentifiering och schemalagda meddelanden. Nya sessioner kommer nya schemalagda meddelanden och nya dubbletter att fungera. 

3. Att redundansväxla en komplex distribuerad infrastruktur bör återställas [minst en](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) gång.

4. Synkronisera enheter kan ta lite tid, cirka 50 – 100 entiteter per minut. Prenumerationer och regler räknas också som entiteter.

## <a name="availability-zones"></a>Tillgänglighetszoner

Service Bus Premium-SKU: n stöder också [Tillgänglighetszoner](../availability-zones/az-overview.md)som ger felisolerade platser inom en Azure-region.

> [!NOTE]
> Tillgänglighetszoner stöd för Azure Service Bus Premium är bara tillgängligt i [Azure-regioner](../availability-zones/az-overview.md#services-support-by-region) där tillgänglighets zoner finns.

Du kan aktivera Tillgänglighetszoner på nya namnområden, med hjälp av Azure portal. Service Bus stöder inte migrering av befintliga namn rymder. Du kan inte inaktivera redundans när du har aktiverat i namnområdet.

![3][]

## <a name="next-steps"></a>Nästa steg

- Se [REST API referens](/rest/api/servicebus/disasterrecoveryconfigs)för geo-haveri återställning här.
- Kör återställnings exemplet för geo-haveri [på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Se det geo-katastrof återställnings [exempel som skickar meddelanden till ett alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Mer information om Service Bus meddelanden finns i följande artiklar:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST-API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
