---
title: Sänk dina SQL Data Warehouse-kostnader med reserverad kapacitet i Azure
description: Lär dig hur du kan sänka dina SQL Data Warehouse-kostnader med reserverad kapacitet.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 381a709c74f5fcf6bb1f89f07ad84d5e3af0c5e0
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70806274"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Sänk dina SQL Data Warehouse-kostnader med reserverad kapacitet

Du kan spara pengar med Azure SQL Data Warehouse genom att köpa en reservation för din cDWU-användning under ett eller tre år. Om du vill köpa reserverad kapacitet för SQL Data Warehouse måste du välja Azure-region och period. Sedan lägger du till SKU:er för SQL Data Warehouse i kundvagnen och väljer hur många cDWU-enheter du vill köpa.

När du köper en reservation används inte längre Betala per användning-priser för SQL Data Warehouse-användning som matchar reservationsattributen.

En reservation täcker inte avgifter för lagrings eller nätverk kopplade till SQL Data Warehouse-användningen.

När den reserverade kapaciteten löper ut fortsätter SQL Data Warehouse-instanserna att köras men debiteras då enligt Betala per användning-priser. Reservationer förnyas inte automatiskt.

Du hittar prisinformation i [Erbjudanden om reserverad kapacitet för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Du kan köpa reserverad kapacitet för Azure SQL Data Warehouse i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Betala för reservationen [i förväg eller via månadsbetalningar](billing-monthly-payments-reservations.md). Så här köper du reserverad kapacitet:

- Du måste ha rollen ägare för minst en Enterprise-prenumeration eller Betala per användning-prenumeration.
- För Enterprise-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om inställningen är inaktiverad måste du vara EA-administratör.
- I CSP-programmet (Cloud Solution Provider) är det bara administratörsagenter eller säljagenter som kan köpa reserverad SQL Data Warehouse-kapacitet.

Mer information om hur Enterprise-kunder och Betala per användning-kunder debiteras för reservationsköp finns i [Förstå hur Azure-reservationer tillämpas för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md) och [Förstå hur Azure-reservationer tillämpas för din Betala per användning-prenumeration](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Välj rätt storlek innan köpet

Storleken på SQL Data Warehouse-reservationen bör baseras på den totala mängden cDWU-enheter du förbrukar. Inköp görs i steg om 100 cDWU.

Anta till exempel att din totala förbrukning av SQL Data Warehouse är DW3000c. Du vill köpa reserverad kapacitet för hela mängden. Då bör du köpa 30 enheter av reserverad cDWU-kapacitet.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Köpa reserverad kapacitet för SQL Data Warehouse

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Alla tjänster** > **Reservationer**.
3. Välj en prenumeration. Använd prenumerationslistan och välj den prenumeration som används till att betala för den reserverade kapaciteten. Den reserverade kapaciteten debiteras via betalningsmetoden för prenumerationen. Prenumerationstypen måste vara EA (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P), betala per användning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P).
   - För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning.
   - Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.
4. Välj ett omfång. Välj ett prenumerationsomfång i omfångslistan.
   - **Omfång för enskild resursgrupp** – reservationsrabatten tillämpas endast på matchande resurser i den valda resursgruppen.
   - **Omfång för enskild prenumeration** – reservationsrabatten tillämpas på matchande resurser i den valda prenumerationen.
   - **Delat omfång** – reservationsrabatten tillämpas på matchande resurser i berättigade prenumerationer i faktureringskontexten. För EA-kunder är registreringen faktureringskontext. För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.
   - För Enterprise-kunder är EA-registreringen faktureringskontext.
   - För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat.
5. Välj en region som den reserverade kapaciteten ska täcka.
6. Välj en kvantitet. Ange hur många 100 cDWU-enheter du vill köpa.    
   Kvantiteten 30 skulle till exempel ge dig 3 000 cDWU reserverad kapacitet i timmen.
7. Du kan se kostnaderna för reserverad SQL Data Warehouse-kapacitet i avsnittet **Kostnader**.
8. Välj **Köp**.
9. Välj **Visa den här reservationen** om du vill se inköpsstatusen.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](billing-azure-reservations-self-service-exchange-and-refund.md).

Reservationsrabatter tillämpas automatiskt för de SQL Data Warehouse-instanser som matchar den reserverade SQL Data Warehouse-kapacitetens omfång och region. Du kan uppdatera den reserverade SQL Data Warehouse-kapacitetens omfång i [Azure-portalen](https://portal.azure.com/), med PowerShell, CLI eller via API:et.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om hur reservationsrabatter tillämpas för Azure SQL Data Warehouse kan du läsa [Så tillämpas reservationsrabatter för Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Du kan läsa mer om Azure-reservationer i följande artiklar:
  - [Vad är Azure-reservationer?](billing-save-compute-costs-reservations.md)
  - [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
  - [Förstå reservationsrabatter i Azure](billing-understand-reservation-charges.md)
  - [Förstå reservationsanvändning för din Betala per användning-prenumeration](billing-understand-reserved-instance-usage.md)
  - [Förstå reservationsanvändning för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
