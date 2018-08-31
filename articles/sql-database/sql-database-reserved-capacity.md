---
title: Betala i förskott för Azure SQL Database virtuella kärnor att spara pengar | Microsoft Docs
description: Lär dig hur du köper Azure SQL Database reserverad kapacitet för att spara dina beräkningskostnader.
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: carlrab
ms.openlocfilehash: fcb7f2c1bb3e1653b9f8112abc0effaddc45fa54
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306477"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Betala i förskott för beräkningsresurser för SQL-databas med Azure SQL Database reserverad kapacitet

Spara pengar med Azure SQL Database genom förskottsbetalar för beräkningsresurser i Azure SQL Database jämfört med priserna för användningsbaserad betalning. Med Azure SQL Database reserverad kapacitet gör du en i förskott på SQL-databas under en period av ett eller tre år för att få bra rabatter på beräkningskostnaderna. Om du vill köpa SQL Database reserverad kapacitet, måste du ange Azure-region, typen av distribution, tjänst och period. 

Du behöver inte tilldela reservationen till SQL Database-instanser. Matchning av SQL Database-instanser som redan körs eller som nyligen har distribuerats, automatiskt får fördelen. Genom att köpa en reservation, är du förskott för beräkningskostnaderna för SQL Database-instanser för ett eller tre år. När du köper en reservation går SQL-databasen beräkningsrelaterade avgifter som matchar reservationen attribut debiteras inte längre med betala per användning-som-du priser. En reservation omfattar inte programvara-, nätverks- eller kostnader som är associerade med SQL Database-instansen. I slutet av reservationsperioden faktureringsförmånen upphör att gälla och SQL-databaser debiteras enligt användningsbaserad-som-du gå priset. Reservationer inte den automatiska förnyelsen. Information om priser finns i den [SQL Database reserverad kapacitet erbjudande](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Du kan köpa Azure SQL Database reserverad kapacitet den [Azure-portalen](https://portal.azure.com). Reserverad kapacitet om du vill köpa SQL Database:
- Du måste vara i rollen ägare för minst en Enterprise eller användningsbaserad betalning.
- För Enterprise-prenumerationer Azure reservation inköp måste aktiveras i den [EA-portalen](https://ea.azure.com).
-  För Cloud Solution Provider (CSP)-programmet kan endast admin-agenter eller försäljning agents köpa SQL Database reserverad kapacitet.

Information om hur företagskunder och kunder med användningsbaserad betalning debiteras för reservation köp täcks i [vanliga frågor och svar](#frequently-asked-questions).

## <a name="determine-the-right-sql-size-before-purchase"></a>Fastställa lämplig storlek enligt SQL före köp

Storleken på reservationen ska baseras på den totala mängden beräkning som används av befintliga eller snart-till--distribueras SQL enskilda databaser och/eller elastiska pooler på en specifik region och använda samma prestanda nivå och maskinvara generation. 

Låt oss anta att du kör en generell användning, Gen5 – 16 vCore elastisk pool, och två affärskritisk, Gen5 – 4 vCore enskilda databaser. Dessutom kan anta vi att du planerar att distribuera under nästa månad en ytterligare generell användning, Gen5 – 16 vCore elastisk pool, och en affärskritisk, Gen5 – 32 vCore elastisk pool. Anta också att du vet att du behöver dessa resurser för minst 1 år. I det här fallet ska du köpa en 32 virtuella kärnor (2 x 16), 1 år reservation för SQL Database enkel/elastisk Pool för generell användning – Compute Gen5 och ett 40 (2 x 4 + 32) vCore 1 år reservation för SQL Database enkel/elastisk Pool affärskritisk - Compute Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Köpa SQL Database reserverad kapacitet

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** > **reservationer**.
3. Välj **Lägg till** och välj sedan i fönstret Välj produkttyp **SQL Database** att köpa en ny reservation för SQL-databas.
4. Fyll i fälten som krävs. Befintliga eller nya enskilda databaser eller elastiska pooler som matchar de attribut som du väljer kvalificera sig för att få rabatt för reserverad kapacitet. Det faktiska antalet dina SQL Database-instanser som får rabatten är beroende av omfång och kvantitet som väljs.

   ![Skärmbild innan du skickar in SQL-databasen reserverad kapacitet köp](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Fält      | Beskrivning|
    |:------------|:--------------|
    |Namn        |Namnet på den här reservationen.| 
    |Prenumeration|Den prenumeration som används för att betala för SQL Database reserverad kapacitetsreservationen. Betalningsmetoden för prenumerationen debiteras startavgifter reservationens SQL-databas som reserverad kapacitet. Prenumerationstypen måste vara ett företagsavtal (erbjudandets nummer: MS-AZR-0017P) eller Betala per användning (erbjudandets nummer: MS-AZR-0003P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.|    
    |Omfång       |VCore reservationsomfånget kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>Enstaka prenumeration - vCore reservationsrabatten tillämpas på SQL Database-instanser i den här prenumerationen. </li><li>Delad – tillämpas vCore reservationsrabatten på SQL Database-instanser som körs i alla prenumerationer i din faktureringskontexten. För företagskunder, den delade omfattningen registreringen och innehåller alla prenumerationer (utom prenumerationer för utveckling och testning) i registreringen. För kunder med användningsbaserad betalning är den delade omfattningen alla betala per användning-prenumerationer som skapas av kontoadministratören.</li></ul>|
    |Region      |Azure-regionen som omfattas av SQL-databasen reserverad kapacitet reservation.|    
    |Distributionstyp|SQL-distributionstyp som du vill köpa reservationer för.|
    |Servicenivå|Tjänstnivå för SQL Database-instanser.
    |Period        |Ett eller tre år.|
    |Antal    |Antalet instanser som köpts i SQL-databasen reserverad kapacitet reservation. Antalet är antalet körda SQL Database-instanser som kan få fakturering rabatt. Till exempel om du kör 10 SQL Database-instanser i östra USA, skulle du ange antalet som 10 om du vill maximera förmån för alla datorer som körs. |

5. Granska kostnaden för SQL-databasen reserverad kapacitetsreservationen i den **kostnader** avsnittet.
6. Välj **Köp**.
7. Välj **visa den här reservationen** att se status för ditt köp.

## <a name="next-steps"></a>Nästa steg 
VCore-reservationsrabatten tillämpas automatiskt på hur många SQL Database-instanser som matchar reservationsomfånget för SQL Database reserverad kapacitet och attribut. Du kan uppdatera reservationens omfång i SQL Database reserverad kapacitet via [Azure-portalen](https://portal.azure.com), PowerShell, CLI eller via API: et. 

Lär dig hur du hanterar SQL-databas reserverad kapacitet reservation, se [hantera SQL Database reserverad kapacitet](../billing/billing-manage-reserved-vm-instance.md).

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](../billing/billing-save-compute-costs-reservations.md)
- [Hantera Azure-reservationer](../billing/billing-manage-reserved-vm-instance.md)
- [Förstå Azure reservationer rabatt](../billing/billing-understand-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](../billing/billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Azure reservationer i programmet för Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

