---
title: Ansluta HDInsight till ditt lokala nätverk – Azure HDInsight | Microsoft Docs
description: Lär dig hur du skapar ett HDInsight-kluster i en Azure-nätverk och anslut den till ditt lokala nätverk. Lär dig mer om att konfigurera namnmatchning mellan HDInsight och ditt lokala nätverk med hjälp av en anpassad DNS-server.
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: larryfr
ms.openlocfilehash: ea793af7fc4565c054675af7cbf88b74722690f7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="connect-hdinsight-to-your-on-premise-network"></a>Anslut HDInsight till lokalt nätverk

Lär dig hur du ansluter HDInsight till ditt lokala nätverk med hjälp av virtuella Azure-nätverk och en VPN-gateway. Det här dokumentet innehåller planeringsinformation om:

* Använda HDInsight i ett virtuellt Azure-nätverk som ansluter till ditt lokala nätverk.

* Konfigurera DNS-namnmatchningen mellan det virtuella nätverket och ditt lokala nätverk.

* Konfigurerar nätverkssäkerhetsgrupper för att begränsa internet-åtkomst till HDInsight.

* Portar som tillhandahålls av HDInsight på det virtuella nätverket.

## <a name="create-the-virtual-network-configuration"></a>Skapa den virtuella nätverkskonfigurationen

Använd följande dokument om du vill veta hur du skapar ett virtuellt Azure-nätverk som är anslutet till det lokala nätverket:
    
