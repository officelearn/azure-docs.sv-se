---
title: Azure Relay vanliga frågor och svar | Microsoft Docs
description: Få svar på vanliga frågor om Azure Relay.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: sethm
ms.openlocfilehash: 1795c1ec0b4129e3c99fff3fc893148e191ce83e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641500"
---
# <a name="azure-relay-faqs"></a>Azure Relay vanliga frågor och svar

Den här artikeln besvarar några vanliga frågor (FAQ) om [Azure Relay](https://azure.microsoft.com/services/service-bus/). Allmän Azure priser och support information finns i [Azure stöder vanliga frågor och svar](https://azure.microsoft.com/support/faq/).

## <a name="general-questions"></a>Allmänna frågor
### <a name="what-is-azure-relay"></a>Vad är Azure Relay?
Den [Azure vidarebefordrande tjänsten](relay-what-is-it.md) underlättar för dina hybridprogram genom att hjälpa dig mer på ett säkert sätt exponera tjänster som finns i ett företagsnätverk mot det offentliga molnet. Du kan använda tjänsterna utan att öppna en brandväggsanslutning eller kräva störande ändringar i företagets nätverksinfrastruktur.

### <a name="what-is-a-relay-namespace"></a>Vad är ett namnområde för Relay?
En [namnområde](relay-create-namespace-portal.md) är en omfattningsbehållare som du kan använda för att adressera Relay resurser i ditt program. Du måste skapa ett namnområde för att använda Relay. Detta är en av de första stegen i komma igång.

### <a name="what-happened-to-service-bus-relay-service"></a>Vad hände med Service Bus Relay-tjänsten?
Tidigare Service Bus Relay tjänsten kallas nu [vidarebefordrande WCF](relay-wcf-dotnet-get-started.md). Du kan fortsätta att använda den här tjänsten som vanligt. Hybridanslutningar-funktionen är en uppdaterad version av en tjänst som har varit transplanteras från Azure BizTalk-tjänst. Vidarebefordrande WCF och Hybridanslutningar fortsätta att stödjas.

## <a name="pricing"></a>Prissättning
Det här avsnittet besvarar några vanliga frågor om Relay priser struktur. Du kan också se den [Azure svar](https://azure.microsoft.com/support/faq/) för allmän Azure prisinformation. Fullständig information om priser Relay finns [Service Bus prisinformation][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Hur du debiteras för Hybridanslutningar och WCF Relay?
Fullständig information om Relay priser finns i [Hybridanslutningar och WCF-reläer] [ Pricing overview] tabellen på Service Bus informationssida med priser. Förutom de priser som anges på sidan, debiteras du för överföring av associerade data för utgående utanför datacentret där programmet har etablerats.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Hur är debiteras för Hybridanslutningar?
Här följer tre fakturering exempelscenarier för Hybridanslutningar:

*   Scenario 1:
    *   Du har en enda lyssnare, till exempel en instans av Hybrid anslutningar Manager installerad och körs kontinuerligt för hela månaden.
    *   Du kan skicka 3 GB data över anslutningen under månaden. 
    *   Din totala är $5.
*   Scenario 2:
    *   Du har en enda lyssnare, till exempel en instans av Hybrid anslutningar Manager installerad och körs kontinuerligt för hela månaden.
    *   Du kan skicka 10 GB data över anslutningen under månaden.
    *   Din totala är $7,50. Det är $5 för anslutning och första 5 GB + $2,50 för ytterligare 5 GB data.
*   Scenario 3:
    *   Du har två instanser A och B av Hybrid anslutningar Manager installerad och körs kontinuerligt för hela månaden.
    *   Du kan skicka 3 GB data via en under månaden.
    *   Du kan skicka 6 GB data via anslutning B under månaden.
    *   Din totala är $10,50. Det är $5 för anslutning A + $5 för anslutning B + 0,50 $ (för sjätte gigabyte på anslutning B).

Observera att de priser som används i exemplen kan bara användas under förhandsversionen Hybridanslutningar. De priser som kan ändras vid allmän tillgänglighet för Hybridanslutningar.

### <a name="how-are-hours-calculated-for-relay"></a>Hur beräknas timmar för Relay?

Vidarebefordrande WCF är endast tillgänglig i standardnivån namnområden. Priser och [anslutning kvoter](../service-bus-messaging/service-bus-quotas.md) för reläer annars inte har ändrats. Detta innebär att reläer fortsätter att debiteras baserat på antalet meddelanden (inte operations) och relätimmar. Mer information finns i [”Hybrid anslutningar och WCF reläer”](https://azure.microsoft.com/pricing/details/service-bus/) tabellen på sidan prisnivå information.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Vad händer om jag har fler än en lyssnare som är ansluten till en specifik vidarebefordran?
I vissa fall kan kan en enda relay ha flera anslutna lyssnare. Öppna betraktas som ett relä när minst en relay-lyssnaren är ansluten till den. Lägga till lyssnare i ett öppet relay resulterar i ytterligare relätimmar. Antalet relay avsändare (klienter som invoke eller skicka meddelanden till reläer) som är anslutna till ett relä påverkar inte beräkningen av relätimmar.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hur beräknas meddelanden mätaren för WCF-reläer?
(**Detta gäller endast för WCF-reläer. Meddelanden är inte en kostnad för Hybridanslutningar.** )

I allmänhet beräknas fakturerbar för reläer genom att använda samma metod som används för asynkrona entiteter (köer, ämnen och prenumerationer), som beskrivs ovan. Det finns dock några viktiga skillnader.

Skicka ett meddelande till en Service Bus relay behandlas som en ”full via” skicka till relay-lyssnaren som tar emot meddelandet. Den behandlas inte som en send-åtgärd till Service Bus relay, följt av en leverans till relay-lyssnaren. En request-reply style-tjänsten anropa (upp till 64 KB) mot ett relä lyssnare resultatet i två fakturerbar meddelanden: en fakturerbar meddelande för begäran och en fakturerbar meddelandet för svaret (förutsatt att svaret är också 64 KB eller mindre). Detta skiljer sig med hjälp av en kö för att förmedla mellan en klient och en tjänst. Om du använder en kö för att förmedla mellan en klient och en tjänst, kräver samma request-reply-mönster en begäran skickas till kön, följt av dequeue/leverans från kön till tjänsten. Detta följs av en skicka svar till en annan kö och en dequeue/leverans från kön till klienten. Mönstret genom kö resulterar med samma storlek antaganden i hela (upp till 64 KB) i 4 fakturerbar meddelanden. Faktureras för dubbelt så många meddelanden för att genomföra samma mönster som du skapade med relay. Naturligtvis finns fördelar med att använda köer för att uppnå detta mönster, till exempel hållbarhet och belastningsutjämning. Dessa fördelar kan motiverar den extra kostnaden.

Reläer som öppnas med hjälp av den **netTCPRelay** WCF bindning behandlar meddelanden inte som enskilda meddelanden, men som en dataström med data som flödar genom systemet. När du använder den här bindningen har bara avsändaren och lyssnare insyn i fastställandet av de enskilda meddelanden skickas och tas emot. För vidarebefordrar som använder den **netTCPRelay** bindning, behandlas alla data som en dataström för att beräkna fakturerbar meddelanden. I det här fallet beräknar Service Bus den totala mängden data som skickas eller tas emot via varje enskild relay på grundval av 5 minuter. Sedan dividerar den den totala mängden data med 64 KB att fastställa antalet fakturerbar meddelanden för att relay under den tidsperioden.

## <a name="quotas"></a>Kvoter
| Kvoten namn | Omfång |  Anteckningar | Värde |
| --- | --- | --- | --- |
| Samtidiga lyssnare på ett relä |Entitet |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |25 |
| Samtidiga relay-anslutningar per alla relay-slutpunkterna i ett namnområde för tjänsten |Namnrymd |- |5 000 |
| Relay slutpunkter per namnområde för tjänsten |Namnrymd |- |10 000 |
| Meddelandestorlek för [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) och [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) vidarebefordrar |Namnrymd |Inkommande meddelanden som överskrider dessa kvoter avvisas och ett undantag tas emot av den anropande koden. |64 kB |
| Meddelandestorlek för [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) och [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) vidarebefordrar |Namnrymd |Ingen gräns meddelandestorlek. |Obegränsat |

### <a name="does-relay-have-any-usage-quotas"></a>Har Relay användning kvoter?
Som standard för alla Molntjänsten anger Microsoft en sammanställd månatlig kvot som har beräknats för alla prenumerationer för en kund. Vi förstår att dina behov ibland kanske överskrider gränserna. Du kan kontakta kundtjänst när som helst, så att vi kan förstå dina behov och justera dessa gränser korrekt. För Service Bus är sammanställd användning kvoter följande:

* 5 miljarder meddelanden
* 2 miljoner relätimmar

Även om vi förbehåller sig rätten att inaktivera ett konto som överskrider dess månadens kvot för användning, vi tillhandahåller e-postavisering och vi göra flera försöker att kontakta kunden innan du vidtar någon åtgärd. Kunder som överskrider dessa kvoter är fortfarande ansvarig för överdriven avgifter.

### <a name="naming-restrictions"></a>Namngivningsbegränsningar
Ett namn för namnområdet Relay måste vara mellan 6 och 50 tecken.

## <a name="subscription-and-namespace-management"></a>Hantering av prenumerationen och namnområde
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hur Migrera ett namnområde till en annan Azure-prenumeration?

Om du vill flytta ett namnområde från en Azure-prenumeration till en annan prenumeration, du kan använda den [Azure-portalen](https://portal.azure.com) eller använda PowerShell-kommandon. Om du vill flytta en namnområdet till en annan prenumeration måste namnområdet redan vara aktiva. Användaren som kör kommandona måste vara en administratör på både käll- och prenumerationer.

#### <a name="azure-portal"></a>Azure Portal

Om du vill använda Azure-portalen för att migrera Azure Relay-namnområden från en prenumeration till en annan prenumeration, se [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Om du vill använda PowerShell för att flytta ett namnområde från en Azure-prenumeration till en annan prenumeration, använder du följande kommandosekvens. Om du vill utföra den här åtgärden namnområdet måste redan vara aktivt och användaren som kör PowerShell-kommandon måste vara en administratörsanvändare på både käll- och prenumerationer.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Felsökning
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Vad är några av undantag som genereras av Azure Relay API: er och föreslagna åtgärder du kan vidta?
En beskrivning av vanliga undantag och föreslagna åtgärder du kan vidta finns [Relay undantag][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Vad är en signatur för delad åtkomst och vilka språk kan jag använda för att generera en signatur?
Delad åtkomst signaturer (SAS) är en autentiseringsmekanism baserat på SHA-256 säker hashvärden eller URI: er. Information om hur du skapar egna signaturer i noden, PHP, Java, C och C# finns [Service Bus-autentisering med signaturer för delad åtkomst][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Är det möjligt att godkända relay slutpunkter?
Ja. Relay-klienten skickar anslutningar till tjänsten Azure Relay med fullständigt kvalificerade domännamn. Kunder kan lägga till en post för `*.servicebus.windows.net` på brandväggar som stöder vitlistning av DNS.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
