---
title: Betala för Azure-reservationer i förväg eller via månadsbetalningar
description: Läs om hur du kan betala för Azure-reservationer i förväg eller via månadsbetalningar.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 2689bdeb003cec0050141374165142a62f9ea337
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200409"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Betala för reservationer via månadsbetalningar

Fram till nu har du varit tvungen att betala för Azure-reservationer i förväg. Nu kan du betala för reservationer via månadsbetalningar. När du betalar per månad delar du upp totalkostnaden för reservationen mellan de avtalade månaderna snarare än att betala hela beloppet i förväg. Totalbeloppet är detsamma oavsett om du betalar i förväg eller per månad, det tillkommer inte några extra avgifter när du väljer månadsbetalning.

Beloppet du betalar per månad kan variera beroende på den aktuella månadens växelkurs för din lokala valuta.

Månadsbetalningar är tillgängliga för:

- Virtuella datorer
- Azure Storage
- SQL Databases
- SQL Data Warehouse
- Cosmos DB
- Stämpelavgifter för App Service
- Hanterad disk
- Azure-datautforskaren
- Azure Database for MariaDB, MySQL och PostgreSQL
- Azure VMware Solution by CloudSimple


Köp reservationer i [Azure-portalen](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Exempel som visar ett reservationsköp](./media/monthly-payments-reservations/purchase-reservation.png)

När du gör ett reservationsköp kan du visa betalningsschemat. Klicka på **Visa fullständigt betalningsschema**.

![Exempel som visar betalningsschemat för ett reservationsköp](./media/monthly-payments-reservations/prepurchase-schedule.png)

Om du vill visa betalningsschemat efter köpet väljer du en reservation, klickar på **Reservationsbeställnings-id** och sedan på fliken **Betalningar**.

## <a name="view-payments-made"></a>Visa gjorda betalningar

Du kan visa redan gjorda betalningar med hjälp av API:er, i användningsdata och i kostnadsanalyser. För reservationer som betalas varje månad visas frekvenssvärdet som **Återkommande** i användningsdata och i API:et Reservation Charges. För reservationer som betalas i förväg visas värdet som **En gång**.

I kostnadsanalyser visas månatliga inköp i standardvyn. Använd filtret **inköp** för **Debiteringstyp** och **återkommande** för **Frekvens** om du vill se alla inköp. Om du bara vill visa reservationer använder du ett filter för **Reservation**.

![Exempel som visar inköpskostnader för reservationer i en kostnadsanalys](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Byta till månatliga betalningar när avtalet förnyas

När du förnyar en reservation kan du byta till månatlig fakturering.

## <a name="exchange-and-refunds"></a>Byten och återbetalning

Precis som med andra reservationer kan du återbetala eller byta ut reservationer med månatlig fakturering. För närvarande kan du skicka en supportförfrågan om du vill påbörja ett utbyte eller en återbetalning för en reservation som köpts med månatlig fakturering.

När du byter ut en reservation som du betalar för månadsvis måste den totala livstidskostnaden för det nya köpet vara större än de överblivna betalningar som ställs in för den returnerade reservationen. Det finns inga andra gränser och tillkommer inga andra avgifter för byten. Du kan byta ut en reservation som betalats i förväg till en ny reservation som faktureras per månad. Livstidsvärdet för den nya reservationen måste dock vara större än det beräknade värdet för reservationen som returneras.

Om du avbryter en reservation som betalas per månad kan Microsoft ta ut en uppsägningsavgift för de framtida betalningar som ställs in. Återstående åtagna betalningar räknas av mot återbetalningsgränsen på 50 000 USD.

Du kan läsa mer om byten och återbetalningar i [Självservice vid byten och återbetalningar för Azure-reservationer](exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Nästa steg

- Du kan läsa mer om reservationer i [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
- Du kan läsa mer om vad du behöver göra innan du köper en reservation i [Avgör vilken VM-storlek som passar dig innan du köper](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
