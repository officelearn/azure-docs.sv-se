---
title: Förbättra tillgängligheten för ditt program med Azure Advisor
description: Använd Azure Advisor för att förbättra hög tillgänglighet för dina Azure-distributioner.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 997681ed62fa9985e3122ece22565dbae0e65b53
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443115"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Förbättra tillgängligheten för ditt program med Azure Advisor

Azure Advisor hjälper dig att säkerställa och förbättra kontinuiteten i verksamhets kritiska program. Du kan få rekommendationer med hög tillgänglighet från Advisor från fliken **hög tillgänglighet** på instrument panelens instrument panel.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Se till att fel tolerans för virtuell dator

För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Advisor identifierar virtuella datorer som inte ingår i en tillgänglighets uppsättning och rekommenderar att du flyttar dem till en tillgänglighets uppsättning. Den här konfigurationen garanterar att minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse och uppfyller service avtalet för virtuell Azure-dator. Du kan välja att skapa en tillgänglighets uppsättning för den virtuella datorn eller lägga till den virtuella datorn i en befintlig tillgänglighets uppsättning.

> [!NOTE]
> Om du väljer att skapa en tillgänglighets uppsättning måste du lägga till minst en virtuell dator i den. Vi rekommenderar att du grupperar två eller flera virtuella datorer i en tillgänglighets uppsättning för att säkerställa att minst en dator är tillgänglig under ett avbrott.

## <a name="ensure-availability-set-fault-tolerance"></a>Se till att fel tolerans för tillgänglighets uppsättning

För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Advisor identifierar tillgänglighets uppsättningar som innehåller en enda virtuell dator och rekommenderar att du lägger till en eller flera virtuella datorer i den. Den här konfigurationen garanterar att minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse och uppfyller service avtalet för virtuell Azure-dator. Du kan välja att skapa en virtuell dator eller lägga till en befintlig virtuell dator i tillgänglighets uppsättningen.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Använd Managed Disks för ökad datatillförlitlighet

Virtuella datorer som finns i en tillgänglighets uppsättning med diskar som delar antingen lagrings konton eller lagrings skalnings enheter är inte elastiska till enskilda lagrings enhets enheter vid avbrott. Advisor identifierar dessa tillgänglighets uppsättningar och rekommenderar att du migrerar till Azure Managed Disks. På så sätt ser du till att diskarna för de olika virtuella datorerna i tillgänglighets uppsättningen är tillräckligt isolerade för att undvika en enskild felpunkt. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Säkerställ fel tolerans för Application Gateway

Den här rekommendationen säkerställer affärs kontinuiteten för verksamhets kritiska program som drivs av programgatewayer. Advisor identifierar de Application Gateway-instanser som inte har kon figurer ATS för fel tolerans och det föreslår åtgärds åtgärder som du kan vidta. Advisor identifierar medelhög eller stor Programgateway med en instans och rekommenderar att du lägger till minst en instans. Den identifierar också små programgatewayer med enkel eller flera instanser och rekommenderar att du migrerar till medel stora eller stora SKU: er. Advisor rekommenderar dessa åtgärder för att se till att dina Application Gateway-instanser är konfigurerade för att uppfylla de aktuella SLA-kraven för dessa resurser.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Skydda dina virtuella dator data från oavsiktlig borttagning

Att ställa in säkerhets kopiering av virtuella datorer garanterar att dina affärs kritiska data är tillgängliga och skyddar mot oavsiktlig borttagning eller skada. Advisor identifierar virtuella datorer där säkerhets kopiering inte är aktiverat och rekommenderar att du aktiverar säkerhets kopiering. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Se till att du har åtkomst till Azure Cloud-experter när du behöver det

När du kör en verksamhets kritisk arbets belastning är det viktigt att ha till gång till teknisk support vid behov. Advisor identifierar potentiella affärs kritiska prenumerationer som inte har teknisk support som ingår i support avtalet och rekommenderar att du uppgraderar till ett alternativ som omfattar teknisk support.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Skapa Azure Service Health aviseringar för att bli informerad när Azure-problem påverkar dig

