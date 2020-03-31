---
title: Isolera Azure Service Bus-program mot avbrott och katastrofer
description: Den här artikeln innehåller tekniker för att skydda program mot ett eventuellt avbrott i Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 2a7f5d5eacb2d03e64ae95d34e1cf0bd37bbc7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259258"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Metodtips för isolering av program mot Service Bus-avbrott och katastrofer

Verksamhetskritiska tillämpningar måste fungera kontinuerligt, även i närvaro av oplanerade avbrott eller katastrofer. I den här artikeln beskrivs tekniker som du kan använda för att skydda Service Bus-program mot ett potentiellt avbrott eller en katastrof för tjänsten.

Ett avbrott definieras som tillfällig otillgänglighet för Azure Service Bus. Avbrottet kan påverka vissa komponenter i Service Bus, till exempel ett meddelandearkiv, eller till och med hela datacentret. När problemet har åtgärdats blir Service Bus tillgängligt igen. Vanligtvis orsakar ett avbrott inte förlust av meddelanden eller andra data. Ett exempel på ett komponentfel är att det inte finns någon möjlighet för ett visst meddelandearkiv. Ett exempel på ett datacenteromfattande avbrott är ett strömavbrott i datacentret eller en felaktig datacenternätverksväxel. Ett avbrott kan pågå från några minuter till några dagar.

En katastrof definieras som permanent förlust av en servicebussskaleenhet eller datacenter. Datacentret kanske eller kanske inte blir tillgängligt igen. Vanligtvis orsakar en katastrof förlust av vissa eller alla meddelanden eller andra data. Exempel på katastrofer är brand, översvämningar eller jordbävning.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Skydd mot avbrott och katastrofer – Service Bus Premium
Begreppen hög tillgänglighet och haveriberedskap är inbyggda direkt i Azure Service Bus Premium-nivån, både inom samma region (via tillgänglighetszoner) och mellan olika regioner (via geokatastrofåterställning).

### <a name="geo-disaster-recovery"></a>Återställning av geokatastrofer

Service Bus Premium stöder geokatastrofåterställning på namnområdesnivå. Mer information finns i [Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md). Funktionen för haveriberedskap, endast tillgänglig för [Premium SKU,](service-bus-premium-messaging.md) implementerar metadatakatastrofåterställning och förlitar sig på primära och sekundära namnområden för haveriberedskap.

### <a name="availability-zones"></a>Tillgänglighetszoner

Service Bus Premium SKU stöder [tillgänglighetszoner](../availability-zones/az-overview.md)och tillhandahåller felosolerade platser inom samma Azure-region.

