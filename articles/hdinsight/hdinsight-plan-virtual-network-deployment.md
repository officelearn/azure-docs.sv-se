---
title: Planera ett virtuellt nätverk för Azure HDInsight
description: Lär dig hur du planerar en Azure Virtual Network-distribution för att ansluta HDInsight till andra molnresurser eller resurser i ditt datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/21/2020
ms.openlocfilehash: 18774ae4a98b795846459251174ee47671aef39c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769891"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Planera ett virtuellt nätverk för Azure HDInsight

Den här artikeln innehåller bakgrundsinformation om hur du använder [Virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md) (VNets) med Azure HDInsight. Den diskuterar också design och implementering beslut som måste göras innan du kan implementera ett virtuellt nätverk för din HDInsight kluster. När planeringsfasen är klar kan du fortsätta att [skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md). Mer information om IP-adresser för HDInsight-hantering som behövs för att korrekt konfigurera NSG-grupper (Network Security Groups) och användardefinierade vägar finns i [IP-adresser för HDInsight-hantering](hdinsight-management-ip-addresses.md).

Med hjälp av ett virtuellt Azure-nätverk kan följande scenarier:

* Ansluta till HDInsight direkt från ett lokalt nätverk.
* Ansluta HDInsight till datalager i ett Virtuellt Azure-nätverk.
* Direkt åtkomst till Apache Hadoop-tjänster som inte är tillgängliga offentligt via Internet. Apache Kafka API:er eller Apache HBase Java API.

> [!IMPORTANT]
> Om du skapar ett HDInsight-kluster i ett VNET skapas flera nätverksresurser, till exempel nätverkskort och belastningsutjämnare. Ta **inte** bort dessa nätverksresurser eftersom de behövs för att klustret ska fungera korrekt med det virtuella nätverket.
>
> Efter den 28 februari 2019 etableras nätverksresurserna (till exempel nätverkskort, LBs osv.) för NYA HDInsight-kluster som skapats i ett VNET i samma HDInsight-klusterresursgrupp. Tidigare har dessa resurser etablerats i VNET-resursgruppen. Det finns ingen ändring av de kluster som körs och de kluster som skapats utan ett VNET.

## <a name="planning"></a>Planering

Följande är de frågor som du måste besvara när du planerar att installera HDInsight i ett virtuellt nätverk:

