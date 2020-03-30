---
title: Så tillämpas en Azure-reservationsrabatt
description: Den här artikeln förklarar hur rabatter för reserverade instanser tillämpas generellt.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: fb6e7e3fe3b7b45b503dca66dc5b039a9eceb689
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80135495"
---
# <a name="how-a-reservation-discount-is-applied"></a>Så tillämpas en reservationsrabatt

Den här artikeln förklarar hur rabatter för reserverade instanser tillämpas generellt. Reservationsrabatten gäller för resursanvändning som matchar de attribut som du väljer när du köper reservationen. Bland attributen finns det omfång där matchande virtuella datorer, SQL-databaser, Azure Cosmos DB eller andra resurser körs. Om du till exempel vill ha en reservationsrabatt för fyra virtuella Standard D2-datorer i regionen USA, västra väljer du den prenumeration där de virtuella datorerna körs.

Reservationsrabatter *går förlorade om de inte används*. Om du inte har några matchande resurser för en viss timme förlorar du en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

Du kan till exempel skapa en resurs senare och ha en matchande reservation som underutnyttjas. Reservationsrabatten gäller automatiskt för den nya matchande resursen.

Om de virtuella datorerna körs i olika prenumerationer i din registrering/ditt konto väljer du omfånget som delat. Delat omfång gör att reservationsrabatten kan tillämpas mellan prenumerationer. Du kan ändra omfånget efter att du har köpt en reservation. Mer information finns i [Hantera Azure-reservationer](manage-reserved-vm-instance.md).

En reservationsrabatt gäller bara för resurser som är kopplade till Enterprise-avtal, Microsoft-kundavtal, CSP:er eller prenumerationer med Betala per användning-priser. Resurser som körs i en prenumeration med andra erbjudandetyper får inte reservationsrabatten.

## <a name="when-the-reservation-term-expires"></a>När reservationsperioden upphör att gälla

I slutet av reservationsperioden går faktureringsrabatten ut och resurserna debiteras enligt principen betala per användning. Som standard är reservationerna inte inställda på automatisk förnyelse. Du kan välja att aktivera automatisk förnyelse av en reservation genom att välja alternativet i förnyelseinställningarna. Med automatisk förnyelse köps en ersättningsreservation när den befintliga reservationen upphör att gälla. Som standard har ersättningsreservationen samma attribut som reservationen som går ut. Om du vill ändrar du faktureringsfrekvensen, giltighetsperioden eller kvantiteten i förnyelseinställningarna. Alla användare med ägaråtkomst till reservationen och prenumerationen som används för fakturering kan konfigurera förnyelse.  

## <a name="discount-applies-to-different-sizes"></a>Rabatten tillämpas på olika storlekar

När du köper en reservation kan rabatten tillämpas på andra instanser med attribut som är i samma storleksgrupp. Den här funktionen kallas flexibel instansstorlek. Flexibiliteten hos rabattens täckning beror på typen av reservation och de attribut som du väljer när du köper reservationen.

Tjänstplaner:

- Reserverade VM-instanser: När du köper reservationen och väljer **Optimerad för flexibel instansstorlek** beror rabattens täckning på den VM-storlek som du väljer. Reservationen kan tillämpas på de virtuella datorernas (VM) storlekar i seriegrupp med samma storlek. Mer information finns i artikeln om [flexibel storlek för virtuella datorer med reserverade VM-instanser](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Reserverad kapacitet i Azure Storage: Du kan köpa reserverad kapacitet för Azure Storage-standardkonton på 100 TiB eller 1 PiB per månad. Reserverad kapacitet i Azure Storage är tillgänglig i alla regioner för alla åtkomstnivåer (frekvent, lågfrekvent eller arkiv) och för alla replikeringsalternativ (LRS, GRS eller ZRS).
- Reserverad SQL Database-kapacitet: Rabattens täckning beror på vilken prestandanivå du väljer. Mer information finns i artikeln om [hur en Azure-reservationsrabatt tillämpas](understand-reservation-charges.md).
- Reserverad Azure Cosmos DB-kapacitet: Rabattens täckning beror på det etablerade dataflödet. Mer information finns i artikeln om [hur en Azure Cosmos DB-reservationsrabatt tillämpas](understand-cosmosdb-reservation-charges.md).

## <a name="how-discounts-apply-to-specific-azure-services"></a>Så tillämpas rabatter för specifika Azure-tjänster

Läs följande artiklar som gäller för dig och lär dig hur rabatter tillämpas för en specifik Azure-tjänst:

- [App Service](reservation-discount-app-service-isolated-stamp.md)
- [Azure Cache for Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [Database for MariaDB](understand-reservation-charges-mariadb.md)
- [Database for MySQL](understand-reservation-charges-mysql.md)
- [Database for PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Data Explorer](understand-azure-data-explorer-reservation-charges.md)
- [Dedicated Host](billing-understand-dedicated-hosts-reservation-charges.md)
- [Disklagring](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [Programvaruplaner](understand-suse-reservation-charges.md)
- [Storage](understand-storage-charges.md)
- [SQL Database](understand-reservation-charges.md)
- [SQL Data Warehouse](reservation-discount-azure-sql-dw.md)
- [Virtuella datorer](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>Nästa steg

- [Hantera Azure Reservations](manage-reserved-vm-instance.md)
- [Förstå reservationsanvändning för din prenumeration med Betala per användning-priser](understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](reserved-instance-windows-software-costs.md)