* [Använd Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Använda Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Använda Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>Konfigurera namnmatchning

Om du vill tillåta HDInsight och resurser i anslutna nätverk för att kommunicera med namn, måste du utföra följande åtgärder:

* Skapa en anpassad DNS-server i Azure Virtual Network.

* Konfigurera det virtuella nätverket om du vill använda anpassade DNS-servern i stället för default Azure rekursiv matchning.

* Konfigurera vidarebefordran mellan anpassade DNS-servern och lokala DNS-servern.

Den här konfigurationen gör följande:

* Begäranden om fullständiga domännamn som har DNS-suffixet __för det virtuella nätverket__ vidarebefordras till den anpassade DNS-servern. Den anpassade DNS-servern vidarebefordrar sedan dessa begäranden till Azure rekursiv matcharen som returnerar IP-adressen.

* Alla andra begäranden vidarebefordras till den lokala DNS-servern. Även begäranden för offentliga internet-resurser, till exempel microsoft.com vidarebefordras till den lokala DNS-servern för namnmatchning.

I följande diagram är gröna linjer begäranden för resurser som slutar i DNS-suffixet för det virtuella nätverket. Blå raderna är begäranden för resurser i det lokala nätverket eller på internet.

![Diagram över hur DNS-förfrågningar har lösts i konfigurationen i det här dokumentet](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>Skapa en anpassad DNS-server

> [!IMPORTANT]
> Du måste skapa och konfigurera DNS-servern innan du installerar HDInsight till det virtuella nätverket.

Så här skapar du en Linux VM som använder den [binda](https://www.isc.org/downloads/bind/) DNS-programvara, Använd följande steg:

> [!NOTE]
> Följande steg används den [Azure-portalen](https://portal.azure.com) att skapa en virtuell dator i Azure. Andra sätt att skapa en virtuell dator finns i följande dokument:
>
> * [Skapa VM – Azure CLI](../virtual-machines/linux/quick-create-cli.md)
> * [Skapa VM - Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. Från den [Azure-portalen](https://portal.azure.com)väljer __+__, __Compute__, och __Ubuntu Server 16.04 LTS__.

    ![Skapa en virtuell Ubuntu-dator](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. Ange följande information i avsnittet __Grundläggande inställningar__:

    * __Namnet__: ett eget namn som identifierar den här virtuella datorn. Till exempel __DNSProxy__.
    * __Användarnamnet__: namnet på SSH-konto.
    * __Offentlig SSH-nyckel__ eller __lösenord__: autentiseringsmetod för SSH-konto. Vi rekommenderar att du använder offentliga nycklar som de är säkrare. Mer information finns i [skapa och använda SSH-nycklar för Linux virtuella datorer](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentet.
    * __Resursgruppen__: Välj __använda befintliga__, och välj sedan den resursgrupp som innehåller det virtuella nätverket som skapade tidigare.
    * __Plats__: Välj platsen som det virtuella nätverket.

    ![Grundläggande konfiguration av virtuell dator](./media/connect-on-premises-network/vm-basics.png)

    Lämna andra transaktioner på standardvärdena och välj sedan __OK__.

3. Från den __välja en storlek__ väljer VM-storlek. Välj alternativet minsta och lägsta kostnaden för den här självstudiekursen. Om du vill fortsätta använda den __Välj__ knappen.

4. Från den __inställningar__ ange följande information:

    * __Virtuellt nätverk__: Välj det virtuella nätverk som du skapade tidigare.

    * __Undernät__: Välj standardundernät för det virtuella nätverket. Gör __inte__ välja det undernät som används av VPN-gatewayen.

    * __Diagnostiklagringskonto__: Välj ett befintligt lagringskonto eller skapa en ny.

    ![Inställningarna för virtuella nätverk](./media/connect-on-premises-network/virtual-network-settings.png)

    Lämna de andra posterna på standardvärdet, och välj sedan __OK__ att fortsätta.

5. Från den __inköp__ väljer den __inköp__ för att skapa den virtuella datorn.

6. När den virtuella datorn har skapats, dess __översikt__ avsnitt visas. Välj i listan till vänster __egenskaper__. Spara den __offentliga IP-adressen__ och __privata IP-adressen__ värden. Den används i nästa avsnitt.

    ![Offentliga och privata IP-adresser](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Installera och konfigurera Bind (DNS-programvara)

1. Använda SSH för att ansluta till den __offentliga IP-adressen__ för den virtuella datorn. I följande exempel ansluter till en virtuell dator på 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Ersätt `sshuser` med SSH-användarkonto som du angav när du skapar klustret.

    > [!NOTE]
    > Det finns flera olika sätt att hämta den `ssh` verktyget. Linux-, Unix- och macOS anges som en del av operativsystemet. Om du använder Windows, bör du något av följande alternativ:
    >
    > * [Azure-molnet Shell](../cloud-shell/quickstart.md)
    > * [Bash på Ubuntu på Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Använd följande kommandon från SSH-sessionen om du vill installera bindning:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Om du vill konfigurera Bind att vidarebefordra begäran till din DNS-server för lokal namnmatchning, Använd följande text som innehållet i den `/etc/bind/named.conf.options` filen:

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]
    > Ersätter värdena i den `goodclients` avsnitt med IP-adressintervall i virtuella nätverk och lokala nätverk. Det här avsnittet beskriver de adresser som DNS-servern accepterar begäranden från.
    >
    > Ersätt den `192.168.0.1` post i den `forwarders` avsnitt med IP-adress i lokala DNS-servern. Den här posten dirigerar DNS-förfrågningar till din lokala DNS-servern för namnmatchning.

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

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    Den `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` texten är den __DNS-suffix__ för den här virtuella nätverket. Spara det här värdet eftersom det används senare.

5. Konfigurera Bind att matcha DNS-namn för resurser i det virtuella nätverket med följande text som innehållet i den `/etc/bind/named.conf.local` filen:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > Du måste ersätta den `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` med DNS-suffix som du hämtade tidigare.

    Om du vill redigera den här filen använder du följande kommando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Om du vill spara filen, Använd __Ctrl + X__, __Y__, och sedan __RETUR__.

6. Om du vill starta bindning, använder du följande kommando:

    ```bash
    sudo service bind9 restart
    ```

7. Om du vill kontrollera att binda kan matcha namnen på de resurser i ditt lokala nätverk, använder du följande kommandon:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > Ersätt `dns.mynetwork.net` med fullständigt kvalificerade domännamnet (FQDN) för en resurs i ditt lokala nätverk.
    >
    > Ersätt `10.0.0.4` med den __interna IP-adress__ på anpassade DNS-servern i det virtuella nätverket.

    Svaret ser ut som följande text:

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurera det virtuella nätverket om du vill använda anpassade DNS-servern

Använd följande steg om du vill konfigurera det virtuella nätverket om du vill använda anpassade DNS-servern i stället för Azure rekursiv matcharen:

1. I den [Azure-portalen](https://portal.azure.com), Välj det virtuella nätverket och väljer sedan __DNS-servrar__.

2. Välj __anpassade__, och ange den __interna IP-adress__ för anpassade DNS-servern. Välj slutligen __spara__.

    ![Ange anpassade DNS-server för nätverket](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>Konfigurera den lokala DNS-servern

I det föregående avsnittet konfigurerat du anpassade DNS-servern vidarebefordrar begäranden till den lokala DNS-servern. Därefter måste du konfigurera den lokala DNS-servern vidarebefordrar begäranden till den anpassade DNS-servern.

Specifika anvisningar för hur du konfigurerar DNS-servern i dokumentationen för din DNS-server-program. Leta efter instruktioner för hur du konfigurerar en __villkorlig vidarebefordrare__.

Villkorlig vidarebefordran vidarebefordrar bara begäranden för en specifik DNS-suffix. I det här fallet måste du konfigurera en vidarebefordrare för DNS-suffixet för det virtuella nätverket. Begäranden för det här suffixet ska vidarebefordras till IP-adressen för den anpassade DNS-servern. 

Följande är ett exempel på en villkorlig vidarebefordrare konfiguration för den **binda** DNS-programvara:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Information om hur du använder DNS på **Windows Server 2016**, finns det [Lägg till DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentationen...

När du har konfigurerat den lokala DNS-servern, kan du använda `nslookup` från lokala nätverket för att kontrollera att du kan matcha namnen i det virtuella nätverket. I följande exempel 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Det här exemplet använder den lokala DNS-servern på 196.168.0.4 för att matcha namnet på den anpassade DNS-servern. Ersätt IP-adressen med den lokala DNS-servern. Ersätt den `dnsproxy` adress med det fullständiga domännamnet för anpassade DNS-servern.

## <a name="optional-control-network-traffic"></a>Valfritt: Kontrollen nätverkstrafik

Du kan använda nätverkssäkerhetsgrupper (NSG) eller användardefinierade vägar (UDR) för att kontrollera nätverkstrafik. NSG: er kan du filtrera inkommande och utgående trafik och tillåta eller neka trafik. Udr: er kan du kontrollera hur trafiken flödar mellan resurser i det virtuella nätverket, internet och det lokala nätverket.

> [!WARNING]
> HDInsight kräver inkommande åtkomst från särskilda IP-adresser i Azure-molnet och obegränsad utgående åtkomst. När du använder NSG: er eller udr: er för att styra trafik, måste du utföra följande steg:

1. Hitta IP-adresser för den plats som innehåller det virtuella nätverket. En lista över nödvändiga IP-adresser per plats, se [krävs IP-adresser](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. För IP-adresser identifierades i steg 1, tillåter inkommande trafik från den IP-adresser.

   * Om du använder __NSG__: Tillåt __inkommande__ trafik på port __443__ för IP-adresser.
   * Om du använder __UDR__: Ange den __nästa hopp__ typ för vägen som __Internet__ för IP-adresser.

Ett exempel på hur Azure PowerShell eller Azure CLI för att skapa NSG: er finns i [utöka HDInsight med Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) dokumentet.

## <a name="create-the-hdinsight-cluster"></a>Skapa HDInsight-kluster

> [!WARNING]
> Innan du installerar HDInsight i det virtuella nätverket måste du konfigurera anpassade DNS-servern.

Följ stegen i den [skapar ett HDInsight-kluster med hjälp av Azure portal](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentet för att skapa ett HDInsight-kluster.

> [!WARNING]
> * Du måste välja den plats som innehåller det virtuella nätverket när klustret skapas.
>
> * I den __avancerade inställningar__ en del av konfigurationen, måste du välja virtuella nätverk och undernät som du skapade tidigare.

## <a name="connecting-to-hdinsight"></a>Ansluter till HDInsight

De flesta dokumentation på HDInsight förutsätter att du har åtkomst till klustret via internet. Till exempel att du kan ansluta till klustret i https://CLUSTERNAME.azurehdinsight.net. Den här adressen använder offentliga gatewayen, som inte är tillgängligt om du har använt NSG: er eller udr: er för att begränsa åtkomst från internet.

Viss dokumentation refererar också till `headnodehost` när du ansluter till klustret från en SSH-session. Den här adressen är endast tillgänglig från noder i ett kluster och kan inte användas på klienter som är anslutna via det virtuella nätverket.

Om du vill ansluta direkt till HDInsight till det virtuella nätverket, Använd följande steg:

1. Använd någon av följande metoder för att identifiera HDInsight-klusternoder interna fullständigt kvalificerade domännamn:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Information om den port som en tjänst är tillgänglig på finns i [portar som används av Hadoop-tjänster på HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentet.

    > [!IMPORTANT]
    > Vissa tjänster som finns på huvudnoderna är bara aktiva på en nod i taget. Om du försöker att komma åt en tjänst på en huvudnod och det misslyckas, kan du växla till andra huvudnod.
    >
    > Till exempel är Ambari endast aktiv i en huvudnod i taget. Om du försöker komma åt Ambari på en huvudnod och returnerar ett 404-fel, körs det i andra huvudnod.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder HDInsight i ett virtuellt nätverk finns [utöka HDInsight med hjälp av Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md).

* Mer information om virtuella Azure-nätverk finns i [översikt över Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Mer information om nätverkssäkerhetsgrupper finns [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md).

* Mer information om användardefinierade vägar finns [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md).
