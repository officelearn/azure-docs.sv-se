---
title: Reservationsrekommendationer för Azure
description: Läs om reservationsrekommendationer för Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 661e3bfa149718eb2893c5722ab3931a8a9f9afe
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797942"
---
# <a name="reservation-recommendations"></a>Reservationsrekommendationer

Inköpsrekommendationer för reserverade Azure-instanser (RI) tillhandahålls av [API:et för reservationsrekommendationer](/rest/api/consumption/reservationrecommendations) för Azure Consumption, [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) och när du köper reservationer i Microsoft Azure-portalen.

Följande steg definierar hur rekommendationer beräknas:

1. Rekommendationsmotorn utvärderar hur mycket dina resurser används per timme inom det angivna omfånget som kan utgöras av de senaste 7, 30 eller 60 dagarna.
2. Motorn simulerar dina kostnader med och utan reservationer enligt dessa förbrukningsdata.
3. Kostnaderna simuleras enligt olika kvantiteter och den kvantitet som maximerar dina besparingar rekommenderas.
4. Om dina resurser stängs av regelbundet kommer simuleringen inte att hitta några besparingsmöjligheter och inte ge dig någon inköpsrekommendation.

## <a name="recommendations-in-the-azure-portal"></a>Rekommendationer på Azure-portalen

Rekommendationer för reservationsköp visas också i Azure-portalen när du köper reservationer. Rekommendationer visas med **rekommenderat antal**. Du får maximala besparingar om du köper den kvantitet som Azure rekommenderar. Du kan köpa vilken kvantitet du vill, men om du köper en annan kvantitet får du inte optimala besparingar.

Låt oss titta på några exempel.

På bilden i följande exempel rekommenderar Azure en inköpskvantitet på 6 för den reservationen som valts.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Exempel som visar rekommendation för reservationsköp" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Mer information om rekommendationen visas när du väljer länken **Visa information**. Följande bild visar mer information om rekommendationen. Den rekommenderade kvantiteten beräknas för den högsta möjliga användningen, baserat på din användningshistorik. Din rekommendation kanske inte gäller 100 procent användning om du har inkonsekvent användning. Observera att användningen varierar med tiden i exemplet. Kostnaden för reservationen, möjliga besparingar och användningsprocenten visas.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Exempel som visar rekommendation för reservationsköp" :::

När du ökar eller minskar reservationen ändras kvantiteten utöver rekommendationen, diagrammet och de uppskattade värdena. Om du ökar reservationskvantiteten så att den överstiger den rekommenderade kvantiteten minskar besparingarna eftersom du kommer att få lägre reservationsutnyttjande. Du kommer med andra ord att betala för reservationer som inte utnyttjar fullt ut.

Du får också lägre besparingar om du minskar reservationskvantiteten. Även om du får större utnyttjande kommer det förmodligen att finnas perioder då dina reservationer inte täcker din användning. För den användning som överstiger reservationskvantiteten används dyrare resurser som betalas per användning. Följande exempel illustrerar detta. Vi har minskat reservationskvantiteten manuellt till 4. Reservationsanvändningen ökar, men de totala besparingarna minskar eftersom du också betalar per användning för resurser.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Exempel som visar ändrad rekommendation för reservationsköp" :::

Om du vill maximera besparingarna med reservationer försöker du köpa reservationer så nära rekommendationen som möjligt.

## <a name="recommendations-in-azure-advisor"></a>Rekommendationer i Azure Advisor

Rekommendationer för reservationsköp finns i Azure Advisor. Tänk på följande punkter:

- Advisor tillhandahåller endast rekommendationer som omfattas av en prenumeration.
- Rekommendationerna beräknas utifrån trenden för de senaste 30 dagarnas användning.
- Den rekommenderade kvantiteten och besparingar är för en 3-årig reservation, om tillgängligt. Om en 3 års reservation inte säljs för tjänsten beräknas rekommendationen med priset för 1 års reservation.
- Rekommendationer tar i beaktande alla rabatter som du kan ha för dina priser för användning på begäran.
- Om du köper en reservation med delad omfattning kan det ta upp till 30 dagar innan Advisors rekommendationer slutar att visas.

## <a name="other-expected-api-behavior"></a>Annat förväntat API-beteende

- Om du försöker få rekommendationer som bygger på data från de senaste sju dagarna kanske detta misslyckas om de virtuella datorerna har stängts ned i mer än en dag.

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur rabatten för Azure-reservation tillämpas på virtuella datorer](../manage/understand-vm-reservation-charges.md).
