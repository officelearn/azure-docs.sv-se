---
title: Vad är Azure Reservations? | Microsoft Docs
description: Läs mer om Azure-reservationer och priser för att spara på dina virtuella datorer, SQL-databaser, Azure Cosmos DB och andra resurskostnader.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b20983c45cd62b9812cdb52de32a6e29da459efe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370806"
---
# <a name="what-are-azure-reservations"></a>Vad är Azure Reservations?

Azure reservationer hjälpa dig att spara pengar genom att förväg betala för ett år eller tre års virtuella datorer, SQL-databas för beräkning kapacitet, dataflöde för Azure Cosmos DB eller andra Azure-resurser. Betala förväg kan du få rabatt på de resurser du använder. Reservationer kan avsevärt minska den virtuella datorn, SQL database beräkning, Azure Cosmos DB, eller annan resurs kostar upp till 72% jämfört med användningsbaserad betalning. Reservationer ger en rabatt på fakturering och påverka inte körtiden för dina resurser.

Du kan köpa en reservation den [Azure-portalen](https://aka.ms/reservations).

## <a name="why-buy-a-reservation"></a>Varför ska jag köpa en reservation?

Om du har virtuella datorer, Azure Cosmos DB eller SQL-databaser som körs under lång tid kan ger köpa en reservation dig det billigaste alternativet. Till exempel att när du kör kontinuerligt fyra instanser av en tjänst utan en reservation, kommer du att debiteras enligt priserna för användningsbaserad betalning. Om du köper en reservation för dessa resurser kan få du direkt rabatten. Resurserna är inte längre debiteras enligt priserna för användningsbaserad betalning.

## <a name="charges-covered-by-reservation"></a>Avgifter som täcks av reservation

Service-planer:

- Reserverade VM-instans: En reservation täcker endast beräkningskostnader för virtuell dator. Det täcker inte ytterligare kostnader för programvara, nätverk och lagring.
- Azure Cosmos DB reserverad kapacitet: En reservation täcker dataflödet som etableras för dina resurser. Det täcker inte lagring och nätverk avgifter.
- SQL Database reserverade vCore: Endast beräkningskostnaderna ingår i en reservation. Licensen faktureras separat.

För Windows-datorer och SQL-databas, kan du täcker licensieringskostnaderna med [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Vem är kvalificerad att köpa en reservation?

Du måste ha en prenumeration ägarrollen i ett företag (MS-AZR - 0017P eller MS-AZR - 0148P) eller en prenumeration med användningsbaserad betalning (MS-AZR - 003P eller MS-AZR - 0023P) om du vill köpa en plan. Cloud solution providers kan använda Azure-portalen eller [Partnercenter](/partner-center/azure-reservations) att köpa reservationer för Azure.

EA-kunder kan begränsa inköp till EA-administratörer genom att inaktivera den **lägga till reserverade instanser** alternativ i EA-portalen. EA-administratörer måste vara prenumerationsägare för minst en EA-prenumeration att köpa en reservation. Alternativet är användbart för företag som vill ha en centraliserad team att köpa reservationer för olika kostnadsställen. Efter köpet, centraliserad team kan lägga till kostnaden center ägare reservationerna. Ägare kan sedan begränsa reservationen prenumerationerna. Centralt team behöver inte ha ägaråtkomst för prenumerationen där reservationen köps.

En reservationsrabatten gäller bara för resurser som är associerade med Enterprise, betala per användning eller CSP-prenumeration-typer.

## <a name="reservation-scope"></a>Reservationsomfånget

Ett omfång för reservation bestämmer de resurser som rabatten gäller för. Ett omfång för reservation kan ha följande värden:

**Dela omfattning** -rabatten gäller för matchande resurserna i prenumerationer som kan användas i kontexten för fakturering.

- För Enterprise Agreement-kunder är faktureringskontexten registreringen.
 För kunder som betalar per användning är fakturering omfattningen alla berättigade prenumerationer som skapats av kontoadministratören.

**Enstaka prenumeration** -rabatten gäller för de matchande resurserna i den valda prenumerationen.

Du kan [uppdatera omfattningen när du köper en reservation](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

## <a name="discounted-subscription-and-offer-types"></a>Rabatterade prenumerationen och typer av erbjudanden

Reservation rabatter gäller följande berättigade prenumerationer och erbjuder typer.

- Enterprise-avtal (erbjuder siffror: MS-AZR-0017P eller MS-AZR - 0148 P)
- Betala per användning (erbjuder siffror: MS-AZR-0003P eller MS-AZR - 0023 P)
- CSP-prenumerationer

Resurser som körs i en prenumeration med andra typer av erbjudanden får inte rabatten.

## <a name="how-is-a-reservation-billed"></a>Hur faktureras en reservation?

Reservationen sker via den betalningsmetod som är kopplad till prenumerationen. Om du har en prenumeration på Enterprise dras reservation kostnaden från ditt åtagandebelopp. Om ditt saldo för åtagandebeloppet inte täcker kostnaden för reservationen, du debiteras överförbrukning. Om du har en prenumeration med användningsbaserad betalning debiteras då kreditkortet som du har på ditt konto omedelbart. Om du faktureras med faktura visas avgifterna på din nästa faktura.

## <a name="how-reservation-discount-is-applied"></a>Hur reservationsrabatten tillämpas

Rabatten gäller för Resursanvändning som matchar de attribut som du väljer när du köper reservationen. Attributen innehåller området där de matchande virtuella datorerna, SQL-databaser, Azure Cosmos DB eller andra resurser kör. Till exempel om du vill en reservationsrabatten för fyra Standard D2 virtuella datorer i regionen USA, västra, välj sedan den prenumeration där de virtuella datorerna körs.

En reservationsrabatten är ”*användning – it-eller-förlora-it*”. Om du inte behöver matcha resurser för en timme, sedan förlora en Reservationskvantitet för den timmen. Du kan inte utföra vidarebefordra oanvända reserverade timmar.

När du stänger en resurs kan tillämpas reservationsrabatten automatiskt till en annan matchande resurs i det specificerade omfånget. Om inga matchande resurser finns i det specificerade omfånget så är de reserverade timmarna *förlorad*.

Du kan senare skapa en resurs och har en matchande reservation som är underutnyttjade. I det här exemplet gäller reservationsrabatten automatiskt till den nya matchande resursen.

Om de virtuella datorerna körs i olika prenumerationer i din registrering/konto, väljer du området som delad. Delat omfång kan reservationsrabatten tillämpas i alla prenumerationer. Du kan ändra omfånget när du köper en reservation. Mer information finns i [hantera Azure-reservationer](billing-manage-reserved-vm-instance.md).

En reservationsrabatten gäller bara för resurser som är associerade med Enterprise, betala per användning eller CSP-prenumeration-typer. Resurser som körs i en prenumeration med andra typer av erbjudanden får inte rabatten.

## <a name="when-the-reservation-term-expires"></a>När reservationsperioden upphör att gälla

I slutet av reservationsperioden fakturering rabatten upphör att gälla och den virtuella datorn, SQL database, Azure Cosmos DB eller annan resurs debiteras enligt avgifterna för betala per användning-som-du gå priset. Azure reservationer inte den automatiska förnyelsen. Om du vill fortsätta få rabatten fakturering, måste du köpa en ny reservation för berättigade tjänster och programvara.

## <a name="discount-applies-to-different-sizes"></a>Rabatten gäller för olika storlekar

När du köper en reservation gälla rabatten andra instanser med attribut som tillhör storleksgruppen samma. Den här funktionen kallas instans storlek flexibilitet. Flexibiliteten i rabatt täckning beror på vilken typ av reservation och de attribut som du väljer när du köper reservationen.

Service-planer:

- Reserverade VM-instanser: När du köper reservationen och välj **optimerade för**: **instans storlek flexibilitet**, rabatt täckning beror på virtuella datorstorlek som du väljer. Reservationen kan använda för storlekar för virtuella datorer (VM) i gruppen för serien av samma storlek. Mer information finns i [VM-storlek flexibilitet med reserverade VM-instanser](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- SQL Database reserverad kapacitet: Rabatt täckning beror på den prestandanivå som du väljer. Mer information finns i [förstå hur ett Azure reservationsrabatten tillämpas](billing-understand-reservation-charges.md).
- Azure Cosmos DB reserverad kapacitet: Rabatt-täckning är beroende av det etablerade dataflödet. Mer information finns i [förstå hur ett Azure Cosmos DB-reservationsrabatten tillämpas](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure reservationer i följande artiklar:
    - [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
    - [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
    - [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
    - [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)
    - [Azure reservationer i programmet för Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

- Läs mer om reservationer för service-planer:
    - [Virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB-resurser med Azure Cosmos DB reserverad kapacitet](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [SQL Database-beräkningsresurser med Azure SQL Database reserverad kapacitet](../sql-database/sql-database-reserved-capacity.md) mer information om reservationer för programvaruplaner:
    - [Red Hat programvaruplaner från Azure-reservationer](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [SUSE programvaruplaner från Azure-reservationer](../virtual-machines/linux/prepay-suse-software-charges.md)
