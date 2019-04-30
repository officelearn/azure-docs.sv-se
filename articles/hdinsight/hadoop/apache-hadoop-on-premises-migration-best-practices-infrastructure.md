---
title: Migrera lokala Apache Hadoop-kluster till Azure HDInsight - Metodtips för infrastruktur
description: Lär dig Metodtips för infrastruktur för att migrera lokala Hadoop-kluster till Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: hrasheed
ms.openlocfilehash: 1bd06507bd8a20cf504c1ff4cd9fe7e3b9196a3c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129322"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight - Metodtips för infrastruktur

Den här artikeln ger rekommendationer för hantering av infrastrukturen i Azure HDInsight-kluster. Det är en del i en serie som ger bästa praxis för att hjälpa migrera lokala Apache Hadoop-system till Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Planera för kapacitet för HDInsight-kluster

De viktiga saker för kapacitetsplanering för HDInsight-kluster är följande:

- **Välj region** -Azure-regionen anger där klustret fysiskt har etablerats. Klustret måste vara i samma Region som data för att minimera svarstiden för läsningar och skrivningar.
- **Välj lagringsplats och storlek** -standardlagring måste finnas i samma Region som klustret. I ett kluster för 48-noder rekommenderar vi att du har 4 till 8 lagringskonton. Även om det kan redan finnas tillräckligt med totalt lagringsutrymme, tillhandahåller varje lagringskonto ytterligare nätverkets bandbredd för compute-noder. Använd ett slumpmässigt namn för varje lagringskonto, utan ett prefix när det är flera lagringskonton. Syftet med slumpmässiga naming minskar risken för storage flaskhalsar (begränsningen) eller läge för vanliga fel för alla konton. Använd bara en behållare per lagringskonto för bättre prestanda.
- **Välj VM-storlek och typ (nu stöder G-serien)** – varje typ av kluster har en uppsättning nodtyper och varje nodtyp har specifika alternativ för VM-storlek och typ. VM-storlek och typ bestäms av CPU-belastningen ström, RAM-storleken och nätverksfördröjning. En simulerade arbetsbelastning kan användas för att fastställa optimal storlek och typ för varje nodtyper.
- **Välj antalet arbetarnoder** -det inledande antalet arbetsnoder kan fastställas med hjälp av de simulerade arbetsbelastningarna. Klustret skalas senare genom att lägga till fler arbetsnoder för att möta toppefterfrågan belastningen. Klustret skalas senare när ytterligare arbetsnoder inte krävs.

Mer information finns i artikeln [kapacitetsplanering för HDInsight-kluster](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Använd rekommenderade typ av virtuell dator för kluster

Se [standard noden konfiguration och VM-storlekar för kluster](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) rekommenderas för typer av virtuella datorer för varje typ av HDInsight-kluster.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Kontrollera tillgängligheten för Hadoop-komponenter i HDInsight

Varje HDInsight-version är en molndistribution av en version av Hortonworks Data Platform (HDP) och består av en uppsättning komponenter för Hadoop-ekosystemet. Se [versionshantering för HDInsight](../hdinsight-component-versioning.md) mer information om alla komponenter i HDInsight och deras aktuella versioner.

Du kan också använda Apache Ambari-Gränssnittet eller Ambari REST API för att kontrollera Hadoop-komponenter och versioner i HDInsight.

Program eller komponenter som var tillgängliga i lokala kluster men inte ingår i HDInsight-kluster kan läggas på en kantnod eller på en virtuell dator i samma virtuella nätverk som HDInsight-kluster. Ett tredje parts Hadoop-program som inte är tillgänglig på Azure HDInsight kan installeras med alternativet ”program” i HDInsight-kluster. Anpassade Hadoop-program kan installeras på HDInsight-kluster med ”skriptåtgärder”. I följande tabell visas några av de vanliga program och deras alternativ för integrering av HDInsight:

|**Programmet**|**Integrering**
|---|---|
|Luftflödet|IaaS eller HDInsight kantnoden
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Ingen (endast HDP)
|Datameer|Kantnoden för HDInsight
|Datastax (Cassandra)|IaaS (CosmosDB ett alternativ i Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB ett alternativ i Azure)
|NiFi|IaaS 
|Presto|IaaS eller HDInsight kantnoden
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW ett alternativ i Azure)
|Tableau|IaaS 
|Vattenlinjen|Kantnoden för HDInsight
|StreamSets|HDInsight edge 
|Palantir|IaaS 
|SailPoint|Iaas 

