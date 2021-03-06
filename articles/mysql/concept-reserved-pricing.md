---
title: Förskotts beräkning med reserverad kapacitet – Azure Database for MySQL
description: Förskotts betalning för Azure Database for MySQL beräknings resurser med reserverad kapacitet
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 33c2617a22945ecb11586bc282e7f9f61b6e49ca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008556"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>Förskotts betalning för Azure Database for MySQL beräknings resurser med reserverad kapacitet

Azure Database for MySQL nu kan du spara pengar genom att betala för beräknings resurser jämfört med priser enligt principen betala per användning. Med Azure Database for MySQL reserverad kapacitet gör du ett åtagande på MySQL server under en eller tre års period för att få en betydande rabatt på beräknings kostnaderna. Om du vill köpa Azure Database for MySQL reserverad kapacitet måste du ange Azure-region, distributions typ, prestanda nivå och period. </br>

Du behöver inte tilldela reservationen till vissa Azure Database for MySQL-servrar. Om du redan kör Azure Database for MySQL eller som nyligen har distribuerats får du automatiskt fördelarna med reserverad prissättning. Genom att köpa en reservation betalar du för beräknings kostnaderna för en period på en eller tre år. Så snart du köper en reservation debiteras inte längre Azure Database for MySQL-beräkningarna som matchar reservations attributen enligt priserna för betala per användning. En reservation omfattar inte program vara, nätverk eller lagrings kostnader som är kopplade till MySQL-databasservern. I slutet av reservations perioden upphör fakturerings förmånen och Azure Database for MySQL debiteras enligt priset betala per användning. Reservationer förnyas inte automatiskt. För pris information, se [Azure Database for MySQL reserverat kapacitets erbjudande](https://azure.microsoft.com/pricing/details/mysql/). </br>

Du kan köpa Azure Database for MySQL reserverad kapacitet i [Azure Portal](https://portal.azure.com/). Betala för reservationen [i förväg eller via månadsbetalningar](../cost-management-billing/reservations/prepare-buy-reservation.md). Så här köper du den reserverade kapaciteten:

* Du måste ha ägar rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.
* För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen.
* För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa Azure Database for MySQL reserverad kapacitet. </br>

Information om hur företags kunder och kunder som betalar per användning debiteras för reservations köp, se [förstå Azure reservation-användning för företagets registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) och [förstå Azure reservation-användning för din prenumeration enligt principen betala per](../cost-management-billing/reservations/understand-reserved-instance-usage.md)användning.


## <a name="determine-the-right-database-size-before-purchase"></a>Fastställ rätt databas storlek innan köpet

Reservations storleken bör baseras på den totala mängd data som används av den befintliga eller närmast distribuerade servern inom en angiven region och som använder samma prestanda nivå och maskin varu generation.</br>

Anta till exempel att du kör ett allmänt syfte, Gen5 – 32 vCore MySQL-databas och två minnesoptimerade, Gen5 – 16 vCore MySQL-databaser. Vi vill också att du planerar att distribuera under nästa månad till ett ytterligare allmänt syfte, Gen5 – 32 vCore Database Server och en minnesoptimerade Gen5 – 16 vCore-databas server. Anta att du vet att du kommer att behöva dessa resurser i minst 1 år. I det här fallet bör du köpa en 64 (2x32) virtuella kärnor, 1 års reservation för enkel databas generell användning – Gen5 och en 48 (2x16 + 16) vCore 1 års reservation för enkel databas minne optimerad-Gen5


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Köp Azure Database for MySQL reserverad kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Alla tjänster** > **Reservationer**.
3. Välj **Lägg till** och sedan i fönstret köp reservationer väljer du **Azure Database for MySQL** för att köpa en ny reservation för MySQL-databaserna.
4. Fyll i de obligatoriska fälten. Befintliga eller nya databaser som matchar de attribut du väljer kvalificera för att hämta den reserverade kapacitets rabatten. Det faktiska antalet Azure Database for MySQL-servrar som får rabatten beroende på omfattning och vald kvantitet.


:::image type="content" source="media/concepts-reserved-pricing/mysql-reserved-price.png" alt-text="Översikt över reserverade priser":::


I följande tabell beskrivs obligatoriska fält.

| Fält | Beskrivning |
| :------------ | :------- |
| Prenumeration   | Prenumerationen som används för att betala för den Azure Database for MySQL reserverade kapacitets reservationen. Betalnings metoden för prenumerationen debiteras mot startkostnader för den Azure Database for MySQL reserverade kapacitets reservationen. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. För en enskild prenumeration med priset betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.
| Omfång | VCore-reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: </br></br> **Delat** används reservations rabatten för vCore på Azure Database for MySQL servrar som körs i en prenumeration i din fakturerings kontext. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat.</br></br> **Enskild prenumeration** tillämpas reservations rabatten för vCore på Azure Database for MySQL servrar i den här prenumerationen. </br></br> **En enda resurs grupp**, reservations rabatten tillämpas på Azure Database for MySQL servrar i den valda prenumerationen och den valda resurs gruppen i den prenumerationen.
| Region | Den Azure-region som omfattas av reservationen Azure Database for MySQL reserverad kapacitet.
| Distributions typ | Den Azure Database for MySQL resurs typ som du vill köpa reservationen för.
| Prestanda nivå | Tjänst nivån för Azure Database for MySQL-servrarna.
| Term | Ett år
| Quantity | Mängden data bearbetnings resurser som köps i Azure Database for MySQL reserverade kapacitets reservationen. Antalet är ett antal virtuella kärnor i den valda Azure-regionen och prestanda nivån som reserveras och kommer att få fakturerings rabatten. Om du till exempel kör eller planerar att köra en Azure Database for MySQL-server med den totala beräknings kapaciteten för Gen5 16 virtuella kärnor i regionen USA, östra, skulle du ange kvantitet som 16 för att maximera förmånen för alla servrar.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilitet för vCore-storlek

vCore storleks flexibilitet hjälper dig att skala upp eller ned i en prestanda nivå och region, utan att förlora den reserverade kapacitets förmånen. 

## <a name="need-help--contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Reservations rabatten för vCore tillämpas automatiskt på antalet Azure Database for MySQL servrar som Azure Database for MySQL matchar omfånget och attributen för reserverade kapacitets reservationer. Du kan uppdatera omfånget för Azure Database för MySQL reserverad kapacitets reservation via Azure Portal, PowerShell, CLI eller via API: et. </br></br>
Information om hur du hanterar Azure Database for MySQL reserverad kapacitet finns i Hantera Azure Database for MySQL reserverad kapacitet.

Du kan läsa mer om Azure-reservationer i följande artiklar:

* [Vad är Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Hantera Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Förstå reservationsrabatter i Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Förstå reservationsanvändning för din Betala per användning-prenumeration](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
* [Förstå reservationsanvändning för din Enterprise-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](/partner-center/azure-reservations)