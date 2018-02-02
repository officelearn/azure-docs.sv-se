---
title: Isolering av Azure Service Bus program mot avbrott och katastrofer | Microsoft Docs
description: "Metoder för att skydda mot en potentiella avbrott i Service Bus-program."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fd9fa8ab-f4c4-43f7-974f-c876df1614d4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 7b01412202b5091ad3ae420089049bf456f9a30b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Metodtips för program mot Service Bus-avbrott och katastrofer isolering

Verksamhetskritiska program måste kontinuerligt, fungerar även med oplanerade avbrott eller katastrofer. Det här avsnittet beskrivs metoder du kan använda för att skydda Service Bus program mot en potentiella avbrott eller katastrof.

Ett avbrott har definierats som temporär otillgänglighet Azure Service Bus. Avbrottet kan påverka vissa komponenter av Service Bus, till exempel ett meddelandearkiv eller även hela datacentret. När problemet har korrigerats, aktiveras Service Bus igen. Normalt orsakar avbrott inte förlorade meddelanden eller andra data. Ett exempel på ett komponentfel är att en viss meddelandearkiv. Ett exempel på ett datacenter hela avbrott är strömavbrott i datacentret, eller en felaktig datacenter nätverksväxel. Ett avbrott kan sista från några minuter till några dagar.

En katastrof definieras som en Service Bus-skalningsenhet eller datacenter permanent förlorade. Datacenter kan eller kan inte bli tillgänglig igen. Vanligtvis förlorar en katastrof åtkomsten till vissa eller alla meddelanden eller andra data. Exempel på katastrofer är brand, överbelasta eller jordbävning.

## <a name="current-architecture"></a>Aktuella arkitektur
Service Bus använder flera meddelandearkiv för att lagra meddelanden som skickas till köer och ämnen. En icke-partitionerat kö eller ett ämne tilldelas till ett meddelandearkiv. Om den här meddelandearkiv är tillgänglig, misslyckas alla åtgärder på den kö eller ett ämne.

Alla Service Bus meddelandeentiteter (köer, ämnen, reläer) finns i ett namnområde för tjänsten som är kopplad till ett datacenter. Stöd för Service Bus [ *Geo-återställning* och *georeplikering* ](service-bus-geo-dr.md) på namnområdesnivån.

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Skydda köer och ämnen mot messaging store-fel
En icke-partitionerat kö eller ett ämne tilldelas till ett meddelandearkiv. Om den här meddelandearkiv är tillgänglig, misslyckas alla åtgärder på den kö eller ett ämne. En partitionerad kö å andra sidan består av flera fragment. Varje fragment lagras i en annan meddelandearkiv. När ett meddelande skickas till en partitionerad kö eller ett ämne, tilldelar Service Bus meddelandet till en av fragment. Om motsvarande meddelandearkiv är tillgänglig, skriver Service Bus meddelandet till en annan fragment om möjligt. Mer information om partitionerade enheter finns [partitionerade meddelandeentiteter][Partitioned messaging entities].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Skydd mot datacenteravbrott eller katastrofer
Om du vill tillåta växling mellan två datacenter, kan du skapa ett namnområde för Service Bus-tjänsten i varje datacenter. Till exempel namnområde för Service Bus-tjänsten **contosoPrimary.servicebus.windows.net** kanske finns i USA Central/Nord-region och **contosoSecondary.servicebus.windows.net** kan finnas i oss söder/centrala region. Om en Service Bus-meddelanden entiteten måste vara tillgänglig med en datacenter-avbrott, kan du skapa den entiteten i båda namnområden.

Mer information finns i avsnittet ”fel i Service Bus i ett Azure-datacenter” i [asynkrona meddelanden mönster och hög tillgänglighet][Asynchronous messaging patterns and high availability].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Skyddar relay slutpunkter mot datacenteravbrott eller katastrofer
GEO-replikering relay slutpunkter kan en tjänst som Exponerar en relay-slutpunkt för att kunna nås med Service Bus avbrott. För att uppnå geo-replikering måste tjänsten skapar två relay-slutpunkter i olika namnområden. Namnområden måste finnas i olika datacenter och de två slutpunkterna måste ha olika namn. Till exempel en primär slutpunkten kan nås **contosoPrimary.servicebus.windows.net/myPrimaryService**, medan motparten sekundära kan nås **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Tjänsten lyssnar på båda slutpunkter och en klient kan anropa tjänsten via antingen slutpunkt. Ett klientprogram slumpmässigt hämtar en av reläer som primär slutpunkt och skickar en begäran till den aktiva slutpunkten. Om åtgärden misslyckas med en felkod, anger det här felet relay-slutpunkten inte är tillgänglig. Programmet öppnar en kanal till säkerhetskopierad slutpunkt och kör begäran. Då aktivt och säkerhetskopiering slutpunkter växla roll: klientprogrammet anser gamla aktiv slutpunkt är den nya säkerhetskopierad slutpunkten och gamla säkerhetskopiering slutpunkten ska vara den nya aktiva slutpunkten. Om både skicka operations misslyckas rollerna i två entiteter förblir oförändrade och returneras ett fel.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Skydda köer och ämnen mot datacenteravbrott eller katastrofer
Om du vill uppnå återhämtning mot datacenteravbrott när du använder asynkrona meddelandetjänster, Service Bus stöder två metoder: *active* och *passiva* replikering. För varje metod om en viss kö eller ett ämne måste vara tillgänglig med en datacenter-avbrott, kan du skapa den i både namnområden. Båda entiteter kan ha samma namn. Till exempel en primär kö kan nås **contosoPrimary.servicebus.windows.net/myQueue**, medan motparten sekundära kan nås **contosoSecondary.servicebus.windows.net/myQueue**.

