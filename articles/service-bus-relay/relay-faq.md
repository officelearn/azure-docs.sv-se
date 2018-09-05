---
title: Azure Relay vanliga frågor och svar | Microsoft Docs
description: Få svar på vanliga frågor om Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e30e8c94547ac0f9106a69f1e99cf9a7c03abea5
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43695905"
---
# <a name="azure-relay-faqs"></a>Azure Relay vanliga frågor och svar

Den här artikeln får du svar på några vanliga frågor (FAQ) om [Azure Relay](https://azure.microsoft.com/services/service-bus/). Allmänna Azure prissättning och information finns i den [Azure stöd för vanliga frågor och svar](https://azure.microsoft.com/support/faq/).

## <a name="general-questions"></a>Allmänna frågor
### <a name="what-is-azure-relay"></a>Vad är Azure Relay?
Den [Azure Relay-tjänsten](relay-what-is-it.md) underlättar för dina hybridprogram genom att hjälpa dig mer på ett säkert sätt exponera tjänster som finns i ett företagsnätverk mot det offentliga molnet. Du kan visa tjänsterna utan att behöva öppna en brandväggsanslutning och utan att kräva störande ändringar i företagets nätverksinfrastruktur.

### <a name="what-is-a-relay-namespace"></a>Vad är ett Relay-namnområde?
En [namnområde](relay-create-namespace-portal.md) är en gemensam behållare som du kan använda för att adressera Relay-resurser i ditt program. Du måste skapa ett namnområde för att använda vidarebefordran. Detta är en av de första stegen i komma igång.

### <a name="what-happened-to-service-bus-relay-service"></a>Vad hände med Service Bus Relay-tjänsten?
Tidigare kallad Service Bus Relay-tjänsten kallas nu [WCF Relay](relay-wcf-dotnet-get-started.md). Du kan fortsätta att använda den här tjänsten som vanligt. Funktionen Hybrid Connections är en uppdaterad version av en tjänst som har varit transplanteras från Azure BizTalk Services. WCF Relay och Hybridanslutningar fortfarande användas.

## <a name="pricing"></a>Prissättning
Det här avsnittet får du svar på några vanliga frågor och svar om Relay prissättningsstruktur. Du kan också se den [Azure stöd för vanliga frågor och svar](https://azure.microsoft.com/support/faq/) för allmänna Azure information om priser. Fullständig information om priser för Relay finns [Service Bus prisinformation][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Hur tar du betalt för Hybridanslutningar och WCF Relay?
Fullständig information om priser för Relay finns i [Hybridanslutningar och WCF-reläer] [ Pricing overview] tabell på Service Bus sidan med prisinformation. Förutom priserna på sidan, debiteras du för överföring av associerade data för utanför datacentret där programmet har etablerats.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Hur debiteras jag för Hybridanslutningar?
Här följer tre exempel fakturering scenarier för Hybridanslutningar:

*   Scenario 1:
    *   Du har en enda lyssnare, till exempel en instans av Hybrid installerad och köras kontinuerligt under hela månaden.
    *   Du skickar 3 GB data via anslutningen under månaden. 
    *   Den totala avgiften är 5 USD.
*   Scenario 2:
    *   Du har en enda lyssnare, till exempel en instans av Hybrid installerad och köras kontinuerligt under hela månaden.
    *   Du skickar 10 GB data via anslutningen under månaden.
    *   Den totala avgiften är $7,50. Det är 5 USD för anslutningen och första 5 GB + $2.50 för ytterligare 5 GB data.
*   Scenario 3:
    *   Du har två instanser A och B av Hybrid installerad och köras kontinuerligt under hela månaden.
    *   Du skickar 3 GB data via anslutning A under månaden.
    *   Du kan skicka 6 GB data via anslutning b. under månaden.
    *   Den totala avgiften är $10,50. Det är 5 USD för anslutning A + 5 USD för anslutning B + 0,50 (för den sjätte GB på anslutning B).

Observera att priserna i exemplen kan bara användas under förhandsversionsperioden Hybridanslutningar. Priserna kan komma att ändras vid allmän tillgänglighet av Hybridanslutningar.

### <a name="how-are-hours-calculated-for-relay"></a>Hur beräknas timmar för ett relä?

WCF Relay är endast tillgängliga i Standard-nivån namnområden. Priser och [anslutning kvoter](../service-bus-messaging/service-bus-quotas.md) för reläer annars inte har ändrats. Det innebär att fortsätta reläer debiteras baserat på antalet meddelanden (inte åtgärder) och relätimmar. Mer information finns i den [”Hybrid-anslutningar och WCF-reläer”](https://azure.microsoft.com/pricing/details/service-bus/) tabell på sidan med prisinformation.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Vad händer om jag har fler än en lyssnare som är anslutna till en specifik vidarebefordran?
I vissa fall kan kan en enda relay ha flera anslutna lyssnare. Öppna betraktas som ett relä när minst en relälyssnaren är ansluten till den. Lägga till lyssnare i ett öppet relay resulterar i ytterligare relätimmar. Antalet relay avsändare (klienter som anropar eller skicka meddelanden till reläer) som är anslutna till ett relä påverkar inte beräkningen av relätimmar.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hur beräknas meddelanden för WCF-reläer?
(**Detta gäller endast för WCF-reläer. Meddelanden är inte en kostnad för Hybridanslutningar.** )

I allmänhet beräknas faktureringsbara meddelanden för genom att använda samma metod som används för asynkrona entiteter (köer, ämnen och prenumerationer), som beskrivs ovan. Det finns dock några viktiga skillnader.

Skicka ett meddelande till ett Service Bus-relä behandlas som en ”fullständig via” skicka till den relälyssnaren som tar emot meddelandet. Det är inte behandlas som en sändningsåtgärden till Service Bus-relä, följt av en leverans till relälyssnaren. En begäran / svar-stil tjänstanrop (på upp till 64 KB) mot ett relä lyssnare resultatet i två faktureringsbara meddelanden: ett faktureringsbart meddelande för begäran och ett faktureringsbart meddelande för svaret (förutsatt att svaret är också 64 KB eller mindre). Detta skiljer sig från att använda en kö för att hantera mellan en klient och en tjänst. Om du använder en kö för att hantera mellan en klient och en tjänst, kräver att skicka begäran till kön, följt av en ta/leverans från kön till tjänsten i samma begäran / svar-mönster. Detta följs av skicka svar till en annan kö och en ta/leverans från kön till klienten. Med hjälp av samma storlek antaganden i hela (upp till 64 KB), genom kö-mönster som leder 4 faktureringsbara meddelanden. Debiteras du för två gånger antalet meddelanden att implementera samma mönster som du skapade med relay. Det finns självklart fördelar med att använda köer för att uppnå det här mönstret, till exempel hållbarhet och belastningsutjämning. Dessa fördelar kan motivera den extra kostnaden.

Reläer som öppnas med hjälp av den **netTCPRelay** WCF-bindning behandlar meddelanden inte som enskilda meddelanden, men som en dataström med data som flödar genom systemet. När du använder den här bindningen har endast avsändaren och lyssnare insyn i synkroniseringstecken enskilda meddelanden som skickas och tas emot. För vidarebefordrar som använder den **netTCPRelay** bindning, behandlas alla data som en dataström för att beräkna faktureringsbara meddelanden. I det här fallet beräknar Service Bus den totala mängden data som skickas eller tas emot via varje enskild relay på basis av 5 minuter. Sedan dividerar den den totala mängden data med 64 KB att fastställa antalet faktureringsbara meddelanden för relay under den tidsperioden.

## <a name="quotas"></a>Kvoter
| Kvotnamn | Omfång |  Anteckningar | Värde |
| --- | --- | --- | --- |
| Samtidiga lyssnare på en relay |Entitet |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |25 |
| Samtidiga relay-anslutningar per alla relay-slutpunkterna i ett namnområde för tjänsten |Namnrymd |- |5 000 |
| Relay slutpunkter per namnområde för tjänsten |Namnrymd |- |10 000 |
| Meddelandestorlek för [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) och [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) vidarebefordrar |Namnrymd |Inkommande meddelanden som överskrider kvoterna avvisas och ett undantag tas emot av den anropande koden. |64 kB |
| Meddelandestorlek för [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) och [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) vidarebefordrar |Namnrymd |Ingen gräns meddelandestorlek. |Obegränsat |

### <a name="does-relay-have-any-usage-quotas"></a>Finns det några användningskvoter Relay?
Som standard för valfri molntjänst anger Microsoft en sammanställd månatliga kvot för användning som beräknas för alla prenumerationer för en kund. Vi förstår att dina behov ibland kanske överskrider gränserna. Du kan kontakta kundtjänst när som helst, så att vi får veta vad du behöver och kan justera gränserna på rätt sätt. För Service Bus är de sammanlagda användningskvoter följande:

* 5 miljarder meddelanden
* 2 miljoner relätimmar

Även om vi förbehåller oss rätten att inaktivera ett konto som överskrider dess förbrukningskvoter vi ger e-postavisering och vi göra flera försöker att kontakta kunden innan du vidtar åtgärder. Kunder som överskrider kvoterna är fortfarande ansvarig för onödiga avgifter.

### <a name="naming-restrictions"></a>Namngivningsbegränsningar
Ett namn för Relay-namnområdet måste vara mellan 6 och 50 tecken långt.

## <a name="subscription-and-namespace-management"></a>Prenumeration och namnområde
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hur migrerar jag ett namnområde till en annan Azure-prenumeration?

Om du vill flytta ett namnområde från en Azure-prenumeration till en annan prenumeration, du kan använda den [Azure-portalen](https://portal.azure.com) eller Använd PowerShell-kommandon. Om du vill flytta ett namnområde till en annan prenumeration, måste namnområdet redan vara aktiv. Användaren som kör kommandona måste vara en administratörsanvändare på både käll- och prenumerationer.

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
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Vad är några av undantag som genereras av Azure Relay API: er och förslag på åtgärder du kan vidta?
En beskrivning av vanliga undantag och föreslagna åtgärder som du kan vidta finns i [vidarebefordrar undantag][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Vad är en signatur för delad åtkomst och vilka språk kan jag använda för att generera en signatur?
Signaturer för delad åtkomst (SAS) är en autentiseringsmetod baserad på säkra SHA-256-hashvärden eller URI: er. Information om hur du skapar dina egna signaturer i Node, PHP, Java, C och C# finns i [Service Bus-autentisering med signaturer för delad åtkomst][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Är det möjligt att vitlista relay slutpunkter?
Ja. Relay-klienten skickar anslutningar till Azure Relay-tjänsten med hjälp av fullständigt kvalificerade domännamn. Kunder kan lägga till en post för `*.servicebus.windows.net` för brandväggar som har stöd för DNS-listan över tillåtna program.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
