---
title: Vanliga frågor om Azure Relay | Microsoft-dokument
description: Den här artikeln innehåller svar på några av de vanligaste frågorna om Azure Relay-tjänsten.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: d5032b427316a3c4e07013af4e8214e239a6efb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514008"
---
# <a name="azure-relay-faqs"></a>Vanliga frågor om Azure Relay

Den här artikeln besvarar några vanliga frågor om [Azure Relay](https://azure.microsoft.com/services/service-bus/). Allmän Azure-pris- och supportinformation finns i vanliga frågor och frågor om [Azure-support](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Allmänna frågor
### <a name="what-is-azure-relay"></a>Vad är Azure Relay?
[Azure Relay-tjänsten](relay-what-is-it.md) underlättar dina hybridprogram genom att hjälpa dig att på ett säkrare sätt exponera tjänster som finns i ett företagsnätverk för det offentliga molnet. Du kan exponera tjänsterna utan att öppna en brandväggsanslutning och utan att kräva påträngande ändringar i en företagsnätverksinfrastruktur.

### <a name="what-is-a-relay-namespace"></a>Vad är ett relay-namnområde?
Ett [namnområde](relay-create-namespace-portal.md) är en omfångsbehållare som du kan använda för att adressera Relay-resurser i ditt program. Du måste skapa ett namnområde för att kunna använda Relay. Detta är ett av de första stegen för att komma igång.

### <a name="what-happened-to-service-bus-relay-service"></a>Vad hände med servicebussrelästjänsten?
Den tidigare namngivna tjänsten Service Bus Relay kallas nu [WCF Relay](service-bus-relay-tutorial.md). Du kan fortsätta att använda den här tjänsten som vanligt. Funktionen Hybridanslutningar är en uppdaterad version av en tjänst som har transplanterats från Azure BizTalk Services. WCF Relay- och Hybridanslutningar stöds båda.

## <a name="pricing"></a>Prissättning
Det här avsnittet besvarar några vanliga frågor om Relay-prisstrukturen. Du kan också se vanliga frågor om [Azure-support](https://azure.microsoft.com/support/faq/) för allmän Azure-prisinformation. Fullständig information om Relay-priser finns i [Prisinformation för Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Hur laddar du för hybridanslutningar och WCF Relay?
Fullständig information om Relay-priser finns i tabellen [Hybridanslutningar och WCF Relays][Pricing overview] på prisinformationssidan för Service Bus. Utöver de priser som anges på den sidan debiteras du för associerade dataöverföringar för utgående utanför det datacenter där ditt program etableras.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Hur faktureras jag för hybridanslutningar?
Här är tre exempel på faktureringsscenarier för hybridanslutningar:

*   Scenario 1:
    *   Du har en enda lyssnare, till exempel en instans av Hybrid Connections Manager installerad och kontinuerligt igång för hela månaden.
    *   Du skickar 3 GB data över anslutningen under månaden. 
    *   Din totala avgift är $ 5.
*   Scenario 2:
    *   Du har en enda lyssnare, till exempel en instans av Hybrid Connections Manager installerad och kontinuerligt igång för hela månaden.
    *   Du skickar 10 GB data över anslutningen under månaden.
    *   Din totala avgift är $ 7,50. Det är $ 5 för anslutningen och första 5 GB + $ 2,50 för ytterligare 5 GB data.
*   Scenario 3:
    *   Du har två instanser, A och B, i Hybrid Connections Manager installerade och kontinuerligt körs under hela månaden.
    *   Du skickar 3 GB data över anslutning A under månaden.
    *   Du skickar 6 GB data över anslutning B under månaden.
    *   Din totala avgift är $ 10,50. Det är $ 5 för anslutning A + $ 5 för anslutning B + $ 0,50 (för den sjätte gigabyte på anslutning B).

Observera att de priser som används i exemplen endast gäller under förhandsgranskningsperioden Hybridanslutningar. Priserna kan komma att ändras vid allmän tillgänglighet för hybridanslutningar.

### <a name="how-are-hours-calculated-for-relay"></a>Hur beräknas timmar för relä?

WCF Relay är endast tillgängligt i namnområden på standardnivå. Priser och [anslutningskvoter](../service-bus-messaging/service-bus-quotas.md) för reläer har annars inte ändrats. Det innebär att reläer fortsätter att debiteras baserat på antalet meddelanden (inte operationer) och relätimmar. Mer information finns i tabellen ["Hybridanslutningar och WCF-reläer"](https://azure.microsoft.com/pricing/details/service-bus/) på prisinformationssidan.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Vad händer om jag har fler än en lyssnare ansluten till ett visst relä?
I vissa fall kan ett enda relä ha flera anslutna lyssnare. Ett relä anses vara öppet när minst en relälyssnare är ansluten till den. Att lägga till lyssnare i ett öppet relä resulterar i ytterligare relätimmar. Antalet reläavsändare (klienter som anropar eller skickar meddelanden till reläer) som är anslutna till ett relä påverkar inte beräkningen av relätimmar.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hur beräknas meddelandemätaren för WCF Relays?
(**Detta gäller endast WCF-reläer. Meddelanden är inte en kostnad för hybridanslutningar.**)

I allmänhet beräknas fakturerbara meddelanden för reläer med samma metod som används för förmedlade entiteter (köer, ämnen och prenumerationer), som beskrivits tidigare. Det finns dock några anmärkningsvärda skillnader.

Att skicka ett meddelande till ett Service Bus-relä behandlas som en "full through" skicka till relälyssen som tar emot meddelandet. Det behandlas inte som en sändningsåtgärd till servicebussreläet, följt av en leverans till relälyssen. En tjänstanropstjänstanrop (på upp till 64 kB) mot en relay listener resulterar i två fakturerbara meddelanden: ett fakturerbart meddelande för begäran och ett fakturerbart meddelande för svaret (förutsatt att svaret också är 64 kB eller mindre). Detta skiljer sig från att använda en kö för att medla mellan en klient och en tjänst. Om du använder en kö för att medla mellan en klient och en tjänst kräver samma mönstret för begäran och svar en begäran som skickas till kön, följt av en dequeue/leverans från kön till tjänsten. Detta följs av ett svar som skickas till en annan kö och en dequeue/leverans från den kön till klienten. Med samma storlek antaganden i hela (upp till 64 KB), den medierade kömönstret resulterar i 4 fakturerbara meddelanden. Du skulle faktureras för dubbelt så många meddelanden för att implementera samma mönster som du utför med hjälp av relä. Naturligtvis finns det fördelar med att använda köer för att uppnå detta mönster, till exempel hållbarhet och belastningsutjämning. Dessa fördelar kan motivera den extra kostnaden.

Reläer som öppnas med hjälp av **netTCPRelay** WCF-bindningen behandlar meddelanden inte som enskilda meddelanden, utan som en dataström som flödar genom systemet. När du använder den här bindningen är det bara avsändaren och lyssnaren som kan se hur de enskilda meddelandena som skickas och tas emot. För reläer som använder **netTCPRelay-bindningen** behandlas alla data som en ström för beräkning av fakturerbara meddelanden. I det här fallet beräknar Service Bus den totala mängden data som skickas eller tas emot via varje enskilt relä på 5-minuters basis. Sedan dividerar den totala mängden data med 64 KB för att bestämma antalet fakturerbara meddelanden för det reläet under den tidsperioden.

## <a name="quotas"></a>Kvoter
| Kvotnamn | Omfång |  Anteckningar | Värde |
| --- | --- | --- | --- |
| Samtidiga lyssnare på ett relä |Entitet |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |25 |
| Samtidiga reläanslutningar per alla relay-slutpunkter i ett tjänstnamnområde |Namnområde |- |5 000 |
| Reläslutpunkter per tjänstnamnområde |Namnområde |- |10 000 |
| Meddelandestorlek för [nätonewayrelaybinding-](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) och [neteventrelaybinding-reläer](/dotnet/api/microsoft.servicebus.neteventrelaybinding) |Namnområde |Inkommande meddelanden som överskrider dessa kvoter avvisas och ett undantag tas emot av den anropande koden. |64 kB |
| Meddelandestorlek för relayer för [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) och [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) |Namnområde |Ingen gräns för meddelandestorlek. |Unlimited |

### <a name="does-relay-have-any-usage-quotas"></a>Har Relay några användningskvoter?
Som standard anger Microsoft en sammanlagd månatlig användningskvot som beräknas för alla en kunds prenumerationer för alla molntjänster. Vi förstår att dina behov ibland kan överskrida dessa gränser. Du kan kontakta kundtjänst när som helst, så att vi kan förstå dina behov och justera dessa gränser på lämpligt sätt. För Service Bus är de sammanlagda användningskvoterna följande:

* 5 miljarder meddelanden
* 2 miljoner relätimmar

Även om vi förbehåller oss rätten att inaktivera ett konto som överskrider dess månatliga användningskvoter, tillhandahåller vi e-postmeddelanden och vi gör flera försök att kontakta kunden innan vi vidtar några åtgärder. Kunder som överskrider dessa kvoter är fortfarande ansvariga för överskjutande avgifter.

### <a name="naming-restrictions"></a>Namngivningsbegränsningar
Ett relay-namnområdesnamn måste vara mellan 6 och 50 tecken långt.

## <a name="subscription-and-namespace-management"></a>Prenumerations- och namnområdeshantering
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hur migrerar jag ett namnområde till en annan Azure-prenumeration?

Om du vill flytta ett namnområde från en Azure-prenumeration till en annan prenumeration kan du antingen använda [Azure-portalen](https://portal.azure.com) eller använda PowerShell-kommandon. Om du vill flytta ett namnområde till en annan prenumeration måste namnområdet redan vara aktivt. Användaren som kör kommandona måste vara administratörsanvändare på både käll- och målprenumerationerna.

#### <a name="azure-portal"></a>Azure Portal

Information om hur du använder Azure-portalen för att migrera Azure Relay-namnområden från en prenumeration till en annan prenumeration finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Om du vill använda PowerShell för att flytta ett namnområde från en Azure-prenumeration till en annan prenumeration använder du följande sekvens av kommandon. För att kunna utföra den här åtgärden måste namnområdet redan vara aktivt och användaren som kör PowerShell-kommandona måste vara administratörsanvändare på både käll- och målprenumerationerna.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Felsökning
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Vilka är några av de undantag som genereras av Azure Relay API:er och föreslagna åtgärder som du kan vidta?
En beskrivning av vanliga undantag och föreslagna åtgärder som du kan vidta finns i [Återutläsa undantag][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Vad är en signatur för delad åtkomst och vilka språk kan jag använda för att generera en signatur?
SAS (Shared Access Signatures) är en autentiseringsmekanism som baseras på SHA-256-secure hashar eller URI:er. Information om hur du genererar egna signaturer i Node.js, PHP, Python, Java, C och C#, finns i [Service Bus-autentisering med signaturer för delad åtkomst][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Är det möjligt att vitlista reläslutpunkter?
Ja. Relay-klienten upprättar anslutningar till Azure Relay-tjänsten med hjälp av fullständigt kvalificerade domännamn. Kunder kan lägga `*.servicebus.windows.net` till en post för brandväggar som stöder DNS-vitlistning.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Komma igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
