---
title: Så här skapas Azure-reservationsrekommendationer
description: Lär dig hur Azure-reservationsrekommendationer skapas för virtuella datorer.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851359"
---
# <a name="how-reservation-recommendations-are-created"></a>Så här skapas reservationsrekommendationer

Inköpsrekommendationer för reserverade Azure-instanser (RI) skapas av [API:et för reservationsrekommendationer](/rest/api/consumption/reservationrecommendations) för Azure Consumption. Rekommendationer från API:et används även av [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs). Advisor visar rekommendationer i Azure-portalen.

Om du har virtuella datorer som körs i Azure kan du dra nytta av rabatterat pris för RI och förskottsbetalning för dina virtuella datorer. Rekommendations-API:et för Microsoft Consumption utvärderar din användning under 7, 30 och 60 dagar och rekommenderar optimala konfigurationer för RI. Det beräknar den kostnad som du skulle betala om du inte hade haft RI jämfört med den kostnad som du betalar med RI för att optimera besparingar.

Azure Advisor visar rekommendationer baserat på en 30-dagarsperiod.

För enkelhetens skull visar följande exempel beräkningar som sker för en sjudagars rekommendation. Samma metod används vid beräkning av 30- eller 60-dagars rekommendationer.

## <a name="calculation-method-example"></a>Exempel på beräkningsmetod

Anta att din användning av virtuella Windows-datorer per timme för en specifik SKU och region varierar under sju dagar (168 timmar). Den lägsta användningen är 65 enheter och den högsta användningen är 127 enheter under de sju dagarna. Timmen 79 i det här exemplet använde 80 virtuella datorer, och du köpte 75 RI.

Om du köper 75 reserverade instanser betalar du följande kostnader för timme 79:

- 75 reserverade instanser. Kostnaden betalas i förväg när du köper RI.
- Reserverade instanser omfattar maskinvarukostnaden för att köra virtuella datorer, så du betalar för 75 timmar till priset för enbart programvara.
- Användningen för timme 79 är 80, så du betalar för fem timmar med mätarpriset för kombinationen med Windows plus maskinvara. Kombinationspriset baseras antingen på ditt Enterprise-avtal (EA) eller på Betala per användning-priset.

Om du köper 75 RI kan du beräkna den totala kostnaden genom att lägga till föregående timkostnader. Du kan även beräkna din nuvarande kostnad med hjälp av ditt pris. Skillnaden mellan de två beloppen är dina besparingar för sju dagar i det här exemplet.

API:et utför beräkningar för varje specifik användningspunkt. Sedan returnerar det den rekommenderade kvantitet där besparingarna maximeras. I följande exempel visar grafen att besparingarna är som högst vid 68. Besparingarna minskar efteråt, och API:et rekommenderar därför 68.

![Diagram över toppbesparingar](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>Annat förväntat API-beteende

- API:et visar möjliga besparingar med [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) för Windows när förmånen används. Om förmånen inte används baseras API-rekommendationerna på kärnkostnaden för Windows. Om Azure Hybrid-förmån är tillgängligt för dig bör du överväga att använda det för att öka besparingarna.
- När du använder en tillbakablicksperiod på sju dagar får du kanske inte rekommendationer när virtuella datorer stängs av i mer än en dag.

## <a name="next-steps"></a>Nästa steg
- Lär dig [hur rabatten för Azure-reservation tillämpas på virtuella datorer](../manage/understand-vm-reservation-charges.md).
