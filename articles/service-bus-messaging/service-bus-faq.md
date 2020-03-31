---
title: Vanliga frågor och svar om Azure Service Bus (FAQ) | Microsoft-dokument
description: Den här artikeln innehåller svar på några av de vanliga frågorna om Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3cd4e69481fb452391e6dc027cb41fd6dae71b7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760257"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus - Vanliga frågor och svar (Vanliga frågor och svar)

I den här artikeln beskrivs några vanliga frågor om Microsoft Azure Service Bus. Du kan också besöka vanliga frågor om [Azure-support](https://azure.microsoft.com/support/faq/) för allmän Azure-priser och supportinformation.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Allmänna frågor om Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Vad är Azure Service Bus?
[Azure Service Bus](service-bus-messaging-overview.md) är en asynkron meddelandemolnplattform som gör att du kan skicka data mellan frikopplade system. Microsoft erbjuder den här funktionen som en tjänst, vilket innebär att du inte behöver vara värd för din egen maskinvara för att kunna använda den.

### <a name="what-is-a-service-bus-namespace"></a>Vad är ett servicebussnamnområde?
Ett [namnområde](service-bus-create-namespace-portal.md) innehåller en behållare för att adressera Service Bus-resurser i ditt program. Det är nödvändigt att skapa ett namnområde för att använda Service Bus och är ett av de första stegen för att komma igång.

### <a name="what-is-an-azure-service-bus-queue"></a>Vad är en Azure Service Bus-kö?
En [Service Bus-kö](service-bus-queues-topics-subscriptions.md) är en entitet där meddelanden lagras. Köer är användbara när du har flera program eller flera delar av ett distribuerat program som behöver kommunicera med varandra. Kön liknar ett distributionscenter i att flera produkter (meddelanden) tas emot och sedan skickas från den platsen.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Vad är Azure Service Bus ämnen och prenumerationer?
Ett ämne kan visualiseras som en kö och när du använder flera prenumerationer blir det en rikare meddelandemodell. i huvudsak ett kommunikationsverktyg för en till många. Den här publicera/prenumerera modellen (eller *pub / sub)* gör det möjligt för ett program som skickar ett meddelande till ett ämne med flera prenumerationer för att få det meddelandet tas emot av flera program.

### <a name="what-is-a-partitioned-entity"></a>Vad är en partitionerad entitet?
En vanlig kö eller ett vanligt ämne hanteras av en enda meddelandemäklare och lagras i ett meddelandearkiv. Stöds endast i basic- och standardmeddelandenivåerna, en [partitionerad kö eller ett ämne](service-bus-partitioning.md) hanteras av flera meddelandemäklare och lagras i flera meddelandearkiv. Den här funktionen innebär att det övergripande dataflödet för en partitionerad kö eller ett ämne inte längre begränsas av prestanda för en enda meddelandemäklare eller meddelandebutik. Dessutom gör ett tillfälligt avbrott i ett meddelandearkiv inte en partitionerad kö eller ett ämne otillgängligt.

Beställning säkerställs inte när du använder partitionerade entiteter. Om en partition inte är tillgänglig kan du fortfarande skicka och ta emot meddelanden från de andra partitionerna.

 Partitionerade entiteter stöds inte längre i [Premium SKU](service-bus-premium-messaging.md). 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Vilka portar behöver jag för att öppna i brandväggen? 
Du kan använda följande protokoll med Azure Service Bus för att skicka och ta emot meddelanden:

- Advanced Message Queuing Protocol (AMQP)
- Service Bus Messaging Protocol (SBMP)
- HTTP

Se följande tabell för de utgående portar som du behöver öppna för att använda dessa protokoll för att kommunicera med Azure Event Hubs. 

| Protokoll | Portar | Information | 
| -------- | ----- | ------- | 
| AMQP | 5671 och 5672 | Se [AMQP-protokollguide](service-bus-amqp-protocol-guide.md) | 
| SBMP (på andra sätt) | 9350 till 9354 | Se [anslutningsläge](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Vilka IP-adresser behöver jag för att vitlista?
Så här hittar du rätt IP-adresser till den vita listan för dina anslutningar:

1. Kör följande kommando från en kommandotolk: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Notera den IP-adress `Non-authoritative answer`som returneras i . Den här IP-adressen är statisk. Den enda tidpunkt det skulle ändras är om du återställer namnområdet till ett annat kluster.

Om du använder zonredundansen för ditt namnområde måste du göra några ytterligare steg: 

1. Först kör du nslookup på namnområdet.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anteckna namnet i avsnittet **icke-auktoritärt svar,** som är i något av följande format: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Kör nslookup för var och en med suffix s1, s2 och s3 för att hämta IP-adresserna för alla tre instanser som körs i tre tillgänglighetszoner, 


## <a name="best-practices"></a>Bästa praxis
### <a name="what-are-some-azure-service-bus-best-practices"></a>Vilka är några metodtips för Azure Service Bus?
Se [Metodtips för prestandaförbättringar med Service Bus][Best practices for performance improvements using Service Bus] – i den här artikeln beskrivs hur du optimerar prestanda vid utbyte av meddelanden.

### <a name="what-should-i-know-before-creating-entities"></a>Vad bör jag veta innan jag skapar entiteter?
Följande egenskaper för en kö och ett ämne är oföränderliga. Tänk på den här begränsningen när du etablerar dina entiteter, eftersom dessa egenskaper inte kan ändras utan att skapa en ny ersättningsenhet.

* Partitionering
* Sessioner
* Dubblettidentifiering
* Expressenhet

## <a name="pricing"></a>Prissättning
I det här avsnittet får du svar på några vanliga frågor om servicebussprisstrukturen.

I [servicebussens pris- och faktureringsartikel](https://azure.microsoft.com/pricing/details/service-bus/) beskrivs faktureringsmätarena i Service Bus. Specifik information om servicebussprisalternativ finns i [Service Bus-prisinformation](https://azure.microsoft.com/pricing/details/service-bus/).

Du kan också besöka vanliga frågor om [Azure-support](https://azure.microsoft.com/support/faq/) för allmän Azure-prisinformation. 

### <a name="how-do-you-charge-for-service-bus"></a>Hur tar du betalt för Service Bus?
Fullständig information om servicebusspriser finns i [Service Bus-prisinformation][Pricing overview]. Utöver de priser som anges debiteras du för associerade dataöverföringar för utgående utanför det datacenter där ditt program etableras.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Vilken användning av Service Bus är föremål för dataöverföring? Vad är inte det?
All dataöverföring inom en viss Azure-region tillhandahålls utan kostnad, liksom eventuell inkommande dataöverföring. Dataöverföring utanför en region är föremål för utgående avgifter, som finns [här](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Tar Service Bus betalt för lagring?
Nej, Service Bus tar inte betalt för lagring. Det finns dock en kvot som begränsar den maximala mängden data som kan sparas per kö/ämne. Se nästa vanliga frågor.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Jag har ett servicebussstandardnamnområde. Varför ser jag avgifter under resursgruppen "$system"?
Azure Service Bus har nyligen uppgraderat faktureringskomponenterna. Om du har ett servicebussstandardnamnområde kan du därför se radartiklar för resursen "/subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system" under resursgruppen $system.

Dessa avgifter representerar basavgiften per Azure-prenumeration som har etablerat ett Service Bus Standard-namnområde. 

Det är viktigt att notera att dessa inte är nya avgifter, dvs de fanns i den tidigare faktureringsmodellen också. Den enda förändringen är att de nu är listade under "$system". Detta görs på grund av contraints i det nya faktureringssystemet som grupperar avgifter för prenumerationsnivå, inte kopplade till en viss resurs, under resurs-ID :et "$system".

## <a name="quotas"></a>Kvoter

En lista över servicebussgränser och kvoter finns i [översikten över servicebusskvoter][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Har Service Bus några användningskvoter?
Som standard anger Microsoft en sammanlagd månatlig användningskvot som beräknas för alla en kunds prenumerationer för alla molntjänster. Om du behöver mer än dessa gränser kan du kontakta kundtjänst när som helst för att förstå dina behov och justera dessa gränser på lämpligt sätt. För Service Bus är den sammanlagda användningskvoten 5 miljarder meddelanden per månad.

Microsoft förbehåller sig rätten att inaktivera ett kundkonto som har överskridit sina användningskvoter under en viss månad, men e-postmeddelanden skickas och flera försök görs att kontakta en kund innan någon åtgärd vidtas. Kunder som överskrider dessa kvoter är fortfarande ansvariga för avgifter som överskrider kvoterna.

Precis som med andra tjänster på Azure tillämpar Service Bus en uppsättning specifika kvoter för att säkerställa att det finns en rättvis användning av resurser. Du hittar mer information om dessa kvoter i [översikten Service Bus-kvoter][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Hur hanterar man meddelanden av storlek > 1 MB?
Service Bus meddelandetjänster (köer och ämnen / prenumerationer) tillåter program för att skicka meddelanden av storlek upp till 256 KB (standardnivå) eller 1 MB (premium nivå). Om du har att göra med meddelanden av storlek större än 1 MB, använd anspråkskontroll mönster som beskrivs i [detta blogginlägg](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Felsökning
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Varför kan jag inte skapa ett namnområde när jag har tagit bort det från en annan prenumeration? 
När du tar bort ett namnområde från en prenumeration väntar du i 4 timmar innan du återskapar det med samma namn i en annan prenumeration. Annars kan följande felmeddelande visas: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Vilka är några av de undantag som genereras av Azure Service Bus API:er och deras föreslagna åtgärder?
En lista över möjliga undantag för Service Bus finns i [Översikt över undantag][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Vad är en signature för delad åtkomst och vilka språk har stöd för att generera en signatur?
Signaturer för delad åtkomst är en autentiseringsmekanism som baseras på SHA-256-säkra hashar eller URI:er. Information om hur du genererar egna signaturer i Node.js, PHP, Java, Python och C#finns i artikeln [Signaturer för delad åtkomst.][Shared Access Signatures]

## <a name="subscription-and-namespace-management"></a>Prenumerations- och namnområdeshantering
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hur migrerar jag ett namnområde till en annan Azure-prenumeration?

Du kan flytta ett namnområde från en Azure-prenumeration till en annan med hjälp av [azure-portalen](https://portal.azure.com) eller PowerShell-kommandona. För att åtgärden ska kunna utföras måste namnområdet redan vara aktivt. Användaren som kör kommandona måste vara administratör för både käll- och målprenumerationer.

#### <a name="portal"></a>Portalen

Om du vill använda Azure-portalen för att migrera Service Bus-namnområden till en annan prenumeration följer du anvisningarna [här](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Följande sekvens av PowerShell-kommandon flyttar ett namnområde från en Azure-prenumeration till en annan. För att kunna utföra den här åtgärden måste namnområdet redan vara aktivt och användaren som kör PowerShell-kommandona måste vara administratör för både käll- och målprenumerationerna.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Nästa steg
Mer information om Service Bus finns i följande artiklar:

* [Introduktion till Azure Service Bus Premium (blogginlägg)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introduktion till Azure Service Bus Premium (Kanal9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Översikt över servicebuss](service-bus-messaging-overview.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
