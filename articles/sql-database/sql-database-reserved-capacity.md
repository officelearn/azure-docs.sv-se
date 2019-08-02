---
title: Förskotts betalning för Azure SQL Database virtuella kärnor för att spara pengar | Microsoft Docs
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
ms.date: 07/19/2019
ms.openlocfilehash: adbc2c8818a3d7eff679dadc2245fb0ff8686fc2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566926"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Förskotts betalning för SQL Database beräknings resurser med Azure SQL Database reserverad kapacitet

Spara pengar med Azure SQL Database genom att betala för beräknings resurser jämfört med priser enligt principen betala per användning. Med Azure SQL Database reserverad kapacitet gör du ett åtagande på SQL Database under en period av en eller tre år för att få en betydande rabatt på beräknings kostnaderna. Om du vill köpa SQL Database reserverad kapacitet måste du ange Azure-region, distributions typ, prestanda nivå och period.


Du behöver inte tilldela reservationen till vissa SQL Database instanser (enstaka databaser, elastiska pooler eller hanterade instanser). Matchning av SQL Database instanser, som redan körs eller som nyligen distribueras, får automatiskt förmånen. Genom att köpa en reservation betalar du för beräknings kostnaderna för en period på en eller tre år. När du köper en reservation debiteras inte längre de SQL Database beräknings kostnader som matchar reservations attributen enligt priserna för betala per användning. En reservation omfattar inte program vara, nätverk eller lagrings kostnader som är associerade med SQL Database-instansen. I slutet av reservations perioden upphör fakturerings förmånen och SQL-databaserna debiteras enligt priset betala per användning. Reservationer förnyas inte automatiskt. För pris information, se [SQL Database reserverat kapacitets erbjudande](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Du kan köpa Azure SQL Database reserverad kapacitet i [Azure Portal](https://portal.azure.com). Köpa SQL Database reserverad kapacitet:

- Du måste ha ägar rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.
- För företags prenumerationer måste **Lägg till reserverade instanser** vara aktiverade i [EA-portalen](https://ea.azure.com). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen.
- För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa SQL Database reserverad kapacitet.

Information om hur företags kunder och kunder som betalar per användning debiteras för reservations köp, se [förstå Azure reservation-användning för din företags registrering](../billing/billing-understand-reserved-instance-usage-ea.md) och [förstå Azure reservation-användning för ditt företag som du betalar per användning prenumeration](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Fastställ rätt SQL-storlek innan köpet

Reservations storleken bör baseras på den totala mängd data som används av befintliga eller närmast distribuerade enskilda databaser, elastiska pooler eller hanterade instanser inom en viss region och som använder samma prestanda nivå och maskin varu generation.

Anta till exempel att du kör ett allmänt syfte, Gen5 – 16 vCore elastisk pool och två affärs kritiska Gen5 – 4 vCore-databaser. Vi vill också att du planerar att distribuera under nästa månad till ett ytterligare allmänt syfte, Gen5 – 16 vCore elastisk pool och en affärs kritisk Gen5 – 32 vCore elastisk pool. Vi antar också att du vet att du behöver dessa resurser i minst 1 år. I det här fallet bör du köpa en 32 (2x16) virtuella kärnor, 1 års reservation för en enskild databas/elastisk pool generell användning – Gen5 och en 40 (2x4 + 32) vCore 1 års reservation för en enskild databas/elastisk pool affärs kritiskt-Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Köp SQL Database reserverad kapacitet

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj **Lägg till** och sedan i fönstret köp reservationer väljer du **SQL Database** för att köpa en ny reservation för SQL Database.
4. Fyll i de obligatoriska fälten. Befintliga eller nya enskilda databaser, elastiska pooler eller hanterade instanser som matchar de attribut som du har valt kvalificera för att hämta rabatten för reserverad kapacitet. Det faktiska antalet SQL Database-instanser som erhåller rabatten beror på omfattning och kvantitet som valts.
    ![Skärm bild innan du skickar SQL Database reserverat kapacitets köp](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

I följande tabell beskrivs obligatoriska fält.

| Fält      | Beskrivning|
|------------|--------------|
|Subscription|Prenumerationen som används för att betala för den SQL Database reserverade kapacitets reservationen. Betalnings metoden för prenumerationen debiteras mot startkostnader för den SQL Database reserverade kapacitets reservationen. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. För en enskild prenumeration med priset betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.|
|Omfång       |VCore-reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <br/><br/>**Delad**, reservations rabatten för vCore tillämpas på SQL Database instanser som körs i en prenumeration i din fakturerings kontext. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För kunder som betalar per användning är det delade omfånget alla prenumerationer enligt principen betala per användning som har skapats av konto administratören.<br/><br/>**Enskild prenumeration**tillämpas reservations rabatten för vCore på SQL Database instanser i den här prenumerationen. <br/><br/>**En enda resurs grupp**, reservations rabatten tillämpas på SQL Database instanser i den valda prenumerationen och den valda resurs gruppen i den prenumerationen.|
|Region      |Den Azure-region som omfattas av reservationen SQL Database reserverad kapacitet.|
|Distributionstyp|Den SQL-resurs typ som du vill köpa reservationen för.|
|Prestandanivå|Tjänst nivån för SQL Database instanser.
|Term        |Ett år eller tre år.|
|Antal    |Mängden data bearbetnings resurser som köps i SQL Database reserverade kapacitets reservationen. Antalet är ett antal virtuella kärnor i den valda Azure-regionen och prestanda nivån som reserveras och kommer att få fakturerings rabatten. Om du till exempel kör eller planerar att köra SQL Database instanser med den totala beräknings kapaciteten för Gen5 16 virtuella kärnor i regionen USA, östra, skulle du ange kvantitet som 16 för att maximera förmånen för alla instanser. |

1. Granska kostnaden för den SQL Database reserverade kapacitets reservationen i avsnittet **kostnader** .
1. Välj **Köp**.
1. Välj **Visa den här reservationen** för att se inköpets status.

## <a name="cancellations-and-exchanges"></a>Avbokningar och byten

Om du behöver avbryta din SQL Database reservation av reserverad kapacitet kan det finnas 12% tidig uppsägnings avgift. Återbetalningar baseras på det lägsta priset – antingen ditt inköpspris eller det aktuella priset för reservationen. Återbetalningar begränsas till 50 000 USD per år. Din återbetalning består av det återstående proportionellt beräknade saldot minus uppsägningsavgiften på 12 %. Om du vill avbryta går du till reservationen i Azure Portal och väljer **åter betalning**.

Om du behöver ändra din reservation av SQL Database-reserverad kapacitet till en annan region, distributionstyp, prestandanivå eller period kan du byta till en annan reservation med samma eller högre värde. Startdatumet för den nya reservationens överförs inte från den utbytte reservationen. Perioden på 1 eller 3 år startar när du skapar den nya reservationen. Till Exchange går du till reservationen i Azure Portal och väljer **Exchange**.

Mer information om hur du utbyter eller återbetalar reservationer finns i [reservations utbyte och åter betalningar](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="vcore-size-flexibility"></a>flexibilitet för vCore-storlek

vCore storleks flexibilitet hjälper dig att skala upp eller ned i en prestanda nivå och region, utan att förlora den reserverade kapacitets förmånen. SQL Database reserverad kapacitet ger också flexibiliteten att tillfälligt flytta dina aktiva databaser mellan pooler och enkla databaser som en del av din normala verksamhet (inom samma region och prestanda nivå) utan att förlora den reserverade kapaciteten lönsamhet. Genom att behålla en icke-tillämpad buffert i din reservation kan du effektivt hantera prestanda toppar utan att överskrida budgeten.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Reservations rabatten för vCore tillämpas automatiskt på antalet SQL Database instanser som SQL Database matchar omfånget och attributen för reserverade kapacitets reservationer. Du kan uppdatera omfånget för den SQL Database reserverade kapacitets reservationen via [Azure Portal](https://portal.azure.com), POWERSHELL, CLI eller via API: et.

Information om hur du hanterar reservationen för SQL Database reserverad kapacitet finns i [hantera SQL Database reserverad kapacitet](../billing/billing-manage-reserved-vm-instance.md).

Mer information om Azure Reservations finns i följande artiklar:

- [Vad är Azure Reservations?](../billing/billing-save-compute-costs-reservations.md)
- [Hantera Azure Reservations](../billing/billing-manage-reserved-vm-instance.md)
- [Förstå Azure Reservations rabatt](../billing/billing-understand-reservation-charges.md)
- [Förstå reservations användningen för din prenumeration enligt principen betala per användning](../billing/billing-understand-reserved-instance-usage.md)
- [Förstå reservations användningen för företags registreringen](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Azure Reservations i programmet Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
