---
title: Isolering av Azure Service Bus-program mot avbrott och katastrofer | Microsoft Docs
description: Tekniker för att skydda mot potentiella avbrott i ett Service Bus-program.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/14/2018
ms.author: spelluru
ms.openlocfilehash: 5401d43f11c8afc02f48dd643fd4ff2f9611e06e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696725"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Metodtips för isolering av program mot Service Bus-avbrott och katastrofer

Verksamhetskritiska program måste fungera kontinuerligt, även om det förekommer oplanerade avbrott eller katastrofer. Den här artikeln beskrivs metoder som du kan använda för att skydda Service Bus-program mot ett möjliga tjänstavbrott eller en katastrof.

Ett avbrott har definierats som Azure Service Bus är tillfälligt otillgänglig. Driftstörningarna kan påverka vissa komponenter i Service Bus, till exempel ett meddelandearkiv eller även hela datacentret. När problemet har åtgärdats, aktiveras Service Bus igen. Ett avbrott medför normalt förlorade meddelanden eller andra data. Ett exempel på ett komponentfel är otillgänglig för en viss meddelandearkiv. Ett exempel på ett avbrott i hela datacentret är ett strömavbrott i datacentret eller en felaktig datacenter nätverksväxel. Ett avbrott kan vara från ett par minuter till några dagar.

En katastrof har definierats som permanent förlusten av en Service Bus-skalningsenhet eller datacenter. Datacentret kan eller kan inte bli tillgänglig igen. Vanligtvis går en katastrof förlorade vissa eller alla meddelanden eller andra data. Exempel på katastrofer är fire, överbelasta eller jordbävning.

## <a name="current-architecture"></a>Aktuella arkitektur
Service Bus använder flera meddelandearkiv för att lagra meddelanden som skickas till köer eller ämnen. En icke-partitionerad kö eller ämne tilldelas till ett meddelandearkiv. Om den här meddelandearkiv är tillgänglig, misslyckas alla åtgärder på den kö eller ämne.

Alla Service Bus meddelandeentiteter (köer, ämnen, reläer) finns i ett namnområde för tjänsten som är kopplad till ett datacenter. Service Bus har nu stöd [ *geohaveriberedskap* och *georeplikering* ](service-bus-geo-dr.md) på namnområdesnivå.

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Skydda köer och ämnen mot meddelanden store-fel
En icke-partitionerad kö eller ämne tilldelas till ett meddelandearkiv. Om den här meddelandearkiv är tillgänglig, misslyckas alla åtgärder på den kö eller ämne. En partitionerad kö, å andra sidan består av flera fragment. Varje fragment lagras i en annan meddelandearkiv. När ett meddelande skickas till en partitionerad kö eller ett ämne tilldelar Service Bus meddelandet till en av fragment. Om motsvarande meddelandearkivet är tillgänglig, skriver Service Bus meddelandet till en annan fragment om möjligt. Partitionerade enheter stöds inte längre i den [Premium-SKU](service-bus-premium-messaging.md). 

Mer information om partitionerade enheter finns i [partitionerade meddelandeentiteter][Partitioned messaging entities].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Skydda mot datacenter-avbrott och katastrofer
För att möjliggöra växling mellan två datacenter, kan du skapa ett namnområde för Service Bus-tjänsten i varje datacenter. Till exempel namnområde för Service Bus tjänsten **contosoPrimary.servicebus.windows.net** kanske finns i regionen Nord/centrala USA och **contosoSecondary.servicebus.windows.net**kanske finns i regionen USA/Central Syd. Om en Service Bus-meddelandefunktionen entiteten måste vara tillgänglig när det finns ett avbrott på datacentret, kan du skapa entiteten i båda namnområden.

