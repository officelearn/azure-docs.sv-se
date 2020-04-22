---
title: Förskottsbetalning för programvaruplaner - Azure Reservations
description: Läs om hur du kan betala i förskott för programvaruplaner för att spara pengar över dina kostnader för användningsbaserad betalning.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: a527b75d376f05ab6190187b7a03d6da775055ab
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759282"
---
# <a name="prepay-for-azure-software-plans"></a>Förskottsbetala för programvaruplaner för Azure

När du betalar i förskott för din SUSE- och RedHat-programvaruanvändning i Azure kan du spara pengar över dina kostnader för användningsbaserad betalning. Rabatterna gäller endast SUSE- och RedHat-mätare och inte på användning av den virtuella datorn. Du kan köpa reservationer för virtuella datorer separat för ytterligare besparingar.

Du kan köpa SUSE- och RedHat-programplaner i Azure-portalen. Så här köper du ett abonnemang:

- Du måste ha ägarrollen för minst en Enterprise- eller enskild prenumeration med priser för användningsbaserad betalning.
- För Enterprise-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om inställningen är inaktiverad måste du vara EA-administratör för prenumerationen.
- För CSP-programmet (Cloud Solution Provider) kan administratörsagenter eller försäljningsagenter köpa programvaruplanerna.

## <a name="buy-a-software-plan"></a>Köp en programvaruplan

1. Logga in på Azure-portalen och gå till [Reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Klicka på **Lägg till** och välj sedan den programvaruplan som du vill köpa.
Fyll i de obligatoriska fälten. Alla SUSE Linux VM eller RedHat VM som matchar attributen för det du köper får rabatten. Det faktiska antalet distributioner som får rabatten beror på omfattningen och kvantiteten som valts.
3. Välj en prenumeration. Det används för att betala för planen.
Prenumerationsbetalningsmetoden debiteras de initiala kostnaderna för bokningen. Prenumerationstypen måste vara ett enterprise-avtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P) eller individuellt avtal med betalning per användning -prissättning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P).
    - För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning.
    - För en enskild prenumeration med priser för användningsbaserad betalning debiteras avgifterna till prenumerationens kreditkorts- eller fakturabetalningsmetod.
4. Välj ett omfång. Omfattningen kan omfatta en prenumeration eller flera prenumerationer (delat scope).
    - Enskild prenumeration - Planrabatten tillämpas på matchande användning i prenumerationen.
    - Delad - Planrabatten tillämpas på matchande instanser i en prenumeration i din faktureringskontext. För företagskunder är faktureringskontexten registreringen och inkluderar alla prenumerationer i registreringen. För enskilda abonnemang med priskunder för användningsbaserad betalning är faktureringskontexten alla enskilda planer med prisprenumerationer för användningsbaserad betalning som skapats av kontoadministratören.
    - Enskild resursgrupp - Tillämpar reservationsrabatten endast på matchande resurser i den valda resursgruppen.
5. Välj en produkt för att välja storleken på den virtuella datorn och bildtypen. Rabatten gäller endast för den valda vm-storleken.
6. Välj en ett- eller treårsperiod.
7. Välj en kvantitet, vilket är antalet förbetalda VM-instanser som kan få faktureringsrabatten.
8. Lägg till produkten i kundvagnen, granska och köp.

Reservationsrabatten tillämpas automatiskt på den programmätare som du betalar för i förväg. Vm-beräkningsavgifter omfattas inte av planen. Du kan köpa vm-bokningarna separat.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Rabatt gäller för olika SUSE VM-storlekar

Precis som reserverade VM-instanser erbjuder SUSE Linux-planer flexibilitet i instansstorleken. Din rabatt gäller även när du distribuerar en virtuell dator som har en annan storlek än SUSE-planen som du köpte. Mer information finns i [Förstå hur rabatten för programvaruabonnemang tillämpas](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>RedHat plan rabatt

Planer är endast tillgängliga för virtuella Red Hat Enterprise Linux-datorer. Rabatten gäller inte för RedHat Enterprise Linux SAP HANA virtuella datorer eller RedHat Enterprise Linux SAP Business Apps virtuella datorer.

RedHat-planrabatter gäller endast för den virtuella datorns storlek som du väljer vid inköpstillfället. RHEL-abonnemang kan inte återbetalas eller bytas ut efter köpet.


## <a name="cancellation-and-exchanges-not-allowed"></a>Avbokning och byten är inte tillåtna

Du kan inte avbryta eller byta ut en SUSE- eller RedHat-plan som du har köpt. Kontrollera din användning för att försäkra dig om att du köper rätt plan. Mer information om hur du vill identifiera vad du ska köpa finns i [Förstå hur rabatten på programvaruplanen tillämpas](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Mer information finns i följande artiklar:

- [Vad är Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Hantera reservationer i Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Förstå hur SUSE-reservationsrabatten tillämpas](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
