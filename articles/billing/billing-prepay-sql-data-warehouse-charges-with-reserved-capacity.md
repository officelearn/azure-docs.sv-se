---
title: Spara kostnader för SQL Data Warehouse avgifter med reserverad Azure-kapacitet
description: Lär dig hur du sparar kostnader för SQL Data Warehouse avgifter med reserverad kapacitet för att spara pengar.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 381a709c74f5fcf6bb1f89f07ad84d5e3af0c5e0
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806274"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Spara kostnader för SQL Data Warehouse avgifter med reserverad kapacitet

Du kan spara pengar med Azure SQL Data Warehouse genom att använda en reservation för din cDWU-användning under en varaktighet på en eller tre år. Om du vill köpa SQL Data Warehouse reserverad kapacitet måste du välja Azure-region och-period. Lägg därefter till SQL Data Warehouse SKU:n till din kundvagn och välj den mängd cDWU-enheter du vill köpa.

När du köper en reservation debiteras inte längre den SQL Data Warehouse-användning som matchar reservationsattributen enligt användningsbaserade priser.

En reservation täcker inte lagrings- eller nätverkskostnader kopplade till SQL Data Warehouse-användningen.

När den reserverade kapaciteten löper ut, fortsätter SQL Data Warehouse-instanserna att köras men debiteras enligt användningsbaserade priser. Reservationer förnyas inte automatiskt.

För pris information, se [SQL Data Warehouse reserverat kapacitets erbjudande](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Du kan köpa Azure SQL Data Warehouse reserverad kapacitet i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Betala för reservationen [fram eller med månads betalningar](billing-monthly-payments-reservations.md). Så här köper du reserverad kapacitet:

- Du måste ha ägar rollen för minst en prenumeration på företag eller betala per användning.
- För företags prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om inställningen är inaktive rad måste du vara en EA-administratör.
- För Cloud Solution Provider (CSP)-programmet kan endast administratörs agenter eller försäljnings agenter köpa SQL Data Warehouse reserverad kapacitet.

Mer information om hur företags kunder och kunder som betalar per användning debiteras för reservations köp finns i [förstå Azure reservation-användning för din företags registrering](billing-understand-reserved-instance-usage-ea.md) och [förstå användningen av Azure reservation för din Betala per](billing-understand-reserved-instance-usage.md)användning-prenumeration.

## <a name="choose-the-right-size-before-purchase"></a>Välj rätt storlek innan köpet

SQL Data Warehouse reservations storleken ska baseras på det totala antalet beräknings data lager enheter (cDWU) som du använder. Inköp görs i steg om 100 cDWU.

Anta till exempel att den totala förbrukningen av SQL Data Warehouse är DW3000c. Du vill köpa reserverad kapacitet för allt. Därför bör du köpa 30 enheter av cDWU reserverad kapacitet.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Köp SQL Data Warehouse reserverad kapacitet

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj **alla tjänster** > **reservationer**.
3. Välj en prenumeration. Använd prenumerations listan för att välja den prenumeration som används för att betala för den reserverade kapaciteten. Betalnings metoden för prenumerationen debiteras kostnaderna för den reserverade kapaciteten. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller "betala per användning" (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P).
   - För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning.
   - Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.
4. Välj ett omfång. Använd listan omfång för att välja en prenumerations omfattning.
   - **Definitions område för enskild resurs grupp** – tillämpar reservations rabatten på de matchande resurserna enbart i den valda resurs gruppen.
   - **Enda prenumerations omfång** – tillämpar reservations rabatten på de matchande resurserna i den valda prenumerationen.
   - **Delat omfång** – tillämpar reservations rabatten på matchande resurser i berättigade prenumerationer som finns i fakturerings kontexten. För Enterprise-avtal kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med priser enligt principen betala per användning är fakturerings omfånget alla berättigade prenumerationer som skapats av konto administratören.
   - För företags kunder är fakturerings kontexten EA-registrering.
   - För kunder som betalar per användning är det delade omfånget alla prenumerationer enligt principen betala per användning som har skapats av konto administratören.
5. Välj en region för att välja en Azure-region som omfattas av den reserverade kapaciteten.
6. Välj en kvantitet. Ange det antal 100-data lager enheter (cDWU) som du vill köpa.    
   En kvantitet på 30 skulle till exempel ge dig 3 000 cDWU reserverad kapacitet varje timme.
7. Granska reservations kostnaden för SQL Data Warehouse reserverad kapacitet i avsnittet **kostnader** .
8. Välj **Köp**.
9. Välj **Visa den här reservationen** för att se din inköps status.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut eller återbetala reservationer med vissa begränsningar. Mer information finns i [självbetjänings utbyten och åter betalningar för Azure reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

En reservations rabatt tillämpas automatiskt på antalet SQL Data Warehouse instanser som matchar omfattnings området för den reserverade kapaciteten SQL Data Warehouse och regionen. Du kan uppdatera omfånget för den SQL Data Warehouse reserverad kapacitet med [Azure Portal](https://portal.azure.com/), POWERSHELL, CLI eller via API: et.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om hur reservations rabatter gäller för Azure SQL Data Warehouse, se [hur reservations rabatten gäller för Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Mer information om Azure Reservations finns i följande artiklar:
  - [Vad är Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
  - [Förstå Azure Reservations rabatt](billing-understand-reservation-charges.md)
  - [Förstå reservationsanvändning för din Betala per användning-prenumeration](billing-understand-reserved-instance-usage.md)
  - [Förstå reservationsanvändning för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
