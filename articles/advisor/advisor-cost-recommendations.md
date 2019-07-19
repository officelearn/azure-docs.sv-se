---
title: Minska tjänste kostnaderna med Azure Advisor | Microsoft Docs
description: Använd Azure Advisor för att optimera kostnaden för dina Azure-distributioner.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 96e939f8e3da58a123d9a6733b71b74c2ff0ba87
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311900"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Minska tjänste kostnaderna med Azure Advisor

Advisor hjälper dig att optimera och minska dina totala Azure-utgifter genom att identifiera inaktiva och underutnyttjade resurser. Du kan få kostnads rekommendationer från fliken **kostnad** på Advisor-instrumentpanelen.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimera den virtuella datorn genom att ändra storlek på eller stänga av underutnyttjade instanser 

Även om vissa program scenarier kan resultera i låg användning genom design, kan du ofta Spara pengar genom att hantera storleken och antalet virtuella datorer. Advisor övervakar din användning av virtuella datorer i 7 dagar och identifierar virtuella datorer med låg användning. Virtuella datorer betraktas som en låg belastning om processor användningen är 5% eller mindre och användningen av nätverket är mindre än 2% eller om den aktuella arbets belastningen kan hanteras av en mindre virtuell dator storlek.

Advisor visar den beräknade kostnaden för att fortsätta att köra den virtuella datorn, så att du kan välja att stänga av den eller ändra storlek på den.

Om du vill vara mer aggressiv vid identifiering av underutnyttjade virtuella datorer kan du justera processor användnings regeln per prenumeration.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Minska kostnaderna genom att ta bort unetablerad ExpressRoute-kretsar

Advisor identifierar ExpressRoute-kretsar som har varit i leverantörs statusen *inte etablerad* i mer än en månad och rekommenderar att du tar bort kretsen om du inte planerar att etablera kretsen med anslutnings leverantören.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Minska kostnaderna genom att ta bort eller konfigurera om inaktiva virtuella nätverks-gatewayer

Advisor identifierar virtuella nätverksgateway som har varit inaktiva i över 90 dagar. Eftersom dessa gateways faktureras per timme bör du överväga att konfigurera om eller ta bort dem om du inte tänker använda dem längre. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Köp reserverade instanser av virtuella datorer för att spara pengar jämfört med betala per användning-kostnad

Advisor granskar användningen av den virtuella datorn under de senaste 30 dagarna och avgör om du kan spara pengar genom att köpa en Azure-reservation. Advisor visar de regioner och storlekar där du kan ha flest besparingar och visar de uppskattade besparingarna från inköps reservationer. Med Azure-reservationer kan du i förväg köpa bas kostnaderna för dina virtuella datorer. Rabatter gäller automatiskt för nya eller befintliga virtuella datorer som har samma storlek och region som dina reservationer. [Läs mer om Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor meddelar dig även om reserverade instanser som upphör att gälla inom de närmaste 30 dagarna. Vi rekommenderar att du köper nya reserverade instanser för att undvika att betala per användning-prissättning.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Ta bort ej associerade offentliga IP-adresser för att spara pengar

Advisor identifierar offentliga IP-adresser som för närvarande inte är associerade till Azure-resurser, till exempel belastningsutjämnare eller virtuella datorer. Dessa offentliga IP-adresser levereras med en nominell avgift. Om du inte planerar att använda dem kan du ta bort dem genom att ta bort dem.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Ta bort Azure Data Factory pipelines som inte fungerar

Azure Advisor identifierar Azure Data Factory pipeliner som Miss söker upprepade gånger och rekommenderar att du löser problemen eller tar bort de misslyckade pipelinen om de inte längre behövs. Du debiteras för dessa pipelines även om de inte betjänar dig när de Miss söker. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Använd standard ögonblicks bilder för Managed Disks
För att spara 60% av kostnaden rekommenderar vi att du lagrar dina ögonblicks bilder i standard lagring, oavsett lagrings typ för den överordnade disken. Detta är standard alternativet för Managed Disks ögonblicks bilder. Azure Advisor identifierar ögonblicks bilder som lagras Premium Storage och rekommenderar att du migrerar din ögonblicks bild från Premium till standard lagring. [Läs mer om priser för Managed disks](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Få åtkomst till kostnads rekommendationer i Azure Advisor

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

2.  Klicka på fliken **kostnad** på Advisor-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång](advisor-get-started.md)
* [Rekommendationer för Advisor-prestanda](advisor-cost-recommendations.md)
* [Rekommendationer om rekommendationer för hög tillgänglighet](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-cost-recommendations.md)
