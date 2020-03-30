---
title: Förskottsbetalning för beräkning med reserverad kapacitet - Azure Cache för Redis
description: Förskottsbetalning för Azure Cache för Redis-beräkningsresurser med reserverad kapacitet
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530307"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Förskottsbetalning för Azure Cache för Redis-beräkningsresurser med reserverad kapacitet

Azure Cache för Redis hjälper dig nu att spara pengar genom att betala i förskott för beräkningsresurser jämfört med priser för användningsbaserad betalning. Med Azure Cache för Redis reserverad kapacitet gör du ett förskottsåtagande på cache för en eller tre år för att få en betydande rabatt på beräkningskostnaderna. Om du vill köpa Azure-cache för Redis reserverad kapacitet måste du ange Azure-region, tjänstnivå och term.

Du behöver inte tilldela reservationen till specifika Azure-cache för Redis-instanser. En som redan kör Azure Cache för Redis eller de som nyligen har distribuerats får automatiskt fördelen av reserverad prissättning, upp till den reserverade cachestorleken. Genom att köpa en bokning betalar du i förväg för beräkningskostnaderna för en period av ett eller tre år. Så fort du köper en reservation debiteras inte längre Azure Cache for Redis-beräkningsavgifter som matchar reservationsattributen enligt priserna för fördelning per dun. En reservation omfattar inte nätverk eller lagringsavgifter som är associerade med cachen. I slutet av bokningsperioden upphör faktureringsförmånen och Azure Cache för Redis faktureras till priset för betalning. Bokningar förnyas inte automatiskt. Prisinformation finns i [Azure Cache for Redis reserved capacity offering](https://azure.microsoft.com/pricing/details/cache).

Du kan köpa Azure Cache för Redis reserverad kapacitet i [Azure-portalen](https://portal.azure.com/). Så här köper du den reserverade kapaciteten:

* Du måste vara i ägarrollen för minst en Enterprise eller enskild prenumeration med abonnemang som gäller.
* För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om den inställningen är inaktiverad måste du också vara EA-administratör för prenumerationen.
* För CSP-program (Cloud Solution Provider) kan endast administratörsagenter eller försäljningsagenter köpa Azure-cache för Redis reserverad kapacitet.

Mer information om hur företagskunder och pay-as-you-go-kunder debiteras för reservationsköp finns i [förstå Azure-reservationsanvändning för din Enterprise-registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) och [förstå Azure-reservationsanvändning för din prenumeration med användningsbaserad betalning](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-cache-size-before-purchase"></a>Bestäm rätt cachestorlek före köp

Storleken på reservationen bör baseras på den totala mängden beräkning som används av den befintliga eller snart distribuerade cachen inom en viss region och med samma tjänstnivå.

Anta till exempel att du kör ett allmänt syfte, Gen5 – 32 vCore-cache och två minnesoptimerade, Gen5 – 16 vCore-cacheminnen. Anta vidare att du planerar att distribuera ytterligare ett allmänt ändamål inom nästa månad, Gen5 – 32 vCore-databasserver och ett minne optimerat, Gen5 – 16 vCore-databasserver. Låt oss anta att du vet att du behöver dessa resurser i minst 1 år. I det här fallet bör du köpa en 64 (2x32) vCores, 1 års reservation för enkel databas allmänt ändamål - Gen5 och en 48 (2x16 + 16) vCore 1 års reservation för enkel databas minne optimerad - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Köpa Azure-cache för Redis reserverad kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj Alla > **servicereservationer**. **All services**
3. Välj **Lägg till** och välj sedan Azure Cache för **Redis** i fönstret Inköpsreservationer för att köpa en ny reservation för dina cacheminnen.
4. Fyll i de obligatoriska fälten. Befintliga eller nya databaser som matchar de attribut du väljer kvalificerar sig för att få rabatt med reserverad kapacitet. Det faktiska antalet Azure-cache för Redis-instanser som får rabatten beror på omfattningen och kvantiteten som valts.


![Översikt över reserverad prissättning](media/cache-reserved-pricing/cache-reserved-price.png)


I följande tabell beskrivs obligatoriska fält.

| Field | Beskrivning |
| :------------ | :------- |
| Prenumeration   | Prenumerationen som används för att betala för Azure-cachen för Redis reserverad kapacitetsreservation. Betalningsmetoden för prenumerationen debiteras initiala kostnader för Azure Cache for Redis reserverad kapacitetsreservation. Prenumerationstypen måste vara ett företagsavtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett individuellt avtal med betalning per användning -prissättning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. För en enskild prenumeration med priser för användningsbaserad betalning debiteras avgifterna till kreditkorts- eller fakturabetalningsmetoden för prenumerationen.
| Omfång | Reservationens omfattning kan omfatta en prenumeration eller flera prenumerationer (delat scope). Om du väljer: </br></br> **Delad**tillämpas reservationsrabatten på Azure Cache för Redis-instanser som körs i alla prenumerationer i din faktureringskontext. För företagskunder är det delade omfånget registreringen och inkluderar alla prenumerationer i registreringen. För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat.</br></br> **Enkel prenumeration**tillämpas reservationsrabatten på Azure Cache för Redis-instanser i den här prenumerationen. </br></br> **Enskild resursgrupp**tillämpas reservationsrabatten på Azure Cache för Redis-instanser i den valda prenumerationen och den valda resursgruppen i den prenumerationen.
| Region | Azure-regionen som omfattas av Azure Cache for Redis reserverad kapacitetsreservation.
| Prisnivå | Tjänstnivån för Azure Cache för Redis-servrar.
| Period | Ett år eller tre år
| Kvantitet | Mängden beräkningsresurser som köps i Azure-cachen för redis reserverad kapacitetsreservation. Kvantiteten är ett antal cacheminnen i den valda Azure-regionen och tjänstnivån som reserveras och får faktureringsrabatten. Om du till exempel kör eller planerar att köra en Azure Cache för Redis-servrar med den totala cachekapaciteten på 26 GB i regionen östra USA, anger du kvantitet som 26 för att maximera förmånen för alla cacheminnen.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="cache-size-flexibility"></a>Flexibilitet i cachestorlek

Flexibilitet för cachestorlek hjälper dig att skala upp eller ned inom en tjänstnivå och region, utan att förlora den reserverade kapacitetsförmånen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Reservationsrabatten tillämpas automatiskt på Azure Cache för Redis-instanser som matchar reservationsomfattningen och attributen. Du kan uppdatera omfattningen av reservationen via Azure-portalen, PowerShell, Azure CLI eller API:et.

*  Mer information om hur reserverade kapacitetsrabatter tillämpas på Azure Cache för Redis finns [i Förstå Azure-reservationsrabatten](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Du kan läsa mer om Azure-reservationer i följande artiklar:

    * [Vad är Azure-reservationer?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Hantera Azure-reservationer](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Förstå reservationsrabatter i Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Förstå reservationsanvändning för din Betala per användning-prenumeration](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Förstå reservationsanvändning för din Enterprise-registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)

