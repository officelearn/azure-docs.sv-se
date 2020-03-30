---
title: Minska servicekostnaderna med Azure Advisor
description: Använd Azure Advisor för att optimera kostnaden för dina Azure-distributioner.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259700"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Minska servicekostnaderna med Azure Advisor

Advisor hjälper dig att optimera och minska dina totala Azure-utgifter genom att identifiera inaktiva och underutnyttjade resurser.Du kan få kostnadsrekommendationer från fliken **Kostnad** på advisor-instrumentpanelen.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimera utgifterna för den virtuella datorn genom att ändra storlek på eller stänga av underutnyttjade instanser 

Även om vissa programscenarier kan resultera i låg användning av design, kan du ofta spara pengar genom att hantera storleken och antalet på dina virtuella datorer. Advisor avancerade utvärderingsmodeller anser virtuella datorer för avstängning när P95th av max av max värdet av CPU-utnyttjande är mindre än 3% och nätverksutnyttjande är mindre än 2% under en 7-dagarsperiod. Virtuella datorer anses för rätt storlek när det är möjligt att passa den aktuella belastningen i en mindre SKU (inom samma SKU-familj) eller ett mindre antal # instans så att den aktuella belastningen inte går över 80% utnyttjande när icke-användare inför arbetsbelastningar och inte över 40% när användaren står inför arbetsbelastning. Här bestäms typen av arbetsbelastning genom att analysera processoranvändningsegenskaperna för arbetsbelastningen.

De rekommenderade åtgärderna är avstängning eller storlek, specifika för den resurs som rekommenderas för. Advisor visar de beräknade kostnadsbesparingarna för antingen rekommenderade åtgärder - ändra storlek eller avstängning. För att ändra storlek på rekommenderad åtgärd tillhandahåller Advisor också aktuell och målinriktad SKU-information. 

Om du vill vara mer aggressiv på att identifiera underutnyttjade virtuella datorer kan du justera cpu-användningsregeln per prenumeration.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Minska kostnaderna genom att eliminera oetablerade ExpressRoute-kretsar

Advisor identifierar ExpressRoute-kretsar som har varit i providerstatusen *Inte etablerat* i mer än en månad och rekommenderar att du tar bort kretsen om du inte planerar att etablera kretsen med din anslutningsleverantör.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Minska kostnaderna genom att ta bort eller konfigurera om inaktiva virtuella nätverksgatewayer

Advisor identifierar virtuella nätverksgateways som har varit inaktiva i över 90 dagar. Eftersom dessa gateways faktureras varje timme bör du överväga att konfigurera om eller ta bort dem om du inte tänker använda dem längre. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Köp reserverade instanser för virtuella datorer och spara pengar jämfört med om du betalar per användning

Advisor granskar användningen av virtuella datorer under de senaste 30 dagarna och avgör om du kan spara pengar genom att köpa en Azure-reservation. Advisor visar dig de regioner och storlekar där du potentiellt har mest besparingar och kommer att visa dig de beräknade besparingarna från att köpa bokningar. Med Azure-reservationer kan du förköpa baskostnaderna för dina virtuella datorer. Rabatter gäller automatiskt för nya eller befintliga virtuella datorer som har samma storlek och region som dina reservationer. [Läs mer om Azure-reserverade VM-instanser.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Rådgivare kommer också att meddela dig om reserverade instanser som du har som kommer att löpa ut inom de närmaste 30 dagarna. Det kommer att rekommendera att du köper nya reserverade instanser för att undvika att betala per användning prissättning.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Ta bort oassocierade offentliga IP-adresser för att spara pengar

Advisor identifierar offentliga IP-adresser som för närvarande inte är associerade till Azure-resurser som belastningsutjämnare eller virtuella datorer. Dessa offentliga IP-adresser kommer med en nominell avgift. Om du inte planerar att använda dem kan det leda till kostnadsbesparingar om du tar bort dem.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Ta bort Azure Data Factory-pipelines som inte fungerar

Azure Advisor identifierar Azure Data Factory-pipelines som upprepade gånger misslyckas och rekommenderar att du löser problemen eller tar bort de misslyckade pipelines om de inte längre behövs. Du kommer att faktureras för dessa rörledningar även om de inte tjänar dig medan de misslyckas. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Använda standardögonblicksbilder för hanterade diskar
Vi rekommenderar att du lagrar ögonblicksbilder i Standard Storage, oavsett den överordnade diskens lagringstyp. Det gör att du kan att spara 60 % av kostnaderna. Det här alternativet är standardalternativet för ögonblicksbilder av hanterade diskar. Azure Advisor identifierar ögonblicksbilder som lagras i Premium Storage och rekommenderar att du migrerar ögonblicksbilden från Premium till Standard Storage. [Läs mer om priser för hanterade diskar](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Använda livscykelhantering
Azure Advisor använder information om antalet Azure-blob-lagringsobjekt, den totala storleken och transaktionerna för att identifiera om ett eller flera av dina lagringskonton skulle vara bäst lämpade för att möjliggöra livscykelhantering på nivådata. Det kommer att uppmana dig att skapa livscykelhanteringsregler för att automatiskt nivå dina data till Cool eller Arkiv för att optimera dina lagringskostnader samtidigt som du behåller dina data i Azure blob-lagring för programkompatibilitet.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Så här kommer du åt kostnadsrekommendationer i Azure Advisor

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj [**Advisor**](https://aka.ms/azureadvisordashboard) från valfri sida.

1. Välj fliken Kostnad på instrumentpanelen **För Rådgivare.** **Advisor**

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Advisor](advisor-overview.md)
* [Komma igång](advisor-get-started.md)
* [Advisors prestandarekommendationer](advisor-performance-recommendations.md)
* [Advisor rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Säkerhetsrekommendationer för rådgivare](advisor-security-recommendations.md)
* [Advisor Operational Excellence rekommendationer](advisor-operational-excellence-recommendations.md)
