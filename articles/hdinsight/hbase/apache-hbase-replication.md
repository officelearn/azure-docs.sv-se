---
title: Konfigurera HBase-kluster-replikering i Azure-nätverk
description: Lär dig hur du ställer in HBase-replikering från en HDInsight-version till en annan för Utjämning av nätverksbelastning, hög tillgänglighet, noll stilleståndstid vid migrering och uppdateringar och katastrofåterställning.
services: hdinsight,virtual-network
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: cfc71f34f4b1d8027714c9fb610beebf8cd3be47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978547"
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Konfigurera HBase-kluster-replikering i Azure-nätverk

Lär dig hur du ställer in HBase-replikering inom ett virtuellt nätverk, eller mellan två virtuella nätverk i Azure.

Kluster-replikering använder en käll-push-metod. Ett HBase-kluster kan vara en källa eller ett mål eller det uppfyller båda rollerna samtidigt. Replikering är asynkrona. Målet för replikering är slutlig konsekvens. När källan får redigerar du en kolumnfamilj när replikering har aktiverats, sprids redigera till alla målkluster. När data replikeras från ett kluster till ett annat, spåras källklustret och alla kluster som redan har förbrukat data, för att förhindra replikering slingor.

I den här självstudien konfigurerar du en källa-mål-replikering. Andra klustertopologier finns i den [referensguiden för Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Följande är HBase-replikering användning ärenden för ett enda virtuellt nätverk:

* Belastningsbalansering. Du kan till exempel köra genomsökningar eller MapReduce-jobb på målklustret och mata in data i källklustret.
* Lägger till hög tillgänglighet.
* Migrera data från ett HBase-kluster till ett annat.
* Uppgradera ett Azure HDInsight-kluster från en version till en annan.

Följande är HBase-replikering användning ärenden för två virtuella nätverk:

* Konfigurera haveriberedskap.
* Utjämning av nätverksbelastning och partitionering programmet.
* Lägger till hög tillgänglighet.

Du kan replikera kluster med hjälp av [skripta åtgärd](../hdinsight-hadoop-customize-cluster-linux.md) skript från [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Se [få en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Ställ in miljöer

Du har tre alternativ:

- Två HBase-kluster i en Azure-nätverk.
- Två HBase-kluster i två olika virtuella nätverk i samma region.
- Två HBase-kluster i två olika virtuella nätverk i två olika regioner (geo-replikering).

Den här artikeln tar upp den geo-replikering.

För att du ställer in miljöer, vi har skapat några [Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-overview.md). Om du föredrar att ställa in miljöer genom att använda andra metoder, se:

- [Skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Skapa HBase-kluster i Azure-nätverk](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Konfigurera två virtuella nätverk i två olika regioner

Om du vill använda en mall som skapar två virtuella nätverk i två olika regioner och VPN-anslutning mellan virtuella nätverk, väljer du följande **distribuera till Azure** knappen. Malldefinitionen lagras i en [offentliga blob-lagring](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Vissa hårdkodade värden i mallen:

**Virtuellt nätverk 1**

| Egenskap  | Värde |
|----------|-------|
| Plats | Västra USA |
| Namn på virtuellt nätverk | &lt;ClusterNamePrevix >-vnet1 |
| Adressutrymmets prefix | 10.1.0.0/16 |
| Namn på undernät | subnät 1 |
| Undernätets prefix | 10.1.0.0/24 |
| Namn på undernät (gateway) | GatewaySubnet (det går inte att ändras) |
| Undernätsprefix (gateway) | 10.1.255.0/27 |
| Gateway-namn | vnet1gw |
| Gateway-typ | Vpn |
| Typ av VPN-gateway | Routningsbaserad |
| Gateway-SKU | Basic |
| Gateway IP | vnet1gwip |

**VNet-2**

| Egenskap  | Värde |
|----------|-------|
| Plats | Östra USA |
| Namn på virtuellt nätverk | &lt;ClusterNamePrevix >-vnet2 |
| Adressutrymmets prefix | 10.2.0.0/16 |
| Namn på undernät | subnät 1 |
| Undernätets prefix | 10.2.0.0/24 |
| Namn på undernät (gateway) | GatewaySubnet (det går inte att ändras) |
| Undernätsprefix (gateway) | 10.2.255.0/27 |
| Gateway-namn | vnet2gw |
| Gateway-typ | Vpn |
| Typ av VPN-gateway | Routningsbaserad |
| Gateway-SKU | Basic |
| Gateway IP | vnet1gwip |

## <a name="setup-dns"></a>Konfigurera DNS

Mallen skapar en Ubuntu-dator i var och en av de två virtuella nätverken i det sista avsnittet.  I det här avsnittet Installera bindning på två DNS-virtuella datorer och sedan konfigurera DNS-vidarebefordran på två virtuella datorer.

Om du vill installera Bind behöver yon hitta offentliga IP-adress för två DNS-virtuella datorer.

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Öppna DNS-virtuell dator genom att välja **resursgrupper > [resursgruppens namn] > [vnet1DNS]**.  Resursgruppens namn är det som du skapar i föregående procedur. Standard-DNS VM-namn är *vnet1DNS* och *vnet2NDS*.
3. Välj **egenskaper** att öppna egenskapssidan för det virtuella nätverket.
4. Anteckna den **offentliga IP-adressen**, och också kontrollera den **privat IP-adress**.  Den privata IP-adressen ska vara **10.1.0.4** för vnet1DNS och **10.2.0.4** för vnet2DNS.  

Följ anvisningarna nedan om du vill installera bindning:

1. Använda SSH för att ansluta till den __offentliga IP-adressen__ för den virtuella datorn i DNS. I följande exempel ansluter till en virtuell dator på 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Ersätt `sshuser` med SSH-användarkonto som du angav när du skapar den virtuella datorn i DNS.

    > [!NOTE]
    > Det finns en mängd olika sätt att hämta den `ssh` verktyget. Linux-, Unix- och macOS anges som en del av operativsystemet. Om du använder Windows, bör du något av följande alternativ:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash på Ubuntu på Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Använd följande kommandon från SSH-sessionen om du vill installera bindning:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Konfigurera Bind att vidarebefordra begäran till din DNS-server med en lokal namnmatchning. Du gör detta genom att använda följande text som innehållet i den `/etc/bind/named.conf.options` fil:

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
    > Ersätt värdena i den `goodclients` avsnittet med IP-adressintervall i de två virtuella nätverken. Det här avsnittet definierar de adresser som den här DNS-servern tar emot förfrågningar från.

    Om du vill redigera den här filen använder du följande kommando:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Om du vill spara filen, Använd __Ctrl + X__, __Y__, och sedan __RETUR__.

4. Använd följande kommando från SSH-sessionen:

    ```bash
    hostname -f
    ```

    Det här kommandot returnerar ett värde som liknar följande text:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    Den `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` texten är den __DNS-suffix__ för den här virtuella nätverket. Du bör spara det här värdet eftersom det används senare.

    Du måste också ta reda på DNS-suffix från DNS-servern. Du behöver det i nästa steg.

5. Om du vill konfigurera bindning för DNS-namnmatchning för resurser i det virtuella nätverket, Använd följande text som innehållet i den `/etc/bind/named.conf.local` fil:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]
    > Du måste ersätta det `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` med DNS-suffixet för det virtuella nätverket. Och vidarebefordrare IP-Adressen är den privata IP-adressen för DNS-servern i det virtuella nätverket.

    Om du vill redigera den här filen använder du följande kommando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Om du vill spara filen, Använd __Ctrl + X__, __Y__, och sedan __RETUR__.

6. Om du vill starta bindning, använder du följande kommando:

    ```bash
    sudo service bind9 restart
    ```

7. Kontrollera att bindning kan matcha namnen på resurser i det virtuella nätverket genom att använda följande kommandon:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net 10.2.0.4
    ```

    > [!IMPORTANT]
    > Ersätt `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` med det fullständigt kvalificerade domännamnet (FQDN) för den virtuella datorn i DNS i andra nätverk.
    >
    > Ersätt `10.2.0.4` med den __interna IP-adressen__ för din anpassade DNS-server i det virtuella nätverket.

    Svaret ser ut som följande text:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Fram till nu så du kan inte kontrollera IP-adress från det andra nätverket utan angiven DNS-serverns IP-adress.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurera det virtuella nätverket för att använda den anpassa DNS-servern

Om du vill konfigurera det virtuella nätverket om du vill använda anpassade DNS-servern i stället för Azures rekursiva matchare, använder du följande steg:

1. I den [Azure-portalen](https://portal.azure.com), Välj det virtuella nätverket och välj sedan __DNS-servrar__.

2. Välj __anpassade__, och ange den __interna IP-adressen__ för anpassad DNS-servern. Välj slutligen __spara__.

6. Öppna DNS-server-dator i vnet1 och klicka på **starta om**.  Du måste starta om alla virtuella datorer i det virtuella nätverket att göra DNS-konfigurationen ska börja gälla.
7. Upprepa steg konfigurerar anpassade DNS-servern för vnet2.

Du kan ansluta till två DNS-virtuella datorer med SSH och pinga den DNS-servern i det virtuella nätverket med hjälp av dess värdnamn om du vill testa DNS-konfigurationen. Om det inte fungerar, använder du följande kommando för att kontrollera status för DNS:

```bash
sudo service bind9 status
```

## <a name="create-hbase-clusters"></a>Skapa HBase-kluster

Skapa ett HBase-kluster i var och en av de två virtuella nätverk med följande konfiguration:

- **Resursgruppens namn**: Använd samma resursgruppnamn som du skapade de virtuella nätverken.
- **Typ av kluster**: HBase
- **Version**: HBase 1.1.2 (HDI 3.6)
- **Plats**: använda samma plats som det virtuella nätverket.  Som standard är vnet1 *västra USA*, och vnet2 är *USA, östra*.
- **Storage**: skapa ett nytt lagringskonto för klustret.
- **Virtuellt nätverk** (från avancerade inställningar på portalen): Välj vnet1 som du skapade i föregående procedur.
- **Undernät**: standardnamnet som används i mallen är **subnet1**.

För att säkerställa miljön är rätt konfigurerad, måste du kunna pinga den huvudnoden FQDN mellan två kluster.

## <a name="load-test-data"></a>Test av data

När du replikerar ett kluster måste du ange de tabeller som du vill replikera. I det här avsnittet ska läsa du in data i källklustret. I nästa avsnitt aktiverar du replikering mellan två kluster.

Skapa en **kontakter** tabellen och infoga data i tabellen, följ instruktionerna på [självstudier för HBase: komma igång med Apache HBase i HDInsight](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Aktivera replikering

Följande steg beskriver hur du anropar åtgärdsskriptet skriptet från Azure-portalen. Information om hur du kör en skriptåtgärd med hjälp av Azure PowerShell och den klassiska Azure-CLI finns i [anpassa HDInsight-kluster med skriptåtgärd](../hdinsight-hadoop-customize-cluster-linux.md).

**Att aktivera HBase-replikering från Azure portal**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna källa HBase-kluster.
3. Kluster-menyn väljer du **skriptåtgärder**.
4. Längst ned på sidan Välj **Skicka ny**.
5. Välj eller ange följande information:

  1. **Namnet**: Ange **Aktivera replikering**.
  2. **Bash-Webbadress för skript**: Ange **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **HEAD**: se till att det här alternativet väljs. Ta bort andra nodtyper.
  4. **Parametrar**: exempelparametrarna i följande aktiverar replikering för alla befintliga tabeller och sedan kopiera alla data från källklustret till målklustret:

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Använd värdnamnet i stället för FQDN för både källa och mål klustrets DNS-namn.

6. Välj **Skapa**. Skriptet kan ta en stund att köra, särskilt när du använder den **- copydata** argumentet.

Obligatoriska argument:

|Namn|Beskrivning|
|----|-----------|
|-s,--src-kluster | Anger DNS-namnet på källan HBase-kluster. Till exempel: -s hbsrccluster,--src-cluster = hbsrccluster |
|-d,--dst-kluster | Anger DNS-namnet på målet (replik) HBase-kluster. Till exempel: -s dsthbcluster,--src-cluster = dsthbcluster |
|-sp,--src-ambari-lösenord | Anger lösenordet för serveradministratören för Ambari på källan HBase-kluster. |
|-dp,--dst-ambari-lösenord | Anger lösenordet för serveradministratören för Ambari på HBase målklustret.|

Valfria argument:

|Namn|Beskrivning|
|----|-----------|
|-su-och--src-ambari-användare | Anger ett administratörsanvändarnamn för Ambari på källan HBase-kluster. Standardvärdet är **admin**. |
|-du--dst-ambari-användare | Anger ett administratörsanvändarnamn för Ambari på mål HBase-kluster. Standardvärdet är **admin**. |
|-t,--tabell lista | Anger tabellerna som ska replikeras. Till exempel:--Tabellista = ”tabell1; tabell2; tabell 3”. Om du inte anger tabeller, replikeras alla befintliga HBase-tabeller.|
|-m, – dator | Anger huvudnoden där skriptåtgärd körs. Värdet är antingen **hn1** eller **hn0**. Eftersom den **hn0** Huvudnoden är vanligtvis ju mer upptagen, bör du använda **hn1**. Använd det här alternativet när du kör skriptet $0 som en skriptåtgärd från HDInsight-portalen eller Azure PowerShell.|
|-cp, - copydata | Gör det möjligt att migrera befintliga data i tabellerna där replikering har aktiverats. |
|-rpm, -replikera-phoenix-metadata | Aktiverar replikering på Phoenix systemtabeller. <br><br>*Använd det här alternativet med försiktighet.* Vi rekommenderar att du återskapar Phoenix tabeller på repliken kluster innan du använder det här skriptet. |
|-h, – hjälp | Visar användningsinformation. |

Den `print_usage()` delen av den [skriptet](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) har en detaljerad förklaring av parametrarna.

Du kan använda SSH för att ansluta till HBase-kluster mål och kontrollera sedan att data har replikerats när skriptåtgärd har distribuerats.

### <a name="replication-scenarios"></a>Replikeringsscenarier

I följande lista visas ibland allmän användning och deras parameterinställningar:

- **Aktivera replikering för alla tabeller mellan två kluster**. Det här scenariot kräver inte kopiera eller migrera befintliga data i tabeller och den använder inte Phoenix tabeller. Använd följande parametrar:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Aktivera replikering på specifika tabeller**. Om du vill aktivera replikering på tabell1 och tabell2 tabell 3 måste du använda följande parametrar:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Aktivera replikering på specifika tabeller och kopiera den befintliga informationen**. Om du vill aktivera replikering på tabell1 och tabell2 tabell 3 måste du använda följande parametrar:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Aktivera replikering för alla tabeller och replikera Phoenix metadata från källan till målet**. Phoenix metadata replikering är inte exakt. Du kan använda den med försiktighet. Använd följande parametrar:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Kopiera och migrera data

Det finns två separata skript åtgärd skript för att kopiera och migrera data när replikering har aktiverats:

- [Skript för små tabeller](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabeller som är några få gigabyte i storlek och hela kopian förväntas Slutför i mindre än en timme)

- [Skript för stora tabeller](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabeller som förväntas ta längre tid än en timme att kopiera)

Du kan följa samma steg som beskrivs i [Aktivera replikering](#enable-replication) att anropa skriptåtgärden. Använd följande parametrar:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

Den `print_usage()` delen av den [skriptet](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) har en detaljerad beskrivning av parametrar.

### <a name="scenarios"></a>Scenarier

- **Kopiera specifika tabeller (test1 test2 och test3) för alla rader som redigeras fram till nu (aktuella tidsstämpeln)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Eller:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Kopiera specifika tabeller med ett angivet tidsintervall**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Inaktivera replikering

Inaktivera replikering med ett annat skript åtgärdsskriptet från [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Du kan följa samma steg som beskrivs i [Aktivera replikering](#enable-replication) att anropa skriptåtgärden. Använd följande parametrar:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

Den `print_usage()` delen av den [skriptet](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) har en detaljerad förklaring av parametrarna.

### <a name="scenarios"></a>Scenarier

- **Inaktivera replikering på alla tabeller**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  eller

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Inaktivera replikering på angivna tabellerna (tabell1 tabell2 och tabell 3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du ställer in HBase-replikering inom ett virtuellt nätverk, eller mellan två virtuella nätverk. Om du vill veta mer om HDInsight och HBase kan du läsa följande artiklar:

* [Kom igång med Apache HBase i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight HBase-översikt](./apache-hbase-overview.md)
* [Skapa HBase-kluster i Azure-nätverk](./apache-hbase-provision-vnet.md)