> [!NOTE]
> Stöd för tillgänglighetszoner för Azure Service Bus Premium är endast tillgängligt i [Azure-regioner](../availability-zones/az-overview.md#services-support-by-region) där tillgänglighetszoner finns.

Du kan aktivera tillgänglighetszoner endast på nya namnområden med hjälp av Azure-portalen. Service Bus stöder inte migrering av befintliga namnområden. Du kan inte inaktivera zonredundans när du har aktiverat den på namnområdet.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Skydd mot avbrott och katastrofer - Service Bus Standard
För att uppnå återhämtning mot datacenteravbrott när du använder standardprisnivån för meddelanden stöder Service Bus två metoder: *aktiv* och *passiv* replikering. Om en viss kö eller ett visst ämne måste vara tillgängligt i närvaro av ett datacenter avbrott för varje metod kan du skapa den i båda namnområdena. Båda entiteterna kan ha samma namn. En primär kö kan till exempel nås under **contosoPrimary.servicebus.windows.net/myQueue**, medan dess sekundära motsvarighet kan nås under **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> Inställningen **Aktiv replikering** **och passiv replikering** är lösningar för allmänna ändamål och inte specifika funktioner i Service Bus. Replikeringslogiken (skicka till 2 olika namnområden) finns på avsändarprogrammen och mottagaren måste ha anpassad logik för dubblettidentifiering.

Om programmet inte kräver permanent kommunikation mellan avsändare och mottagare kan programmet implementera en varaktig klientkö för att förhindra meddelandeförlust och skydda avsändaren från tillfälliga servicebussfel.

### <a name="active-replication"></a>Aktiv replikering
Aktiv replikering använder enheter i båda namnområdena för varje åtgärd. Alla klienter som skickar ett meddelande skickar två kopior av samma meddelande. Den första kopian skickas till den primära entiteten (till exempel **contosoPrimary.servicebus.windows.net/sales**) och den andra kopian av meddelandet skickas till den sekundära entiteten (till exempel **contosoSecondary.servicebus.windows.net/sales**).

En klient tar emot meddelanden från båda köerna. Mottagaren bearbetar den första kopian av ett meddelande och den andra kopian undertrycks. Om du vill ignorera dubblettmeddelanden måste avsändaren tagga varje meddelande med en unik identifierare. Båda kopiorna av meddelandet måste vara taggade med samma identifierare. Du kan använda egenskaperna [BrokeredMessage.MessageId][BrokeredMessage.MessageId] eller [BrokeredMessage.Label,][BrokeredMessage.Label] eller en anpassad egenskap för att tagga meddelandet. Mottagaren måste ha en lista över meddelanden som den redan har tagit emot.

Exemplet [Geo-replikering med Service Bus Standard-nivå][Geo-replication with Service Bus Standard Tier] visar aktiv replikering av meddelandeentiteter.

> [!NOTE]
> Den aktiva replikeringsmetoden fördubblar antalet åtgärder, därför kan den här metoden leda till högre kostnader.
> 
> 

### <a name="passive-replication"></a>Passiv replikering
I det felfria fallet använder passiv replikering bara en av de två meddelandeentiteterna. En klient skickar meddelandet till den aktiva entiteten. Om åtgärden på den aktiva entiteten misslyckas med en felkod som anger det datacenter som är värd för den aktiva entiteten kanske inte är tillgängligt, skickar klienten en kopia av meddelandet till den säkerhetskopierade entiteten. Vid den tidpunkten växlar aktiva enheter och säkerhetskopieringsenheter roller: den sändande klienten anser att den gamla aktiva entiteten är den nya säkerhetskopierade entiteten och den gamla säkerhetskopieringsentiteten är den nya aktiva entiteten. Om båda sändningsåtgärderna misslyckas förblir rollerna för de två entiteterna oförändrade och ett fel returneras.

En klient tar emot meddelanden från båda köerna. Eftersom det finns en chans att mottagaren får två kopior av samma meddelande måste mottagaren undertrycka dubblettmeddelanden. Du kan undertrycka dubbletter på samma sätt som beskrivs för aktiv replikering.

I allmänhet är passiv replikering mer ekonomisk än aktiv replikering eftersom endast en åtgärd utförs i de flesta fall. Svarstid, dataflöde och monetära kostnader är identiska med det icke-replikerade scenariot.

När du använder passiv replikering kan meddelanden i följande scenarier gå förlorade eller tas emot två gånger:

* **Meddelandefördröjning eller -meddelande:** Anta att avsändaren har skickat ett meddelande m1 till den primära kön och sedan blir kön otillgänglig innan mottagaren tar emot m1. Avsändaren skickar ett efterföljande meddelande m2 till den sekundära kön. Om den primära kön inte är tillgänglig för tillfället får mottagaren m1 när kön blir tillgänglig igen. Vid en katastrof kan mottagaren aldrig få m1.
* **Dubblettmottagning**: Anta att avsändaren skickar ett meddelande m till den primära kön. Service Bus har bearbetat m men kan inte skicka ett svar. När skicka åtgärden time out, skickar avsändaren en identisk kopia av m till den sekundära kön. Om mottagaren kan ta emot den första kopian av m innan den primära kön blir otillgänglig, tar mottagaren emot båda kopiorna av m vid ungefär samma tidpunkt. Om mottagaren inte kan ta emot den första kopian av m innan den primära kön blir otillgänglig, tar mottagaren först bara emot den andra kopian av m, men får sedan en andra kopia av m när den primära kön blir tillgänglig.

Exemplet [Geo-replikering med Service Bus Standard Tier][Geo-replication with Service Bus Standard Tier] visar passiv replikering av meddelandeentiteter.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Skydda relay-slutpunkter mot datacenter avbrott eller katastrofer
Geo-replikering av [Azure Relay-slutpunkter](../service-bus-relay/relay-what-is-it.md) gör att en tjänst som exponerar en relay-slutpunkt kan nås i närvaro av avbrott i Service Bus. För att uppnå geo-replikering måste tjänsten skapa två relay-slutpunkter i olika namnområden. Namnområdena måste finnas i olika datacenter och de två slutpunkterna måste ha olika namn. En primär slutpunkt kan till exempel nås under **contosoPrimary.servicebus.windows.net/myPrimaryService,** medan dess sekundära motsvarighet kan nås under **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Tjänsten lyssnar sedan på båda slutpunkterna och en klient kan anropa tjänsten via någon av slutpunkterna. Ett klientprogram väljer slumpmässigt ett av reläerna som primär slutpunkt och skickar sin begäran till den aktiva slutpunkten. Om åtgärden misslyckas med en felkod indikerar det här felet att relay-slutpunkten inte är tillgänglig. Programmet öppnar en kanal till slutpunkten för säkerhetskopiering och ger begäran ny upp. Då växlar de aktiva slutpunkterna och säkerhetskopieringsslutpunkterna roller: klientprogrammet anser att den gamla aktiva slutpunkten är den nya slutpunkten för säkerhetskopiering och den gamla slutpunkten för säkerhetskopiering som den nya aktiva slutpunkten. Om båda sändningsåtgärderna misslyckas förblir rollerna för de två entiteterna oförändrade och ett fel returneras.

## <a name="next-steps"></a>Nästa steg
Mer information om haveriberedskap finns i följande artiklar:

* [Geo-haveriberedskap för Azure Service Bus](service-bus-geo-dr.md)
* [Affärskontinuitet för Azure SQL-databas][Azure SQL Database Business Continuity]
* [Designa elastiska program för Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
