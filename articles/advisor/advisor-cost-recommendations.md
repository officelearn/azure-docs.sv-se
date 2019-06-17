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
ms.openlocfilehash: fef7b82e6969de16d1815250d2373c99021b0e86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254729"
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

Advisor granskar din användning av virtuella datorer under de senaste 30 dagarna och avgöra om du kan spara pengar genom att köpa en Azure-reservation. Advisor visar regioner och storlekar där du potentiellt har de flesta besparingarna och visa de uppskattade besparingar från att köpa reservationer. Med Azure reservationer, kan du förhandsköpa grundläggande kostnaderna för dina virtuella datorer. Rabatter gäller automatiskt för nya eller befintliga virtuella datorer som har samma storlek och region som din reservationer. [Läs mer om reserverade VM-instanser.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor meddelar även om reserverade instanser som du har upphör att gälla inom 30 dagar. Det kommer att rekommendera att du köper nya reserverade instanser för att undvika att betala betalar per användning.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Ta bort olänkade offentliga IP-adresser för att spara pengar

Advisor identifierar offentliga IP-adresser som inte är associerade till Azure-resurser, till exempel belastningsutjämnare eller virtuella datorer. De här offentliga IP-adresser som levereras med en nominell avgift. Om du inte planerar att använda dem, kan tar bort dem resultera i kostnadsbesparingar.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Ta bort Azure Data Factory-pipelines som misslyckas

Azure Advisor identifierar Azure Data Factory-pipelines som misslyckas upprepade gånger och rekommenderar att du lösa problemen eller ta bort pipelines misslyckas om de inte längre behövs. Du kommer att debiteras för dessa pipelines även om även om de inte har du även om de inte importeras. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Använda ögonblicksbilder av Standard Managed Disks
Om du vill spara 60% av kostnaderna, rekommenderar vi lagrar dina ögonblicksbilder i standardlagring, oavsett vilken lagringstyp av den överordnade disken. Det här är standardalternativet för Managed Disks-ögonblicksbilder. Azure Advisor identifierar ögonblicksbilder som är lagrade Premium Storage och det rekommenderas att migrera din ögonblicksbild från Premium till Standard Storage. [Läs mer om priser för Managed Disk](https://aka.ms/aa_manageddisksnapshot_learnmore)

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
