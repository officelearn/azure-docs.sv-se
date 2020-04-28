---
title: Spara kostnader
description: Lär dig hur du köper Azure SQL Database reserverad kapacitet för att spara pengar på dina beräknings kostnader.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75979979"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Spara kostnader för SQL Database beräknings resurser med Azure SQL Database reserverad kapacitet

Spara pengar med Azure SQL Database genom att genomföra en reservation för beräknings resurser jämfört med priser enligt principen betala per användning. Med Azure SQL Database reserverad kapacitet gör du ett åtagande för SQL Database som används under en period på en eller tre år för att få en betydande rabatt på beräknings kostnaderna. Om du vill köpa SQL Database reserverad kapacitet måste du ange Azure-region, distributions typ, prestanda nivå och period.


Du behöver inte tilldela reservationen till vissa SQL Database instanser (enstaka databaser, elastiska pooler eller hanterade instanser). Matchning av SQL Database instanser, som redan körs eller som nyligen distribueras, får automatiskt förmånen. Genom att köpa en reservation ska du spara förbrukningen för beräknings kostnaderna under en period på en eller tre år. När du köper en reservation debiteras inte längre de SQL Database beräknings kostnader som matchar reservations attributen enligt priserna för betala per användning. En reservation omfattar inte program vara, nätverk eller lagrings kostnader som är associerade med SQL Database-instansen. I slutet av reservations perioden upphör fakturerings förmånen och SQL-databaserna debiteras enligt priset betala per användning. Reservationer förnyas inte automatiskt. För pris information, se [SQL Database reserverat kapacitets erbjudande](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Du kan köpa Azure SQL Database reserverad kapacitet i [Azure Portal](https://portal.azure.com). Betala för reservationen [i förväg eller via månadsbetalningar](../cost-management-billing/reservations/monthly-payments-reservations.md). Köpa SQL Database reserverad kapacitet:

- Du måste ha ägar rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.
- För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen.
- För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa SQL Database reserverad kapacitet.

Information om hur företags kunder och kunder som betalar per användning debiteras för reservations köp, se [förstå Azure reservation-användning för företagets registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) och [förstå Azure reservation-användning för din prenumeration enligt principen betala per](../cost-management-billing/reservations/understand-reserved-instance-usage.md)användning.

## <a name="determine-the-right-sql-size-before-purchase"></a>Fastställ rätt SQL-storlek innan köpet

Reservations storleken bör baseras på den totala mängd data som används av befintliga eller närmast distribuerade enskilda databaser, elastiska pooler eller hanterade instanser inom en viss region och som använder samma prestanda nivå och maskin varu generation.

Anta till exempel att du kör ett allmänt syfte, Gen5 – 16 vCore elastisk pool och två affärs kritiska Gen5 – 4 vCore-databaser. Vi vill också att du planerar att distribuera under nästa månad till ett ytterligare allmänt syfte, Gen5 – 16 vCore elastisk pool och en affärs kritisk Gen5 – 32 vCore elastisk pool. Vi antar också att du vet att du behöver dessa resurser i minst 1 år. I det här fallet bör du köpa en 32 (2x16) virtuella kärnor, 1 års reservation för en enskild databas/elastisk pool generell användning – Gen5 och en 40 (2x4 + 32) vCore 1 års reservation för en enskild databas/elastisk pool affärs kritiskt-Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Köp reserverad kapacitet för SQL Database

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj **Lägg till** och sedan i fönstret köp reservationer väljer du **SQL Database** för att köpa en ny reservation för SQL Database.
4. Fyll i de obligatoriska fälten. Befintliga eller nya enskilda databaser, elastiska pooler eller hanterade instanser som matchar de attribut som du har valt kvalificera för att hämta rabatten för reserverad kapacitet. Det faktiska antalet SQL Database-instanser som erhåller rabatten beror på omfattning och kvantitet som valts.
    ![Skärm bild innan du skickar SQL Database reserverat kapacitets köp](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

I följande tabell beskrivs obligatoriska fält.

| Field      | Beskrivning|
|------------|--------------|
|Prenumeration|Prenumerationen som används för att betala för den SQL Database reserverade kapacitets reservationen. Betalnings metoden för prenumerationen debiteras mot startkostnader för den SQL Database reserverade kapacitets reservationen. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. För en enskild prenumeration med priset betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.|
|Omfång       |VCore-reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <br/><br/>**Delad**, reservations rabatten för vCore tillämpas på SQL Database instanser som körs i en prenumeration i din fakturerings kontext. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat.<br/><br/>**Enskild prenumeration**tillämpas reservations rabatten för vCore på SQL Database instanser i den här prenumerationen. <br/><br/>**En enda resurs grupp**, reservations rabatten tillämpas på SQL Database instanser i den valda prenumerationen och den valda resurs gruppen i den prenumerationen.|
|Region      |Den Azure-region som omfattas av reservationen SQL Database reserverad kapacitet.|
|Distributions typ|Den SQL-resurs typ som du vill köpa reservationen för.|
|Prestanda nivå|Tjänst nivån för SQL Database instanser.
|Period        |Ett år eller tre år.|
|Kvantitet    |Mängden data bearbetnings resurser som köps i SQL Database reserverade kapacitets reservationen. Antalet är ett antal virtuella kärnor i den valda Azure-regionen och prestanda nivån som reserveras och kommer att få fakturerings rabatten. Om du till exempel kör eller planerar att köra SQL Database instanser med den totala beräknings kapaciteten för Gen5 16 virtuella kärnor i regionen USA, östra, skulle du ange kvantitet som 16 för att maximera förmånen för alla instanser. |

1. Granska kostnaden för den SQL Database reserverade kapacitets reservationen i avsnittet **kostnader** .
1. Välj **Köp**.
1. Välj **Visa den här reservationen** för att se inköpets status.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Mer information finns i [självbetjänings utbyten och åter betalningar för Azure reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>flexibilitet för vCore-storlek

vCore storleks flexibilitet hjälper dig att skala upp eller ned i en prestanda nivå och region, utan att förlora den reserverade kapacitets förmånen. SQL Database reserverad kapacitet ger också flexibiliteten att tillfälligt flytta dina aktiva databaser mellan pooler och enkla databaser som en del av din normala verksamhet (inom samma region och prestanda nivå) utan att förlora den reserverade kapacitets förmånen. Genom att behålla en icke-tillämpad buffert i din reservation kan du effektivt hantera prestanda toppar utan att överskrida budgeten.

## <a name="limitation"></a>Begränsning

Det går inte att reservera DTU-baserade (Basic-, standard-eller Premium) SQL-databaser.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Reservations rabatten för vCore tillämpas automatiskt på antalet SQL Database instanser som SQL Database matchar omfånget och attributen för reserverade kapacitets reservationer. Du kan uppdatera omfånget för den SQL Database reserverade kapacitets reservationen via [Azure Portal](https://portal.azure.com), POWERSHELL, CLI eller via API: et.

Information om hur du hanterar reservationen för SQL Database reserverad kapacitet finns i [hantera SQL Database reserverad kapacitet](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Hantera Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Förstå reservationsrabatter i Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)