Om programmet inte kräver permanent avsändaren att mottagaren kommunikation kan programmet implementera en beständig klientsidan kö för att förhindra förlust av meddelandet och att skydda avsändaren från Service Bus tillfälligt fel.

## <a name="active-replication"></a>Aktiv replikering
Aktiv replikering använder entiteter i båda namnområdena för varje åtgärd. Alla klienter som skickar ett meddelande skickas två kopior av samma meddelande. Den första kopian skickas till den primära enheten (till exempel **contosoPrimary.servicebus.windows.net/sales**), och den andra kopian av meddelandet skickas till den sekundära enheten (till exempel **contosoSecondary.servicebus.windows.net/sales**).

En klient tar emot meddelanden från både köer. Mottagaren bearbetar den första kopian av ett meddelande och den andra kopian ignoreras. Om du inte dubblerade meddelanden, tagga avsändaren varje meddelande med en unik identifierare. Båda kopiorna av meddelandet måste vara taggade med samma ID. Du kan använda den [BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] eller [BrokeredMessage.Label] [ BrokeredMessage.Label] egenskaper eller en anpassad egenskap att tagga meddelandet. Mottagaren måste upprätthålla en lista med meddelanden som redan har fått.

Den [Geo-replikering med asynkrona meddelanden i Service Bus] [ Geo-replication with Service Bus Brokered Messages] exemplet visar aktiv replikering av meddelandeentiteter.

> [!NOTE]
> Metoden som aktiv replikering fördubblar antalet åtgärder, därför den här metoden kan leda till högre kostnad.
> 
> 

## <a name="passive-replication"></a>Passiva replikering
I fallen som felfri använder passiva replikering endast en av de två meddelandeentiteter. En klient skickar meddelandet till enheten som är aktiva. Om åtgärden på den aktiva entiteten misslyckas och en felkod som anger datacentret som är värd för den aktiva enheten inte kanske är tillgänglig, skickar klienten en kopia av meddelandet till enheten som säkerhetskopiering. Då aktivt och säkerhetskopieringsenheter växla roll: skicka klienten anser att gamla active entiteten ny säkerhetskopiering entitet och gamla säkerhetskopiering entiteten är den nya active entiteten. Om både skicka operations misslyckas rollerna i två entiteter förblir oförändrade och returneras ett fel.

En klient tar emot meddelanden från både köer. Eftersom det finns en risk att mottagaren får två kopior av samma meddelande, mottagaren inte dubbla meddelanden. Du kan förhindra dubbletter på samma sätt som beskrivs för aktiv replikering.

I allmänhet replikeras passiva mer kostnadseffektiv än aktiv replikering eftersom endast en åtgärd utförs i de flesta fall. Svarstid, genomflöde och kostnaden är identiska med det icke-replikerade scenariot.

När du använder passiva replikering, i följande scenarier kan meddelanden tappas bort eller fått två gånger:

* **Meddelandet fördröjning eller förlust**: Anta att avsändaren har skickat ett meddelande m1 till primära kön och sedan kön blir otillgänglig innan mottagaren tar emot m1. Avsändaren skickar ett meddelande m2 till sekundär kö. Om den primära kön är inte tillgänglig för tillfället får mottagaren m1 när kön har blivit tillgänglig igen. Vid en katastrof får mottagaren m1.
* **Duplicera mottagning**: förutsätter att avsändaren skickar ett meddelande m till primära kön. Service Bus bearbetar m men misslyckas med att skicka ett svar. När send-åtgärden på grund av timeout, skickar avsändaren en identisk kopia av m till sekundär kö. Om mottagaren kan ta emot den första kopian av m innan primära kön blir otillgänglig, får mottagaren båda kopiorna av m vid ungefär samma tillfälle. Om mottagaren inte kan ta emot den första kopian av m innan primära kön blir otillgänglig, mottagaren tar emot först den andra kopian av m, men tar emot en andra kopia av m när den primära kön blir tillgänglig.

Den [Geo-replikering med Service Bus asynkrona meddelanden] [ Geo-replication with Service Bus Brokered Messages] exemplet visar passiva replikering av meddelandeentiteter.

## <a name="geo-replication"></a>Geo-replikering

Service Bus stöder Geo-återställning och Geo-replikering på namnområdesnivån. Mer information finns i [Azure Service Bus Geo-återställning](service-bus-geo-dr.md). Disaster recovery funktionen, tillgänglig för den [Premium-SKU](service-bus-premium-messaging.md) endast implementerar metadata katastrofåterställning och bygger på primära och sekundära disaster recovery-namnområden.

## <a name="next-steps"></a>Nästa steg
Mer information om återställning finns i följande artiklar:

* [Azure Service Bus Geo-katastrofåterställning](service-bus-geo-dr.md)
* [Företagskontinuitet för Azure SQL-databas][Azure SQL Database Business Continuity]
* [Utforma flexibel program för Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency
