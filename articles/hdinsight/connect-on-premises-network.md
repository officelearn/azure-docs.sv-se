---
title: Ansluta Azure HDInsight till ditt lokala nätverk
description: Lär dig hur du skapar ett HDInsight-kluster i ett Virtuellt Azure-nätverk och ansluter det sedan till ditt lokala nätverk. Lär dig hur du konfigurerar namnmatchning mellan HDInsight och ditt lokala nätverk med hjälp av en anpassad DNS-server.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 2ed7a5b9c81d1b50f80f379a88688b69c49ed382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897923"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Ansluta HDInsight till det lokala nätverket

Lär dig hur du ansluter HDInsight till ditt lokala nätverk med hjälp av Azure Virtual Networks och en VPN-gateway. Det här dokumentet innehåller planeringsinformation om:

* Använda HDInsight i ett virtuellt Azure-nätverk som ansluter till ditt lokala nätverk.
* Konfigurera DNS-namnmatchning mellan det virtuella nätverket och det lokala nätverket.
* Konfigurera nätverkssäkerhetsgrupper för att begränsa internetåtkomsten till HDInsight.
* Portar som tillhandahålls av HDInsight i det virtuella nätverket.

## <a name="overview"></a>Översikt

Om du vill att HDInsight och resurser i det anslutna nätverket ska kunna kommunicera med namn måste du utföra följande åtgärder:

1. Skapa Virtuella Azure-nätverk.
1. Skapa en anpassad DNS-server i Azure Virtual Network.
1. Konfigurera det virtuella nätverket så att den anpassade DNS-servern används i stället för standardutlösaren för Azure Recursive.
1. Konfigurera vidarebefordran mellan den anpassade DNS-servern och den lokala DNS-servern.

Dessa konfigurationer möjliggör följande beteende:

* Begäranden om fullständigt kvalificerade domännamn som har DNS-suffixet __för det virtuella nätverket__ vidarebefordras till den anpassade DNS-servern. Den anpassade DNS-servern vidarebefordrar sedan dessa begäranden till Azure Rekursiv Resolver, som returnerar IP-adressen.
* Alla andra begäranden vidarebefordras till den lokala DNS-servern. Även begäranden om offentliga internetresurser som microsoft.com vidarebefordras till den lokala DNS-servern för namnmatchning.

I följande diagram är gröna linjer begäranden om resurser som slutar i DNS-suffixet i det virtuella nätverket. Blå linjer är begäranden om resurser i det lokala nätverket eller på det offentliga internet.