* Behöver du installera HDInsight i ett befintligt virtuellt nätverk? Eller skapar du ett nytt nätverk?

    Om du använder ett befintligt virtuellt nätverk kan du behöva ändra nätverkskonfigurationen innan du kan installera HDInsight. Mer information finns i [lägg till HDInsight i ett befintligt virtuellt nätverksavsnitt.](#existingvnet)

* Vill du ansluta det virtuella nätverket som innehåller HDInsight till ett annat virtuellt nätverk eller ditt lokala nätverk?

    Om du enkelt vill arbeta med resurser i olika nätverk kan du behöva skapa en anpassad DNS och konfigurera DNS-vidarebefordran. Mer information finns i [avsnittet ansluta flera nätverk.](#multinet)

* Vill du begränsa/omdirigera inkommande eller utgående trafik till HDInsight?

    HDInsight måste ha obegränsad kommunikation med specifika IP-adresser i Azure-datacentret. Det finns också flera portar som måste tillåtas via brandväggar för klientkommunikation. Mer information finns i avsnittet [kontrollera nätverkstrafik.](#networktraffic)

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>Lägga till HDInsight i ett befintligt virtuellt nätverk

Följ stegen i det här avsnittet för att ta reda på hur du lägger till en ny HDInsight i ett befintligt Virtuellt Azure-nätverk.

> [!NOTE]  
> Du kan inte lägga till ett befintligt HDInsight-kluster i ett virtuellt nätverk.

1. Använder du en klassisk distributionsmodell eller Resurshanterarens distributionsmodell för det virtuella nätverket?

    HDInsight 3.4 och senare kräver ett virtuellt resurshanterarenätverk. Tidigare versioner av HDInsight krävde ett klassiskt virtuellt nätverk.

    Om ditt befintliga nätverk är ett klassiskt virtuellt nätverk måste du skapa ett virtuellt resurshanterarenätverk och sedan ansluta de två. [Ansluta klassiska virtuella nätverk till nya virtuella nätverk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    När HDInsight installeras i Resource Manager-nätverket kan de interagera med resurser i det klassiska nätverket.

2. Använder du nätverkssäkerhetsgrupper, användardefinierade vägar eller virtuella nätverksenheter för att begränsa trafiken till eller utanför det virtuella nätverket?

    Som en hanterad tjänst kräver HDInsight obegränsad åtkomst till flera IP-adresser i Azure-datacentret. Om du vill tillåta kommunikation med dessa IP-adresser uppdaterar du alla befintliga nätverkssäkerhetsgrupper eller användardefinierade vägar.

    HDInsight är värd för flera tjänster som använder en mängd olika portar. Blockera inte trafik till dessa portar. En lista över portar som ska tillåtas via brandväggar för virtuella enheter finns i avsnittet Säkerhet.

    Om du vill hitta din befintliga säkerhetskonfiguration använder du följande Azure PowerShell- eller Azure CLI-kommandon:

    * Nätverkssäkerhetsgrupper

        Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller det virtuella nätverket och ange sedan kommandot:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Mer information finns i dokumentet [Felsöka nätverkssäkerhetsgrupper.](../virtual-network/diagnose-network-traffic-filter-problem.md)

        > [!IMPORTANT]  
        > Regler för nätverkssäkerhetsgrupper tillämpas i ordning baserat på regelprioritet. Den första regeln som matchar trafikmönstret tillämpas och inga andra tillämpas för den trafiken. Beställ regler från de flesta tillåtande till minst tillåtande. Mer information finns i [dokumentet Filternätverkstrafik med dokument för nätverkssäkerhetsgrupper.](../virtual-network/security-overview.md)

    * Användardefinierade vägar

        Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller det virtuella nätverket och ange sedan kommandot:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Mer information finns i [felsökningsvägsdokumentet.](../virtual-network/diagnose-network-routing-problem.md)

3. Skapa ett HDInsight-kluster och välj Azure Virtual Network under konfigurationen. Använd stegen i följande dokument för att förstå processen för att skapa kluster:

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) (Skapa HDInsight med hjälp av Azure-portalen)
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Skapa HDInsight med hjälp av Azure PowerShell)
    * [Skapa HDInsight med Azure Classic CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Skapa HDInsight med hjälp av en Azure Resource Manager-mall](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Att lägga till HDInsight i ett virtuellt nätverk är ett valfritt konfigurationssteg. Var noga med att välja det virtuella nätverket när du konfigurerar klustret.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Ansluta flera nätverk

Den största utmaningen med en konfiguration med flera nätverk är namnmatchning mellan nätverken.

Azure tillhandahåller namnmatchning för Azure-tjänster som är installerade i ett virtuellt nätverk. Med den här inbyggda namnmatchningen kan HDInsight ansluta till följande resurser med hjälp av ett fullständigt kvalificerat domännamn (FQDN):

* Alla resurser som är tillgängliga på Internet. Till exempel microsoft.com, windowsupdate.com.

* Alla resurser som finns i samma Virtuella Azure-nätverk med hjälp av det __interna DNS-namnet__ på resursen. När du till exempel använder standardnamnsmatchningen är följande exempel på interna DNS-namn som tilldelats HDInsight-arbetsnoder:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Båda dessa noder kan kommunicera direkt med varandra och andra noder i HDInsight med hjälp av interna DNS-namn.

Standardnamnsmatchningen tillåter __inte__ att HDInsight löser namnen på resurser i nätverk som är anslutna till det virtuella nätverket. Det är till exempel vanligt att ansluta till det lokala nätverket till det virtuella nätverket. Med endast standardnamnsmatchning kan HDInsight inte komma åt resurser i det lokala nätverket efter namn. Det motsatta är också sant, resurser i det lokala nätverket kan inte komma åt resurser i det virtuella nätverket efter namn.

> [!WARNING]  
> Du måste skapa den anpassade DNS-servern och konfigurera det virtuella nätverket så att det använder den innan du skapar HDInsight-klustret.

Om du vill aktivera namnmatchning mellan det virtuella nätverket och resurser i anslutna nätverk måste du utföra följande åtgärder:

1. Skapa en anpassad DNS-server i Det virtuella Azure-nätverket där du planerar att installera HDInsight.

2. Konfigurera det virtuella nätverket så att det anpassade DNS-servern används.

3. Hitta det Azure-tilldelade DNS-suffixet för ditt virtuella nätverk. Det här värdet `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`liknar . Information om hur du hittar DNS-suffixet finns i avsnittet [Exempel: Anpassad DNS.](hdinsight-create-virtual-network.md#example-dns)

4. Konfigurera vidarebefordran mellan DNS-servrarna. Konfigurationen beror på vilken typ av fjärrnätverk.

   * Om fjärrnätverket är ett lokalt nätverk konfigurerar du DNS på följande sätt:

     * __Anpassad DNS__ (i det virtuella nätverket):

         * Vidarebefordra begäranden för DNS-suffixet i det virtuella nätverket till Azure rekursiva resolver (168.63.129.16). Azure hanterar begäranden om resurser i det virtuella nätverket

         * Vidarebefordra alla andra begäranden till den lokala DNS-servern. Den lokala DNS hanterar alla andra namnmatchningsbegäranden, även begäranden om internetresurser som Microsoft.com.

     * __Lokal DNS:__ Vidarebefordra begäranden för dns-suffixet för det virtuella nätverket till den anpassade DNS-servern. Den anpassade DNS-servern vidarebefordrar sedan till Azure rekursiv resolver.

       Den här konfigurationen dirigerar begäranden om fullständigt kvalificerade domännamn som innehåller DNS-suffixet för det virtuella nätverket till den anpassade DNS-servern. Alla andra begäranden (även för offentliga internetadresser) hanteras av den lokala DNS-servern.

   * Om fjärrnätverket är ett annat Virtuellt Azure-nätverk konfigurerar du DNS på följande sätt:

     * __Anpassad DNS__ (i varje virtuellt nätverk):

         * Begäranden om DNS-suffixet för de virtuella nätverken vidarebefordras till anpassade DNS-servrar. DNS i varje virtuellt nätverk ansvarar för att lösa resurser i nätverket.

         * Vidarebefordra alla andra begäranden till Azure rekursiva resolver. Den rekursiva resolver ansvarar för att lösa lokala resurser och internetresurser.

       DNS-servern för varje nätverk vidarebefordrar begäranden till den andra, baserat på DNS-suffixet. Andra begäranden löses med azure rekursiv resolver.

     Ett exempel på varje konfiguration finns i avsnittet [Exempel: Anpassad DNS.](hdinsight-create-virtual-network.md#example-dns)

Mer information finns i dokumentet [Namnmatchning för virtuella datorer och rollinstanser.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="directly-connect-to-apache-hadoop-services"></a>Direkt ansluta till Apache Hadoop-tjänster

Du kan ansluta till `https://CLUSTERNAME.azurehdinsight.net`klustret på . Den här adressen använder en offentlig IP, som kanske inte kan nås om du har använt NSG för att begränsa inkommande trafik från Internet. När du distribuerar klustret i ett virtuella nätverk kan `https://CLUSTERNAME-int.azurehdinsight.net`du dessutom komma åt det med den privata slutpunkten . Den här slutpunkten matchas till en privat IP i det virtuella nätverket för klusteråtkomst.

Så här ansluter du till Apache Ambari och andra webbsidor via det virtuella nätverket:

1. Om du vill identifiera de interna fullständigt kvalificerade domännamnen (FQDN) för HDInsight-klusternoderna använder du någon av följande metoder:

    Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller det virtuella nätverket och ange sedan kommandot:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Leta reda på FQDN för huvudnoderna i listan över noder som returneras och använd FQDN för att ansluta till Ambari och andra webbtjänster. Använd `http://<headnode-fqdn>:8080` till exempel för att komma åt Ambari.

    > [!IMPORTANT]  
    > Vissa tjänster som finns på huvudnoderna är bara aktiva på en nod i taget. Om du försöker komma åt en tjänst på en huvudnod och returnerar ett 404-fel växlar du till den andra huvudnoden.

2. Information om vilken nod och port som en tjänst är tillgänglig på finns i [Portarna som används av Hadoop-tjänster i HDInsight-dokumentet.](./hdinsight-hadoop-port-settings-for-services.md)

## <a name="controlling-network-traffic"></a><a id="networktraffic"></a>Styra nätverkstrafiken

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Tekniker för att kontrollera inkommande och utgående trafik till HDInsight-kluster

Nätverkstrafik i ett Virtuellt Azure-nätverk kan styras med följande metoder:

* **Med NSG (Network Security Groups)** kan du filtrera inkommande och utgående trafik till nätverket. Mer information finns i [dokumentet Filternätverkstrafik med dokument för nätverkssäkerhetsgrupper.](../virtual-network/security-overview.md)

* **Virtuella nätverksenheter** (NVA) kan endast användas med utgående trafik. NVA:er replikerar funktionaliteten hos enheter som brandväggar och routrar. Mer information finns i dokumentet [Nätverksenheter.](https://azure.microsoft.com/solutions/network-appliances)

Som en hanterad tjänst kräver HDInsight obegränsad åtkomst till HDInsight-hälso- och hanteringstjänster både för inkommande och utgående trafik från VNET. När du använder NSG måste du se till att dessa tjänster fortfarande kan kommunicera med HDInsight-klustret.

![Diagram över HDInsight-entiteter som skapats i Azure custom VNET](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight med nätverkssäkerhetsgrupper

Om du planerar att använda **nätverkssäkerhetsgrupper** för att styra nätverkstrafiken utför du följande åtgärder innan du installerar HDInsight:

1. Identifiera den Azure-region som du planerar att använda för HDInsight.

2. Identifiera de tjänsttaggar som krävs av HDInsight för din region. Mer information finns i [NSG-tjänsttaggar (Network Security Group) för Azure HDInsight](hdinsight-service-tags.md).

3. Skapa eller ändra nätverkssäkerhetsgrupperna för undernätet som du planerar att installera HDInsight i.

    * __Nätverkssäkerhetsgrupper__: tillåt __inkommande__ trafik på port __443__ från IP-adresserna. Detta säkerställer att HDInsight-hanteringstjänster kan nå klustret utanför det virtuella nätverket.

Mer information om nätverkssäkerhetsgrupper finns i [översikten över nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Styra utgående trafik från HDInsight-kluster

Mer information om hur du kontrollerar utgående trafik från HDInsight-kluster finns i [Konfigurera begränsning av utgående nätverkstrafik för Azure HDInsight-kluster](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premises"></a>Påtvingad tunnel till lokal

Tvingad tunnelning är en användardefinierad routningskonfiguration där all trafik från ett undernät tvingas till ett visst nätverk eller en viss plats, till exempel det lokala nätverket. HDInsight stöder __inte__ påtvingad tunnelning av trafik till lokala nätverk.

## <a name="required-ip-addresses"></a><a id="hdinsight-ip"></a>Obligatoriska IP-adresser

Om du använder nätverkssäkerhetsgrupper eller användardefinierade vägar för att styra trafiken läser du [IP-adresser för HDInsight-hantering](hdinsight-management-ip-addresses.md).

## <a name="required-ports"></a><a id="hdinsight-ports"></a>Obligatoriska portar

Om du planerar att använda en **brandvägg** och komma åt klustret utifrån på vissa portar kan du behöva tillåta trafik på de portar som behövs för ditt scenario. Som standard behövs ingen särskild vitlistning av portar så länge som Azure-hanteringstrafiken som förklaras i föregående avsnitt tillåts nå kluster på port 443.

En lista över portar för specifika tjänster finns i [portarna som används av Apache Hadoop-tjänster i HDInsight-dokumentet.](hdinsight-hadoop-port-settings-for-services.md)

Mer information om brandväggsregler för virtuella enheter finns i [scenariodokumentet för virtuella enheter.](../virtual-network/virtual-network-scenario-udr-gw-nva.md)

## <a name="load-balancing"></a>Belastningsutjämning

När du skapar ett HDInsight-kluster skapas även en belastningsutjämnare. Typen av den här belastningsutjämnaren är på [den grundläggande SKU-nivån](../load-balancer/concepts-limitations.md#skus), som har vissa begränsningar. Ett av dessa begränsningar är att om du har två virtuella nätverk i olika regioner kan du inte ansluta till grundläggande belastningsutjämnare. Se [vanliga frågor och svar om virtuella nätverk: begränsningar för global vnet-peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers), för mer information.

## <a name="next-steps"></a>Nästa steg

* Kodexempel och exempel på hur du skapar Virtuella Azure-nätverk finns i [Skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md).
* Ett exempel från på sluten tid för att konfigurera HDInsight för att ansluta till ett lokalt nätverk finns i [Anslut HDInsight till ett lokalt nätverk](./connect-on-premises-network.md).
* Mer information om virtuella Azure-nätverk finns i [översikten över Virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md).
* Mer information om nätverkssäkerhetsgrupper finns i [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).
* Mer information om användardefinierade vägar finns i [Användardefinierade vägar och IP-vidarebefordran](../virtual-network/virtual-networks-udr-overview.md).
