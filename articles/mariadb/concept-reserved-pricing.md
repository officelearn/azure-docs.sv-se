---
title: Förskottsbetalning för beräkning med reserverad kapacitet - Azure Database för MariaDB
description: Förskottsbetalning för Azure Database för MariaDB-beräkningsresurser med reserverad kapacitet
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9a6bd3cab41c69075f5170a8a3aad4f059d970e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159055"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Förskottsbetalning för Azure Database för MariaDB-beräkningsresurser med reserverad kapacitet

Azure Database för MariaDB hjälper dig nu att spara pengar genom att betala i förskott för beräkningsresurser jämfört med priser för användningsbaserad betalning. Med Azure Database för MariaDB reserverad kapacitet gör du ett förskottsåtagande på MariaDB-servrar under en en eller tre år för att få en betydande rabatt på beräkningskostnaderna. Om du vill köpa Azure-databas för MariaDB-reserverad kapacitet måste du ange Azure-region, distributionstyp, prestandanivå och term. </br>

Du behöver inte tilldela reservationen till specifika Azure-databas för MariaDB-servrar. En redan körande Azure-databas för MariaDB eller de som nyligen har distribuerats, kommer automatiskt att få nytta av reserverad prissättning. Genom att köpa en bokning betalar du i förväg för beräkningskostnaderna för en period av ett eller tre år. Så fort du köper en reservation debiteras inte längre Azure-databasen för MariaDB-beräkningsavgifter som matchar reservationsattributen enligt priserna för er för att betala per er. En reservation omfattar inte programvaru-, nätverks- eller lagringsavgifter som är kopplade till MariaDB-databasservern. I slutet av bokningsperioden upphör faktureringsförmånen och Azure-databasen för MariaDB faktureras till priset för betalning. Bokningar förnyas inte automatiskt. Prisinformation finns i [Azure-databasen för MariaDB reserverad kapacitetserbjudande](https://azure.microsoft.com/pricing/details/mariadb/). </br>

Du kan köpa Azure Database för MariaDB reserverad kapacitet i [Azure-portalen](https://portal.azure.com/). Så här köper du den reserverade kapaciteten:

* Du måste vara i ägarrollen för minst en Enterprise eller enskild prenumeration med abonnemang som gäller.
* För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om den inställningen är inaktiverad måste du också vara EA-administratör för prenumerationen.
* För CSP-program (Cloud Solution Provider) kan endast administratörsagenter eller försäljningsagenter köpa Azure Database för Reserverad kapacitet för MariaDB. </br>

Information om hur företagskunder och pay-as-you-go-kunder debiteras för bokningsköp, se [förstå Azure-reservationsanvändning för din Enterprise-registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) och [förstå Azure-reservationsanvändning för din Pay-As-You-Go-prenumeration](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-server-size-before-purchase"></a>Ta reda på rätt serverstorlek före köp

Storleken på reservationen bör baseras på den totala mängden beräkning som används av den befintliga eller snart distribuerade databasinstansen inom en viss region och med samma prestandanivå och maskinvarugenerering.</br>

Anta till exempel att du kör ett allmänt syfte, Gen5 – 32 vCore MariaDB-databas och två minnesoptimerade, Gen5 – 16 vCore MariaDB-databaser. Anta vidare att du planerar att distribuera ytterligare ett allmänt ändamål inom nästa månad, Gen5 – 32 vCore-databasserver och ett minne optimerat, Gen5 – 16 vCore-databasserver. Låt oss anta att du vet att du behöver dessa resurser i minst 1 år. I det här fallet bör du köpa en 64 (2x32) vCores, 1 års reservation för enkel databas allmänt ändamål - Gen5 och en 48 (2x16 + 16) vCore 1 års reservation för enkel databas minne optimerad - Gen5


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Köp Azure-databas för MariaDB reserverad kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj Alla > **servicereservationer**. **All services**
3.  Välj **Lägg till** och välj sedan Azure Database för **MariaDB** i fönstret Inköpsreservationer om du vill köpa en ny reservation för dina MariaDB-databaser.
4.  Fyll i de obligatoriska fälten. Befintliga eller nya databaser som matchar de attribut du väljer kvalificerar sig för att få rabatt med reserverad kapacitet. Det faktiska antalet Azure-databas för MariaDB-servrar som får rabatten beror på omfattningen och kvantiteten som valts.


![Översikt över reserverad prissättning](media/concepts-reserved-pricing/mariadb-reserved-price.png)


I följande tabell beskrivs obligatoriska fält.

| Field | Beskrivning |
| :------------ | :------- |
| Prenumeration   | Prenumerationen som används för att betala för Azure-databasen för MariaDB reserverad kapacitetsreservation. Betalningsmetoden för prenumerationen debiteras initiala kostnader för Azure-databasen för MariaDB reserverad kapacitetsreservation. Prenumerationstypen måste vara ett företagsavtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett individuellt avtal med betalning per användning -prissättning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. För en enskild prenumeration med priser för användningsbaserad betalning debiteras avgifterna till kreditkorts- eller fakturabetalningsmetoden för prenumerationen.
| Omfång | VCore-reservationens omfattning kan omfatta en prenumeration eller flera prenumerationer (delat scope). Om du väljer: </br></br> **Delad**tillämpas vCore-reservationsrabatten på Azure Database för MariaDB-servrar som körs i alla prenumerationer i din faktureringskontext. För företagskunder är det delade omfånget registreringen och inkluderar alla prenumerationer i registreringen. För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat.</br></br> **Enkel prenumeration**tillämpas vCore-reservationsrabatten på Azure Database för MariaDB-servrar i den här prenumerationen. </br></br> **Enskild resursgrupp**tillämpas reservationsrabatten på Azure Database för MariaDB-servrar i den valda prenumerationen och den valda resursgruppen i den prenumerationen.
| Region | Azure-regionen som omfattas av Azure-databasen för MariaDB reserverad kapacitetsreservation.
| Distributionstyp | Den Azure-databas för MariaDB-resurstyp som du vill köpa reservationen för.
| Prestandanivå | Tjänstnivån för Azure-databasen för MariaDB-servrar.
| Period | Ett år
| Kvantitet | Mängden beräkningsresurser som köps i Azure-databasen för MariaDB reserverad kapacitetsreservation. Kvantiteten är ett antal virtuella kärnor i den valda Azure-regionen och prestandanivån som reserveras och får faktureringsrabatten. Om du till exempel kör eller planerar att köra en Azure-databas för MariaDB-servrar med den totala beräkningskapaciteten för Gen5 16 virtuella kärnor i regionen östra USA, anger du kvantitet som 16 för att maximera förmånen för alla servrar.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>vCore storlek flexibilitet

vCore storlek flexibilitet hjälper dig att skala upp eller ner inom en prestandanivå och region, utan att förlora den reserverade kapacitet förmån. 

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

VCore-reservationsrabatten tillämpas automatiskt på antalet Azure-databas för MariaDB-servrar som matchar Azure-databasen för MariaDB reserverad kapacitetsreservation och attribut. Du kan uppdatera omfattningen av Azure-databasen för MariaDB reserverad kapacitetsreservation via Azure-portalen, PowerShell, CLI eller via API:et. </br></br>
Mer information om hur du hanterar Azure-databasen för MariaDB reserverad kapacitet finns i hantera Azure Database för MariaDB reserverad kapacitet.

Du kan läsa mer om Azure-reservationer i följande artiklar:

* [Vad är Azure-reservationer?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Hantera Azure-reservationer](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Förstå reservationsrabatter i Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Förstå reservationsanvändning för din Betala per användning-prenumeration](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [Förstå reservationsanvändning för din Enterprise-registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)
