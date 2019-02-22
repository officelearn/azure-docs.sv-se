---
title: Minska tjänstekostnaderna med hjälp av Azure Advisor | Microsoft Docs
description: Använda Azure Advisor för att optimera kostnaden för dina Azure-distributioner.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: cc5c4310d5f9886518bee6612113fba174130bec
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648724"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Minska tjänstekostnaderna med hjälp av Azure Advisor

Advisor hjälper dig att optimera och minska de totala Azure spendera genom att identifiera inaktiv och underutnyttjade resurser. Du kan hämta kosta rekommendationerna från den **kostnaden** på Advisor-instrumentpanelen.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimera VM spendera genom att ändra storlek på eller stänga av underutnyttjade instanser 

Även om vissa Programscenarier kan resultera i låg användning av design, kan du ofta spara pengar genom att hantera storlek och antalet virtuella datorer. Advisor övervakar din användning av virtuella datorer i 7 dagar och identifierar virtuella datorer med låg användning. Virtuella datorer betraktas som låg användning om deras CPU-belastning är 5% eller mindre och sin nätverksanvändning är mindre än 2% eller om den nuvarande arbetsbelastningen kan utföras av en mindre storlek på den virtuella datorn.

Advisor visar den uppskattade kostnaden för fortsätter att köra den virtuella datorn så att du kan välja att stänga av eller ändra storlek på den.

Om du vill ha mer aggressivt för att fastställa underutnyttjade virtuella datorer, kan du justera Genomsnittlig CPU användning regeln på basis av per prenumeration.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Minska kostnaderna genom att eliminera avetablerad ExpressRoute-kretsar

Advisor identifierar ExpressRoute-kretsar som har haft leverantörsstatusen *inte etablerats* för mer än en månad och rekommenderar att radera kretsen om du inte planerar att etablera den hos din anslutning providern.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Minska kostnaderna genom att ta bort eller konfigurera om inaktiva virtuella nätverksgatewayer

Advisor identifierar portar för virtuellt nätverk som har varit inaktiv i över 90 dagar. Eftersom dessa gateways faktureras per timme, bör du konfigurera om eller ta bort dem. Om du inte planerar att använda dem längre. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Köp reserverade VM-instanser och spara pengar jämfört med betala per användning

Advisor granskar din användning av virtuella datorer under de senaste 30 dagarna och avgöra om du kan spara pengar genom att köpa en Azure-reservation. Advisor visar regioner och storlekar där du potentiellt har de flesta besparingarna och visa de uppskattade besparingar från att köpa reservationer. 

Med Azure reservationer, kan du förhandsköpa grundläggande kostnaderna för dina virtuella datorer. Rabatter gäller automatiskt för nya eller befintliga virtuella datorer som har samma storlek och region som din reservationer. [Läs mer om reserverade VM-instanser.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Hur du kommer åt kostnadsrekommendationer i Azure Advisor

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

2.  På Advisor-instrumentpanelen klickar du på den **kostnaden** fliken.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:
* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång](advisor-get-started.md)
* [Advisor-rekommendationer](advisor-cost-recommendations.md)
* [Advisor-rekommendationer för hög tillgänglighet](advisor-cost-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-cost-recommendations.md)
