---
title: Vad är Azure Reservations? | Microsoft Docs
description: Läs mer om Azure-reservationer och priser för att spara på dina virtuella datorer, SQL-databaser, Azure Cosmos DB och andra resurskostnader.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: 27b31b6dec480fee417bf9ccb241199204f28f96
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582657"
---
# <a name="what-are-azure-reservations"></a>Vad är Azure Reservations?

Azure reservationer hjälper dig att spara pengar genom att förväg betala för ett eller tre års av virtuell dator, beräkningskapacitet för SQL-databas, dataflöde för Azure Cosmos DB eller andra Azure-resurser. Betala förväg kan du få rabatt på de resurser du använder. Reservationer kan avsevärt minska den virtuella datorn, SQL database beräkning, Azure Cosmos DB, eller annan resurs kostar upp till 72% jämfört med användningsbaserad betalning. Reservationer ger en rabatt på fakturering och påverka inte körtiden för dina resurser.

Du kan köpa en reservation den [Azure-portalen](https://aka.ms/reservations). Mer information finns i följande avsnitt:

- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Betala i förskott för Azure Cosmos DB-resurser med Azure Cosmos DB reserverad kapacitet](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Varför ska jag köpa en reservation?

Om du har virtuella datorer, Azure Cosmos DB eller SQL-databaser som körs under lång tid kan ger köpa en reservation dig det billigaste alternativet. Till exempel om du kör kontinuerligt fyra instanser av en tjänst utan en reservation, debiteras du enligt priserna för användningsbaserad betalning. Om du köper en reservation för dessa resurser kan få du direkt rabatten. Resurserna är inte längre debiteras enligt priserna för användningsbaserad betalning.

## <a name="what-charges-does-a-reservation-cover"></a>Vilka avgifter fungerar en reservation cover?

- Reserverade VM-instans: En reservation täcker endast beräkningskostnader för virtuell dator. Det täcker inte ytterligare kostnader för programvara, nätverk och lagring.
- SQL Database reserverade vCore: endast beräkningskostnaderna ingår i en reservation. Licensen faktureras separat.
- Azure Cosmos DB reserverad kapacitet: en reservation omfattar dataflödet som etableras för dina resurser, de täcker inte avgifter för lagring och nätverk. 

För Windows-datorer och SQL-databas, kan du täcker licensieringskostnaderna med [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Vem är kvalificerad att köpa en reservation?

Azure-kunder med dessa typer av prenumerationer kan köpa en reservation:

- Erbjudandetyp för prenumeration på Enterprise-avtal (MS-AZR-0017P).
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)-prenumerationens erbjudandetyp (MS-AZR-003P). Du måste ha rollen ”ägare” på prenumerationen för att köpa en reservation.
- Cloud Solution Provider (CSP)-partner kan använda Azure-portalen eller [Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations) att köpa reservationer för Azure.

En reservationsrabatten gäller bara för resurser som är associerade med Enterprise, betala per användning eller CSP-prenumeration-typer.

## <a name="how-is-a-reservation-billed"></a>Hur faktureras en reservation?

Reservationen sker via den betalningsmetod som är kopplad till prenumerationen. Om du har en prenumeration på Enterprise dras reservation kostnaden från ditt åtagandebelopp. Om ditt saldo för åtagandebeloppet inte täcker kostnaden för reservationen, du debiteras överförbrukning. Om du har en prenumeration med användningsbaserad betalning debiteras då kreditkortet som du har på ditt konto omedelbart. Om du faktureras med faktura visas avgifterna på din nästa faktura.

## <a name="how-is-the-reservation-discount-applied"></a>Hur tillämpas reservationsrabatten?

Rabatten gäller för Resursanvändning som matchar de attribut som du väljer när du köper reservationen. Attributen innehåller området där de matchande virtuella datorerna, SQL-databaser, Azure Cosmos DB eller andra resurser kör. Till exempel om du vill en reservationsrabatten för fyra Standard D2 virtuella datorer i regionen USA, västra, Välj den prenumeration som där de virtuella datorerna körs. Om de virtuella datorerna körs i olika prenumerationer i din registrering/konto, väljer du området som delad. Delat omfång kan reservationsrabatten tillämpas i alla prenumerationer. Du kan ändra omfånget när du köper en reservation. Mer information finns i [hantera Azure-reservationer](billing-manage-reserved-vm-instance.md).

En reservationsrabatten gäller bara för resurser som är associerade med Enterprise, betala per användning eller CSP-prenumeration-typer. Resurser som körs i en prenumeration med andra typer av erbjudanden får inte rabatten. Enterprise Dev/Test-prenumerationer för enterprise-registreringar inte berättigar till förmånerna för reservation.

För att bättre förstå hur reservationer påverkar din fakturering, finns i följande avsnitt:

-  [Förstå Azure Reserved VM Instances rabatt](billing-understand-vm-reservation-charges.md)
- [Förstå Azure reservationsrabatten](billing-understand-vm-reservation-charges.md)
- [Förstå Azure Cosmos DB-reservationsrabatten](billing-understand-cosmosdb-reservation-charges.md)
- [Förstå Azure reservationsrabatten och användning för SUSE](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>Vad händer när reservationsperioden upphör att gälla?

I slutet av reservationsperioden fakturering rabatten upphör att gälla och den virtuella datorn, SQL database, Azure Cosmos DB eller annan resurs debiteras enligt avgifterna för betala per användning-som-du gå priset. Azure reservationer inte den automatiska förnyelsen. Om du vill fortsätta få rabatten fakturering, måste du köpa en ny reservation för berättigade tjänster och programvara.

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>Rabatten gäller för olika storlekar med flexibilitet för datorinstanser storlek

När du köper en reservation gälla rabatten andra instanser med attribut som tillhör storleksgruppen samma. Flexibiliteten i rabatt täckning beror på vilken typ av reservation och de attribut som du väljer när du köper reservationen.

- Reserverade VM-instanser: När du köper reservationen, om du väljer **optimerade för**: **instans storlek flexibilitet**, rabatt täckning beror på virtuella datorstorlek som du väljer. Reservationen kan använda för storlekar för virtuella datorer (VM) i gruppen för serien av samma storlek. Mer information finns i [VM-storlek flexibilitet med reserverade VM-instanser](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- SUSE Linux Enterprise programvaruplanen: rabatt täckning är beroende av vcpu: er för de virtuella datorerna där du kör SUSE-programvara. Mer information finns i [förstå hur SUSE Linux Enterprise software plan rabatten](billing-understand-suse-reservation-charges.md).
- SQL Database reserverad kapacitet: den rabatt täckningen beror på den prestandanivå som du väljer. Mer information finns i [förstå hur ett Azure reservationsrabatten tillämpas](billing-understand-reservation-charges.md).
- Azure Cosmos DB reserverad kapacitet: den rabatt täckningen beror på det etablerade dataflödet. Mer information finns i [förstå hur ett Azure Cosmos DB-reservationsrabatten tillämpas](billing-understand-cosmosdb-reservation-charges.md).

## <a name="next-steps"></a>Nästa steg

Starta sparas på dina virtuella datorer genom att köpa en [reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md), [SQL Database reserverad kapacitet](../sql-database/sql-database-reserved-capacity.md), eller [Azure Cosmos DB reserverad kapacitet](../cosmos-db/cosmos-db-reserved-capacity.md).

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)
- [Azure reservationer i programmet för Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
