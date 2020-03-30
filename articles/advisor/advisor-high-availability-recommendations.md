---
title: Förbättra tillgängligheten för ditt program med Azure Advisor
description: Använd Azure Advisor för att förbättra hög tillgänglighet för dina Azure-distributioner.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 997681ed62fa9985e3122ece22565dbae0e65b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443115"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Förbättra tillgängligheten för ditt program med Azure Advisor

Azure Advisor hjälper dig att säkerställa och förbättra kontinuiteten i dina affärskritiska program. Du kan få rekommendationer om hög tillgänglighet av Advisor från fliken **Hög tillgänglighet på** advisor-instrumentpanelen.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Säkerställ feltolerans för virtuella datorer

För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Advisor identifierar virtuella datorer som inte ingår i en tillgänglighetsuppsättning och rekommenderar att du flyttar dem till en tillgänglighetsuppsättning. Den här konfigurationen säkerställer att under antingen en planerad eller oplanerad underhållshändelse är minst en virtuell dator tillgänglig och uppfyller Azure-serviceavtalet för virtuella datorer. Du kan välja att skapa en tillgänglighetsuppsättning för den virtuella datorn eller lägga till den virtuella datorn i en befintlig tillgänglighetsuppsättning.

> [!NOTE]
> Om du väljer att skapa en tillgänglighetsuppsättning måste du lägga till minst en virtuell dator till den. Vi rekommenderar att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning för att säkerställa att minst en dator är tillgänglig under ett avbrott.

## <a name="ensure-availability-set-fault-tolerance"></a>Se till att feltolerans för tillgänglighet ställs in

För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Advisor identifierar tillgänglighetsuppsättningar som innehåller en enda virtuell dator och rekommenderar att du lägger till en eller flera virtuella datorer i den.Den här konfigurationen säkerställer att under antingen en planerad eller oplanerad underhållshändelse är minst en virtuell dator tillgänglig och uppfyller Azure-serviceavtalet för virtuella datorer.Du kan välja att skapa en virtuell dator eller lägga till en befintlig virtuell dator i tillgänglighetsuppsättningen.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Använd Managed Disks för ökad datatillförlitlighet

Virtuella datorer som har en tillgänglighetsuppsättning med diskar som delar lagringskonton eller lagringsskalenheter är inte motståndskraftiga mot fel på enskilda lagringsenheter vid avbrott. Advisor identifierar dessa tillgänglighetsuppsättningar och rekommenderar att migrera till Azure Managed Disks. Detta säkerställer att diskarna för de olika virtuella datorerna i tillgänglighetsuppsättningen är tillräckligt isolerade för att undvika en enda felpunkt. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Kontrollera feltolerans mot programgateway

Den här rekommendationen säkerställer kontinuiteten i verksamhetskritiska program som drivs av programgateways. Advisor identifierar programgatewayinstanser som inte är konfigurerade för feltolerans och föreslår åtgärder som du kan vidta. Advisor identifierar medelstora eller stora en instansprogramgateways och rekommenderar att du lägger till minst en instans till. Den identifierar också små programgateways för en eller flera instanser och rekommenderar att migrera till medelstora eller stora SKU:er. Advisor rekommenderar dessa åtgärder för att säkerställa att dina programgatewayinstanser är konfigurerade för att uppfylla de aktuella SLA-kraven för dessa resurser.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Skydda data från virtuella datorer från oavsiktlig radering

Genom att konfigurera säkerhetskopiering av virtuella datorer säkerställs tillgängligheten för dina affärskritiska data och skydd mot oavsiktlig borttagning eller skada. Advisor identifierar virtuella datorer där säkerhetskopiering inte är aktiverat och rekommenderar att du aktiverar säkerhetskopiering. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Se till att du har åtkomst till Azure-molnexperter när du behöver det

