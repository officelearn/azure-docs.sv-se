---
title: Spara kostnader
description: Lär dig hur du köper reserverad azure SQL-databaskapacitet för att spara på dina beräkningskostnader.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979979"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Spara kostnader för beräkningsresurser för SQL Database med reserverad azure SQL-databaskapacitet

Spara pengar med Azure SQL Database genom att åta sig en reservation för beräkningsresurser jämfört med priser för betalning per steg. Med reserverad kapacitet för Azure SQL Database gör du ett åtagande för SQL Database-användning under en period av ett eller tre år för att få en betydande rabatt på beräkningskostnaderna. Om du vill köpa reserverad kapacitet för SQL-databasen måste du ange Azure-region, distributionstyp, prestandanivå och term.


Du behöver inte tilldela reservationen till specifika SQL Database-instanser (enskilda databaser, elastiska pooler eller hanterade instanser). Matchande SQL Database-instanser, som redan körs eller de som nyligen har distribuerats, får automatiskt förmånen. Genom att köpa en bokning förbinder du dig att använda för beräkningskostnaderna för en period av ett eller tre år. Så fort du köper en reservation debiteras inte längre beräkningsavgifterna för SQL Database som matchar reservationsattributen enligt priserna för er för att betala i taget. En reservation omfattar inte programvaru-, nätverks- eller lagringsavgifter som är associerade med SQL Database-instansen. I slutet av bokningsperioden upphör faktureringsförmånen och SQL-databaserna faktureras till priset för betalning. Bokningar förnyas inte automatiskt. Prisinformation finns i erbjudandet om reserverad kapacitet för [SQL-databas](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Du kan köpa reserverad azure SQL-databaskapacitet i [Azure-portalen](https://portal.azure.com). Betala för reservationen [i förväg eller via månadsbetalningar](../cost-management-billing/reservations/monthly-payments-reservations.md). Så här köper du reserverad kapacitet för SQL Database:

- Du måste vara i ägarrollen för minst en Enterprise eller enskild prenumeration med abonnemang som gäller.
- För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com). Om den inställningen är inaktiverad måste du också vara EA-administratör för prenumerationen.
- För CSP-program (Cloud Solution Provider) kan endast administratörsagenter eller försäljningsagenter köpa reserverad kapacitet för SQL Database.

Information om hur företagskunder och pay-as-you-go-kunder debiteras för bokningsköp, se [förstå Azure-reservationsanvändning för din Enterprise-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) och [förstå Azure-reservationsanvändning för din Pay-As-You-Go-prenumeration](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Bestäm rätt SQL-storlek före köp

Storleken på reservationen bör baseras på den totala mängden beräkning som används av befintliga eller snart distribuerade enskilda databaser, elastiska pooler eller hanterade instanser inom en viss region och med samma prestandanivå och maskinvarugenerering.

Anta till exempel att du kör ett allmänt syfte, Gen5 – 16 vCore elastisk pool och två affärskritiska, Gen5 – 4 vCore-enskilda databaser. Anta vidare att du planerar att distribuera ytterligare ett allmänt syfte inom den närmaste månaden, Gen5 – 16 vCore elastisk pool och en affärskritisk, Gen5 – 32 vCore elastisk pool. Anta också att du vet att du behöver dessa resurser i minst 1 år. I det här fallet bör du köpa en 32 (2x16) vCores, 1 års bokning för enkel databas / elastisk pool allmänt ändamål - Gen5 och en 40 (2x4 + 32) vCore 1 års bokning för enda databas / elastisk pool verksamhet kritisk - Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Köp reserverad kapacitet för SQL Database

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj Alla > **servicereservationer**. **All services**
3. Välj **Lägg till** och välj sedan SQL **Database** för att köpa en ny reservation för SQL Database i fönstret Inköpsreservation.
4. Fyll i de obligatoriska fälten. Befintliga eller nya enskilda databaser, elastiska pooler eller hanterade instanser som matchar de attribut du väljer kvalificerar sig för att få rabatt med reserverad kapacitet. Det faktiska antalet SQL Database-instanser som får rabatten beror på omfattningen och kvantiteten som valts.
    ![Skärmbild innan sql-databasens inköp av reserverad kapacitet skickas](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

I följande tabell beskrivs obligatoriska fält.

| Field      | Beskrivning|
|------------|--------------|
|Prenumeration|Prenumerationen som används för att betala för sql-databasen reserverad kapacitetsreservation. Betalningsmetoden för prenumerationen debiteras initiala kostnader för sql-databasreserverad kapacitetsreservation. Prenumerationstypen måste vara ett företagsavtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett individuellt avtal med betalning per användning -prissättning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. För en enskild prenumeration med priser för användningsbaserad betalning debiteras avgifterna till kreditkorts- eller fakturabetalningsmetoden för prenumerationen.|
|Omfång       |VCore-reservationens omfattning kan omfatta en prenumeration eller flera prenumerationer (delat scope). Om du väljer: <br/><br/>**Delad**tillämpas vCore-reservationsrabatten på SQL Database-instanser som körs i prenumerationer i din faktureringskontext. För företagskunder är det delade omfånget registreringen och inkluderar alla prenumerationer i registreringen. För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat.<br/><br/>**Enkel prenumeration**tillämpas vCore-reservationsrabatten på SQL Database-instanser i den här prenumerationen. <br/><br/>**Enskild resursgrupp**tillämpas reservationsrabatten på SQL Database-instanser i den valda prenumerationen och den valda resursgruppen i den prenumerationen.|
|Region      |Azure-regionen som omfattas av SQL Database reserverad kapacitetsreservation.|
|Distributionstyp|Den SQL-resurstyp som du vill köpa reservationen för.|
|Prestandanivå|Tjänstnivån för SQL Database-instanserna.
|Period        |Ett år eller tre år.|
|Kvantitet    |Mängden beräkningsresurser som köps inom sql-databasen reserverad kapacitet reservation. Kvantiteten är ett antal virtuella kärnor i den valda Azure-regionen och prestandanivån som reserveras och får faktureringsrabatten. Om du till exempel kör eller planerar att köra SQL Database-instanser med den totala beräkningskapaciteten för Gen5 16-virtuella kärnor i regionen östra USA, anger du kvantitet som 16 för att maximera förmånen för alla instanser. |

1. Granska kostnaden för sql-databasen reserverad kapacitetsreservation i avsnittet **Kostnader.**
1. Välj **Köp**.
1. Välj **Visa den här reservationen** om du vill se status för ditt köp.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>vCore storlek flexibilitet

vCore storlek flexibilitet hjälper dig att skala upp eller ner inom en prestandanivå och region, utan att förlora den reserverade kapacitet förmån. Reserverad kapacitet för SQL-databas ger dig också flexibiliteten att tillfälligt flytta dina heta databaser mellan pooler och enskilda databaser som en del av din normala verksamhet (inom samma region och prestandanivå) utan att förlora den reserverade kapaciteten Fördel. Genom att behålla en buffert som inte tillämpas i din bokning kan du effektivt hantera prestandatopparna utan att överskrida din budget.

## <a name="limitation"></a>Begränsning

Du kan inte reservera DTU-baserade (grundläggande, standard- eller premium) SQL-databaser.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

VCore-reservationsrabatten tillämpas automatiskt på antalet SQL Database-instanser som matchar SQL Database reserverad kapacitetsreservation och attribut. Du kan uppdatera omfattningen av sql-databasreserverad kapacitetsreservation via [Azure Portal](https://portal.azure.com), PowerShell, CLI eller via API:et.

Mer information om hur du hanterar reservationen för reserverad kapacitet för SQL-databas finns i [hantera reserverad kapacitet för SQL-databas](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure-reservationer?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Hantera Azure-reservationer](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Förstå reservationsrabatter i Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)
