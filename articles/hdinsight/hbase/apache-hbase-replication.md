---
title: HBase kluster replikering i virtuella nätverk - Azure HDInsight
description: Lär dig hur du konfigurerar HBase-replikering från en HDInsight-version till en annan för belastningsutjämning, hög tillgänglighet, nollavvikningsmigrering och uppdateringar samt haveriberedskap.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 1e6465584dd4e67f736b94d2939678c1a69163bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435672"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Konfigurera Apache HBase-klusterreplikering i virtuella Azure-nätverk

Lär dig hur du konfigurerar [Apache HBase-replikering](https://hbase.apache.org/) i ett virtuellt nätverk eller mellan två virtuella nätverk i Azure.

Klusterreplikering använder en käll-push-metod. Ett HBase-kluster kan vara en källa eller ett mål, eller så kan det uppfylla båda rollerna samtidigt. Replikering är asynkron. Målet med replikeringen är eventuell konsekvens. När källan tar emot en redigering i en kolumnfamilj när replikering är aktiverad sprids redigeringen till alla målkluster. När data replikeras från ett kluster till ett annat spåras källklustret och alla kluster som redan har förbrukat data för att förhindra replikeringsloopar.

I den här artikeln ställer du in en käll-mål replikering. Andra klustertopologier finns i [Referensguiden](https://hbase.apache.org/book.html#_cluster_replication)för Apache HBase .

Följande är HBase-replikeringsanvändningsfall för ett enda virtuellt nätverk:

* Belastningsbalansering. Du kan till exempel köra genomsökningar eller MapReduce-jobb i målklustret och få in data i källklustret.
* Lägga till hög tillgänglighet.
* Migrera data från ett HBase-kluster till ett annat.
* Uppgradera ett Azure HDInsight-kluster från en version till en annan.

Följande är HBase-replikeringsanvändningsfall för två virtuella nätverk:

* Ställa in haveriberedskap.
* Belastningsutjämning och partitionering av programmet.
* Lägga till hög tillgänglighet.

Du kan replikera kluster med [skriptåtgärdsskript](../hdinsight-hadoop-customize-cluster-linux.md) från [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Krav
Innan du börjar den här artikeln måste du ha en Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Konfigurera miljöer

Du har tre konfigurationsalternativ:

- Två Apache HBase-kluster i ett virtuellt Azure-nätverk.
- Två Apache HBase-kluster i två olika virtuella nätverk i samma region.
- Två Apache HBase-kluster i två olika virtuella nätverk i två olika regioner (geo-replikering).

Den här artikeln täcker geo-replikeringsscenariot.

För att hjälpa dig att konfigurera miljöerna har vi skapat några [Azure Resource Manager-mallar](../../azure-resource-manager/management/overview.md). Om du föredrar att ställa in miljöerna med andra metoder läser du:

- [Skapa Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Skapa Apache HBase-kluster i Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Konfigurera två virtuella nätverk i två olika regioner

Om du vill använda en mall som skapar två virtuella nätverk i två olika regioner och VPN-anslutningen mellan virtuella nätverk väljer du följande **knappen Distribuera till Azure.** Malldefinitionen lagras i en [offentlig blob-lagring](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Några av de hårdkodade värdena i mallen:

**VNet 1**

| Egenskap | Värde |
|----------|-------|
| Location | USA, västra |
| VNet-namn | &lt;ClusterNamePrevix>-vnet1 |
| Prefix för adressutrymme | 10.1.0.0/16 |
| Namn på undernät | undernät 1 |
| Prefix för undernät | 10.1.0.0/24 |
| Namn på undernät (gateway) | GatewaySubnet (kan inte ändras) |
| Prefix för undernät (gateway) | 10.1.255.0/27 |
| Gateway-namn | vnet1gw (på andra) |
| Gateway-typ | Vpn |
| VPN-typ för gateway | Routningsbaserad |
| Gateway SKU | Basic |
| Gateway IP | vnet1gwip |

**VNet 2**

| Egenskap | Värde |
|----------|-------|
| Location | USA, östra |
| VNet-namn | &lt;ClusterNamePrevix>-vnet2 |
| Prefix för adressutrymme | 10.2.0.0/16 |
| Namn på undernät | undernät 1 |
| Prefix för undernät | 10.2.0.0/24 |
| Namn på undernät (gateway) | GatewaySubnet (kan inte ändras) |
| Prefix för undernät (gateway) | 10.2.255.0/27 |
| Gateway-namn | vnet2gw (på andra) |
| Gateway-typ | Vpn |
| VPN-typ för gateway | Routningsbaserad |
| Gateway SKU | Basic |
| Gateway IP | vnet1gwip |

## <a name="setup-dns"></a>Konfigurera DNS

I det sista avsnittet skapar mallen en virtuell Ubuntu-dator i vart och ett av de två virtuella nätverken.  I det här avsnittet installerar du Bind på de två virtuella DNS-datorerna och konfigurerar sedan DNS-vidarebefordran på de två virtuella datorerna.

För att installera Bind måste yon hitta den offentliga IP-adressen för de två virtuella DNS-datorerna.

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Öppna den virtuella DNS-datorn genom att välja **Resursgrupper > [resursgruppnamn] > [vnet1DNS]**.  Resursgruppsnamnet är det du skapar i den sista proceduren. Standardnamnen för virtuella DNS-datorer är *vnet1DNS* och *vnet2NDS*.
3. Välj **Egenskaper** om du vill öppna egenskapssidan för det virtuella nätverket.
4. Skriv ned den **offentliga IP-adressen**och verifiera även den **privata IP-adressen**.  Den privata IP-adressen ska vara **10.1.0.4** för vnet1DNS och **10.2.0.4** för vnet2DNS.  
5. Ändra DNS-servrarna för båda virtuella nätverken för att använda Standard-DNS-servrar (Azure-Provided) för att tillåta inkommande och utgående åtkomst för att hämta paket för att installera Bind i följande steg.

Så här installerar du Bind:

1. Använd SSH för att ansluta till den __offentliga IP-adressen__ för den virtuella DNS-datorn. I följande exempel ansluters till en virtuell dator på 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Ersätt `sshuser` med det SSH-användarkonto som du angav när du skapade den virtuella DNS-datorn.

    > [!NOTE]  
    > Det finns en mängd olika `ssh` sätt att få verktyget. På Linux, Unix och macOS tillhandahålls det som en del av operativsystemet. Om du använder Windows bör du överväga något av följande alternativ:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash på Ubuntu på Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH ( OpenSSH ( OpenSSH ( OpenShttps://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Om du vill installera Bind använder du följande kommandon från SSH-sessionen:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Konfigurera Bind för att vidarebefordra namnmatchningsbegäranden till din lokala DNS-server. Det gör du genom att använda följande `/etc/bind/named.conf.options` text som innehållet i filen:

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
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > Ersätt värdena `goodclients` i avsnittet med IP-adressintervallet för de två virtuella nätverken. Det här avsnittet definierar de adresser som den här DNS-servern accepterar begäranden från.

    Så här redigerar du filen:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Om du vill spara filen använder du __Ctrl+X__, __Y__och sedan __Enter__.

4. Använd följande kommando i SSH-sessionen:

    ```bash
    hostname -f
    ```

    Det här kommandot returnerar ett värde som liknar följande text:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    Texten `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` är __DNS-suffixet__ för det här virtuella nätverket. Du bör spara det här värdet eftersom det används senare.

    Du måste också ta reda på DNS-suffixet från den andra DNS-servern. Du behöver det i nästa steg.

5. Om du vill konfigurera Bind för att matcha DNS-namn för resurser `/etc/bind/named.conf.local` i det virtuella nätverket använder du följande text som innehållet i filen:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Du måste `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` ersätta med DNS-suffixet för det andra virtuella nätverket. Och vidarebefordraren IP är den privata IP-adressen för DNS-servern i det andra virtuella nätverket.

    Så här redigerar du filen:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Om du vill spara filen använder du __Ctrl+X__, __Y__och sedan __Enter__.

6. Starta Bindning genom att använda följande kommando:

    ```bash
    sudo service bind9 restart
    ```

7. Om du vill kontrollera att bindning kan matcha namnen på resurserna i det andra virtuella nätverket använder du följande kommandon:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Ersätt `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` med det fullständigt kvalificerade domännamnet (FQDN) för den virtuella DNS-datorn i det andra nätverket.
    >
    > Ersätt `10.2.0.4` med den __interna IP-adressen__ för den anpassade DNS-servern i det andra virtuella nätverket.

    Svaret liknar följande text:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Hittills kan du inte slå upp IP-adressen från det andra nätverket utan angiven DNS-server-IP-adress.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurera det virtuella nätverket så att den anpassade DNS-servern används

Så här konfigurerar du det virtuella nätverket så att den anpassade DNS-servern används i stället för Azure rekursiv resolver:

1. I [Azure-portalen](https://portal.azure.com)väljer du det virtuella nätverket och väljer sedan __DNS-servrar__.

2. Välj __Anpassad__och ange den __interna IP-adressen__ för den anpassade DNS-servern. Slutligen väljer du __Spara__.

6. Öppna den virtuella DNS-servern i vnet1 och klicka på **Starta om**.  Du måste starta om alla virtuella datorer i det virtuella nätverket för att DNS-konfigurationen ska börja gälla.
7. Upprepa steg konfigurera den anpassade DNS-servern för vnet2.

Om du vill testa DNS-konfigurationen kan du ansluta till de två virtuella DNS-datorerna med SSH och pinga DNS-servern för det andra virtuella nätverket med hjälp av dess värdnamn. Om det inte fungerar använder du följande kommando för att kontrollera DNS-status:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Skapa Apache HBase-kluster

Skapa ett [Apache HBase-kluster](https://hbase.apache.org/) i vart och ett av de två virtuella nätverken med följande konfiguration:

- **Resursgruppsnamn:** Använd samma resursgruppsnamn som du skapade de virtuella nätverken.
- **Klustertyp**: HBase
- **Version**: HBase 1.1.2 (HDI 3.6)
- **Plats**: Använd samma plats som det virtuella nätverket.  Som standard är vnet1 *västra USA*och vnet2 är *östra USA*.
- **Lagring**: Skapa ett nytt lagringskonto för klustret.
- **Virtuellt nätverk** (från Avancerade inställningar på portalen): Välj vnet1 som du skapade i den sista proceduren.
- **Undernät:** Standardnamnet som används i mallen är **undernät1**.

För att säkerställa att miljön är korrekt konfigurerad måste du kunna pinga headnodens FQDN mellan de två klustren.

## <a name="load-test-data"></a>Läsa in testdata

När du replikerar ett kluster måste du ange de tabeller som du vill replikera. I det här avsnittet läser du in vissa data i källklustret. I nästa avsnitt aktiverar du replikering mellan de två klustren.

Om du vill skapa en **kontakttabell** och infoga vissa data i tabellen följer du instruktionerna i [Apache HBase-självstudiekursen: Kom igång med Apache HBase i HDInsight](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Om du vill replikera tabeller från ett anpassat namnområde måste du se till att lämpliga anpassade namnområden också definieras i målklustret.
>

## <a name="enable-replication"></a>Aktivera replikering

I följande steg beskrivs hur du anropar skriptet från Azure-portalen. Information om hur du kör en skriptåtgärd med hjälp av Azure PowerShell och Azure Classic CLI finns i [Anpassa HDInsight-kluster med hjälp av skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md).

**Så här aktiverar du HBase-replikering från Azure-portalen**

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Öppna källan HBase-kluster.
3. Välj **Skriptåtgärder**på klustermenyn .
4. Högst upp på sidan väljer du **Skicka ny**.
5. Markera eller ange följande information:

   1. **Namn**: Ange **Aktivera replikering**.
   2. **Bash Script URL:** Ange **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
   3. **Huvud**: Se till att detta är valt. Rensa de andra nodtyperna.
   4. **Parametrar**: Följande exempelparametrar möjliggör replikering för alla befintliga tabeller och kopierar sedan alla data från källklustret till målklustret:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > Använd värdnamn i stället för FQDN för både käll- och målklustrets DNS-namn.
      >
      > Denna genomgång förutsätter hn1 som aktiv headnode. Kontrollera klustret för att identifiera den aktiva huvudnoden.

6. Välj **Skapa**. Skriptet kan ta en stund att köra, särskilt när du använder **argumentet -copydata.**

Nödvändiga argument:

|Namn|Beskrivning|
|----|-----------|
|-s, --src-kluster | Anger DNS-namnet på källan HBase-kluster. Till exempel: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Anger DNS-namnet på HBase-klustret för mål (replik). Till exempel: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-lösenord | Anger admin-lösenordet för Ambari i HBase-källklustret. |
|-dp, --dst-ambari-lösenord | Anger administratörslösenordet för Ambari i HBase-klustret för målet.|

Valfria argument:

|Namn|Beskrivning|
|----|-----------|
|-su, --src-ambari-användare | Anger administratörsanvändarnamnet för Ambari i HBase-källklustret. Standardvärdet är **admin**. |
|-du, --dst-ambari-användare | Anger administratörsanvändarnamnet för Ambari i HBase-klustret för målet. Standardvärdet är **admin**. |
|-t, --tabell-lista | Anger vilka tabeller som ska replikeras. Till exempel: --table-list="table1;table2;table3". Om du inte anger tabeller replikeras alla befintliga HBase-tabeller.|
|-m, --maskin | Anger huvudnoden där skriptåtgärden körs. Värdet ska väljas baserat på vilken är den aktiva huvudnoden. Använd det här alternativet när du kör skriptet $0 som en skriptåtgärd från HDInsight-portalen eller Azure PowerShell.|
|-cp, -copydata | Aktiverar migreringen av befintliga data i tabellerna där replikering är aktiverat. |
|-rpm, -replicate-phoenix-meta | Aktiverar replikering på Phoenix-systemtabeller. <br><br>*Använd det här alternativet med försiktighet.* Vi rekommenderar att du återskapar Phoenix-tabeller på replikkluster innan du använder skriptet. |
|-h, --hjälp | Visar användningsinformation. |

Avsnittet `print_usage()` i [skriptet](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) har en detaljerad förklaring av parametrar.

När skriptåtgärden har distribuerats kan du använda SSH för att ansluta till målet HBase-klustret och sedan kontrollera att data har replikerats.

### <a name="replication-scenarios"></a>Replikeringsscenarier

I följande lista visas några allmänna användningsfall och deras parameterinställningar:

- **Aktivera replikering på alla tabeller mellan de två klustren**. Det här scenariot kräver inte kopiering eller migrering av befintliga data i tabellerna och phoenix-tabeller används inte. Använd följande parametrar:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Aktivera replikering i specifika tabeller**. Om du vill aktivera replikering i tabell1, tabell2 och tabell3 använder du följande parametrar:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Aktivera replikering i specifika tabeller och kopiera befintliga data**. Om du vill aktivera replikering i tabell1, tabell2 och tabell3 använder du följande parametrar:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Aktivera replikering på alla tabeller och replikera Phoenix-metadata från källa till mål**. Phoenix metadata replikering är inte perfekt. Använd den med försiktighet. Använd följande parametrar:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Kopiera och migrera data

Det finns två separata skriptåtgärdsskript tillgängliga för kopiering eller migrering av data när replikeringen är aktiverad:

- [Skript för små tabeller](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabeller som är några gigabyte i storlek och den totala kopian förväntas avslutas på mindre än en timme)

- [Skript för stora tabeller](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabeller som förväntas ta längre tid än en timme att kopiera)

Du kan följa samma procedur som beskrivs i [Aktivera replikering](#enable-replication) för att anropa skriptåtgärden. Använd följande parametrar:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

Avsnittet `print_usage()` i [skriptet](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) har en detaljerad beskrivning av parametrar.

### <a name="scenarios"></a>Scenarier

- **Kopiera specifika tabeller (test1, test2 och test3) för alla rader som redigerats hittills (aktuell tidsstämpel):**

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Eller:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Kopiera specifika tabeller med ett angivet tidsintervall:**

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Inaktivera replikering

Om du vill inaktivera replikering använder du ett annat skriptåtgärdsskript från [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Du kan följa samma procedur som beskrivs i [Aktivera replikering](#enable-replication) för att anropa skriptåtgärden. Använd följande parametrar:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

Avsnittet `print_usage()` i [skriptet](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) har en detaljerad förklaring av parametrar.

### <a name="scenarios"></a>Scenarier

- **Inaktivera replikering i alla tabeller:**

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  eller

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Inaktivera replikering på angivna tabeller (tabell1, tabell2 och tabell3):**

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

> [!NOTE]
> Om du tänker ta bort målklustret kontrollerar du att du tar bort det från peer-listan i källklustret. Detta kan göras genom att köra kommandot remove_peer '1' vid hbase-skalet på källklustret. Om inte detta källklustret kanske inte fungerar korrekt.
>

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar Apache HBase-replikering i ett virtuellt nätverk eller mellan två virtuella nätverk. Mer information om HDInsight och Apache HBase finns i följande artiklar:

* [Komma igång med Apache HBase i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Översikt över HDInsight Apache HBase](./apache-hbase-overview.md)
* [Skapa Apache HBase-kluster i Azure Virtual Network](./apache-hbase-provision-vnet.md)

