---
title: Förstå hur en reservationsrabatt tillämpas på Azure-disklagring
description: Läs om hur Azures rabatt på reserverade diskar kan användas för dina Premium SSD-hanterade diskar.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902138"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Förstå hur reservationsrabatten tillämpas på Azure-disklagring

När du har köpt en reserverad Azure-diskkapacitet tillämpas reservationsrabatten automatiskt på diskresurser som matchar reservationens villkor. Reservationsrabatten gäller endast diskkapacitet, ögonblicksbilder av disken debiteras med priset per användning.

Mer information om Azures diskreservation finns i [Spara kostnader med Azures diskreservation](../../virtual-machines/linux/disks-reserved-capacity.md).
Information om prissättningen för Azures diskreservation finns i [Prissättning för Azure-diskar](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatten för Azure-diskar är en ”use-it-or-lose-it”-rabatt och tillämpas på hanterade diskresurser per timme. Om du inte har några hanterade diskresurser som uppfyller villkoren för reservationen under en viss timme, förlorar du reservationskvantiteten under den timmen. Outnyttjade timmar kan inte sparas.

När du tar bort en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna förlorade.

## <a name="discount-examples"></a>Exempel på rabatter

I följande exempel visas hur reservationsrabatten för Azure-diskar tillämpas, beroende på distribution:

Anta att du har köpt 100 P30-diskar (~1 TiB/disk) med reserverad kapacitet i regionen USA, västra 2 för en period på 1 år. Anta att kostnaden i den här exempelreservationen är 140 100 USD. Du kan välja att betala hela beloppet direkt, eller betala ett fast pris på 11 675 USD per månad under de kommande 12 månaderna.
Anta för dessa exempel att du har registrerat dig för en månatlig reservationsbetalningsplan. Följande scenarier beskriver vad som händer om du underutnyttjar, överutnyttjar eller använder exakt din reserverade kapacitet.

### <a name="underusing-your-capacity"></a>Underutnyttja kapaciteten

Anta att du under en viss timme inom reservationsperioden bara har distribuerat 99 P30 Premium SSD av din diskreservation på 100 P30. 1 P30 används inte den timmen och överförs inte till nästa period.

### <a name="overusing-your-capacity"></a>Överutnyttja kapaciteten

Anta att du under en viss timme inom reservationsperioden använder 101 P30 Premium SSD-diskar. Reservationsrabatten tillämpas endast på 100 P30-diskar och den återstående P30-disken debiteras med ett pris per användning under den timmen. Om din användning nästa timme minskar till 100 P30-diskar, omfattas all användning av reservationen.

### <a name="tiering-your-capacity"></a>Använda hela kapaciteten

Anta att du under en viss timme inom din reservationsperiod vill använda totalt 200 P30 Premium SSD. Under de första 30 minuterna använder du bara 100 stycken. Under den här perioden täcks din användning fullständigt, eftersom du har gjort en reservation på 100 P30-diskar. Om du sedan avbryter användningen av de första 100 (så att du använder noll) och börjar använda de återstående 100 under kvarvarande 30 minuter, omfattas även den användningen av din reservation.

![Beskrivning av exempel på hur du underutnyttjar, överutnyttjar och använder den exakta kapaciteten](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Minska kostnader med Azures diskreservation (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Minska kostnader med Azures diskreservation (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)