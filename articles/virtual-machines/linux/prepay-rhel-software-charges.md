---
title: Köpa planer för Red Hat Enterprise Linux – Azure reservationer | Microsoft Docs
description: Lär dig hur du betalar i förskott för din användning av Red Hat och spara pengar jämfört din betala per användning.
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
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653147"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>Betala i förskott för Red Hat Enterprise Linux planer från Azure-reservationer

Betala i förskott för din användning av Red Hat och spara pengar jämfört din betala per användning. Rabatter gäller endast för Red Hat-mätare och inte på VM-användning. Du kan köpa reservationer för virtuella datorer separat att spara ännu mer.

Du kan köpa planer för Red Hat-programvara i Azure-portalen. Att köpa en plan:

- Du måste vara i en ägarrollen för minst en Enterprise eller användningsbaserad betalning.
- För Enterprise-prenumerationer, **lägga till reserverade instanser** måste aktiveras i den [EA-portalen](https://ea.azure.com). Eller, om den här inställningen har inaktiverats kan du måste vara en EA-administratör för prenumerationen.
- Efter programmet Cloud Solution Provider (CSP), kan administratören agenter eller försäljning agenter köpa Red Hat-planer.

## <a name="buy-a-red-hat-software-plan"></a>Köp en plan för Red Hat-programvara

1. Gå till [reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) i Azure-portalen.
1. Välj **Lägg till** och välj Red Hat Linux.
1. Fyll i fälten som krävs. Red Hat Linux virtuella datorer som matchar attribut för vad du köper hämtar rabatten. Det faktiska antalet distributioner som får rabatten är beroende av omfång och kvantitet som väljs.

    | Fält      | Beskrivning|
    |:------------|:--------------|
    |Namn        |Namnet på det här köpet.|
    |Prenumeration|Den prenumeration som används för att betala för den här planen. Betalningsmetoden för prenumerationen debiteras startavgifter för reservationen. Prenumerationstypen måste vara ett enterprise-avtal (erbjuder siffror: MS-AZR-0017P eller MS-AZR - 0148 P) eller betala per användning (erbjuder siffror: MS-AZR-0003P eller MS-AZR - 0023 P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. Får en Betala per användning-prenumeration faktureras avgifterna från kreditkortet eller enligt fakturabetalningsmetoden.|
    |Scope       |Omfånget kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <ul><li>Enstaka prenumeration - plan rabatt tillämpas på Red Hat Linux-användning i den här prenumerationen. </li><li>Delad – tillämpas plan rabatt på Red Hat Linux-användning i alla prenumerationer i din faktureringskontexten. För företagskunder, den delade omfattningen registreringen och innehåller alla prenumerationer i registreringen. För kunder med användningsbaserad betalning är den delade omfattningen alla betala per användning-prenumerationer som skapas av kontoadministratören.</li></ul>|
    |Planera     |Välj planen som Red Hat Linux. För hjälp med att identifiera vad du köpa finns i förstå hur tillämpas reservationsrabatten för Red Hat Linux Enterprise-programvara.|
    |Storlek på virtuell dator     |Red Hat Linux priserna beror på antalet virtuella processorer på den virtuella datorn. Välj det alternativ som representerar antalet virtuella processorer på Red Hat Linux-datorer.|
    |Period        |Ett eller tre år.|
    |Kvantitet    |Antalet virtuella datorer som du köper det här Red Hat Linux-plan för. Antalet är antalet kör Red Hat Linux-instanser som kan få fakturering rabatt.|
1. Välj **Köp**.
1. Välj **visa den här reservationen** att se status för ditt köp.

Reservationsrabatten tillämpas automatiskt på alla som kör Red Hat-datorer som matchar reservationen. Rabatten gäller endast för Red Hat-mätaren. De virtuella datorn visar beräkningsrelaterade avgifterna omfattas inte av den här planen.

## <a name="discount-applies-to-different-vm-sizes"></a>Rabatten gäller för olika storlekar på Virtuella datorer

Red Hat Linux planer erbjuder flexibilitet för datorinstanser storlek som reserverade VM-instanser. Det innebär att rabatten gäller även när du distribuerar en virtuell dator som är en annan storlek från Red Hat-plan som du har köpt. Mer information finns i förstå hur Red Hat Linux Enterprise software reservationsrabatten tillämpas.

## <a name="cancellation-and-exchanges-not-allowed"></a>Annulleringen och utbyten inte tillåtet

Du kan inte avbryta eller byta en Red Hat-plan som du har köpt. Kontrollera din användning för att försäkra dig om att du köper rätt plan. För hjälp med att identifiera vad du köpa finns i förstå hur tillämpas reservationsrabatten för Red Hat Linux Enterprise-programvara.

## <a name="next-steps"></a>Nästa steg

Läs hur du hanterar en reservation i [hantera Azure-reservationer](../../billing/billing-manage-reserved-vm-instance.md).

Mer information finns i följande artiklar:

- [Vad är Azure reservationer?](../../billing/billing-save-compute-costs-reservations.md)
- [Hantera reservationer i Azure](../../billing/billing-manage-reserved-vm-instance.md)
- Förstå hur Red Hat-reservationsrabatten tillämpas
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](../../billing/billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.