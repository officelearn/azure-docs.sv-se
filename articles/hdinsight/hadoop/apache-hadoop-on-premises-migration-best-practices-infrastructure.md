---
title: 'Infrastruktur: Lokala Apache Hadoop till Azure HDInsight'
description: Lär dig metodtips för infrastruktur för att migrera lokala Hadoop-kluster till Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: d7ee8ae121e3cbb9760a87c95d12109a9b05e0c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951521"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight – metodtips för infrastruktur

Den här artikeln innehåller rekommendationer för att hantera infrastrukturen för Azure HDInsight-kluster. Det är en del av en serie som innehåller metodtips för att hjälpa till med att migrera lokala Apache Hadoop-system till Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Planera för kapacitet för HDInsight-kluster

De viktigaste valen för hdinsight kluster kapacitetsplanering är följande:

**Regionen**  
Azure-regionen avgör var klustret är fysiskt etablerat. För att minimera svarstiden för läsningar och skrivningar bör klustret beta i samma region som data.

**Lagringsplats och storlek**  
Standardlagringen måste finnas i samma region som klustret.För ett kluster med 48 nod rekommenderas att ha 4 till 8 lagringskonton. Även om det kanske redan finns tillräckligt med total lagring, ger varje lagringskonto ytterligare nätverksbandbredd för beräkningsnoderna. När det finns flera lagringskonton använder du ett slumpmässigt namn för varje lagringskonto utan prefix. Syftet med slumpmässig namngivning minskar risken för flaskhalsar för lagring (begränsning) eller common-mode-fel i alla konton. För bättre prestanda, använd bara en behållare per lagringskonto.

**VM-storlek och vm-typ (stöder nu G-serien)**  
Varje klustertyp har en uppsättning nodtyper och varje nodtyp har specifika alternativ för sin vm-storlek och typ. Vm-storlek och typ bestäms av processorhanteringskraft, RAM-storlek och nätverksfördröjning. En simulerad arbetsbelastning kan användas för att bestämma den optimala vm-storleken och typen för varje nodtyper.

**Antal arbetsnoder**  
Det ursprungliga antalet arbetsnoder kan bestämmas med hjälp av simulerade arbetsbelastningar. Klustret kan skalas senare genom att lägga till fler arbetsnoder för att uppfylla belastningskraven. Klustret kan senare skalas tillbaka när ytterligare arbetsnoder inte krävs.

