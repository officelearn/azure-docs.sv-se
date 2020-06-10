---
title: Minska tjänste kostnaderna med Azure Advisor
description: Använd Azure Advisor för att optimera kostnaden för dina Azure-distributioner.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 99dfec669d8981a557b2e8a8d8979292af74616f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658571"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Minska tjänste kostnaderna med Azure Advisor

Advisor hjälper dig att optimera och minska dina totala Azure-utgifter genom att identifiera inaktiva och underutnyttjade resurser.Du kan få kostnads rekommendationer från fliken **kostnad** på Advisor-instrumentpanelen.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimera utgifterna för den virtuella datorn genom att ändra storlek på eller stänga av underutnyttjade instanser 

Även om vissa program scenarier kan resultera i låg användning genom design, kan du ofta Spara pengar genom att hantera storleken och antalet virtuella datorer. Advisor avancerade utvärderings modeller förväntar sig att virtuella datorer stängs av när P95th av Max värdet för processor användning är mindre än 3% och nätverks användningen är mindre än 2% under en 7 dagars period. Virtuella datorer anses vara av rätt storlek när det är möjligt att anpassa den aktuella belastningen på en mindre SKU (inom samma SKU-familj) eller ett mindre antal instanser, så att den aktuella inläsningen inte går över 80%-användning när icke-användare riktade arbets belastningar och inte över 40% När användarens arbets belastning. Här bestäms typen av arbets belastning genom analys av arbets Belastningens processor användnings egenskaper.

De rekommenderade åtgärderna är avstängda eller ändra storlek, vad gäller resurs som rekommenderas för. Advisor visar de uppskattade kostnaderna för antingen rekommenderade åtgärder – ändra storlek eller stänga av. För att ändra storlek på Rekommenderad åtgärd tillhandahåller Advisor den aktuella och riktade SKU-informationen. 

Om du vill vara mer aggressiv vid identifiering av underutnyttjade virtuella datorer kan du justera processor användnings regeln per prenumeration.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Optimera utgifter för MariaDB-, MySQL-och PostgreSQL-servrar med rätt storlek 
Advisor analyserar din användning och rekommenderar att om dina MariaDB/MySQL/PostgreSQL-databas server resurser har underutnyttjats under en längre tid under de senaste 7 dagarna. Lågt resursutnyttjande ger oönskade utgifter som kan åtgärdas utan betydande prestandapåverkan. För att minska kostnaderna och hantera dina resurser effektivt rekommenderar vi att du minskar beräkningsstorleken (virtuella kärnor) med hälften.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Minska kostnaderna genom att ta bort unetablerad ExpressRoute-kretsar

Advisor identifierar ExpressRoute-kretsar som har varit i leverantörs statusen *inte etablerad* i mer än en månad och rekommenderar att du tar bort kretsen om du inte planerar att etablera kretsen med anslutnings leverantören.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Minska kostnaderna genom att ta bort eller konfigurera om inaktiva virtuella nätverksgatewayer

Advisor identifierar virtuella nätverksgateway som har varit inaktiva i över 90 dagar. Eftersom dessa gateways faktureras per timme bör du överväga att konfigurera om eller ta bort dem om du inte tänker använda dem längre. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Köp reserverade instanser för virtuella datorer och spara pengar jämfört med om du betalar per användning