Vi rekommenderar att du konfigurerar Azure Service Health aviseringar för att bli informerad när problem med Azure-tjänsten påverkar dig. [Azure Service Health](https://azure.microsoft.com/features/service-health/) är en kostnads fri tjänst som ger personlig vägledning och support när du påverkas av ett problem med en Azure-tjänst. Advisor identifierar prenumerationer som inte har konfigurerade varningar och rekommenderar att du skapar en.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurera Traffic Manager slut punkter för återhämtning

Traffic Manager profiler med mer än en slut punkts upplevelse högre tillgänglighet om någon av de två slut punkterna Miss lyckas. Att placera slut punkter i olika regioner förbättrar tillförlitligheten i tjänsten. Advisor identifierar Traffic Manager-profiler där det bara finns en slut punkt och rekommenderar att du lägger till minst en slut punkt i en annan region.

Om alla slut punkter i en Traffic Manager profil som har kon figurer ATS för närhets dirigering finns i samma region kan användare från andra regioner uppleva anslutnings fördröjningar. Genom att lägga till eller flytta en slut punkt till en annan region får du bättre prestanda och ger bättre tillgänglighet om alla slut punkter i en region kraschar. Advisor identifierar Traffic Manager profiler som kon figurer ATS för närhet av routning där alla slut punkter finns i samma region. Du bör lägga till eller flytta en slut punkt till en annan Azure-region.

Om en Traffic Manager profil har kon figurer ATS för geografisk routning dirigeras trafiken till slut punkter som baseras på definierade regioner. Om en region Miss lyckas finns det ingen fördefinierad redundans. Om du har en slut punkt där regional gruppering har kon figurer ATS till "alla (värld)" undviker du att trafiken släpps och att tjänstens tillgänglighet ökar. Advisor identifierar Traffic Manager profiler som kon figurer ATS för geografisk routning där det inte finns någon slut punkt konfigurerad att ha den regionala grupperingen "alla (värld)". Vi rekommenderar att du ändrar konfigurationen för att göra en slut punkt "alla (värld).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Använd mjuk borttagning på ditt Azure Storage konto för att spara och återställa data efter oavsiktlig överskrivning eller borttagning

Aktivera [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) på ditt lagrings konto så att borttagna blobar övergår till ett mjukt borttaget tillstånd i stället för att tas bort permanent. När data skrivs över skapas en mjukt borttagen ögonblicksbild för att spara läget för den överskrivna informationen. Med hjälp av mjuk borttagning kan du återställa om det sker oavsiktliga borttagningar eller Skriv över. Advisor identifierar Azure Storage konton som inte har mjuk borttagning aktiverat och föreslår att du aktiverar det.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurera din VPN-gateway till aktiv-aktiv för anslutnings återhämtning

I en aktiv-aktiv konfiguration kommer båda instanserna av en VPN-gateway att upprätta S2S VPN-tunnlar till din lokala VPN-enhet. När en händelse för ett planerat underhåll eller oplanerad händelse inträffar till en gateway-instans kommer trafiken att växlas över till den andra aktiva IPsec-tunneln automatiskt. Azure Advisor identifierar VPN-gatewayer som inte är konfigurerade som aktiva – aktiva och föreslår att du konfigurerar dem för hög tillgänglighet.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Använd VPN-gatewayer för produktion för att köra produktions arbets belastningar

Azure Advisor kommer att kontrol lera om det finns några VPN-gatewayer som är en grundläggande SKU och rekommenderar att du använder en produktions-SKU i stället. Bas-SKU: n är utformad för utveckling och testning. Produktions-SKU: er erbjuder ett högre antal tunnlar, BGP-stöd, aktiva-aktiva konfigurations alternativ, anpassad IPsec/IKE-princip och högre stabilitet och tillgänglighet.

## <a name="repair-invalid-log-alert-rules"></a>Reparera ogiltiga logg aviserings regler

Azure Advisor identifierar aviserings regler som har ogiltiga frågor angivna i villkors avsnittet. Loggaviseringsregler skapas i Azure Monitor och används för att köra analysfrågor enligt angivna intervall. Frågans resultat avgör sedan om en avisering måste utlösas eller inte. Analysfrågorna kan bli ogiltiga med tiden om något ändras i de resurser, tabeller eller kommandon som det hänvisas till. Advisor rekommenderar att du korrigerar frågan i aviserings regeln för att förhindra att den automatiskt inaktive ras och att övervaknings täckningen för dina resurser i Azure används. [Läs mer om fel sökning av aviserings regler](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Konfigurera konsekvent indexerings läge på din Cosmos DB-samling

Azure Cosmos DB behållare som kon figurer ATS med Lazy Indexing-läge kan påverka uppdaterings resultatet av frågeresultatet. Advisor identifierar behållare som kon figurer ATS på det här sättet och rekommenderar att du växlar till konsekvent läge. [Läs mer om indexerings principer i Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Ställ in Azure Cosmos DB-containrarna med en partitionsnyckel

Azure Advisor identifierar Azure Cosmos DB icke-partitionerade samlingar som närmar sig sin etablerade lagrings kvot. Det rekommenderar att du migrerar de här samlingarna till nya samlingar med en partitionsnyckel så att de automatiskt kan skalas ut av tjänsten. [Läs mer om hur du väljer en partitionsnyckel](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Uppgradera Azure Cosmos DB .NET-SDK till den senaste versionen från Nuget

Azure Advisor identifierar Azure Cosmos DB konton som använder gamla versioner av .NET SDK och rekommenderar att du uppgraderar till den senaste versionen från NuGet för de senaste korrigeringarna, prestanda förbättringar och nya funktioner. [Läs mer om Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Uppgradera Azure Cosmos DB Java-SDK till den senaste versionen från Maven

Azure Advisor identifierar Azure Cosmos DB konton som använder gamla versioner av Java SDK och rekommenderar att du uppgraderar till den senaste versionen från Maven för de senaste korrigeringarna, prestanda förbättringar och nya funktioner. [Läs mer om Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Uppgradera Azure Cosmos DB Spark-anslutningsprogrammet till den senaste versionen från Maven

Azure Advisor identifierar Azure Cosmos DB konton som använder tidigare versioner av Cosmos DB Spark-anslutningsprogrammet och rekommenderar att du uppgraderar till den senaste versionen från Maven för de senaste korrigeringarna, prestanda förbättringar och nya funktioner. [Läs mer om Cosmos DB Spark-anslutning](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Aktivera replikering av virtuell dator
Virtuella datorer som inte har replikering aktive rad till en annan region är inte elastiska till regionala avbrott. Replikering av virtuella datorer minskar eventuell negativ inverkan på verksamheten under tiden för ett avbrott i Azure-regionen. Advisor identifierar virtuella datorer som inte har någon aktive rad replikering och rekommenderar att du aktiverar replikering, så om ett avbrott inträffar kan du snabbt ta fram dina virtuella datorer i en fjärran sluten Azure-region. [Läs mer om replikering av virtuella datorer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Få åtkomst till rekommendationer för hög tillgänglighet i Advisor

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

2.  På Advisor-instrumentpanelen klickar du på fliken **hög tillgänglighet** .

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Rekommendationer om Advisor-kostnader](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
