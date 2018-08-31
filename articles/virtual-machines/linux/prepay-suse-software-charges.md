---
title: Köpa planer för SUSE Linux - Azure reservationer | Microsoft Docs
description: Lär dig hur du betalar i förskott för din SUSE-användning och spara pengar jämfört din betala per användning.
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2018
ms.author: yashar
ms.openlocfilehash: 9c3976a5fa98049de03f2a65b71f1fc927947142
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307834"
---
# <a name="prepay-for-suse-software-plans-from-azure-reservations"></a>Betala i förskott för SUSE programvaruplaner från Azure-reservationer

Betala i förskott för din SUSE-användning och spara pengar jämfört din betala per användning. Rabatter gäller endast för SUSE-mätare och inte på VM-användning. Du kan köpa reservationer för virtuella datorer separat att spara ännu mer.

Du kan köpa planer för SUSE-programvara i Azure-portalen. Att köpa en plan:

- Du måste vara i en ägarrollen för minst en Enterprise eller användningsbaserad betalning.
- Köp av reservation måste aktiveras för Enterprise-prenumerationer för i den [EA-portalen](https://ea.azure.com).
- Efter programmet Cloud Solution Provider (CSP), kan administratören agenter eller försäljning agenter köpa SUSE-planer.

## <a name="buy-a-suse-software-plan"></a>Köp en plan för SUSE-programvara

1. Gå till [reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) i Azure-portalen.
1. Välj **Lägg till** och välj SUSE Linux.
1. Fyll i fälten som krävs. Alla SUSE Linux virtuella datorer som matchar attribut för vad du köper hämtar rabatten. Det faktiska antalet distributioner som får rabatten är beroende av omfång och kvantitet som väljs.

    | Fält      | Beskrivning|
    |:------------|:--------------|
    |Namn        |Namnet på det här köpet.|
    |Prenumeration|Den prenumeration som används för att betala för den här planen. Betalningsmetoden för prenumerationen debiteras startavgifter för reservationen. Prenumerationstypen måste vara ett företagsavtal (erbjudandets nummer: MS-AZR-0017P) eller Betala per användning (erbjudandets nummer: MS-AZR-0003P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.|
    |Omfång       |Omfånget kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>Enstaka prenumeration - plan rabatt tillämpas på SUSE Linux-användning i den här prenumerationen. </li><li>Delad – tillämpas plan rabatt på SUSE Linux-användning i alla prenumerationer i din faktureringskontexten. För företagskunder, den delade omfattningen registreringen och innehåller alla prenumerationer (utom prenumerationer för utveckling och testning) i registreringen. För kunder med användningsbaserad betalning är den delade omfattningen alla betala per användning-prenumerationer som skapas av kontoadministratören.</li></ul>|
    |Programprenumeration     |Välj SUSE Linux-plan. Hjälp med att identifiera vad du kan köpa finns i [förstå hur SUSE Linux Enterprise software reservationsrabatten tillämpas](../../billing/billing-understand-suse-reservation-charges.md).|
    |Storlek på virtuell dator     |SUSE Linux priserna beror på antalet virtuella processorer på den virtuella datorn. Välj det alternativ som representerar antalet virtuella processorer på din SUSE Linux-datorer.|
    |Period        |Ett eller tre år.|
    |Antal    |Antalet virtuella datorer som du köper det här SUSE Linux-plan för. Antalet är antalet SUSE Linux-instanser som kan få fakturering rabatten som körs.|
1. Välj **Köp**.
1. Välj **visa den här reservationen** att se status för ditt köp.

Reservationsrabatten tillämpas automatiskt på alla SUSE virtuella datorer som körs som matchar reservationen. Rabatten gäller endast för SUSE-mätaren. De virtuella datorn visar beräkningsrelaterade avgifterna omfattas inte av den här planen.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>Rabatten gäller för olika storlekar på Virtuella datorer med flexibilitet för datorinstanser storlek

SUSE Linux planer erbjuder instans storlek flexibilitet som reserverade VM-instanser. Det innebär att rabatten gäller även när du distribuerar en virtuell dator som är en annan storlek från SUSE-plan som du har köpt. Mer information finns i [förstå hur SUSE Linux Enterprise software reservationsrabatten tillämpas](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="cancellation-and-exchanges-not-allowed"></a>Annulleringen och utbyten inte tillåtet

Du kan inte avbryta eller byta en SUSE-plan som du har köpt. Kontrollera din användning för att kontrollera att du köper rätt plan. Hjälp med att identifiera vad du kan köpa finns i [förstå hur SUSE Linux Enterprise software reservationsrabatten tillämpas](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="next-steps"></a>Nästa steg

Läs hur du hanterar en reservation i [hantera Azure-reservationer](../../billing/billing-manage-reserved-vm-instance.md).

Mer information finns i följande artiklar:

- [Vad är Azure reservationer?](../../billing/billing-save-compute-costs-reservations.md)
- [Hantera reservationer i Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Förstå hur SUSE reservationsrabatten tillämpas](../../billing/billing-understand-suse-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](../../billing/billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.