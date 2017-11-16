---
title: "Vanliga frågor och svar om Azure Service Bus | Microsoft Docs"
description: "Besvarar några vanliga frågor om Azure Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: cc75786d-3448-4f79-9fec-eef56c0027ba
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: sethm
ms.openlocfilehash: e64e7d9f203debe19dfa222f501c7902cfe2ae98
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="service-bus-faq"></a>Vanliga frågor och svar om Service Bus
Den här artikeln beskrivs några vanliga frågor om Microsoft Azure Service Bus. Du kan också besöka den [Azure stöder vanliga frågor och svar](http://go.microsoft.com/fwlink/?LinkID=185083) allmän Azure priser och support information.

## <a name="general-questions-about-azure-service-bus"></a>Allmänna frågor om Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Vad är Azure Service Bus?
[Azure Service Bus](service-bus-messaging-overview.md) är en asynkron meddelandetjänst molnplattform som gör det möjligt att skicka data mellan frikopplad system. Microsoft erbjuder den här funktionen som en tjänst, vilket innebär att du inte behöver någon egen maskinvara vara värd för att kunna använda den.

### <a name="what-is-a-service-bus-namespace"></a>Vad är en Service Bus-namnrymd?
En [namnområde](service-bus-create-namespace-portal.md) innehåller en omfattningsbehållare för adressering av Service Bus-resurser i ditt program. Skapa ett namnområde krävs för att använda Service Bus och är en av de första stegen i komma igång.

### <a name="what-is-an-azure-service-bus-queue"></a>Vad är en Azure Service Bus-kö?
En [Service Bus-kö](service-bus-queues-topics-subscriptions.md) är en entitet som meddelanden lagras. Köer är användbara när du har flera program eller flera delar av ett distribuerat program som behöver kommunicera med varandra. Kön liknar ett distributionscenter i att flera produkter (meddelanden) tas emot och skickas sedan från den platsen.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Vad är Azure Service Bus-ämnen och prenumerationer?
Ett ämne kan vara visualiseras som en kö och när du använder flera prenumerationer, blir den en rikare meddelanden modellen. i grunden en en-till-många-kommunikationsverktyg. Den här modellen för Publicera/prenumerera (eller *pub/sub*) aktiverar ett program som skickar ett meddelande till ett ämne med flera prenumerationer har meddelandet tas emot av flera program.

### <a name="what-is-a-partitioned-entity"></a>Vad är en partitionerad enhet?
En vanlig kö eller ett ämne hanteras av en enda meddelande broker och lagras i ett meddelandearkiv. En [partitionerade kö eller ett ämne](service-bus-partitioning.md) hanteras av flera meddelandet mäklare och lagras i flera meddelandearkiv. Det innebär att det totala genomflödet i en partitionerad kö eller ett ämne begränsas inte längre av prestanda i ett enda meddelande broker eller meddelandearkiv. Dessutom kan återges ett tillfälligt avbrott i ett meddelandearkiv inte en partitionerad kö eller ett ämne inte tillgänglig.

Observera att beställa inte säkerställs när du använder partitionerade enheter. I händelse av att en partition är tillgänglig, kan du fortfarande skicka och ta emot meddelanden från andra partitioner.

## <a name="best-practices"></a>Bästa praxis
### <a name="what-are-some-azure-service-bus-best-practices"></a>Vad är Azure Service Bus Metodtips?
Se [bästa praxis för bättre prestanda med hjälp av Service Bus] [ Best practices for performance improvements using Service Bus] – den här artikeln beskriver hur du optimerar prestanda när du skickar meddelanden.

### <a name="what-should-i-know-before-creating-entities"></a>Vad bör jag veta innan du skapar enheter?
Följande egenskaper för en kö och avsnittet är oföränderliga. Ta hänsyn till den här begränsningen när du etablerar-enheterna när dessa egenskaper inte kan ändras utan att skapa en ny entitet ersättning.

* Storlek
* Partitionering
* Sessioner
* Identifiering av dubbletter
* Express entitet

## <a name="pricing"></a>Prissättning
Det här avsnittet besvarar några vanliga frågor om Service Bus priser struktur.

Den [Service Bus priser och fakturering](service-bus-pricing-billing.md) artikeln förklarar fakturering mätare i Service Bus. Mer information om Service Bus priser alternativ finns [Service Bus prisinformation](https://azure.microsoft.com/pricing/details/service-bus/).

Du kan också besöka den [Azure svar](http://go.microsoft.com/fwlink/?LinkID=185083) för allmän Azure prisinformation. 

### <a name="how-do-you-charge-for-service-bus"></a>Hur du debiteras för Service Bus?
Fullständig information om priser för Service Bus finns [Service Bus prisinformation][Pricing overview]. Förutom de priser som anges, debiteras du för överföring av associerade data för utgående utanför datacentret där programmet har etablerats.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Vilka användning av Service Bus regleras dataöverföring? Vad är inte?
Alla dataöverföring inom en viss Azure-region tillhandahålls utan kostnad, samt eventuella inkommande dataöverföring. Dataöverföring utanför en region regleras utgång avgifter som du hittar [här](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Debiterar Service Bus för lagring
Nej, Service Bus inte debitera för lagring. Det finns dock en kvot för att begränsa maximal mängd data som kan vara beständiga per kö/avsnittet. Se nästa vanliga frågor och svar.

## <a name="quotas"></a>Kvoter

En lista över Service Bus gränser och kvoter finns i [översikt över Service Bus-kvoter][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Har Service Bus användning kvoter?
Som standard för alla moln anger tjänsten Microsoft en sammanställd månatlig kvot som har beräknats för alla prenumerationer för en kund. Eftersom vi förstår att du behöver mer än dessa gränser, kan du kontakta kundtjänst när som helst så att vi kan förstå dina behov och justera dessa gränser korrekt. För Service Bus är den sammanlagd kvoten 5 miljarder meddelanden per månad.

Medan vi förbehåller sig rätten att inaktivera ett kundkonto som har överskridit sina kvoter för användning i en viss månad, vi ange e-postavisering och göra flera försök att kontakta en kund innan du vidtar någon åtgärd. Kunder som överstiger dessa kvoter är fortfarande ansvarig för avgifter som överskrider kvoter.

Precis som med andra tjänster i Azure tillämpar Service Bus en uppsättning specifika kvoter så att det är verkliga förbrukningen av resurser. Du hittar mer information om dessa kvoter i den [översikt över Service Bus-kvoter][Quotas overview].

## <a name="troubleshooting"></a>Felsökning
### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Vilka är några av de undantag som genereras av Azure Service Bus-API: er och föreslagna åtgärder?
En lista över Service Bus undantag, se [undantag översikt][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Vad är en signatur för delad åtkomst och vilka språk som stöd för generering av en signatur?
Signaturer för delad åtkomst är en autentiseringsmekanism baserat på SHA-256 säker hashvärden eller URI: er. Information om hur du skapar egna signaturer i noden, PHP, Java och C\#, finns det [signaturer för delad åtkomst] [ Shared Access Signatures] artikel.

## <a name="subscription-and-namespace-management"></a>Hantering av prenumerationen och namnområde
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hur Migrera ett namnområde till en annan Azure-prenumeration?

Du kan flytta ett namnområde från en Azure-prenumeration till en annan, med hjälp av antingen den [Azure-portalen](https://portal.azure.com) eller PowerShell-kommandon. För att kunna utföra åtgärden måste namnområdet redan vara aktiv. Användaren köra kommandona måste vara administratör på både käll- och prenumerationer.

#### <a name="portal"></a>Portalen

Följ anvisningarna för att använda Azure-portalen för att migrera Service Bus-namnområden till en annan prenumeration, [här](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Följande sekvens av PowerShell-kommandon flyttar ett namnområde från en Azure-prenumeration till en annan. Om du vill utföra den här åtgärden namnområdet måste redan vara aktivt och användaren som kör PowerShell-kommandon måste vara administratör på både käll- och prenumerationer.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Nästa steg
Mer information om Service Bus finns i följande artiklar:

* [Introduktion till Azure Service Bus Premium (blogginlägg)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introduktion till Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Översikt över Service Bus](service-bus-messaging-overview.md)
* [Översikt av Azure Service Bus-arkitektur](service-bus-fundamentals-hybrid-solutions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