Mer information finns i avsnittet ”fel i Service Bus inom en Azure-datacenter” i [asynkron meddelandehantering mönster och hög tillgänglighet][Asynchronous messaging patterns and high availability].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Skyddar relay slutpunkter mot datacenter-avbrott och katastrofer
GEO-replikering för relay-slutpunkter kan en tjänst som Exponerar en relay-slutpunkt för att kunna nås när det finns Service Bus-avbrott. För att uppnå geo-replikering kan måste tjänsten skapa två relay-slutpunkter i olika namnområden. Namnområden måste finnas i olika datacenter och två slutpunkter måste ha olika namn. Till exempel en primära slutpunkten kan nås **contosoPrimary.servicebus.windows.net/myPrimaryService**, medan motparten sekundära kan nås **contosoSecondary.servicebus.windows.net /mySecondaryService**.

Sedan lyssnar tjänsten på båda slutpunkterna och en klient kan anropa tjänsten via antingen slutpunkt. Ett klientprogram slumpmässigt tar en av reläer som den primära slutpunkten och skickar begäran till aktiv slutpunkt. Om åtgärden misslyckas med felkoden, betyder ett misslyckande att relay slutpunkten inte är tillgänglig. Programmet öppnar en kanal till slutpunkten för säkerhetskopiering och kör begäran. Då aktivt och säkerhetskopiering slutpunkterna växla roll: klientprogrammet tar hänsyn till den gamla aktiv slutpunkten att vara den nya säkerhetskopiering slutpunkten och den gamla säkerhetskopiering slutpunkten ska vara den nya aktiva slutpunkten. Om både skicka åtgärder misslyckas rollerna för de två entiteterna förblir oförändrade och returneras ett fel.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Skydda köer och ämnen mot datacenter-avbrott och katastrofer
För att uppnå elasticitet mot datacenter-avbrott vid med asynkron meddelandetjänst, Service Bus stöder två metoder: *active* och *passiva* replikering. För varje metod, om en viss kö eller ämne måste vara tillgänglig när det finns ett avbrott på datacentret, kan du skapa den i både namnområden. Båda entiteter kan ha samma namn. Till exempel en primära kön kan nås **contosoPrimary.servicebus.windows.net/myQueue**, medan motparten sekundära kan nås **contosoSecondary.servicebus.windows.net/myQueue**.

Om programmet inte kräver permanent avsändaren att mottagaren kommunikation kan programmet implementera en beständig klientsidan kö att förhindra förlust av meddelandet och skärma av att avsändaren inte eventuella tillfälliga fel i Service Bus.

## <a name="active-replication"></a>Aktiv replikering
Aktiv replikering använder entiteter i båda namnrymderna för varje åtgärd. Alla klienter som skickar ett meddelande skickar två kopior av samma meddelande. Den första kopian skickas till den primära entiteten (till exempel **contosoPrimary.servicebus.windows.net/sales**), och den andra kopian av meddelandet skickas till den sekundära entiteten (till exempel  **contosoSecondary.servicebus.windows.net/sales**).

En klient tar emot meddelanden från köer med båda. Mottagaren kan bearbeta den första kopian av ett meddelande och den andra kopian undertrycks. Om du inte dubbletter av meddelanden, måste avsändaren tagga varje meddelande med en unik identifierare. Båda kopiorna av meddelandet måste vara taggad med samma identifierare. Du kan använda den [BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] eller [BrokeredMessage.Label] [ BrokeredMessage.Label] egenskaper eller en anpassad egenskap att tagga meddelandet. Mottagaren måste ha en lista med meddelanden som redan har tagit emot.

Den [Geo-replikering med asynkrona meddelanden i Service Bus] [ Geo-replication with Service Bus Brokered Messages] exempel visar aktiv replikering av meddelandeentiteter.

> [!NOTE]
> Metoden som aktiv replikering fördubblar antalet åtgärder, därför den här metoden kan leda till högre kostnad.
> 
> 

## <a name="passive-replication"></a>Passiv replikering
I fall felfri använder passiva replikering bara en av de två meddelandeentiteter. En klient skickar meddelandet till den aktiva entiteten. Om åtgärden på den aktiva entiteten misslyckas med en felkod som anger det datacenter som är värd för den aktiva enheten kan vara otillgänglig, skickar klienten en kopia av meddelandet till entiteten säkerhetskopiering. Då aktivt och Säkerhetskopieringens entiteter växla roll: skickar klienten betraktar gamla active entiteten ska vara den nya entiteten för säkerhetskopiering och gamla säkerhetskopiering entiteten är den nya aktiva entiteten. Om både skicka åtgärder misslyckas rollerna för de två entiteterna förblir oförändrade och returneras ett fel.

En klient tar emot meddelanden från köer med båda. Eftersom det finns en risk att mottagaren får två kopior av samma meddelande, måste mottagaren inte dubbletter av meddelanden. Du kan förhindra dubbletter på samma sätt som beskrivs för aktiv replikering.

I allmänhet är passiva replikeringen mer ekonomiskt än aktiv replikering eftersom endast en åtgärd utförs i de flesta fall. Svarstid, dataflöde och kostnaden är identiska med icke-replikerade-scenario.

När du använder passiva replikering, i följande scenarier kan meddelanden vara tappas bort eller tas emot två gånger:

* **Meddeladefördröjning eller förlust**: förutsätter att avsändaren har skickat ett meddelande m1 till den primära kön och sedan kön blir otillgänglig innan mottagaren tar emot m1. Avsändaren skickar ett statusmeddelande m2 till den sekundära kön. Om den primära kön är inte tillgänglig för tillfället, får mottagaren m1 när kön blir tillgänglig igen. Vid en katastrof får mottagaren m1.
* **Duplicera mottagningen**: Anta att avsändaren skickar ett meddelande m till den primära kön. Service Bus har bearbetar m men misslyckas med att skicka ett svar. När åtgärden Skicka tidsgränsen skickar avsändaren en identisk kopia av m till den sekundära kön. Om mottagaren är kan ta emot den första kopian av m innan den primära kön blir otillgänglig, får mottagaren bägge m vid ungefär samma tillfälle. Om mottagaren inte kan ta emot den första kopian av m innan den primära kön blir otillgänglig, mottagaren får inledningsvis endast den andra kopian av m, men de tar emot en andra kopia av m när den primära kön blir tillgänglig.

Den [Geo-replikering med Service Bus om asynkrona meddelanden] [ Geo-replication with Service Bus Brokered Messages] exempel visar passiva replikering av meddelandeentiteter.

## <a name="geo-replication"></a>Geo-replikering

Service Bus stöder Geo-haveriberedskap och Geo-replikering på namnområdesnivå. Mer information finns i [Azure Service Bus geohaveriberedskap](service-bus-geo-dr.md). Disaster recovery funktionen tillgänglig för den [Premium-SKU](service-bus-premium-messaging.md) , implementerar metadata katastrofåterställning och förlitar sig på primära och sekundära disaster recovery-namnområden.

## <a name="availability-zones-preview"></a>Tillgänglighetszoner (förhandsversion)

Har stöd för Service Bus Premium-SKU [Tillgänglighetszoner](../availability-zones/az-overview.md), vilket ger felisolerade platser inom en Azure-region. 

> [!NOTE]
> Förhandsversionen av Tillgänglighetszoner stöds bara i den **centrala USA**, **östra USA 2**, och **Frankrike, centrala** regioner.

Du kan aktivera Tillgänglighetszoner på nya namnområden, med hjälp av Azure portal. Service Bus stöder inte migreringen av befintliga namnområden. Du kan inte inaktivera redundans när du har aktiverat i namnområdet.

![1][]

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om katastrofåterställning kan du läsa följande artiklar:

* [Azure Service Bus Geo-haveriberedskap](service-bus-geo-dr.md)
* [Azure SQL Database-affärskontinuitet][Azure SQL Database Business Continuity]
* [Designa program med återhämtningsförmåga för Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png