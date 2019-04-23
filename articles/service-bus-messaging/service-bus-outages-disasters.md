---
title: Isolering av Azure Service Bus-program mot avbrott och katastrofer | Microsoft Docs
description: Tekniker för att skydda mot potentiella avbrott i ett Service Bus-program.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 24611e265788cf046aa0733bc423917aaf305427
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003028"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Metodtips för isolering av program mot Service Bus-avbrott och katastrofer

Verksamhetskritiska program måste fungera kontinuerligt, även om det förekommer oplanerade avbrott eller katastrofer. Den här artikeln beskrivs metoder som du kan använda för att skydda Service Bus-program mot ett möjliga tjänstavbrott eller en katastrof.

Ett avbrott har definierats som Azure Service Bus är tillfälligt otillgänglig. Driftstörningarna kan påverka vissa komponenter i Service Bus, till exempel ett meddelandearkiv eller även hela datacentret. När problemet har åtgärdats, aktiveras Service Bus igen. Ett avbrott medför normalt förlorade meddelanden eller andra data. Ett exempel på ett komponentfel är otillgänglig för en viss meddelandearkiv. Ett exempel på ett avbrott i hela datacentret är ett strömavbrott i datacentret eller en felaktig datacenter nätverksväxel. Ett avbrott kan vara från ett par minuter till några dagar.

En katastrof har definierats som permanent förlusten av en Service Bus-skalningsenhet eller datacenter. Datacentret kan eller kan inte bli tillgänglig igen. Vanligtvis går en katastrof förlorade vissa eller alla meddelanden eller andra data. Exempel på katastrofer är fire, överbelasta eller jordbävning.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Skydd mot avbrott och katastrofer - Service Bus Premium
Hög tillgänglighet och katastrofåterställning begrepp finns inbyggda i Azure Service Bus Premium-nivå, både inom samma region (via Tillgänglighetszoner) och över olika regioner (via Geohaveriberedskap).

### <a name="geo-disaster-recovery"></a>Geohaveriberedskap

Service Bus Premium har stöd för geohaveriberedskap på namnområdesnivå. Mer information finns i [Azure Service Bus geohaveriberedskap](service-bus-geo-dr.md). Disaster recovery funktionen tillgänglig för den [Premium-SKU](service-bus-premium-messaging.md) , implementerar metadata katastrofåterställning och förlitar sig på primära och sekundära disaster recovery-namnområden.

### <a name="availability-zones"></a>Tillgänglighetszoner

Har stöd för Service Bus Premium-SKU [Tillgänglighetszoner](../availability-zones/az-overview.md), vilket ger felisolerade platser inom samma Azure-region.

