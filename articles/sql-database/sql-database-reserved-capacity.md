---
title: Betala i förskott för Azure SQL Database virtuella kärnor att spara pengar | Microsoft Docs
description: Lär dig hur du köper Azure SQL Database reserverad kapacitet för att spara dina beräkningskostnader.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: ec9bd3ee106571484c513c2d005a374a90c1d17e
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359718"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Betala i förskott för beräkningsresurser för SQL-databas med Azure SQL Database reserverad kapacitet

Spara pengar med Azure SQL Database genom förskottsbetalar för beräkningsresurser jämfört med priserna för användningsbaserad betalning. Med Azure SQL Database reserverad kapacitet gör du en i förskott på SQL-databas under en period av ett eller tre år för att få bra rabatter på beräkningskostnaderna. Om du vill köpa SQL Database reserverad kapacitet, måste du ange Azure-region, typen av distribution, prestandanivån och period.

Du behöver inte tilldela reservationen till specifika SQL Database-instanser (enskilda databaser, elastiska pooler eller hanterade instanser). Matchning av SQL Database-instanser som redan körs eller som nyligen har distribuerats, automatiskt får fördelen. Genom att köpa en reservation, är du förskott för beräkningskostnaderna under en period av ett eller tre år. När du köper en reservation går SQL-databasen beräkningsrelaterade avgifter som matchar reservationen attribut debiteras inte längre med betala per användning-som-du priser. En reservation omfattar inte programvara-, nätverks- eller kostnader som är associerade med SQL Database-instansen. I slutet av reservationsperioden faktureringsförmånen upphör att gälla och SQL-databaser debiteras enligt användningsbaserad-som-du gå priset. Reservationer inte den automatiska förnyelsen. Information om priser finns i den [SQL Database reserverad kapacitet erbjudande](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Du kan köpa Azure SQL Database reserverad kapacitet den [Azure-portalen](https://portal.azure.com). Reserverad kapacitet om du vill köpa SQL Database:

- Du måste vara i rollen ägare för minst en Enterprise eller användningsbaserad betalning.
- För Enterprise-prenumerationer, **lägga till reserverade instanser** måste aktiveras i den [EA-portalen](https://ea.azure.com). Eller, om den här inställningen har inaktiverats kan du måste vara en EA-administratör för prenumerationen.
- För Cloud Solution Provider (CSP)-programmet kan endast admin-agenter eller försäljning agents köpa SQL Database reserverad kapacitet.

Information om hur företagskunder och kunder med användningsbaserad betalning debiteras för reservation inköp i [förstå användningen av Azure reservation för Enterprise-registreringen](../billing/billing-understand-reserved-instance-usage-ea.md) och [förstå Azure reservation användning för prenumerationen med användningsbaserad betalning](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Fastställa lämplig storlek enligt SQL före köp

Storleken på reservationen ska baseras på den totala mängden beräkning som används av den befintliga eller snart-till--distribueras enskilda databaser eller elastiska pooler hanterade instanser i en viss region och använda samma prestanda nivå och maskinvara generation.

Låt oss anta att du kör en generell användning, Gen5 – 16 vCore elastisk pool, och två affärskritisk, Gen5 – 4 vCore enskilda databaser. Dessutom kan anta vi att du planerar att distribuera under nästa månad en ytterligare generell användning, Gen5 – 16 vCore elastisk pool, och en affärskritisk, Gen5 – 32 vCore elastisk pool. Anta också att du vet att du behöver dessa resurser för minst 1 år. I det här fallet ska du köpa en 32 virtuella kärnor (2 x 16), 1 år reservation för enkel databas/elastisk pool generella - Gen5 och ett 40 (2 x 4 + 32) vCore 1 år reservation för enkel databas/elastisk pool affärskritisk - Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Köpa SQL Database reserverad kapacitet

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj **Lägg till** och välj sedan i fönstret Välj produkttyp **SQL Database** att köpa en ny reservation för SQL-databas.
4. Fyll i fälten som krävs. Befintliga eller nya enskilda databaser, elastiska pooler eller hanterade instanser som matchar de attribut som du väljer är du berättigad att hämta rabatten för reserverad kapacitet. Det faktiska antalet dina SQL Database-instanser som får rabatten är beroende av omfång och kvantitet som väljs.

   ![Skärmbild innan du skickar in SQL-databasen reserverad kapacitet köp](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Fält      | Beskrivning|
    |:------------|:--------------|
    |Namn        |Namnet på den här reservationen.|
    |Prenumeration|Den prenumeration som används för att betala för SQL Database reserverad kapacitetsreservationen. Betalningsmetoden för prenumerationen debiteras startavgifter reservationens SQL-databas som reserverad kapacitet. Prenumerationstypen måste vara ett enterprise-avtal (erbjuder siffror: MS-AZR-0017P eller MS-AZR - 0148 P) eller betala per användning (erbjuder siffror: MS-AZR-0003P eller MS-AZR - 0023 P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.|
    |Scope       |VCore reservationsomfånget kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <br/><br/>**Enstaka prenumeration** -vCore rabatten gäller för SQL Database-instanser i den här prenumerationen. <br/><br/>**Delade prenumeration** -vCore rabatten gäller för SQL Database-instanser som körs i alla prenumerationer i din faktureringskontexten. För företagskunder, den delade omfattningen registreringen och innehåller alla prenumerationer i registreringen. För kunder med användningsbaserad betalning är den delade omfattningen alla betala per användning-prenumerationer som skapas av kontoadministratören.|
    |Region      |Azure-regionen som omfattas av SQL-databasen reserverad kapacitet reservation.|
    |Distributionstyp|Den SQL-resurstyp som du vill köpa reservationer för.|
    |Prestandanivå|Tjänstnivå för SQL Database-instanser.
    |Period        |Ett eller tre år.|
    |Kvantitet    |Antalet instanser som köpts i SQL-databasen reserverad kapacitet reservation. Antalet är antalet körda SQL Database-instanser som kan få fakturering rabatt. Till exempel om du kör 10 SQL Database-instanser i östra USA, skulle du ange antalet som 10 om du vill maximera förmån för alla datorer som körs. |
    |||

5. Granska kostnaden för SQL-databasen reserverad kapacitetsreservationen i den **kostnader** avsnittet.
6. Välj **Köp**.
7. Välj **visa den här reservationen** att se status för ditt köp.

## <a name="cancellations-and-exchanges"></a>Avbokningar och utbyten

Om du behöver reserverad kapacitet reservation att avbryta din SQL-databas, det kan finnas 12% uppsägningsavgift. Återbetalningar baseras på det lägsta priset – antingen ditt inköpspris eller det aktuella priset för reservationen. Återbetalningar begränsas till 50 000 USD per år. Din återbetalning består av det återstående proportionellt beräknade saldot minus uppsägningsavgiften på 12 %. Om du vill begära ett avbrott, går du till reservationen i Azure-portalen och välj **återbetala** att skapa en supportbegäran.

Om du behöver ändra din reservation av SQL Database-reserverad kapacitet till en annan region, distributionstyp, prestandanivå eller period kan du byta till en annan reservation med samma eller högre värde. Startdatumet för den nya reservationens överförs inte från den utbytte reservationen. Perioden på 1 eller 3 år startar när du skapar den nya reservationen. Om du vill begära en exchange, gå till reservationen i Azure-portalen och välj **Exchange** att skapa en supportbegäran.

Läs mer om hur du exchange- eller återbetalning reservationer [Reservation utbyte och återbetalningar](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="vcore-size-flexibility"></a>flexibilitet för vCore-storlek

flexibilitet för vCore-storlek kan du skala upp eller ned inom en prestandanivå och region, utan att förlora fördelen reserverad kapacitet. SQL-databas som reserverad kapacitet ger dig flexibiliteten att tillfälligt flytta dina frekvent databaser mellan pooler och enskilda databaser som en del av din normal drift (inom samma region och prestanda nivå) utan att förlora reserverad kapacitet Dra. Du kan effektivt hantera prestanda toppar utan överskrider din budget genom att lagra en icke tillämpade buffert i din reservation.

## <a name="next-steps"></a>Nästa steg

VCore-reservationsrabatten tillämpas automatiskt på hur många SQL Database-instanser som matchar reservationsomfånget för SQL Database reserverad kapacitet och attribut. Du kan uppdatera reservationens omfång i SQL Database reserverad kapacitet via [Azure-portalen](https://portal.azure.com), PowerShell, CLI eller via API: et.

Lär dig hur du hanterar SQL-databas reserverad kapacitet reservation, se [hantera SQL-databas som reserverad kapacitet](../billing/billing-manage-reserved-vm-instance.md).

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure Reservations?](../billing/billing-save-compute-costs-reservations.md)
- [Hantera Azure-reservationer](../billing/billing-manage-reserved-vm-instance.md)
- [Förstå Azure reservationer rabatt](../billing/billing-understand-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](../billing/billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Azure reservationer i programmet för Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
