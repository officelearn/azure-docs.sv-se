---
title: Azure Event Hubs geo-katastrofåterställning | Microsoft Docs
description: Hur du använder geografiska regioner för redundans och utföra katastrofåterställning i Azure Event Hubs
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: sethm
ms.openlocfilehash: 0192f65f394a3bb6d5cffc90639966b5f913b291
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302121"
---
# <a name="azure-event-hubs-geo-disaster-recovery"></a>Azure Event Hubs Geo-katastrofåterställning

När hela Azure-regioner eller Datacenter (om inget [tillgänglighet zoner](../availability-zones/az-overview.md) används) eventuellt driftstopp, det är viktigt för databearbetning fortsätta att arbeta i en annan region eller datacenter. Därför *Geo-återställning* och *georeplikering* är viktiga funktioner för företag. Händelsehubbar i Azure stöder både geo-återställning och geo-replikering på namnområdesnivån. 

Funktionen Geo disaster recovery finns globalt för Event Hubs Standard-SKU.

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att Observera skillnaden mellan ”avbrott” och ”katastrofer”. En *avbrott* är tillfällig frånvaro av Händelsehubbar i Azure och kan påverka vissa komponenter av tjänsten, till exempel ett meddelandearkiv eller även hela datacentret. Men när problemet är åtgärdat blir Händelsehubbar tillgänglig igen. Normalt orsakar avbrott inte meddelanden eller andra data förlorade. Ett exempel på sådana avbrott kan vara ett strömavbrott i datacentret. Vissa avbrott är endast kort anslutning förluster på grund av problem med övergående eller nätverket. 

En *katastrofåterställning* definieras som permanenta eller långsiktig förlust av en Händelsehubbar kluster, Azure-region eller datacenter. Region eller datacenter kan eller kan inte bli tillgänglig igen eller kan vara avstängd för timmar eller dagar. Exempel på sådana katastrofer är brand, överbelasta eller jordbävning. En katastrof som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. Men i de flesta fall bör det finnas ingen dataförlust och meddelanden som kan återställas när Datacenter är säkerhetskopiera.

Geo-disaster recovery-funktionen av Händelsehubbar i Azure är en lösning för katastrofåterställning. Koncept och arbetsflödet som beskrivs i den här artikeln gäller katastrofåterställning scenarier och inte tillfälligt eller tillfälliga avbrott. En detaljerad beskrivning av katastrofåterställning i Microsoft Azure finns [i den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Funktionen disaster recovery implementerar metadata katastrofåterställning och bygger på primära och sekundära disaster recovery-namnområden. Observera att Geo disaster recovery-funktionen är tillgänglig för den [Standard SKU](https://azure.microsoft.com/pricing/details/event-hubs/) endast. Du behöver inte göra ändringar anslutning sträng som anslutningen görs via ett alias.

Följande villkor används i den här artikeln:

-  *Alias*: namn för någon haveriberedskapskonfiguration som du skapat. Aliaset som innehåller en enda stabil anslutningssträng för fullständigt kvalificerade domännamn (FQDN). Program använda anslutningssträng med detta alias för att ansluta till ett namnområde. 

-  *Primära och sekundära namnområdet*: de namnområden som motsvarar aliaset. Det primära namnområdet är ”aktiv” och tar emot meddelanden (det kan vara en befintlig eller ny namnområde). Sekundär namnområdet är ”passiva” och ta emot inte meddelanden. Metadata mellan de båda är synkroniserade, så att både sömlöst kan godkänna meddelanden utan någon kod eller ett anslutningsfel sträng ändringar i programmet. För att säkerställa att endast aktiva namnområdet tar emot meddelanden, måste du använda detta alias. 

-  *Metadata*: entiteter, till exempel händelsehubbar och konsumentgrupper; och deras egenskaper för tjänsten som är associerade med namnområdet. Observera att endast enheter och deras inställningar replikeras automatiskt. Meddelanden och händelser replikeras inte. 

-  *Redundans*: att aktivera sekundär namnområdet.

## <a name="setup-and-failover-flow"></a>Installationen och växling vid fel

Följande avsnitt är en översikt över failover-processen och förklarar hur du ställer in inledande växling vid fel. 

![1][]

### <a name="setup"></a>Konfiguration

Du först skapa eller använda en befintlig primär namnrymd och ett nytt sekundära namnområde och sedan koppla två. Länkning av den här får du ett alias som du kan använda för att ansluta. Eftersom du använder ett alias, behöver du inte ändra anslutningssträngar. Endast nya namnområden kan läggas till redundans para ihop. Slutligen bör du lägga till vissa övervakning för att identifiera om det krävs en växling vid fel. I de flesta fall tjänsten är en del av ett stort ekosystem, vilket automatisk redundans är sällan möjliga som ofta växling vid fel måste utföras synkroniserad med återstående undersystemet eller infrastruktur.

### <a name="example"></a>Exempel

Överväga en lösning för platsen för försäljning (POS) som skickar meddelanden eller händelser i ett exempel på det här scenariot. Händelsehubbar skickar händelser till vissa mappning eller formatera om lösning som sedan vidarebefordrar mappade data till ett annat system för vidare bearbetning. Alla dessa system kan då finnas i samma Azure-region. Beslut om när och vilken del att växla över beror på dataflödet i din infrastruktur. 

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

## <a name="samples"></a>Exempel

Den [på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) visar hur du ställer in och initiera redundans. Det här exemplet visar följande:

- Inställningar som krävs i Azure Active Directory för att använda Azure Resource Manager med Händelsehubbar. 
- Steg som krävs för att köra exempelkoden. 
- Skicka och ta emot från det aktuella primära namnområdet. 

## <a name="considerations"></a>Överväganden

Observera följande viktiga aspekter att tänka på med den här versionen:

1. Du bör också övervägas tid faktorn under växling vid fel planeringen. Till exempel om du förlorar anslutningen längre än 15 till 20 minuter kan du välja att påbörja redundans. 
 
2. Det faktum att inga data replikeras innebär att för tillfället aktiva sessioner inte replikeras. Dessutom kanske dubblettidentifiering och schemalagda meddelanden inte fungerar. Nya sessioner, schemalagda meddelanden och nya dubbletter fungerar. 

3. Misslyckande en infrastruktur för komplexa distribuerade ska vara [testas](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) minst en gång. 

4. Synkronisera enheter kan ta lite tid, ungefär 50 – 100 entiteter per minut.

## <a name="availability-zones-preview"></a>Tillgänglighet zoner (förhandsgranskning)

Event Hubs Standard SKU stöder också [tillgänglighet zoner](../availability-zones/az-overview.md), att tillhandahålla feltolerans isolerad platser inom en Azure-region. 

> [!NOTE]
> Tillgänglighet zoner förhandsgranskningen stöds bara i den **centrala USA**, **östra USA 2**, och **Frankrike centrala** regioner.

Du kan aktivera tillgänglighet zoner på endast nya namnområden med hjälp av Azure portal. Händelsehubbar har inte stöd för migrering av befintliga namnområden. Du kan inte inaktivera redundans när den har aktiverats på ditt namnområde.

![3][]

## <a name="next-steps"></a>Nästa steg

* Den [på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) går igenom ett enkelt arbetsflöde som skapar ett geo-par och initierar en växling vid fel för en katastrofåterställning.
* Den [REST API-referensen](/rest/api/eventhub/disasterrecoveryconfigs) beskriver API: er för att utföra Geo-katastrofberedskapskonfigurationen.

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png