> [!NOTE]
> Tillgänglighetszoner-support för Azure Service Bus Premium är bara tillgängliga i [Azure-regioner](../availability-zones/az-overview.md#services-support-by-region) där tillgänglighetszoner finns.

Du kan aktivera Tillgänglighetszoner på nya namnområden, med hjälp av Azure portal. Service Bus stöder inte migreringen av befintliga namnområden. Du kan inte inaktivera redundans när du har aktiverat i namnområdet.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Skydd mot avbrott och katastrofer - Service Bus Standard
För att uppnå elasticitet mot datacenter-avbrott när du använder standard messaging prisnivå, Service Bus stöder två metoder: *active* och *passiva* replikering. För varje metod, om en viss kö eller ämne måste vara tillgänglig när det finns ett avbrott på datacentret, kan du skapa den i både namnområden. Båda entiteter kan ha samma namn. Till exempel en primära kön kan nås **contosoPrimary.servicebus.windows.net/myQueue**, medan motparten sekundära kan nås **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> Den **aktiv replikering** och **passiva replikering** installationsprogrammet är allmänna lösningar och inte specifika funktioner i Service Bus. Replikering logiken (skicka till 2 olika namnområden) ligger på program som avsändaren och mottagaren måste ha anpassad logik för identifiering av dubbletter.

Om programmet inte kräver permanent avsändaren att mottagaren kommunikation kan programmet implementera en beständig klientsidan kö att förhindra förlust av meddelandet och skärma av att avsändaren inte eventuella tillfälliga fel i Service Bus.

### <a name="active-replication"></a>Aktiv replikering
Aktiv replikering använder entiteter i båda namnrymderna för varje åtgärd. Alla klienter som skickar ett meddelande skickar två kopior av samma meddelande. Den första kopian skickas till den primära entiteten (till exempel **contosoPrimary.servicebus.windows.net/sales**), och den andra kopian av meddelandet skickas till den sekundära entiteten (till exempel  **contosoSecondary.servicebus.windows.net/sales**).

En klient tar emot meddelanden från köer med båda. Mottagaren kan bearbeta den första kopian av ett meddelande och den andra kopian undertrycks. Om du inte dubbletter av meddelanden, måste avsändaren tagga varje meddelande med en unik identifierare. Båda kopiorna av meddelandet måste vara taggad med samma identifierare. Du kan använda den [BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] eller [BrokeredMessage.Label] [ BrokeredMessage.Label] egenskaper eller en anpassad egenskap att tagga meddelandet. Mottagaren måste ha en lista med meddelanden som redan har tagit emot.

Den [Geo-replikering med Service Bus Standard-nivån] [ Geo-replication with Service Bus Standard Tier] exempel visar aktiv replikering av meddelandeentiteter.

> [!NOTE]
> Metoden som aktiv replikering fördubblar antalet åtgärder, därför den här metoden kan leda till högre kostnad.
> 
> 

### <a name="passive-replication"></a>Passiv replikering
I fall felfri använder passiva replikering bara en av de två meddelandeentiteter. En klient skickar meddelandet till den aktiva entiteten. Om åtgärden på den aktiva entiteten misslyckas med en felkod som anger det datacenter som är värd för den aktiva enheten kan vara otillgänglig, skickar klienten en kopia av meddelandet till entiteten säkerhetskopiering. Då aktivt och Säkerhetskopieringens entiteter växla roll: skickar klienten betraktar gamla active entiteten ska vara den nya entiteten för säkerhetskopiering och gamla säkerhetskopiering entiteten är den nya aktiva entiteten. Om både skicka åtgärder misslyckas rollerna för de två entiteterna förblir oförändrade och returneras ett fel.

En klient tar emot meddelanden från köer med båda. Eftersom det finns en risk att mottagaren får två kopior av samma meddelande, måste mottagaren inte dubbletter av meddelanden. Du kan förhindra dubbletter på samma sätt som beskrivs för aktiv replikering.

I allmänhet är passiva replikeringen mer ekonomiskt än aktiv replikering eftersom endast en åtgärd utförs i de flesta fall. Svarstid, dataflöde och kostnaden är identiska med icke-replikerade-scenario.

När du använder passiva replikering, i följande scenarier kan meddelanden vara tappas bort eller tas emot två gånger:

* **Meddeladefördröjning eller förlust**: Anta att avsändaren har skickat ett meddelande m1 till den primära kön och sedan kön blir otillgänglig innan mottagaren tar emot m1. Avsändaren skickar ett statusmeddelande m2 till den sekundära kön. Om den primära kön är inte tillgänglig för tillfället, får mottagaren m1 när kön blir tillgänglig igen. Vid en katastrof får mottagaren m1.
* **Duplicera mottagningen**: Anta att avsändaren skickar ett meddelande m till den primära kön. Service Bus har bearbetar m men misslyckas med att skicka ett svar. När åtgärden Skicka tidsgränsen skickar avsändaren en identisk kopia av m till den sekundära kön. Om mottagaren är kan ta emot den första kopian av m innan den primära kön blir otillgänglig, får mottagaren bägge m vid ungefär samma tillfälle. Om mottagaren inte kan ta emot den första kopian av m innan den primära kön blir otillgänglig, mottagaren får inledningsvis endast den andra kopian av m, men de tar emot en andra kopia av m när den primära kön blir tillgänglig.

Den [Geo-replikering med Service Bus Standard-nivån] [ Geo-replication with Service Bus Standard Tier] exempel visar passiva replikering av meddelandeentiteter.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Skyddar relay slutpunkter mot datacenter-avbrott och katastrofer
GEO-replikering för relay-slutpunkter kan en tjänst som Exponerar en relay-slutpunkt för att kunna nås när det finns Service Bus-avbrott. För att uppnå geo-replikering kan måste tjänsten skapa två relay-slutpunkter i olika namnområden. Namnområden måste finnas i olika datacenter och två slutpunkter måste ha olika namn. Till exempel en primära slutpunkten kan nås **contosoPrimary.servicebus.windows.net/myPrimaryService**, medan motparten sekundära kan nås **contosoSecondary.servicebus.windows.net /mySecondaryService**.

Sedan lyssnar tjänsten på båda slutpunkterna och en klient kan anropa tjänsten via antingen slutpunkt. Ett klientprogram slumpmässigt tar en av reläer som den primära slutpunkten och skickar begäran till aktiv slutpunkt. Om åtgärden misslyckas med felkoden, betyder ett misslyckande att relay slutpunkten inte är tillgänglig. Programmet öppnar en kanal till slutpunkten för säkerhetskopiering och kör begäran. Då aktivt och säkerhetskopiering slutpunkterna växla roll: klientprogrammet tar hänsyn till den gamla aktiv slutpunkten att vara den nya säkerhetskopiering slutpunkten och den gamla säkerhetskopiering slutpunkten ska vara den nya aktiva slutpunkten. Om både skicka åtgärder misslyckas rollerna för de två entiteterna förblir oförändrade och returneras ett fel.

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om katastrofåterställning kan du läsa följande artiklar:

* [Azure Service Bus Geo-haveriberedskap](service-bus-geo-dr.md)
* [Azure SQL Database-affärskontinuitet][Azure SQL Database Business Continuity]
* [Designa program med återhämtningsförmåga för Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
