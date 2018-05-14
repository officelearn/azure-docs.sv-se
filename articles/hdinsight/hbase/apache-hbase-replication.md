---
title: Konfigurera HBase-kluster-replikering i Azure-nätverk | Microsoft Docs
description: Lär dig hur du ställer in HBase-replikering från en HDInsight-version till en annan för belastningsutjämning, hög tillgänglighet, noll avbrottstid migrering och uppdateringar och katastrofåterställning.
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: jgao
ms.openlocfilehash: 56b2b5ae9d3e4a0e682ec3dd47cd5cc30ebf6d58
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
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

Den här artikeln beskriver scenariot geo-replikering.

Som hjälper dig att ställa in miljöerna, har vi skapat några [Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-overview.md). Om du vill konfigurera i miljöer med hjälp av andra metoder, se:

- [Skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Skapa HBase-kluster i Azure-nätverk](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Konfigurera två virtuella nätverk i två olika regioner

För att skapa två virtuella nätverk i två olika regioner och VPN-anslutning mellan till Vnet, väljer du följande bilden för att skapa den. Mallen finns i [offentlig blob storage]] (https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Vissa hårdkodade värden i mallen:

**Virtuellt nätverk 1**

| Egenskap  | Värde |
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

**VNet 2**

| Egenskap  | Värde |
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

## <a name="setup-dns"></a>Konfigurera DNS

I det sista avsnittet skapas en virtuell Ubuntu-dator i var och en av de två virtuella nätverk i mallen.  I det här avsnittet Installera Bind på två DNS-virtuella datorer och sedan konfigurera DNS-vidarebefordran på två virtuella datorer.

Om du vill installera Bind måste yon att hitta den offentliga IP-adressen för två DNS-virtuella datorer.

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Öppna DNS-virtuella datorn genom att välja **resursgrupper > [resursgruppens namn] > [vnet1DNS]**.  Resursgruppens namn är det du har skapat i föregående procedur. Standard DNS-namn för virtuell dator är *vnet1DNS* och *vnet2NDS*.
3. Välj **egenskaper** att öppna egenskapssidan för det virtuella nätverket.
4. Skriv ned den **offentliga IP-adressen**, och även kontrollera den **privata IP-adressen**.  Den privata IP-adressen är **10.1.0.4** för vnet1DNS och **10.2.0.4** för vnet2DNS.  

Använd följande procedur om du vill installera bindning:

1. Använda SSH för att ansluta till den __offentliga IP-adressen__ för den virtuella datorn i DNS. I följande exempel ansluter till en virtuell dator på 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Ersätt `sshuser` med SSH-användarkonto som du angav när du skapar den virtuella datorn i DNS.

    > [!NOTE]
    > Det finns flera olika sätt att hämta den `ssh` verktyget. Linux-, Unix- och macOS anges som en del av operativsystemet. Om du använder Windows, bör du något av följande alternativ:
    >
    > * [Azure-molnet Shell](../../cloud-shell/quickstart.md)
    > * [Bash på Ubuntu på Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Använd följande kommandon från SSH-sessionen om du vill installera bindning:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Om du vill konfigurera Bind att vidarebefordra begäran till din DNS-server för lokal namnmatchning, Använd följande text som innehållet i den `/etc/bind/named.conf.options` filen:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16 #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]
    > Ersätter värdena i den `goodclients` avsnitt med IP-adressintervall i två virtuella nätverk. Det här avsnittet beskriver de adresser som DNS-servern accepterar begäranden från.

    Om du vill redigera den här filen använder du följande kommando:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Om du vill spara filen, Använd __Ctrl + X__, __Y__, och sedan __RETUR__.

4. Använd följande kommando från SSH-session:

    ```bash
    hostname -f
    ```

    Det här kommandot returnerar ett värde som liknar följande:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    Den `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` texten är den __DNS-suffix__ för den här virtuella nätverket. Spara det här värdet eftersom det används senare.

    Du måste också reda på DNS-suffix från DNS-servern. Du behöver den i nästa steg.

5. Konfigurera Bind att matcha DNS-namn för resurser i det virtuella nätverket med följande text som innehållet i den `/etc/bind/named.conf.local` filen:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]
    > Du måste ersätta den `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` med DNS-suffixet för det virtuella nätverket. Vidarebefordraren IP-Adressen är den privata IP-adressen för DNS-servern i det virtuella nätverket.

    Om du vill redigera den här filen använder du följande kommando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Om du vill spara filen, Använd __Ctrl + X__, __Y__, och sedan __RETUR__.

6. Om du vill starta bindning, använder du följande kommando:

    ```bash
    sudo service bind9 restart
    ```

7. Om du vill kontrollera att binda kan matcha namnen på de resurser i det virtuella nätverket, Använd följande kommandon:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net 10.2.0.4
    ```

    > [!IMPORTANT]
    > Ersätt `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` med fullständigt kvalificerade domännamnet (FQDN) för den virtuella datorn i DNS i andra nätverk.
    >
    > Ersätt `10.2.0.4` med den __interna IP-adress__ på anpassade DNS-servern i det virtuella nätverket.

    Svaret ser ut som följande text:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Fram till nu kan du söka efter IP-adress från nätverket utan angivna DNS-serverns IP-adress.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurera det virtuella nätverket om du vill använda anpassade DNS-servern

Använd följande steg om du vill konfigurera det virtuella nätverket om du vill använda anpassade DNS-servern i stället för Azure rekursiv matcharen:

1. I den [Azure-portalen](https://portal.azure.com), Välj det virtuella nätverket och väljer sedan __DNS-servrar__.

2. Välj __anpassade__, och ange den __interna IP-adress__ för anpassade DNS-servern. Välj slutligen __spara__.

6. Öppna den virtuella datorn för DNS-server i vnet1 och på **starta om**.  Du måste starta om alla virtuella datorer i det virtuella nätverket så att DNS-konfigurationen ska börja gälla.
7. Upprepa steg konfigurera anpassade DNS-servern för vnet2.

Du kan ansluta till två DNS-virtuella datorer med hjälp av SSH och pinga DNS-servern för det virtuella nätverket med hjälp av dess värdnamn för att testa DNS-konfigurationen. Om det inte fungerar, använder du följande kommando för att kontrollera status för DNS:

```bash
sudo service bind9 status
```

## <a name="create-hbase-clusters"></a>Skapa HBase-kluster

Skapa ett HBase-kluster i var och en av de två virtuella nätverk med följande konfiguration:

- **Resursgruppens namn**: Använd samma resursgruppens namn som du har skapat de virtuella nätverken.
- **Klustret typen**: HBase
- **Version**: HBase 1.1.2 (HDI 3,6)
- **Plats**: använda samma plats som det virtuella nätverket.  Som standard är vnet1 *västra USA*, och vnet2 *östra USA*.
- **Lagring**: skapa ett nytt lagringskonto för klustret.
- **Virtuellt nätverk** (från avancerade inställningar på portalen): Välj vnet1 som du skapade i föregående procedur.
- **Undernät**: standardnamnet som används i mallen är **Undernät1**.

För att säkerställa att miljön är korrekt konfigurerad, måste du kunna pinga den headnode FQDN mellan två kluster.

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

