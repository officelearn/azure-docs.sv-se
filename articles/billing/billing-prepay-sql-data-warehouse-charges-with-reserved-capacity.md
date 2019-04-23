---
title: Betala i förskott för SQL Data Warehouse-kostnader med Azure reserverad kapacitet | Microsoft Docs
description: Lär dig hur du kan betala i förskott för SQL Data Warehouse avgifter med reserverad kapacitet att spara pengar.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014323"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Betala i förskott för SQL Data Warehouse avgifter med reserverad kapacitet

Du kan spara pengar med Azure SQL Data Warehouse genom förskottsbetalar för din cDWU-användning under en period av ett eller tre år. Om du vill köpa SQL Data Warehouse reserverad kapacitet, måste du välja Azure-region och tidsperiod. Sedan lägga till SQL Data Warehouse-SKU i kundvagnen och Välj antalet cDWU-enheter som du vill köpa.

När du köper en reservation, gå SQL Data Warehouse användningen som matchar reservationen attribut inte längre debiteras enligt användningsbaserad-som-du priser.

En reservation täcker inte lagring eller nätverk avgifter som är associerade med SQL Data Warehouse-användning.

När reserverad kapacitet upphör att gälla SQL Data Warehouse-instanser körs fortfarande, men de är faktureras betala-som-du gå. Reservationer förnyas inte automatiskt.

Information om priser finns i den [SQL Data Warehouse reserverad kapacitet erbjudande](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Du kan köpa Azure SQL Data Warehouse reserverad kapacitet den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Köpa reserverad kapacitet:

- Du måste ha rollen ägare för minst en enterprise eller prenumeration med användningsbaserad betalning.
- För Enterprise-prenumerationer, den **lägga till reserverade instanser** måste vara aktiverad i den [EA-portalen](https://ea.azure.com/). Om inställningen är inaktiverad, måste du vara en EA-administratör.
- Efter programmet Cloud Solution Provider (CSP), kan endast admin-agenter eller försäljning agents köpa SQL Data Warehouse reserverad kapacitet.

Läs mer om hur företagskunder och kunder med användningsbaserad betalning debiteras för reservation köp [förstå användningen av Azure reservation för Enterprise-registreringen](billing-understand-reserved-instance-usage-ea.md) och [förstå Azure reservation-användning för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Välj lämplig storlek enligt före köp

SQL Data Warehouse reservationsstorlek ska baseras på det totala antalet beräkningsenheter data warehouse (cDWU) som du använder. Inköp görs i steg om 100 cDWU.

Anta exempelvis att din total förbrukning av SQL Data Warehouse är DW3000c. Du vill köpa reserverad kapacitet för allt. Därför bör du köpa 30 enheter cDWU reserverad kapacitet.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Köp SQL Data Warehouse reserverad kapacitet

1. Logga in på den [Azure-portalen](https://portal.azure.com/).
2. Välj **alla tjänster** > **reservationer**.
3. Välj en prenumeration. Använd listan prenumeration för att välja den prenumeration som används för att betala för reserverad kapacitet. Betalningsmetoden för prenumerationen debiteras startavgifter för reserverad kapacitet. Prenumerationstypen måste vara ett enterprise-avtal (erbjuder siffror: MS-AZR-0017P eller MS-AZR - 0148 P) eller betala per användning (erbjuder siffror: MS-AZR-0003P eller MS-AZR - 0023 P).
  - För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning.
  - Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.
4. Välj ett omfång. Använd listan omfång för att välja en prenumeration omfattning.
  - Med den **enda** , reservationsrabatten tillämpas på SQL Data Warehouse distribueras i den valda prenumerationen.
  - Med den **delad** , reservationsrabatten tillämpas på instanser som körs på alla prenumerationer i din faktureringskontexten.
    - För företagskunder är faktureringskontexten EA-avtal.
    - För kunder med användningsbaserad betalning är den delade omfattningen alla betala per användning-prenumerationer som skapas av kontoadministratören.
5. Välj en region för att välja en Azure-region som täcks av reserverad kapacitet.
6. Välj en kvantitet. Ange antalet 100 Data Warehouse enheter (cDWU) som du vill köpa.    
  Till exempel ger en kvantitet på 30 du 3 000 cDWU reserverad kapacitet varje timme.
7. Granska SQL Data Warehouse reserverad kapacitet reservationskostnad i den **kostnader** avsnittet.
8. Välj **Köp**.
9. Välj **visa den här reservationen** att se status för ditt köp.

## <a name="cancellations-and-exchanges"></a>Avbokningar och utbyten

Om du behöver avbryta ditt SQL Data Warehouse reserverad kapacitet, det kan finnas 12% uppsägningsavgift. Återbetalningar baseras på det lägsta priset – antingen ditt inköpspris eller det aktuella priset för reservationen. Återbetalningar är begränsade till $50,000.00 per år. Återbetalning felmeddelandet är det återstående proportionella saldot minus 12% uppsägningsavgift. Om du vill begära ett avbrott, går du till reservationen i Azure-portalen och välj **återbetala** att skapa en supportbegäran.

Om du vill ändra din SQL Data Warehouse reserverad kapacitet till en annan region eller en term kan byta du den för en annan reservation som är av samma eller högre värde. Startdatumet för den nya reservationens överförs inte från den utbytte reservationen. Det eller tre år, startar när du skapar den nya reservationen. Om du vill begära ett utbyte, öppna reservationen i Azure-portalen och välj **Exchange** att skapa en supportbegäran.

Läs mer om hur du exchange- eller återbetalning reservationer [Reservation utbyte och återbetalningar](billing-azure-reservations-self-service-exchange-and-refund.md).

Reservationsrabatten tillämpas automatiskt på antalet instanser av SQL Data Warehouse som matchar SQL Data Warehouse reserverad kapacitet omfång och region. Du kan uppdatera omfånget för SQL Data Warehouse reserverad kapacitet med den [Azure-portalen](https://portal.azure.com/), PowerShell, CLI eller via API: et.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur reservation rabatter som gäller för Azure SQL Data Warehouse i [hur reservation rabatter som gäller för Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Om du vill veta mer om Azure reservationer, finns i följande artiklar:
  - [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
  - [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
  - [Förstå Azure reservationer rabatt](billing-understand-reservation-charges.md)
  - [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
  - [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
