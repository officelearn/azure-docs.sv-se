---
title: Vad är Azure reservationer? | Microsoft Docs
description: Läs mer om Azure-reservationer och priser för att spara på dina virtuella datorer, SQL-databaser och lägga pengar på andra resurser.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 93c11852a11e0bb57a0b92090368298fc14b8c2a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626312"
---
# <a name="what-are-azure-reservations"></a>Vad är Azure reservationer?

Azure reservationer kan du spara pengar genom att förväg betala för ett eller tre års av virtuell dator eller SQL-databas beräkningskapacitet. Betala förväg kan du få rabatt på de resurser du använder. Azure reservationer kan avsevärt minska din virtuella datorn eller beräkningskostnaderna för SQL-databas – upp till 72% jämfört med användningsbaserad betalning – med ett eller tre år i förskott. reservationer ger en rabatt på fakturering och påverkar inte körtiden för dina virtuella datorer eller en SQL-databaser.

Du kan köpa en reservation den [Azure-portalen](https://aka.ms/reservations). Mer information finns i följande avsnitt:

- [Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Betala i förskott för beräkningsresurser för SQL-databas med Azure SQL Database reserverad kapacitet](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Varför ska jag köpa en reservation?

Om du har virtuella datorer eller SQL-databaser som körs under lång tid kan ger köpa en reservation dig det billigaste alternativet. Till exempel om du kör kontinuerligt fyra instanser av en tjänst utan en reservation, debiteras du enligt priserna för användningsbaserad betalning. Om du köper en reservation för dessa resurser kan få du direkt rabatten. Resurserna är inte längre debiteras enligt priserna för användningsbaserad betalning.

## <a name="what-charges-does-a-reservation-cover"></a>Vilka avgifter fungerar en reservation cover?

- Reserverade VM-instans: En reservation täcker endast beräkningskostnader för virtuell dator. Det täcker inte ytterligare kostnader för programvara, nätverk och lagring.
- SQL Database reserverade vCore: endast beräkningskostnaderna ingår i en reservation. Licensen faktureras separat.

För Windows-datorer och SQL-databas, kan du täcker licensieringskostnaderna med [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Vem är kvalificerad att köpa en reservation?

Azure-kunder med dessa typer av prenumerationer kan köpa en reservation:

- Erbjudandetyp för prenumeration på Enterprise-avtal (MS-AZR-0017P).
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)-prenumerationens erbjudandetyp (MS-AZR-003P). Du måste ha rollen ”ägare” på prenumerationen för att köpa en reservation. Om du vill köpa reservationer i en enterprise-registrering, måste enterprise-administratör aktivera köp av reservation i EA-portalen. Som standard är denna inställning aktiverad.
- Cloud Solution Provider (CSP)-partner kan använda Azure-portalen eller [Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations) att köpa Azure reservationer. 

En rabatt på Azure reservation gäller endast för virtuella datorer eller SQL-databaser som är associerade med Enterprise, betala per användning eller CSP prenumerationstyper.

## <a name="how-is-a-reservation-billed"></a>Hur faktureras en reservation?

Reservationen sker via den betalningsmetod som är kopplad till prenumerationen. Om du har en prenumeration på Enterprise dras reservation kostnaden från ditt åtagandebelopp. Om ditt saldo för åtagandebeloppet inte täcker kostnaden för reservationen, du debiteras överförbrukning. Om du har en prenumeration med användningsbaserad betalning debiteras då kreditkortet som du har på ditt konto omedelbart. Om du faktureras med faktura visas avgifterna på din nästa faktura.

## <a name="how-is-the-reservation-discount-applied"></a>Hur tillämpas reservationsrabatten?

Rabatten gäller för Resursanvändning som matchar de attribut som du väljer när du köper reservationen. Attributen innehåller omfånget där de matchande virtuella datorerna, SQL-databaser eller andra resurser har körts. Till exempel om du vill en reservationsrabatten för fyra Standard D2 virtuella datorer i regionen USA, västra, Välj den prenumeration som där de virtuella datorerna körs. Om de virtuella datorerna körs i olika prenumerationer i din registrering/konto, väljer du området som delad. Delat omfång kan reservationsrabatten tillämpas i alla prenumerationer. Du kan ändra omfånget när du köper en reservation. Mer information finns i [hantera reservationer i Azure](billing-manage-reserved-vm-instance.md).

Rabatten gäller endast för virtuella datorer eller SQL-databaser som är associerade med enterprise eller användningsbaserad betalning typer. Virtuella datorer eller SQL-databaser som körs i en prenumeration med andra typer av erbjudanden får inte rabatten. Enterprise Dev/Test-prenumerationer för enterprise-registreringar inte berättigar till förmånerna för reservation.

För att bättre förstå hur reservationer påverkar din virtuell dator eller en SQL-databas fakturering, se [förstå hur reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reservation-term-expires"></a>Vad händer när reservationsperioden upphör att gälla?

I slutet av reservationsperioden fakturering rabatten upphör att gälla och den virtuella datorn, SQL-databas eller andra resurser faktureras till betala per användning-som-du gå pris. Azure reservationer inte den automatiska förnyelsen. Om du vill fortsätta få rabatten fakturering, måste du köpa en ny reservation för reservation berättigade tjänster.

## <a name="next-steps"></a>Nästa steg

Starta sparas på dina virtuella datorer genom att köpa en [reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md) eller [SQL Database reserverad kapacitet](../sql-database/sql-database-reserved-capacity.md).

Mer information om reservationer, finns i följande artiklar:

- [Hantera Azure-reservationer](billing-manage-reserved-vm-instance.md)
- [Förstå hur reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)
- [Azure reservationer i programmet för Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta support

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