Mer information finns i artikeln [Apache Hadoop-komponenter som är tillgängliga med olika versioner av HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Anpassa HDInsight-kluster med skriptåtgärder

HDInsight är ett sätt att klusterkonfigurationen som kallas en **skripta åtgärd**. En skriptåtgärder är Bash-skript som körs på noderna i ett HDInsight-kluster och kan användas för att installera ytterligare komponenter och ändra konfigurationsinställningarna.

Skriptåtgärder måste vara lagrade på en URI som kan nås från HDInsight-kluster. De kan användas under eller när klustret har skapats och kan även vara begränsad till endast köras på vissa nodtyper.

Skriptet kan sparas eller körs en gång. Bestående skript används för att anpassa nya arbetsnoderna läggas till i klustret via skalningsåtgärder. Ett bestående skript kan gäller ändringar till en annan nodtyp, till exempel en huvudnod, även när skalningsåtgärder uppstår.

HDInsight innehåller färdiga skript för att installera följande komponenter i HDInsight-kluster:

- Lägg till ett Azure Storage-konto
- Installera Hue
- Installera Presto
- Installera Solr
- Installera Giraph
- Förhandsladda Hive-bibliotek
- Installera eller uppdatera Mono

> [!Note]  
> HDInsight ger inte direct-support för anpassade hadoop-komponenter eller komponenter som installeras med hjälp av skriptåtgärder.

Skriptåtgärder kan även publiceras på Azure Marketplace som ett HDInsight-program.

Mer information finns i följande artiklar:

- [Installera från tredje part Apache Hadoop-program på HDInsight](../hdinsight-apps-install-applications.md)
- [Anpassa HDInsight-kluster med skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publicera ett HDInsight-program på Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Anpassa HDInsight-konfigurationer med Bootstrap

Ändras till konfigurationer i konfigurationsfilerna som `core-site.xml`, `hive-site.xml` och `oozie-env.xml` kan göras med Bootstrap. Följande skript är ett exempel som använder Powershell [AZ modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) cmdlet [New AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster):

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

Mer information finns i artikeln [anpassa HDInsight-kluster med Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Se även [hantera HDInsight-kluster med hjälp av Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Åtkomst-klientverktyg från HDInsight Hadoop-kluster kantnoder

En tom edge-nod är en Linux-dator med samma klientverktyg installeras och konfigureras som på huvudnoderna, men med ingen Hadoop-tjänster som körs. Kantnoden kan användas för följande ändamål:

- åtkomst till klustret
- Testa program
- som är värd för klientprogram

Edge-noder kan skapas och tas bort via Azure portal och kan användas under eller efter att skapa kluster. När du har skapat gränsnoden kan du ansluta till gränsnoden via SSH och kör klientverktyg för att komma åt Hadoop-kluster i HDInsight. Kantnoden ssh-slutpunkten är `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.


Mer information finns i artikeln [använda tomma kantnoder på Apache Hadoop-kluster i HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Använd upp- och Nedskalning funktion i kluster

HDInsight ger flexibilitet när det gäller genom att ge dig möjlighet att skala upp och skala ned antalet arbetarnoder i dina kluster. Den här funktionen kan du minska ett kluster efter timmar eller helger och expanderas under toppefterfrågan för företag. Mer information finns i:

* [Skala HDInsight-kluster](../hdinsight-scaling-best-practices.md).
* [Skala kluster](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Använda HDInsight med Azure-nätverk

Azure-nätverk aktivera Azure-resurser, till exempel Azure virtuella datorer, att kommunicera säkert med varandra, internet, och lokala nätverk genom att filtrera och routning nätverkstrafik.

Med Azure Virtual Network med HDInsight kan följande scenarier:

- Ansluter till HDInsight direkt från ett lokalt nätverk.
- Ansluta HDInsight till data som lagras i ett virtuellt Azure-nätverk.
- Direkt åtkomst till Hadoop-tjänster som inte är tillgänglig offentligt över internet. Till exempel Kafka-API: er eller HBase Java-API.

HDInsight kan antingen läggas till en ny eller befintlig Azure-nätverk. Om HDInsight läggs till ett befintligt virtuellt nätverk, den befintliga nätverkssäkerhetsgrupper och användardefinierade vägar måste uppdateras för att tillåta obegränsad åtkomst till [flera IP-adresser](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) i Azure-datacentret. Se dessutom till att du inte blockerar trafik till den [portar](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ports), som används av HDInsight-tjänster.

> [!Note]  
> HDInsight stöder för närvarande inte Tvingad tunneltrafik. Tvingad tunneltrafik är en undernätsinställning för som tvingar utgående Internet-trafik till en enhet för granskning och loggning. Ta bort Tvingad tunneltrafik innan du installerar HDInsight i ett undernät eller skapa ett nytt undernät för HDInsight. HDInsight stöder inte heller att begränsa utgående nätverksanslutning.

Mer information finns i följande artiklar:

- [Azure-nätverk – översikt över virtuell](../../virtual-network/virtual-networks-overview.md)
- [Utöka Azure HDInsight med hjälp av ett virtuellt Azure-nätverk](../hdinsight-extend-hadoop-virtual-network.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Anslut säkert till Azure-tjänster med Azure Virtual Network-tjänstslutpunkter

HDInsight stöder [virtuella nätverksslutpunkter](../../virtual-network/virtual-network-service-endpoints-overview.md), vilket gör att du kan ansluta säkert till Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB och SQL-databaser. Trafiken flödar via en säker väg från inom Azure-datacentret genom att aktivera en tjänstslutpunkt för Azure HDInsight. Du kan låsa stordata storage-konton till de angivna virtuella nätverken (Vnet) och fortfarande använda HDInsight-kluster sömlöst för att komma åt och bearbeta data med den här högre säkerhet på nätverksnivån.

Mer information finns i följande artiklar:

- [Tjänstslutpunkter för virtuellt nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Förbättra säkerheten för HDInsight med Tjänsteslutpunkter](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Ansluta HDInsight till det lokala nätverket

HDInsight kan anslutas till det lokala nätverket med hjälp av Azure-nätverk och en VPN-gateway. Följande steg kan användas för att upprätta en anslutning:

- Använda HDInsight i ett virtuellt Azure-nätverk som är ansluten till det lokala nätverket.
- Konfigurera DNS-namnmatchningen mellan virtuella nätverk och lokala nätverk.
- Konfigurera nätverkssäkerhetsgrupper eller användardefinierade vägar (UDR) för att styra nätverkstrafiken.

Mer information finns i artikeln [ansluta HDInsight till det lokala nätverket](../connect-on-premises-network.md)

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Bästa metoder för lokalt till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-storage.md)