![Diagram över hur DNS-begäranden matchas i konfigurationen](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Krav

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Om du använder PowerShell behöver du [AZ-modulen](https://docs.microsoft.com/powershell/azure/overview).
* Om du vill använda Azure CLI och ännu inte har installerat det läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Skapa konfiguration av virtuellt nätverk

Använd följande dokument för att lära dig hur du skapar ett virtuellt Azure-nätverk som är anslutet till ditt lokala nätverk:

* [Använda Azure-portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Använda Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Använda Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Skapa anpassad DNS-server

> [!IMPORTANT]  
> Du måste skapa och konfigurera DNS-servern innan du installerar HDInsight i det virtuella nätverket.

I de här stegen används [Azure-portalen](https://portal.azure.com) för att skapa en virtuell Azure-dator. Andra sätt att skapa en virtuell dator finns i [Skapa virtuell dator - Azure CLI](../virtual-machines/linux/quick-create-cli.md) och Skapa virtuell dator - Azure [PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Så här skapar du en virtuell Linux-dator som använder [Bind](https://www.isc.org/downloads/bind/) DNS-programvaran:

1. Logga in på [Azure-portalen](https://portal.azure.com).
  
1. Välj + Skapa **en resurs**på den övre menyn .

    ![Skapa en virtuell Ubuntu-dator](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Välj **Beräkna** > **virtuell dator** för att gå till sidan Skapa en virtuell **dator.**

1. På fliken __Grunderna__ anger du följande information:  
  
    | Field | Värde |
    | --- | --- |
    |Prenumeration |Välj lämplig prenumeration.|
    |Resursgrupp |Markera den resursgrupp som innehåller det virtuella nätverk som skapats tidigare.|
    |Namn på virtuell dator | Ange ett eget namn som identifierar den här virtuella datorn. I det här exemplet används **DNSProxy**.|
    |Region | Välj samma region som det virtuella nätverk som skapades tidigare.  Alla vm-storlekar är inte tillgängliga i alla regioner.  |
    |Alternativ för tillgänglighet |  Välj önskad tillgänglighetsnivå.  Azure erbjuder en rad alternativ för att hantera tillgänglighet och återhämtning för dina program.  Utforma din lösning så att den använder replikerade virtuella datorer i tillgänglighetszoner eller tillgänglighetsuppsättningar för att skydda dina appar och data från datacenteravbrott och underhållshändelser. I det här exemplet används **ingen infrastrukturredundans som krävs**. |
    |Bild | Lämna på **Ubuntu Server 18.04 LTS**. |
    |Autentiseringstyp | __Lösenord__ eller __SSH-offentlig nyckel:__ Autentiseringsmetoden för SSH-kontot. Vi rekommenderar att du använder offentliga nycklar eftersom de är säkrare. I det här exemplet används **lösenord**.  Mer information finns i dokumentet [Skapa och använda SSH-nycklar för virtuella Linux-datorer.](../virtual-machines/linux/mac-create-ssh-keys.md)|
    |Användarnamn |Ange administratörsanvändarnamnet för den virtuella datorn.  I det här exemplet används **sshuser**.|
    |Lösenord eller SSH-nyckel | Det tillgängliga fältet bestäms av ditt val för **autentiseringstyp**.  Ange lämpligt värde.|
    |Offentliga inkommande portar|Välj **Tillåt valda portar**. Välj sedan **SSH (22)** i listrutan **Välj inkommande portar.**|

    ![Grundläggande konfiguration för virtuell dator](./media/connect-on-premises-network/virtual-machine-basics.png)

    Lämna andra poster till standardvärdena och välj sedan fliken **Nätverk.**

4. På fliken **Nätverk** anger du följande information:

    | Field | Värde |
    | --- | --- |
    |Virtuellt nätverk | Välj det virtuella nätverk som du skapade tidigare.|
    |Undernät | Välj standardundernätet för det virtuella nätverk som du skapade tidigare. Välj __inte__ det undernät som används av VPN-gatewayen.|
    |Offentlig IP-adress | Använd det automatiskt befolkade värdet.  |

    ![Inställningar för virtuella HDInsight-nätverk](./media/connect-on-premises-network/virtual-network-settings.png)

    Lämna andra poster till standardvärdena och välj sedan **granska + skapa**.

5. På fliken **Granska + skapa** väljer du **Skapa** för att skapa den virtuella datorn.

### <a name="review-ip-addresses"></a>Granska IP-adresser

När den virtuella datorn har skapats får du ett **meddelande om distribution** med knappen Gå till **resurs.**  Välj **Gå till resursen** för att gå till den nya virtuella datorn.  Gör så här för att identifiera associerade IP-adresser från standardvyn för den nya virtuella datorn:

1. Välj **Egenskaper** **i Inställningar**.

2. Observera värdena för **PUBLIC IP-ADRESS/DNS NAME LABEL** och PRIVATE IP **ADDRESS** för senare användning.

   ![Offentliga och privata IP-adresser](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Installera och konfigurera Bind (DNS-programvara)

1. Använd SSH för att ansluta till den __offentliga IP-adressen__ för den virtuella datorn. Ersätt `sshuser` med det SSH-användarkonto som du angav när du skapade den virtuella datorn. I följande exempel ansluters till en virtuell dator på 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Om du vill installera Bind använder du följande kommandon från SSH-sessionen:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Om du vill konfigurera Bind för att vidarebefordra namnmatchningsbegäranden till `/etc/bind/named.conf.options` din lokala DNS-server använder du följande text som innehållet i filen:

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
    > Ersätt värdena `goodclients` i avsnittet med IP-adressintervallet för det virtuella nätverket och det lokala nätverket. Det här avsnittet definierar de adresser som den här DNS-servern accepterar begäranden från.
    >
    > Ersätt `192.168.0.1` posten i `forwarders` avsnittet med IP-adressen för den lokala DNS-servern. Den här posten dirigerar DNS-begäranden till din lokala DNS-server för lösning.

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

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    Texten `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` är __DNS-suffixet__ för det här virtuella nätverket. Spara det här värdet, som det används senare.

5. Om du vill konfigurera Bind för att matcha DNS-namn för resurser `/etc/bind/named.conf.local` i det virtuella nätverket använder du följande text som innehållet i filen:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > Du måste `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` ersätta det MED DET DNS-suffix som du hämtade tidigare.

    Så här redigerar du filen:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Om du vill spara filen använder du __Ctrl+X__, __Y__och sedan __Enter__.

6. Starta Bindning genom att använda följande kommando:

    ```bash
    sudo service bind9 restart
    ```

7. Om du vill kontrollera att bindningen kan matcha namnen på resurserna i det lokala nätverket använder du följande kommandon:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Ersätt `dns.mynetwork.net` med det fullständigt kvalificerade domännamnet (FQDN) för en resurs i det lokala nätverket.
    >
    > Ersätt `10.0.0.4` med den __interna IP-adressen__ för den anpassade DNS-servern i det virtuella nätverket.

    Svaret liknar följande text:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Konfigurera virtuellt nätverk så att den anpassade DNS-servern används

Om du vill konfigurera det virtuella nätverket så att den anpassade DNS-servern används i stället för Azure rekursiv resolver använder du följande steg från [Azure-portalen:](https://portal.azure.com)

1. Från den vänstra menyn navigerar du till **Alla tjänster** > **Nätverk virtuella** > **nätverk**.

2. Välj ditt virtuella nätverk i listan, som öppnar standardvyn för det virtuella nätverket.  

3. Välj **DNS-servrar**under **Inställningar**i standardvyn .  

4. Välj __Anpassad__och ange den **privata IP-adressen** för den anpassade DNS-servern.

5. Välj __Spara__.  <br />  

    ![Ange den anpassade DNS-servern för nätverket](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Konfigurera lokal DNS-server

I föregående avsnitt konfigurerade du den anpassade DNS-servern för att vidarebefordra begäranden till den lokala DNS-servern. Därefter måste du konfigurera den lokala DNS-servern för att vidarebefordra begäranden till den anpassade DNS-servern.

Mer information om hur du konfigurerar DNS-servern finns i dokumentationen för DNS-serverprogramvaran. Leta efter stegen för hur du konfigurerar en __villkorlig vidarebefordrare__.

En villkorlig vidarebefordran vidarebefordrar endast begäranden för ett visst DNS-suffix. I det här fallet måste du konfigurera en vidarebefordrare för DNS-suffixet i det virtuella nätverket. Begäranden om det här suffixet ska vidarebefordras till IP-adressen för den anpassade DNS-servern. 

Följande text är ett exempel på en villkorlig vidarebefordrare konfiguration för **Bind** DNS-programvaran:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Information om hur du använder DNS på **Windows Server 2016**finns i dokumentationen [till Add-DnsServerConditionalForwarderZone...](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone)

När du har konfigurerat den lokala DNS-servern `nslookup` kan du använda från det lokala nätverket för att kontrollera att du kan matcha namn i det virtuella nätverket. Följande exempel 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

I det här exemplet används den lokala DNS-servern på 196.168.0.4 för att matcha namnet på den anpassade DNS-servern. Ersätt IP-adressen med den för den lokala DNS-servern. Ersätt `dnsproxy` adressen med det fullständigt kvalificerade domännamnet för den anpassade DNS-servern.

## <a name="optional-control-network-traffic"></a>Valfritt: Kontrollera nätverkstrafiken

Du kan använda nätverkssäkerhetsgrupper (NSG) eller användardefinierade vägar (UDR) för att styra nätverkstrafiken. Med NSG:er kan du filtrera inkommande och utgående trafik och tillåta eller neka trafik. Med UDR:er kan du styra hur trafiken flödar mellan resurser i det virtuella nätverket, internet och det lokala nätverket.

> [!WARNING]  
> HDInsight kräver inkommande åtkomst från specifika IP-adresser i Azure-molnet och obegränsad utgående åtkomst. När du använder NSGs eller UDRs för att styra trafiken måste du utföra följande steg:

1. Leta reda på IP-adresserna för den plats som innehåller ditt virtuella nätverk. En lista över obligatoriska IP-adresser efter plats finns [i Obligatoriska IP-adresser](./hdinsight-management-ip-addresses.md).

2. För IP-adresser som identifieras i steg 1, tillåt inkommande trafik från ip-adresser.

   * Om du använder __NSG:__ Tillåt __inkommande__ trafik på port __443__ för IP-adresser.
   * Om du använder __UDR:__ Ange __nästa hopptyp__ för vägen till __Internet__ för IP-adresserna.

Ett exempel på hur du använder Azure PowerShell eller Azure CLI för att skapa NSG-grupper finns i dokumentet [Utöka HDInsight med Azure Virtual Networks.](hdinsight-create-virtual-network.md#hdinsight-nsg)

## <a name="create-the-hdinsight-cluster"></a>Skapa HDInsight-klustret

> [!WARNING]  
> Du måste konfigurera den anpassade DNS-servern innan du installerar HDInsight i det virtuella nätverket.

Använd stegen i [Skapa ett HDInsight-kluster med hjälp av Azure-portaldokumentet](./hdinsight-hadoop-create-linux-clusters-portal.md) för att skapa ett HDInsight-kluster.

> [!WARNING]  
> * När klustret skapas måste du välja den plats som innehåller det virtuella nätverket.
> * I den __avancerade inställningsdelen__ av konfigurationen måste du välja det virtuella nätverk och det undernät som du skapade tidigare.

## <a name="connecting-to-hdinsight"></a>Ansluta till HDInsight

De flesta dokumentation på HDInsight förutsätter att du har tillgång till klustret via Internet. Till exempel att du kan ansluta till klustret i `https://CLUSTERNAME.azurehdinsight.net`. Den här adressen använder den offentliga gatewayen, som inte är tillgänglig om du har använt NSGs eller UDRs för att begränsa åtkomsten från Internet.

Viss dokumentation refererar också till när du ansluter till klustret `headnodehost` från en SSH-session. Den här adressen är endast tillgänglig från noder i ett kluster och kan inte kan anslutas på klienter som är anslutna via det virtuella nätverket.

Så här ansluter du direkt till HDInsight via det virtuella nätverket:

1. Om du vill identifiera de interna fullständigt kvalificerade domännamnen för HDInsight-klusternoderna använder du någon av följande metoder:

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

2. Information om vilken port en tjänst är tillgänglig på finns i [portarna som används av Apache Hadoop-tjänster i HDInsight-dokumentet.](./hdinsight-hadoop-port-settings-for-services.md)

    > [!IMPORTANT]  
    > Vissa tjänster som finns på huvudnoderna är bara aktiva på en nod i taget. Om du försöker komma åt en tjänst på en huvudnod och den misslyckas växlar du till den andra huvudnoden.
    >
    > Apache Ambari är till exempel bara aktiv på en huvudnod åt gången. Om du försöker komma åt Ambari på en huvudnod och den returnerar ett 404-fel körs den på den andra huvudnoden.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder HDInsight i ett virtuellt nätverk finns i [Planera en virtuell nätverksdistribution för Azure HDInsight-kluster](./hdinsight-plan-virtual-network-deployment.md).

* Mer information om virtuella Azure-nätverk finns i [översikten över Virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md).

* Mer information om nätverkssäkerhetsgrupper finns i [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

* Mer information om användardefinierade vägar finns i [Användardefinierade vägar och IP-vidarebefordran](../virtual-network/virtual-networks-udr-overview.md).