Advisor granskar användningen av den virtuella datorn under de senaste 30 dagarna och avgör om du kan spara pengar genom att köpa en Azure-reservation. Advisor visar de regioner och storlekar där du kan ha flest besparingar och visar de uppskattade besparingarna från inköps reservationer. Med Azure-reservationer kan du i förväg köpa bas kostnaderna för dina virtuella datorer. Rabatter gäller automatiskt för nya eller befintliga virtuella datorer som har samma storlek och region som dina reservationer. [Läs mer om Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor meddelar dig även om reserverade instanser som upphör att gälla inom de närmaste 30 dagarna. Vi rekommenderar att du köper nya reserverade instanser för att undvika att betala per användning-prissättning.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Köp reserverade instanser för flera resurs typer för att spara över din betala per användning-kostnad

Vi analyserar användnings mönstret för nedanstående lista över resurser, under de senaste 30 dagarna och rekommenderar reserverat kapacitets köp som maximerar dina besparingar. 
### <a name="cosmos-db-reserved-capacity"></a>Reserverad Cosmos DB-kapacitet
Vi har analyserat ditt Cosmos DB-användningsmönster under de senaste 30 dagarna och rekommenderar att du köper reserverad kapacitet för att optimera kostnaderna. Med reserverad kapacitet kan du köpa användning per timme i Cosmos DB i förväg och spara på dina användningsbaserade kostnader. Reserverad kapacitet är en faktureringsförmån och gäller automatiskt för nya eller befintliga distributioner. Uppskattade besparingar beräknas för enskilda prenumerationer med priser för tre års reservation och genom att extrapolera användningsmönstret som observerats under de senaste 30 dagarna. Rekommendationer för delat reservationsomfång finns i funktionerna för reservationsköpet och kan ge ännu större besparingar.

### <a name="sql-paas-reserved-capacity"></a>Reserverad SQL PaaS-kapacitet
Vi analyserar elastiska SQL PaaS-pooler och användningsmönster för hanterade instanser under de senaste 30 dagarna och rekommenderar att du köper reserverad kapacitet för att maximera dina besparingar. Med reserverad kapacitet kan du köpa användning per timme i SQL DB i förväg och spara på dina SQL-beräkningskostnader. SQL-licensen debiteras separat och dras inte av genom reservationen. Reserverad kapacitet är en faktureringsförmån och gäller automatiskt för nya eller befintliga distributioner. Uppskattade besparingar beräknas för enskilda prenumerationer med priser för tre års reservation och genom att extrapolera användningsmönstret som observerats under de senaste 30 dagarna. Rekommendationer för delat reservationsomfång finns i funktionerna för reservationsköpet och kan ge ytterligare besparingar.

### <a name="app-service-stamp-fee-reserved-capacity"></a>Reserverad kapacitet för App Service-avgift
Vi analyserar App Service isolerade miljöstämplar användnings mönster under de senaste 30 dagarna och rekommenderar inköp med reserverat kapacitet som maximerar dina besparingar. Med reserverad kapacitet kan du köpa användning per timme i förväg för stämpelavgift för isolerad miljö och spara på dina användningsbaserade kostnader. Observera att den reserverade kapaciteten bara gäller för stämpelavgiften och inte för App Service-instanserna. Reserverad kapacitet är en faktureringsförmån och gäller automatiskt för nya eller befintliga distributioner. Uppskattade besparingar beräknas för enskilda prenumerationer med priser för tre års reservation baserat på användningsmönstret under de senaste 30 dagarna.

### <a name="blob-storage-reserved-capacity"></a>Reserverad Blob Storage-kapacitet
Vi har analyserat din användning av Azure Blob och Datalake Storage under de senaste 30 dagarna och beräknat köp av reserverad kapacitet som skulle maximera dina besparingar. Med reserverad kapacitet kan du köpa användning per timme i förväg och spara på dina aktuella på begäran-kostnader. Reserverad kapacitet i Blob Storage gäller bara för data som lagras på Azure Blob (GPv2) och Azure Data Lake Storage (Gen 2). Reserverad kapacitet är en faktureringsförmån och gäller automatiskt för nya eller befintliga distributioner. Uppskattade besparingar beräknas för enskilda prenumerationer med priser för tre års reservation och användningsmönstret under de senaste 30 dagarna. Rekommendationer för delat reservationsomfång finns i funktionerna för reservationsköpet och kan ge ytterligare besparingar.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>MariaDB, MySQL och PostgreSQL reserverad kapacitet
Vi analyserar ditt Azure Database for MariaDB, MySQL och PostgreSQL användnings mönster under de senaste 30 dagarna och rekommenderar reserverat kapacitets köp som maximerar dina besparingar. Med reserverad kapacitet kan du för hands köpa MariaDB, MySQL och PostgreSQL per timme och spara pengar. Reserverad kapacitet är en faktureringsförmån och gäller automatiskt för nya eller befintliga distributioner. Uppskattade besparingar beräknas för enskilda prenumerationer med priser för tre års reservation och användningsmönstret under de senaste 30 dagarna. Rekommendationer för delat reservationsomfång finns i funktionerna för reservationsköpet och kan ge ytterligare besparingar.

### <a name="synapse-analytics-formerly-sql-dw-reserved-capacity"></a>Synapse-analys (tidigare SQL DW) reserverad kapacitet
Vi analyserar användnings mönstret för Azure Synapse Analytics under de senaste 30 dagarna och rekommenderar reserverat kapacitets köp som maximerar dina besparingar. Med reserverad kapacitet kan du köpa användning per timme för Synapse Analytics i förväg och spara pengar jämfört med ”på begäran”-kostnaderna. Reserverad kapacitet är en faktureringsförmån och gäller automatiskt för nya eller befintliga distributioner. Uppskattade besparingar beräknas för enskilda prenumerationer med priser för tre års reservation och användningsmönstret under de senaste 30 dagarna. Rekommendationer för delat reservationsomfång finns i funktionerna för reservationsköpet och kan ge ytterligare besparingar.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Ta bort ej associerade offentliga IP-adresser för att spara pengar

Advisor identifierar offentliga IP-adresser som för närvarande inte är associerade till Azure-resurser, till exempel belastningsutjämnare eller virtuella datorer. Dessa offentliga IP-adresser levereras med en nominell avgift. Om du inte planerar att använda dem kan du ta bort dem genom att ta bort dem.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Ta bort Azure Data Factory-pipelines som inte fungerar

Azure Advisor identifierar Azure Data Factory pipeliner som Miss söker upprepade gånger och rekommenderar att du löser problemen eller tar bort de misslyckade pipelinen om de inte längre behövs. Du debiteras för dessa pipelines även om de inte betjänar dig när de Miss söker. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Använd standard ögonblicks bilder för Managed Disks
Vi rekommenderar att du lagrar ögonblicksbilder i Standard Storage, oavsett den överordnade diskens lagringstyp. Det gör att du kan att spara 60 % av kostnaderna. Det här alternativet är standard alternativet för Managed Disks ögonblicks bilder. Azure Advisor identifierar ögonblicks bilder som lagras Premium Storage och rekommenderar att du migrerar din ögonblicks bild från Premium till standard lagring. [Läs mer om priser för Managed disks](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Använda livscykelhantering
Azure Advisor använder intelligens för Azure Blob Storage-objekt antal, Total storlek och transaktioner för att identifiera om ett eller flera av dina lagrings konton passar bäst för att aktivera livs cykel hantering för att hantera data på nivån. Du uppmanas att skapa livs cykel hanterings regler för att automatiskt anpassa dina data till häftig eller arkivera för att optimera dina lagrings kostnader samtidigt som dina data lagras i Azure Blob Storage för programkompatibilitet.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Rekommendation om skapande av en tillfällig OS-disk
Med en [tillfällig OS-disk](https://docs.microsoft.com/azure/virtual-machines/windows/ephemeral-os-disks)får kunderna följande förmåner: Spara pengar på lagrings kostnaden för operativ system disken. Lägre svarstider för läsning/skrivning till OS-diskar. Snabbare återställning av VM-avbildningar genom återställning av operativsystemet (och den tillfälliga disken) till det ursprungliga tillståndet. Det är bättre att använda en tillfällig OS-disk för IaaS virtuella datorer eller virtuella datorer med tillstånds lösa. Advisor har rekommendationer för resurser som kan dra nytta av den tillfälliga OS-disken. 


## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Få åtkomst till kostnads rekommendationer i Azure Advisor

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Sök efter och välj [**Advisor**](https://aka.ms/azureadvisordashboard) från vilken sida som helst.

1. På instrument panelen för **Advisor** väljer du fliken **kostnad** .

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer om rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)

