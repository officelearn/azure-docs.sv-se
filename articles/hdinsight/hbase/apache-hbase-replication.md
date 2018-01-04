---
title: "Konfigurera HBase-kluster-replikering i Azure-nätverk | Microsoft Docs"
description: "Lär dig hur du ställer in HBase-replikering från en HDInsight-version till en annan för belastningsutjämning, hög tillgänglighet, noll avbrottstid migrering och uppdateringar och katastrofåterställning."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: b5497e9d66833ec8bc291c40d71931aff11820c2
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Konfigurera HBase-kluster-replikering i Azure-nätverk

Lär dig hur du ställer in HBase-replikering i ett virtuellt nätverk eller mellan två virtuella nätverk i Azure.

Kluster-replikering använder en käll-push-metod. Ett HBase-kluster kan vara en käll- eller ett mål eller det kan uppfylla båda roller på samma gång. Replikering är asynkron. Målet för replikering är slutliga konsekvensen. När källan tar emot en redigering till en kolumnfamilj när replikeringen är aktiverad, sprids redigera till alla målkluster. När data från ett kluster till ett annat, spåras källklustret och alla kluster som redan förbrukats data, för att förhindra replikering slingor.

I den här kursen kan du ställa in en källa mål-replikering. Andra klustertopologier finns i [Apache HBase referenshandboken](http://hbase.apache.org/book.html#_cluster_replication).

Följande är HBase replikering användning ärenden för ett enda virtuellt nätverk:

* Belastningsbalansering. Du kan till exempel köra genomsökningar eller MapReduce-jobb på målklustret och mata in data på källklustret.
* Lägger till hög tillgänglighet.
* Migrera data från ett HBase-kluster till ett annat.
* Uppgradera ett Azure HDInsight-kluster från en version till en annan.

Följande är HBase replikering användning fall för två virtuella nätverk:

* Konfigurera katastrofåterställning.
* Belastningsutjämning och partitionering programmet.
* Lägger till hög tillgänglighet.

Du kan replikera kluster med hjälp av [skript åtgärd](../hdinsight-hadoop-customize-cluster-linux.md) skript från [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Se [hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Konfigurera miljöerna

Du har tre konfigurationsalternativ:

- Två HBase-kluster i ett Azure virtual network.
- Två HBase-kluster i två olika virtuella nätverk i samma region.
- Två HBase-kluster i två olika virtuella nätverk i två olika regioner (geo-replikering).

Som hjälper dig att ställa in miljöerna, har vi skapat några [Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-overview.md). Om du vill konfigurera i miljöer med hjälp av andra metoder, se:

- [Skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Skapa HBase-kluster i Azure-nätverk](apache-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>Konfigurera ett virtuellt nätverk

Välj följande bild för att skapa två HBase-kluster i samma virtuella nätverk. Mallen finns på [Azure QuickStart mallar](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>Konfigurera två virtuella nätverk i samma region

Välj följande bild för att skapa två virtuella nätverk med virtuella nätverk peering och två HBase-kluster i samma region. Mallen finns på [Azure QuickStart mallar](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Det här scenariot kräver [virtuellt nätverk peering](../../virtual-network/virtual-network-peering-overview.md). Mallen kan virtuella nätverk peering.   

HBase-replikering använder IP-adresserna för de virtuella datorerna ZooKeeper. Du måste konfigurera statiska IP-adresser för mål HBase ZooKeeper noder.

**Konfigurera statiska IP-adresser**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På den vänstra menyn väljer **resursgrupper**.
3. Välj den resursgrupp som har mål HBase-kluster. Det här är den resursgrupp som du angav när du använde Resource Manager-mall för att skapa miljön. Du kan använda filtret för att begränsa listan. Du kan se en lista över resurser som innehåller två virtuella nätverk.
4. Välj det virtuella nätverk som innehåller mål HBase-kluster. Välj exempelvis **xxxx vnet2**. Tre enheter med namn som börjar med **nic-zookeepermode -** visas. Dessa enheter finns tre ZooKeeper virtuella datorer.
5. Välj någon av de virtuella datorerna ZooKeeper.
6. Välj **IP-konfigurationer**.
7. Välj i listan, **ipConfig1**.
8. Välj **Statiska**, och kopiera eller skriva ned den IP-adressen. Du måste IP-adress när du kör instruktionen skript för att aktivera replikering.

  ![HDInsight HBase-replikering ZooKeeper statisk IP-adress](./media/apache-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Upprepa steg 6 till den statiska IP-adressen för de andra två ZooKeeper-noderna.

För scenariot mellan virtuella nätverk måste du använda den **- IP-** växeln när du anropar den `hdi_enable_replication.sh` skript åtgärd.

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Konfigurera två virtuella nätverk i två olika regioner

Skapa två virtuella nätverk i två olika regioner och VPN-anslutning mellan till Vnet, klicka på följande bild. Mallen finns i [Azure QuickStart mallar](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-geo%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Vissa hårdkodade värden i mallen:

**Virtuellt nätverk 1**

| Egenskap | Värde |
|----------|-------|
| Plats | Västra USA |
| Namn på virtuella nätverk | &lt;ClusterNamePrevix >-vnet1 |
| Adressprefixet utrymme | 10.1.0.0/16 |
| Namn på undernät | undernät 1 |
| Undernätets prefix | 10.1.0.0/24 |
| Undernätnamnet (gateway) | GatewaySubnet (kan ändras) |
| Undernätsprefixet (gateway) | 10.1.255.0/27 |
| Gatewaynamnet | vnet1gw |
| Gateway-typ | Vpn |
| VPN gateway-typ | Routningsbaserad |
| Gateway-SKU | Basic |
| gateway-IP | vnet1gwip |
| Klusternamn | &lt;ClusterNamePrefix > 1 |
| Klusterversion | 3.6 |
| Typ av kluster | hbase |
| Antalet för kluster worker nod | 2 |


**VNet 2**

| Egenskap | Värde |
|----------|-------|
| Plats | Östra USA |
| Namn på virtuella nätverk | &lt;ClusterNamePrevix >-vnet2 |
| Adressprefixet utrymme | 10.2.0.0/16 |
| Namn på undernät | undernät 1 |
| Undernätets prefix | 10.2.0.0/24 |
| Undernätnamnet (gateway) | GatewaySubnet (kan ändras) |
| Undernätsprefixet (gateway) | 10.2.255.0/27 |
| Gatewaynamnet | vnet2gw |
| Gateway-typ | Vpn |
| VPN gateway-typ | Routningsbaserad |
| Gateway-SKU | Basic |
| gateway-IP | vnet1gwip |
| Klusternamn | &lt;ClusterNamePrefix > 2 |
| Klusterversion | 3.6 |
| Typ av kluster | hbase |
| Antalet för kluster worker nod | 2 |

HBase-replikering använder IP-adresserna för de virtuella datorerna ZooKeeper. Du måste konfigurera statiska IP-adresser för mål HBase ZooKeeper noder. Om du vill konfigurera statiska IP-Adressen finns [konfigurera två virtuella nätverk i samma region](#set-up-two-virtual-networks-in-the-same-region) i den här artikeln.

För scenariot mellan virtuella nätverk måste du använda den **- IP-** växeln när du anropar den `hdi_enable_replication.sh` skript åtgärd.

## <a name="load-test-data"></a>Läs in testdata

När du replikerar ett kluster måste du ange de tabeller som du vill replikera. I det här avsnittet kan du läsa in vissa data i källklustret. I nästa avsnitt aktiverar du replikering mellan två kluster.

Så här skapar du en **kontakter** tabell och infoga data i tabellen, följer du anvisningarna [självstudier för HBase: komma igång med Apache HBase i HDInsight](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Aktivera replikering

Följande steg beskriver hur du anropar åtgärdsskriptet skript från Azure-portalen. Information om hur du kör en skriptåtgärd med hjälp av Azure PowerShell och Azure kommandoradsverktyget (Azure CLI) finns i [anpassa HDInsight-kluster med hjälp av skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md).

**Att aktivera HBase-replikering från Azure-portalen**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna källa HBase-kluster.
3. Kluster-menyn väljer du **skriptåtgärder**.
4. Överst på sidan Välj **skicka nya**.
5. Välj eller ange följande information:

  1. **Namnet**: Ange **Aktivera replikering**.
  2. **Bash Webbadress för skriptet**: Ange **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **HEAD**: se till att det här alternativet väljs. Ta bort andra nodtyper.
  4. **Parametrarna**: följande exempel parametrar Aktivera replikering för alla befintliga tabeller och sedan kopiera alla data från källklustret till målklustret:

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Använda värdnamnet i stället för FQDN för både käll- och DNS-klusternamnet.

6. Välj **Skapa**. Skriptet kan ta en stund att köra, särskilt när du använder den **- copydata** argumentet.

Argument som krävs:

|Namn|Beskrivning|
|----|-----------|
|-s - src-kluster | Anger DNS-namnet på källan HBase-kluster. Till exempel: -s hbsrccluster,--src-kluster = hbsrccluster |
|-d,--dst-kluster | Anger DNS-namnet på målet (replik) HBase-kluster. Till exempel: -s dsthbcluster,--src-kluster = dsthbcluster |
|-sp,--src-ambari-lösenordet | Anger administratörslösenordet för Ambari på källan HBase-kluster. |
|-dp,--dst-ambari-lösenordet | Anger administratörslösenordet för Ambari på mål HBase-kluster.|

Valfria argument:

|Namn|Beskrivning|
|----|-----------|
|-su,--src-ambari-användare | Anger användarnamn för administratören för Ambari på källan HBase-kluster. Standardvärdet är **admin**. |
|-du--dst-ambari-användare | Anger användarnamn för administratören för Ambari på mål HBase-kluster. Standardvärdet är **admin**. |
|-t,--tabell-lista | Anger tabellerna som ska replikeras. Till exempel:--tabell lista = ”tabell1; tabell2; tabell 3”. Om du inte anger tabeller, replikeras alla befintliga HBase-tabeller.|
|-m,--dator | Anger huvudnoden där skriptåtgärden körs. Värdet är antingen **hn1** eller **hn0**. Eftersom den **hn0** huvudnod är vanligtvis busier, bör du använda **hn1**. Använd det här alternativet när du kör skriptet $0 som en skriptåtgärd från HDInsight-portalen eller Azure PowerShell.|
|-ip | Krävs när du aktivera replikering mellan två virtuella nätverk. Det här argumentet fungerar som en växel som ska använda statiska IP-adresser för ZooKeeper-noder från repliken kluster i stället för FQDN-namn. Du måste förkonfigurera statiska IP-adresser innan du aktiverar replikering. |
|-cp, - copydata | Gör det möjligt för migrering av befintliga data på tabellerna om replikering har aktiverats. |
|-rpm, -replikera-phoenix-metadata | Aktiverar replikering på Phoenix systemtabeller. <br><br>*Använd det här alternativet med försiktighet.* Vi rekommenderar att du återskapar Phoenix tabeller på replik kluster innan du använder det här skriptet. |
|-h,--hjälp | Visar användningsinformation. |

Den `print_usage()` avsnitt i den [skriptet](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) har en detaljerad förklaring av parametrarna.

Du kan använda SSH för att ansluta till målet HBase-kluster och kontrollerar sedan att data har replikerats när skriptåtgärden har distribuerats.

### <a name="replication-scenarios"></a>Replikeringsscenarier

I följande lista visas ibland allmän användning och deras parameterinställningar:

- **Aktivera replikering för alla tabeller mellan två kluster**. Det här scenariot kräver inte kopiera eller migrera befintliga data i tabeller och den använder inte Phoenix tabeller. Använd följande parametrar:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Aktivera replikering för specifika tabeller**. Aktivera replikering på tabell1 och tabell2 tabell 3 med följande parametrar:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Aktivera replikering för specifika tabeller och kopiera befintliga data**. Aktivera replikering på tabell1 och tabell2 tabell 3 med följande parametrar:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Aktivera replikering för alla tabeller och replikera Phoenix metadata från källan till målet**. Phoenix metadata replikering är inte exakt. Använd den med försiktighet. Använd följande parametrar:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Kopiera och migrera data

Det finns två separata skript åtgärd skript för att kopiera och migrera data när replikeringen har aktiverats:

- [Skriptet för små tabeller](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabeller som är några få gigabyte i storlek och övergripande kopiera förväntas Slutför på mindre än en timme)

- [Skriptet för stora tabeller](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabeller som förväntas ta längre tid än en timme att kopiera)

Du kan följa samma steg som beskrivs i [Aktivera replikering](#enable-replication) att anropa skriptåtgärden. Använd följande parametrar:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

Den `print_usage()` avsnitt i den [skriptet](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) har en detaljerad beskrivning av parametrar.

### <a name="scenarios"></a>Scenarier

- **Kopiera specifika tabeller (test1 test2 och test3) för alla rader som redigeras fram till nu (aktuell tidsstämpel)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Eller:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Kopiera specifika tabeller med ett angivet tidsintervall**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Inaktivera replikering

Inaktivera replikering med ett annat skript åtgärd skript från [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Du kan följa samma steg som beskrivs i [Aktivera replikering](#enable-replication) att anropa skriptåtgärden. Använd följande parametrar:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

Den `print_usage()` avsnitt i den [skriptet](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) har en detaljerad förklaring av parametrarna.

### <a name="scenarios"></a>Scenarier

- **Inaktivera replikering för alla tabeller**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  eller

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Inaktivera replikering för angivna tabeller (tabell1 tabell2 och tabell 3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Nästa steg

I kursen får du har lärt dig hur du ställer in HBase-replikering i ett virtuellt nätverk eller mellan två virtuella nätverk. Mer information om HDInsight och HBase finns i följande artiklar:

* [Kom igång med Apache HBase i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Översikt över HDInsight HBase](./apache-hbase-overview.md)
* [Skapa HBase-kluster i Azure-nätverk](./apache-hbase-provision-vnet.md)
* [Analysera sensordata med Storm och HBase i HDInsight (Hadoop)](../storm/apache-storm-sensor-data-analysis.md)
