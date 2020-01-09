---
title: Minska tjänste kostnaderna med Azure Advisor
description: Använd Azure Advisor för att optimera kostnaden för dina Azure-distributioner.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: afa5a4068d2ec5f4730d261801760fe68d7a330e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443130"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Minska tjänste kostnaderna med Azure Advisor

Advisor hjälper dig att optimera och minska dina totala Azure-utgifter genom att identifiera inaktiva och underutnyttjade resurser. Du kan få kostnads rekommendationer från fliken **kostnad** på Advisor-instrumentpanelen.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimera den virtuella datorn genom att ändra storlek på eller stänga av underutnyttjade instanser 

Även om vissa program scenarier kan resultera i låg användning genom design, kan du ofta Spara pengar genom att hantera storleken och antalet virtuella datorer. Advisor avancerade utvärderings modeller förväntar sig att virtuella datorer stängs av när P95th av Max värdet för processor användning är mindre än 3% och nätverks användningen är mindre än 2% under en 7 dagars period. Virtuella datorer anses vara av rätt storlek när det är möjligt att anpassa den aktuella belastningen på en mindre SKU (inom samma SKU-familj) eller ett mindre antal instanser, så att den aktuella inläsningen inte går över 80%-användning när icke-användare riktade arbets belastningar och inte över 40% När användarens arbets belastning. Här bestäms typen av arbets belastning genom analys av arbets Belastningens processor användnings egenskaper.

De rekommenderade åtgärderna är avstängda eller ändra storlek, vad gäller resurs som rekommenderas för. Advisor visar de uppskattade kostnaderna för antingen rekommenderade åtgärder – ändra storlek eller stänga av. För att ändra storlek på Rekommenderad åtgärd tillhandahåller Advisor den aktuella och riktade SKU-informationen. 

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
Vi rekommenderar att du lagrar ögonblicksbilder i Standard Storage, oavsett den överordnade diskens lagringstyp. Det gör att du kan att spara 60 % av kostnaderna. Det här alternativet är standard alternativet för Managed Disks ögonblicks bilder. Azure Advisor identifierar ögonblicks bilder som lagras Premium Storage och rekommenderar att du migrerar din ögonblicks bild från Premium till standard lagring. [Läs mer om priser för Managed disks](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Få åtkomst till kostnads rekommendationer i Azure Advisor

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj [**Advisor**](https://aka.ms/azureadvisordashboard) från vilken sida som helst.

1. På instrument panelen för **Advisor** väljer du fliken **kostnad** .

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång](advisor-get-started.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer om rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
