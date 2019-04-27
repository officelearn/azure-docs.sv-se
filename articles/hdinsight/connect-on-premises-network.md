---
title: Anslut Azure HDInsight till ditt lokala nätverk
description: Lär dig hur du skapar ett HDInsight-kluster i Azure Virtual Network och sedan ansluter den till ditt lokala nätverk. Lär dig mer om att konfigurera namnmatchning mellan HDInsight och ditt lokala nätverk med hjälp av en anpassad DNS-server.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 52fe8c05101f9647549acec276f0bdb9fa52d1c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60537912"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Ansluta HDInsight till det lokala nätverket

Lär dig mer om att ansluta HDInsight till det lokala nätverket med hjälp av Azure-nätverk och en VPN-gateway. Det här dokumentet innehåller planeringsinformation om:

* Använda HDInsight i ett virtuellt Azure-nätverk som ansluter till ditt lokala nätverk.
* Konfigurera DNS-namnmatchningen mellan det virtuella nätverket och ditt lokala nätverk.
* Konfigurerar nätverkssäkerhetsgrupper för att begränsa internet-åtkomst till HDInsight.
* Portar som tillhandahålls av HDInsight i det virtuella nätverket.

## <a name="overview"></a>Översikt

Om du vill tillåta HDInsight och resurser i anslutna nätverk för att kommunicera med namn, måste du utföra följande åtgärder:

* Skapa Azure-nätverk.
* Skapa en anpassad DNS-server i Azure-nätverk.
* Konfigurera det virtuella nätverket om du vill använda anpassade DNS-servern i stället för standard Azure rekursiva matchare.
* Konfigurera vidarebefordran mellan anpassade DNS-servern och den lokala DNS-servern.

Den här konfigurationen gör på följande:

* Begäranden för fullständigt kvalificerade domännamn som har DNS-suffixet __för det virtuella nätverket__ vidarebefordras till den anpassa DNS-servern. Den anpassa DNS-servern vidarebefordrar sedan dessa begäranden till Azure rekursiva matchare, som returnerar IP-adressen.
* Alla övriga förfrågningar vidarebefordras till den lokala DNS-servern. Även begäranden för offentliga internet-resurser, till exempel microsoft.com vidarebefordras till den lokala DNS-server för namnmatchning.

I följande diagram är gröna linjer begäranden för resurser som slutar med DNS-suffixet för det virtuella nätverket. Blå linjer är begäranden för resurser i det lokala nätverket eller på internet.