När du kör en affärskritisk arbetsbelastning är det viktigt att ha tillgång till teknisk support när det behövs. Advisor identifierar potentiella affärskritiska prenumerationer som inte har teknisk support som ingår i supportplanen och rekommenderar uppgradering till ett alternativ som inkluderar teknisk support.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Skapa Azure Service Health-aviseringar som ska meddelas när Azure-problem påverkar dig

Vi rekommenderar att du ställer in Azure Service Health-aviseringar som ska meddelas när Azure-tjänstproblem påverkar dig. [Azure Service Health](https://azure.microsoft.com/features/service-health/) är en kostnadsfri tjänst som ger personlig vägledning och support när du påverkas av ett Azure-tjänstproblem. Advisor identifierar prenumerationer som inte har aviseringar konfigurerade och rekommenderar att du skapar en.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurera Traffic Manager-slutpunkter för återhämtning

Traffic Manager-profiler med mer än en slutpunkt får högre tillgänglighet om en viss slutpunkt misslyckas. Om du placerar slutpunkter i olika regioner förbättras servicetillförlitligheten ytterligare. Advisor identifierar Traffic Manger-profiler där det bara finns en slutpunkt och rekommenderar att du lägger till minst en slutpunkt till i en annan region.

Om alla slutpunkter i en Traffic Manager-profil som är konfigurerad för närhetsdirigering finns i samma region kan användare från andra regioner drabbas av anslutningsfördröjningar. Om du lägger till eller flyttar en slutpunkt till en annan region förbättras den övergripande prestandan och alla slutpunkter i en region misslyckas. Advisor identifierar Traffic Manager-profiler som konfigurerats för närhetsdirigering där alla slutpunkter finns i samma region. Den rekommenderar att du lägger till eller flyttar en slutpunkt till en annan Azure-region.

Om en Traffic Manager-profil har konfigurerats för geografisk routning dirigeras trafiken till slutpunkter baserat på definierade regioner. Om en region misslyckas finns det ingen fördefinierad redundans. Att ha en slutpunkt där den regionala gruppgruppen är konfigurerad till "Alla (världen)" undviker att trafiken tappas och förbättrar tjänstens tillgänglighet. Advisor identifierar Traffic Manager-profiler som konfigurerats för geografisk routning där det inte finns någon slutpunkt konfigurerad för att ha den regionala grupperingen som "Alla (världen)". Det rekommenderar att ändra konfigurationen för att göra en slutpunkt "Alla (World).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Använd mjuk borttagning på ditt Azure Storage-konto för att spara och återställa data efter oavsiktlig överskrivning eller borttagning

Aktivera [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) på ditt lagringskonto så att borttagna blobbar övergår till ett mjukt borttaget tillstånd i stället för att tas bort permanent. När data skrivs över skapas en mjukt borttagen ögonblicksbild för att spara läget för den överskrivna informationen. Med hjälp av mjuk borttagning kan du återställa om det finns oavsiktliga borttagningar eller skriva över. Advisor identifierar Azure Storage-konton som inte har mjuk borttagning aktiverat och föreslår att du aktiverar det.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurera VPN-gatewayen till aktiv aktiv för anslutningsåtersåterkoppling

I aktiv aktiv konfiguration upprättar båda instanserna av en VPN-gateway S2S VPN-tunnlar till din lokala VPN-enhet. När en planerad underhållshändelse eller oplanerad händelse inträffar till en gateway-instans, kommer trafiken att växlas över till den andra aktiva IPsec-tunneln automatiskt. Azure Advisor identifierar VPN-gateways som inte är konfigurerade som aktiva aktiva och föreslår att du konfigurerar dem för hög tillgänglighet.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Använda vpn-gateways för produktion för att köra dina produktionsarbetsbelastningar

Azure Advisor söker efter alla VPN-gateways som är en Basic SKU och rekommenderar att du använder en produktions-SKU i stället. Basic SKU är utformad för utvecklings- och testningsändamål. Produktionssedel erbjuder ett större antal tunnlar, BGP-stöd, aktiva konfigurationsalternativ, anpassad Ipsec/IKE-princip och högre stabilitet och tillgänglighet.

## <a name="repair-invalid-log-alert-rules"></a>Reparera ogiltiga loggvarningsregler

Azure Advisor identifierar aviseringsregler som har ogiltiga frågor som anges i deras villkorsavsnitt. Loggaviseringsregler skapas i Azure Monitor och används för att köra analysfrågor enligt angivna intervall. Frågans resultat avgör sedan om en avisering måste utlösas eller inte. Analysfrågorna kan bli ogiltiga med tiden om något ändras i de resurser, tabeller eller kommandon som det hänvisas till. Advisor rekommenderar att du korrigerar frågan i aviseringsregeln för att förhindra att den inaktiveras automatiskt och säkerställer övervakning av dina resurser i Azure. [Läs mer om felsökning av varningsregler](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Konfigurera konsekvent indexeringsläge i Cosmos DB-samlingen

Azure Cosmos DB-behållare som konfigurerats med lazy-indexeringsläge kan påverka frågeresultatens färskhet. Advisor identifierar behållare som konfigurerats på det här sättet och rekommenderar att du växlar till konsekvent läge. [Läs mer om indexeringsprinciper i Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Ställ in Azure Cosmos DB-containrarna med en partitionsnyckel

Azure Advisor identifierar Azure Cosmos DB icke-partitionerade samlingar som närmar sig sin etablerade lagringskvot. Det kommer att rekommendera att migrera dessa samlingar till nya samlingar med en partition nyckel definition så att de automatiskt kan skalas ut av tjänsten. [Läs mer om att välja en partitionsnyckel](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Uppgradera Azure Cosmos DB .NET-SDK till den senaste versionen från Nuget

Azure Advisor identifierar Azure Cosmos DB-konton som använder gamla versioner av .NET SDK och rekommenderar uppgradering till den senaste versionen från Nuget för de senaste korrigeringarna, prestandaförbättringarna och de nya funktionsfunktionerna. [Läs mer om Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Uppgradera Azure Cosmos DB Java-SDK till den senaste versionen från Maven

Azure Advisor identifierar Azure Cosmos DB-konton som använder gamla versioner av Java SDK och rekommenderar uppgradering till den senaste versionen från Maven för de senaste korrigeringarna, prestandaförbättringarna och de nya funktionsfunktionerna. [Läs mer om Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Uppgradera Azure Cosmos DB Spark-anslutningsprogrammet till den senaste versionen från Maven

Azure Advisor identifierar Azure Cosmos DB-konton som använder gamla versioner av Cosmos DB Spark-anslutningen och rekommenderar uppgradering till den senaste versionen från Maven för de senaste korrigeringarna, prestandaförbättringarna och de nya funktionsfunktionerna. [Läs mer om Cosmos DB Spark-kontakt](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Aktivera replikering av virtuell dator
Virtuella datorer som inte har replikering aktiverat till en annan region är inte motståndskraftiga mot regionala avbrott. Replikera virtuella datorer minskar eventuella negativa affärseffekter under tiden för ett Avbrott i Azure-regionen. Advisor identifierar virtuella datorer som inte har replikering aktiverat och rekommenderar att du aktiverar replikering så att du i händelse av ett avbrott snabbt kan ta upp dina virtuella datorer i en fjärrregion i Azure. [Läs mer om replikering av virtuella datorer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Så här får du tillgång till rekommendationer om hög tillgänglighet i Advisor

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

2.  Klicka på fliken **Hög tillgänglighet** på instrumentpanelen För Rådgivare.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnadsrekommendationer för rådgivare](advisor-cost-recommendations.md)
* [Advisors prestandarekommendationer](advisor-performance-recommendations.md)
* [Säkerhetsrekommendationer för rådgivare](advisor-security-recommendations.md)
* [Advisor Operational Excellence rekommendationer](advisor-operational-excellence-recommendations.md)
