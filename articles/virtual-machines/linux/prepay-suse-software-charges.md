---
title: Betala i förskott för programvaruplaner - Azure-reservationer | Microsoft Docs
description: Lär dig hur du kan betala i förskott för programvaruplaner för att spara pengar jämfört din betala per användning.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: e15dcdbbcaed32d836bb751ef93ce17e90bd6905
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011103"
---
# <a name="prepay-for-azure-software-plans"></a>Betala i förskott för Azure-programvaruplaner

När du betalar i förskott för din användning av SUSE och RedHat programvara i Azure, kan du spara pengar jämfört din betala per användning. Rabatter gäller endast för SUSE och RedHat mätare och inte på VM-användning. Du kan köpa reservationer för virtuella datorer separat för ytterligare besparingar.

Du kan köpa SUSE och RedHat programvaruplaner i Azure-portalen. Att köpa en plan:

- Du måste ha rollen ägare för minst en Enterprise eller användningsbaserad betalning.
- För Enterprise-prenumerationer, den **lägga till reserverade instanser** måste vara aktiverad i den [EA-portalen](https://ea.azure.com/). Om inställningen är inaktiverad, måste du vara en EA-administratör för prenumerationen.
- Efter programmet Cloud Solution Provider (CSP) köp admin-agenter eller försäljning agents programvaruplaner.

## <a name="buy-a-software-plan"></a>Köp en plan för programvara

1. Logga in på Azure-portalen och gå till [reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Klicka på **Lägg till** och välj sedan den plan för programvara som du vill köpa.
Fyll i fälten som krävs. Alla SUSE Linux VM eller RedHat VM som matchar attribut för vad du köper hämtar rabatten. Det faktiska antalet distributioner som får rabatten är beroende av omfång och kvantitet som väljs.
3. Välj en prenumeration. Används för att betala för abonnemanget.
Betalningsmetoden för prenumerationen debiteras startavgifter för reservationen. Prenumerationstypen måste vara ett Enterprise-avtal (erbjuder siffror: MS-AZR-0017P eller MS-AZR - 0148 P) eller betala per användning (erbjuder siffror: MS-AZR-0003P eller MS-AZR - 0023 P).
    - För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning.
    - För användningsbaserad betalning debiteras avgifterna till prenumerationens kreditkort eller faktura betalningsmetod.
4. Välj ett omfång. Omfånget kan omfatta en prenumeration eller flera prenumerationer (delad omfattning).
    - Enstaka prenumeration - plan rabatt tillämpas på matchar användning i prenumerationen.
    - Delad – rabatten plan på matchande instanser i alla prenumerationer i din faktureringskontexten. För företagskunder fakturering kontexten är registreringen och innehåller alla prenumerationer i registreringen. För kunder med användningsbaserad betalning är faktureringskontexten alla betala per användning-prenumerationer som skapas av kontoadministratören.
5. Välj en produkt för att välja virtuella datorstorlek och bildtypen. Rabatten gäller för den valda VM-storleken bara.
6. Markera en term om ett eller tre år.
7. Välj en kvantitet, vilket är antalet förbetalda VM-instanser som kan få fakturering rabatt.
8. Lägga till produkten i kundvagn, granskning och inköp.

Reservationsrabatten tillämpas automatiskt på mätaren programvara som du betala i förskott. Beräkningskostnader för virtuell dator omfattas inte av planen. Du kan köpa reservationer för virtuella datorer separat.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Rabatten gäller för olika SUSE VM-storlekar

SUSE Linux planer erbjuder instans storlek flexibilitet som reserverade VM-instanser. Rabatten gäller även när du distribuerar en virtuell dator som är en annan storlek från SUSE-plan som du har köpt. Mer information finns i [förstå hur programvaran plan rabatten](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>RedHat plan rabatt

Prenumerationerna finns bara för Red Hat Enterprise Linux virtuella datorer. Rabatten gäller inte för Red Hat Enterprise Linux SAP HANA-datorer eller Red Hat Enterprise Linux SAP Business Apps virtuella datorer.

RedHat plan rabatter gäller endast för VM-storlek som du väljer vid inköpstillfället. RHEL-planer kan inte återbetalas eller bytas ut efter köpet.


## <a name="cancellation-and-exchanges-not-allowed"></a>Annulleringen och utbyten inte tillåtet

Du kan inte avbryta eller byta en SUSE eller Red Hat-plan som du har köpt. Kontrollera din användning för att försäkra dig om att du köper rätt plan. Hjälp att identifiera vad du kan köpa finns i [förstå hur programvaran plan rabatten](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Läs hur du hanterar en reservation i [hantera Azure-reservationer](../../billing/billing-manage-reserved-vm-instance.md).

Mer information finns i följande artiklar:

- [Vad är Azure reservationer?](../../billing/billing-save-compute-costs-reservations.md)
- [Hantera reservationer i Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Förstå hur SUSE reservationsrabatten tillämpas](../../billing/billing-understand-suse-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](../../billing/billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](../../billing/billing-understand-reserved-instance-usage-ea.md)