![Diagram över hur DNS-förfrågningar har lösts i konfigurationen i det här dokumentet](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Om du använder PowerShell, måste den [AZ modulen](https://docs.microsoft.com/powershell/azure/overview).
* Om du vill använda Azure CLI och du inte har installerat den, se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Skapa konfiguration av virtuellt nätverk

Använd följande dokument för att lära dig hur du skapar ett virtuellt Azure-nätverk som är ansluten till ditt lokala nätverk:

* [Använda Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Använda Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Använda Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Skapa anpassad DNS-server

> [!IMPORTANT]  
> Du måste skapa och konfigurera DNS-servern innan du installerar HDInsight till det virtuella nätverket.

De här stegen används den [Azure-portalen](https://portal.azure.com) att skapa en Azure virtuell dator. Andra sätt att skapa en virtuell dator, se [skapa VM – Azure CLI](../virtual-machines/linux/quick-create-cli.md) och [skapa VM – Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Du skapar en Linux-VM som använder den [binda](https://www.isc.org/downloads/bind/) DNS-programvara, Använd följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com).
  
2. I den vänstra menyn navigerar du till **+ skapa en resurs** > **Compute** > **Ubuntu Server 18.04 LTS**.

    ![Skapa en Ubuntu-dator](./media/connect-on-premises-network/create-ubuntu-vm.png)

3. Från den __grunderna__ ange följande information:  
  
    | Fält | Värde |
    | --- | --- |
    |Prenumeration |Välj din rätt prenumeration.|
    |Resursgrupp |Välj den resursgrupp som innehåller det virtuella nätverket som skapades tidigare.|
    |Namn på virtuell dator | Ange ett eget namn som identifierar den här virtuella datorn. Det här exemplet används **DNSProxy**.|
    |Region | Välj samma region som det virtuella nätverket som skapades tidigare.  Inte alla VM-storlekar är tillgängliga i alla regioner.  |
    |Alternativ för tillgänglighet |  Välj din önskade nivå för tillgänglighet.  Azure erbjuder en mängd alternativ för att hantera tillgänglighet och återhämtningskapacitet för dina program.  Skapa din lösning om du vill använda replikerade virtuella datorerna i Tillgänglighetszoner eller Tillgänglighetsuppsättningar för att skydda dina appar och data från avbrott i datacentret och underhåll. Det här exemplet används **ingen redundans för infrastruktur som krävs för**. |
    |Image | Lämna på **Ubuntu Server 18.04 LTS**. |
    |Autentiseringstyp | __Lösenordet__ eller __offentlig SSH-nyckel__: Autentiseringsmetoden för SSH-kontot. Vi rekommenderar att du använder offentliga nycklar som de är säkrare. Det här exemplet används **lösenord**.  Mer information finns i den [skapa och använda SSH-nycklar för Linux-datorer](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentet.|
    |Användarnamn |Ange administratörens användarnamn för den virtuella datorn.  Det här exemplet används **sshuser**.|
    |Lösenordet eller SSH offentlig nyckel | Fältet bestäms av valet du gjorde för **autentiseringstyp**.  Ange lämpligt värde.|
    |Offentliga inkommande portar|Välj **Tillåt valda portar**. Välj sedan **SSH (22)** från den **Välj ingående portar** listrutan.|

    ![Grundläggande konfiguration av virtuell dator](./media/connect-on-premises-network/vm-basics.png)

    Andra poster lämnas kvar standardvärdena och välj sedan den **nätverk** fliken.

4. Från den **nätverk** ange följande information:

    | Fält | Värde |
    | --- | --- |
    |Virtuellt nätverk | Välj det virtuella nätverket som du skapade tidigare.|
    |Undernät | Välj standardundernät för det virtuella nätverket som du skapade tidigare. Gör __inte__ välja det undernät som används av VPN-gatewayen.|
    |Offentlig IP-adress | Använd autopopulated-värdet.  |

    ![Inställningar för virtuella nätverk](./media/connect-on-premises-network/virtual-network-settings.png)

    Andra poster lämnas kvar standardvärdena och välj sedan den **granska + skapa**.

5. Från den **granska + skapa** fliken **skapa** att skapa den virtuella datorn.

### <a name="review-ip-addresses"></a>Granska IP-adresser
När du har skapat den virtuella datorn får du en **distributionen lyckades** -meddelande med en **gå till resurs** knappen.  Välj **gå till resurs** att gå till din nya virtuella dator.  Följ stegen nedan för att identifiera associerade IP-adresser från standardvyn för din nya virtuella dator:

1. Från **inställningar**väljer **egenskaper**.

2. Notera värdena för **offentliga IP-adress/DNS-NAMNETIKETTEN** och **privata IP-adress** för senare användning.

   ![Offentliga och privata IP-adresser](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Installera och konfigurera bindning (DNS-programvara)

1. Använda SSH för att ansluta till den __offentliga IP-adressen__ för den virtuella datorn. Ersätt `sshuser` med SSH-användarkonto som du angav när du skapar den virtuella datorn. I följande exempel ansluter till en virtuell dator på 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Använd följande kommandon från SSH-sessionen om du vill installera bindning:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Om du vill konfigurera bindning att vidarebefordra begäran till din på lokal DNS-server namnmatchning, Använd följande text som innehållet i den `/etc/bind/named.conf.options` fil:

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
    > Ersätt värdena i den `goodclients` avsnitt med IP-adressintervallet för det virtuella nätverket och lokala nätverk. Det här avsnittet definierar de adresser som den här DNS-servern tar emot förfrågningar från.
    >
    > Ersätt den `192.168.0.1` post i den `forwarders` avsnittet med IP-adressen för den lokala DNS-servern. Den här posten dirigerar DNS-förfrågningar till den lokala DNS-servern för matchning.

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

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    Den `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` texten är den __DNS-suffix__ för den här virtuella nätverket. Du bör spara det här värdet eftersom det används senare.

5. Om du vill konfigurera bindning för DNS-namnmatchning för resurser i det virtuella nätverket, Använd följande text som innehållet i den `/etc/bind/named.conf.local` fil:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > Du måste ersätta det `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` med DNS-suffix som du hämtade tidigare.

    Om du vill redigera den här filen använder du följande kommando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Om du vill spara filen, Använd __Ctrl + X__, __Y__, och sedan __RETUR__.

6. Om du vill starta bindning, använder du följande kommando:

    ```bash
    sudo service bind9 restart
    ```

7. Om du vill kontrollera den bindning kan matcha namnen på resurser i ditt lokala nätverk, använder du följande kommandon:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Ersätt `dns.mynetwork.net` med det fullständigt kvalificerade domännamnet (FQDN) för en resurs i ditt lokala nätverk.
    >
    > Ersätt `10.0.0.4` med den __interna IP-adressen__ för din anpassade DNS-server i virtuella nätverk.

    Svaret ser ut som följande text:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Konfigurera ett virtuellt nätverk om du vill använda den anpassa DNS-servern

Om du vill konfigurera det virtuella nätverket om du vill använda anpassade DNS-servern i stället för Azures rekursiva matchare, använder du följande steg från den [Azure-portalen](https://portal.azure.com):

1. I den vänstra menyn navigerar du till **alla tjänster** > **nätverk** > **virtuella nätverk**.

2. Välj ditt virtuella nätverk i listan, vilket öppnar standardvyn för det virtuella nätverket.  

3. Från standardvyn under **inställningar**väljer **DNS-servrar**.  

4. Välj __anpassade__, och ange den **privata IP-adress** för anpassad DNS-servern.   

5. Välj __Spara__.  <br />  

    ![Ange anpassade DNS-servern för det virtuella nätverket](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Konfigurera en lokal DNS-server

I det föregående avsnittet konfigurerat du den anpassa DNS-servern så att den vidarebefordrar begäranden till den lokala DNS-servern. Du måste konfigurera den lokala DNS-servern så att den vidarebefordrar begäranden till den anpassa DNS-servern.

Specifika anvisningar för hur du konfigurerar din DNS-server finns i dokumentationen för din DNS-server-programvara. Se anvisningar för hur du konfigurerar en __villkorlig vidarebefordrare__.

En villkorlig vanlig enbart vidarebefordrar begäranden för ett anslutningsspecifikt DNS-suffix. I så fall måste du konfigurera en vidarebefordrare för DNS-suffixet för det virtuella nätverket. Begäranden för det här suffixet ska vidarebefordras till IP-adressen för den anpassa DNS-servern. 

Följande text är ett exempel på en villkorlig forwarder-konfiguration för den **binda** DNS programvara:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Information om hur du använder DNS på **Windows Server 2016**, finns i den [Lägg till DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentation...

När du har konfigurerat en lokal DNS-server kan du använda `nslookup` från den lokala nätverket för att kontrollera att du kan matcha namnen i det virtuella nätverket. I följande exempel 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Det här exemplet använder den lokala DNS-servern på 196.168.0.4 för att matcha namnet på den anpassa DNS-servern. Ersätt IP-adressen med den lokala DNS-servern. Ersätt den `dnsproxy` adress med det fullständigt kvalificerade domännamnet till den anpassade DNS-servern.

## <a name="optional-control-network-traffic"></a>Valfritt: Kontrollen nätverkstrafik

Du kan använda nätverkssäkerhetsgrupper (NSG) eller användardefinierade vägar (UDR) för att kontrollera nätverkstrafik. NSG: er kan du filtrera inkommande och utgående trafik och tillåter eller nekar trafik. Udr: er kan du styra hur trafiken flödar mellan resurser i det virtuella nätverket, internet och det lokala nätverket.

> [!WARNING]  
> HDInsight kräver inkommande åtkomst från specifika IP-adresser i Azure-molnet och obegränsad utgående åtkomst. När du använder NSG: er eller udr: er för att styra trafik, måste du utföra följande steg:

1. Hitta IP-adresser för den plats som innehåller det virtuella nätverket. En lista över nödvändiga IP-adresser efter plats finns i [nödvändiga IP-adresser](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. För IP-adresser som identifierades i steg 1, tillåter inkommande trafik från den IP-adresser.

   * Om du använder __NSG__: Tillåt __inkommande__ trafik på port __443__ för IP-adresser.
   * Om du använder __UDR__: Ange den __nästa hopp__ typ för vägen som __Internet__ för IP-adresser.

Ett exempel på hur du använder Azure PowerShell eller Azure CLI för att skapa NSG: er finns i den [utöka HDInsight med Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) dokumentet.

## <a name="create-the-hdinsight-cluster"></a>Skapa HDInsight-kluster

> [!WARNING]  
> Innan du installerar HDInsight i det virtuella nätverket måste du konfigurera den anpassa DNS-servern.

Följ stegen i den [skapar ett HDInsight-kluster med Azure portal](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentet för att skapa ett HDInsight-kluster.

> [!WARNING]  
> * När du skapar klustret måste du välja den plats som innehåller det virtuella nätverket.
> * I den __avancerade inställningar__ en del av konfigurationen, du måste välja det virtuella nätverk och undernät som du skapade tidigare.

## <a name="connecting-to-hdinsight"></a>Ansluta till HDInsight

De flesta dokumentation om HDInsight förutsätter att du har åtkomst till klustret via internet. Till exempel att du kan ansluta till klustret i `https://CLUSTERNAME.azurehdinsight.net`. Den här adressen använder den offentliga gatewayen, som inte är tillgänglig om du har använt NSG: er eller udr: er för att begränsa åtkomst från internet.

Viss dokumentation även hänvisar till `headnodehost` när du ansluter till klustret från en SSH-session. Den här adressen är endast tillgänglig från noder i ett kluster och kan inte användas på klienter som ansluter via det virtuella nätverket.

Om du vill ansluta direkt till HDInsight via det virtuella nätverket, Använd följande steg:

1. Använd någon av följande metoder för att identifiera interna fullständigt kvalificerade domännamnen för noderna i HDInsight:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

2. Information om den port som en tjänst är tillgänglig på finns i [portar som används av Apache Hadoop-tjänster på HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentet.

    > [!IMPORTANT]  
    > Vissa tjänster som finns på huvudnoderna är bara aktiva på en nod i taget. Om du försöker få åtkomst till en tjänst på en huvudnod och misslyckas, växla till andra huvudnoden.
    >
    > Till exempel är Apache Ambari endast aktiv på en huvudnod i taget. Om du försöker komma åt Ambari på en huvudnod och den returnerar ett 404-fel, körs det på andra huvudnoden.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder HDInsight i ett virtuellt nätverk finns i [utöka HDInsight med hjälp av Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md).

* Mer information om virtuella Azure-nätverk finns i den [översikt över Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Mer information om nätverkssäkerhetsgrupper finns i [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

* Mer information om användardefinierade vägar finns i [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md).
