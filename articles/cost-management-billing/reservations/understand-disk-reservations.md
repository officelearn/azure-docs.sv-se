---
title: Förstå hur en reservationsrabatt tillämpas på Azure-disklagring
description: Lär dig hur Azures rabatt för reserverade diskar tillämpas på dina Azure Premium SSD-hanterade diskar.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 18fdda3e28761fcf912b716f51b5e270a9b224d0
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586656"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Förstå hur reservationsrabatten tillämpas på Azure-disklagring

När du har köpt en reserverad Azure-diskkapacitet tillämpas en reservationsrabatt automatiskt på diskresurser som matchar reservationens villkor. Reservationsrabatten gäller endast för disk-SKU:er. Ögonblicksbilder av diskar debiteras enligt ”betala per användning”-modellen.

Mer information om Azures diskreservation finns i [Spara kostnader med Azures diskreservation](../../virtual-machines/linux/disks-reserved-capacity.md). Information om priser för Azures diskreservation finns i [Priser för Azure Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatten för Azure-diskar går förlorad om den inte används. Den tillämpas för hanterade diskresurser per timme. Om du för en viss timme inte har några hanterade diskresurser som uppfyller reservationsvillkoren förlorar du en reservationskvantitet för den timmen. Oanvända timmar överförs inte till nästa period.

När du tar bort en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om ingen matchande resurs hittas går de reserverade timmarna förlorade.

## <a name="discount-examples"></a>Exempel på rabatter

Följande exempel illustrerar hur reservationsrabatten för Azure-diskar tillämpas, beroende på distribution.

Anta att du köper och reserverar 100 P30-diskar i regionen USA, västra 2 för en period på ett år. Varje disk har ungefär 1 TiB lagringsutrymme. Anta att kostnaden i den här exempelreservationen är 140 100 USD. Du kan välja att betala hela beloppet i förväg eller betala ett fast månadsbelopp på 11 675 USD under de kommande 12 månaderna.

Följande scenarier beskriver vad som händer om du underutnyttjar, överutnyttjar eller använder exakt din reserverade kapacitet. Anta i dessa exempel att du har registrerat dig för en månatlig reservationsbetalningsplan.

### <a name="underusing-your-capacity"></a>Underutnyttja kapaciteten

Anta att du bara distribuerar 99 av dina 100 reserverade Azure Premium SSD (Solid-State Drive) P30-diskar under en timme inom reservationsperioden. Den sista P30-disken tillämpas inte under den timmen. Den överförs inte heller till nästa period.

### <a name="overusing-your-capacity"></a>Överutnyttja kapaciteten

Anta att du använder 101 Premium SSD P30-diskar under en timme inom reservationsperioden. Reservationsrabatten gäller endast 100 P30-diskar. Den sista P30-disken debiteras enligt Betala per användning för den timmen. Om användningen går ned till 100 P30-diskar under nästa timme, täcker reservationen all användning.

### <a name="tiering-your-capacity"></a>Använda hela kapaciteten

Anta att du under en viss timme inom reservationsperioden vill använda totalt 200 P30 Premium SSD-diskar. Anta också att du bara använder 100 de första 30 minuterna. Under den här perioden täcks din användning fullständigt, eftersom du har gjort en reservation på 100 P30-diskar. Om du sedan avbryter användningen av de första 100 (så att du använder noll) och börjar använda de återstående 100 under kvarvarande 30 minuter, omfattas även den användningen av din reservation.

![Exempel på att underutnyttja, överutnyttja och använda den exakta kapaciteten](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Minska kostnader med Azures diskreservation (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Minska kostnader med Azures diskreservation (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