Mer information finns i artikeln [Kapacitetsplanering för HDInsight-kluster](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Använd rekommenderad typ av virtuell dator för kluster

Se [Standardnodkonfiguration och storlekar för virtuella datorer för kluster](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) för rekommenderade typer av virtuella datorer för varje typ av HDInsight-kluster.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Kontrollera tillgängligheten för Hadoop-komponenter i HDInsight

Varje HDInsight-version är en molndistribution av en uppsättning Hadoop-ekosystemkomponenter. Se [HDInsight Component Versioning](../hdinsight-component-versioning.md) för information om alla HDInsight-komponenter och deras aktuella versioner.

Du kan också använda Apache Ambari UI eller Ambari REST API för att kontrollera Hadoop-komponenterna och versionerna i HDInsight.

Program eller komponenter som var tillgängliga i lokala kluster men som inte ingår i HDInsight-kluster kan läggas till på en kantnod eller på en virtuell dator i samma virtuella nätverk som HDInsight-klustret. Ett Hadoop-program från tredje part som inte är tillgängligt på Azure HDInsight kan installeras med alternativet "Program" i HDInsight-klustret. Anpassade Hadoop-program kan installeras på HDInsight-kluster med hjälp av "skriptåtgärder". I följande tabell visas några av de vanliga programmen och deras hdinsight-integrationsalternativ:

|**Program**|**Integration**
|---|---|
|Luftflöde|IaaS- eller HDInsight-kantnod
|Alluxio (allauxio)|IaaS  
|Arcadia|IaaS 
|Atlas|Ingen (endast HDP)
|Datameer (datameer)|HDInsight kantnod
|Datastax (Cassandra)|IaaS (CosmosDB ett alternativ på Azure)
|DataTorrent (olika)|IaaS 
|Drill|IaaS 
|Antända|IaaS
|Jethro|IaaS 
|Mapador (kartador)|IaaS 
|Mongo|IaaS (CosmosDB ett alternativ på Azure)
|NiFi (na)|IaaS 
|Presto|IaaS- eller HDInsight-kantnod
|Python 2 (Svenska)|PaaS 
|Python 3 (Svenska)|PaaS 
|R|PaaS 
|Sas|IaaS 
|Vertica|IaaS (SQLDW ett alternativ på Azure)
|Tableau|IaaS 
|Vattenlinjen|HDInsight kantnod
|StreamSets (Strömmar)|HDInsight kant 
|Palantir (på annat sätt)|IaaS 
|Segelpunkt|Iaas (iaas) 

Mer information finns i artikeln [Apache Hadoop-komponenter som är tillgängliga med olika HDInsight-versioner](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Anpassa HDInsight-kluster med skriptåtgärder

HDInsight tillhandahåller en metod för klusterkonfiguration som kallas **skriptåtgärd**. En skriptåtgärd är Bash-skript som körs på noderna i ett HDInsight-kluster och kan användas för att installera ytterligare komponenter och ändra konfigurationsinställningar.

Skriptåtgärder måste lagras på en URI som är tillgänglig från HDInsight-klustret. De kan användas under eller efter klusterskapande och kan också begränsas till att köras endast på vissa nodtyper.

Skriptet kan sparas eller köras en gång. De beständiga skripten används för att anpassa nya arbetsnoder som läggs till i klustret genom skalningsåtgärder. Ett beständigt skript kan också tillämpa ändringar på en annan nodtyp, till exempel en huvudnod, när skalningsåtgärder inträffar.

HDInsight tillhandahåller förskrivna skript för att installera följande komponenter i HDInsight-kluster:

- Lägga till ett Azure Storage-konto
- Installera nyans
- Installera Presto
- Installera Solr
- Installera Giraph
- Förinläsning av Hive-bibliotek
- Installera eller uppdatera Mono

> [!Note]  
> HDInsight ger inte direkt stöd för anpassade hadoop-komponenter eller komponenter som installeras med skriptåtgärder.

Skriptåtgärder kan också publiceras på Azure Marketplace som ett HDInsight-program.

Mer information finns i följande artiklar:

- [Installera Apache Hadoop-program från tredje part på HDInsight](../hdinsight-apps-install-applications.md)
- [Anpassa HDInsight-kluster med skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publicera ett HDInsight-program på Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Anpassa HDInsight-configs med Bootstrap

Ändringar i configs i config `core-site.xml` `hive-site.xml` filer `oozie-env.xml` som , och kan göras med Bootstrap. Följande skript är ett exempel med Powershell [AZ-modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) cmdlet [New-AzHDInsightClusterConfig:](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Mer information finns i artikeln [Anpassa HDInsight-kluster med Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Se även [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Få tillgång till klientverktyg från HDInsight Hadoop-klusterkantnoder

En tom kantnod är en virtuell Linux-dator med samma klientverktyg installerade och konfigurerade som på huvudnoderna, men utan Hadoop-tjänster som körs. Kantnoden kan användas för följande ändamål:

- komma åt klustret
- testa klientprogram
- värd för klientprogram

Edge-noder kan skapas och tas bort via Azure-portalen och kan användas under eller efter klusterskapande. När kantnoden har skapats kan du ansluta till kantnoden med SSH och köra klientverktyg för att komma åt Hadoop-klustret i HDInsight. Kannoden ssh-slutpunkten `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`är .


Mer information finns i artikeln [Använd tomma kantnoder på Apache Hadoop-kluster i HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Använda skalnings- och nedskalningsfunktion i kluster

HDInsight ger elasticitet genom att ge dig möjlighet att skala upp och skala ner antalet arbetsnoder i dina kluster. Med den här funktionen kan du krympa ett kluster efter timmar eller helger och expandera det under högsta affärskrav. Mer information finns i:

* [Skala HDInsight-kluster](../hdinsight-scaling-best-practices.md).
* [Skala kluster](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Använda HDInsight med Azure Virtual Network

Azure Virtual Networks gör det möjligt för Azure-resurser, till exempel virtuella Azure-datorer, att kommunicera säkert med varandra, internet och lokala nätverk genom filtrering och routning av nätverkstrafik.

Genom att använda Azure Virtual Network med HDInsight kan följande scenarier:

- Ansluta till HDInsight direkt från ett lokalt nätverk.
- Ansluta HDInsight till datalager i ett Virtuellt Azure-nätverk.
- Direkt åtkomst till Hadoop-tjänster som inte är tillgängliga för allmänheten via Internet. Till exempel Kafka API:er eller HBase Java API.

HDInsight kan antingen läggas till i ett nytt eller befintligt Virtuellt Azure-nätverk. Om HDInsight läggs till i ett befintligt virtuellt nätverk måste de befintliga nätverkssäkerhetsgrupperna och användardefinierade vägar uppdateras för att tillåta obegränsad åtkomst till [flera IP-adresser](../hdinsight-management-ip-addresses.md) i Azure-datacentret. Se också till att inte blockera trafik till [portarna](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ports), som används av HDInsight-tjänster.

> [!Note]  
> HDInsight stöder för närvarande inte tvångstunneler. Tvingad tunnelning är en undernätsinställning som tvingar utgående Internettrafik till en enhet för inspektion och loggning. Ta antingen bort påtvingad tunnel innan du installerar HDInsight i ett undernät eller skapa ett nytt undernät för HDInsight. HDInsight stöder inte heller att begränsa utgående nätverksanslutning.

Mer information finns i följande artiklar:

- [Azure virtual-networks-översikt](../../virtual-network/virtual-networks-overview.md)
- [Utöka Azure HDInsight med hjälp av ett virtuellt Azure-nätverk](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Anslut till Azure-tjänster med Azure Virtual Network-tjänstslutpunkter

HDInsight stöder slutpunkter för [virtuella nätverkstjänster](../../virtual-network/virtual-network-service-endpoints-overview.md), vilket gör att du kan ansluta till Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB och SQL-databaser. Genom att aktivera en tjänstslutpunkt för Azure HDInsight flödar trafiken via en säker väg inifrån Azure-datacentret. Med den här förbättrade säkerhetsnivån på nätverkslagret kan du låsa lagringskonton för stordata till deras angivna virtuella nätverk (VNETs) och ändå använda HDInsight-kluster sömlöst för att komma åt och bearbeta dessa data.

Mer information finns i följande artiklar:

- [Tjänstslutpunkter för virtuellt nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Förbättra HDInsight-säkerhet med tjänstslutpunkter](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Anslut HDInsight till det lokala nätverket

HDInsight kan anslutas till det lokala nätverket med hjälp av Azure Virtual Networks och en VPN-gateway. Följande steg kan användas för att upprätta anslutning:

- Använd HDInsight i ett virtuellt Azure-nätverk som har anslutning till det lokala nätverket.
- Konfigurera DNS-namnmatchning mellan det virtuella nätverket och det lokala nätverket.
- Konfigurera nätverkssäkerhetsgrupper eller användardefinierade vägar (UDR) för att styra nätverkstrafiken.

Mer information finns i artikeln [Anslut HDInsight till ditt lokala nätverk](../connect-on-premises-network.md)

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien: [Metodtips för lagring för lokal till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-storage.md).
