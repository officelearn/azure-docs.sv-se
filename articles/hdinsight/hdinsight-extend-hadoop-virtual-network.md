---
title: Utöka HDInsight med virtuellt nätverk – Azure
description: Lär dig hur du använder Azure Virtual Network för att ansluta HDInsight till andra resurser i molnet eller resurser i ditt datacenter
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/29/2019
ms.openlocfilehash: f97c07c522dfb22818aca84d41d30c023f564d84
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721317"
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Utöka Azure HDInsight med hjälp av Azure Virtual Network

Lär dig hur du använder HDInsight med en [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Med hjälp av Azure Virtual Network gör det möjligt för följande scenarier:

* Ansluter till HDInsight direkt från ett lokalt nätverk.

* Ansluta HDInsight till data som lagras i ett virtuellt Azure-nätverk.

* Direkt åtkomst till [Apache Hadoop](https://hadoop.apache.org/) tjänster som inte är tillgänglig offentligt över internet. Till exempel [Apache Kafka](https://kafka.apache.org/) API: er eller [Apache HBase](https://hbase.apache.org/) Java API.

> [!IMPORTANT]  
> Efter den 28 februari 2019 levereras nätverksresurser (till exempel nätverkskort, LBs osv) för nya kluster som skapas i ett virtuellt nätverk i samma resursgrupp för HDInsight-kluster. Tidigare etablerades resurserna i resursgruppen VNET. Det finns ingen förändring av de aktuella pågående kluster och dessa kluster som skapas utan ett virtuellt nätverk.

## <a name="prerequisites-for-code-samples-and-examples"></a>Krav för kodexempel och exempel

* En förståelse för TCP/IP-nätverk. Om du inte är bekant med TCP/IP-nätverk, bör du samarbeta med någon som innan du gör ändringar i produktionsnätverk.
* Om du använder PowerShell, måste den [AZ modulen](https://docs.microsoft.com/powershell/azure/overview).
* Om du vill använda Azure CLI och du inte har installerat den, se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Om du letar efter stegvis vägledning om hur du ansluter HDInsight till ditt lokala nätverk med hjälp av Azure Virtual Network, finns i den [ansluta HDInsight till det lokala nätverket](connect-on-premises-network.md) dokumentet.

## <a name="planning"></a>Planering

Följande är de frågor som du måste svara på när du planerar att installera HDInsight i ett virtuellt nätverk:

* Behöver du installera HDInsight i ett befintligt virtuellt nätverk? Eller du skapar ett nytt nätverk?

    Om du använder ett befintligt virtuellt nätverk, kan du behöva ändra nätverkskonfigurationen innan du kan installera HDInsight. Mer information finns i den [lägga till HDInsight i ett befintligt virtuellt nätverk](#existingvnet) avsnittet.

* Vill du ansluta det virtuella nätverket som innehåller HDInsight till ett annat virtuellt nätverk eller ditt lokala nätverk?

    För att enkelt för att arbeta med resurser i nätverk, kan du behöva skapa en anpassad DNS och konfigurera vidarebefordran av DNS. Mer information finns i den [ansluta flera virtuella nätverk](#multinet) avsnittet.

* Vill du begränsa/omdirigering inkommande eller utgående trafik till HDInsight?

    HDInsight har obegränsad kommunikation med specifika IP-adresser i Azure-datacentret. Det finns också flera portar som måste tillåtas genom brandväggar för klientkommunikation. Mer information finns i den [styra nätverkstrafiken](#networktraffic) avsnittet.

## <a id="existingvnet"></a>Lägg till HDInsight i ett befintligt virtuellt nätverk

Använd stegen i det här avsnittet för att identifiera hur du lägger till en ny HDInsight i ett befintligt virtuellt Azure-nätverk.

> [!NOTE]  
> Du kan inte lägga till ett befintligt HDInsight-kluster till ett virtuellt nätverk.

1. Använder du en klassisk eller Resource Manager-distributionsmodellen för det virtuella nätverket?

    HDInsight 3.4 och senare kräver ett virtuellt nätverk för Resource Manager. Tidigare versioner av HDInsight krävs ett klassiskt virtuellt nätverk.

    Om ditt befintliga nätverk är ett klassiskt virtuellt nätverk, måste du skapa ett virtuellt nätverk för Resource Manager och sedan ansluta två. [Ansluta klassiska virtuella nätverk till nya virtuella nätverk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    När ansluten, kan HDInsight som installerats i Resource Manager-nätverk interagera med resurser i det klassiska nätverket.

2. Använder du Tvingad tunneltrafik? Tvingad tunneltrafik är en undernätsinställning för som tvingar utgående Internet-trafik till en enhet för granskning och loggning. HDInsight stöder inte tvingande dirigering. Antingen bort Tvingad tunneltrafik innan du distribuerar HDInsight i ett befintligt undernät eller skapar ett nytt undernät ingen Tvingad tunneltrafik för HDInsight.

3. Använder du nätverkssäkerhetsgrupper, användardefinierade vägar eller virtuella nätverksinstallationer för att begränsa trafik till eller från det virtuella nätverket?

    Som en hanterad tjänst kräver HDInsight obegränsad åtkomst till flera IP-adresser i Azure-datacentret. Uppdatera alla befintliga nätverkssäkerhetsgrupper eller användardefinierade vägar för att tillåta kommunikation med dessa IP-adresser.
    
    HDInsight är värd för flera tjänster som använder olika portar. Inte blockera trafik till dessa portar. En lista över portar som ska tillåtas via virtuell installation brandväggar finns i avsnittet Security.
    
    Använd följande Azure PowerShell eller Azure CLI-kommandon för att hitta din befintliga säkerhetskonfiguration:

    * Nätverkssäkerhetsgrupper

        Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller det virtuella nätverket och ange sedan kommando:
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Mer information finns i den [felsöka nätverkssäkerhetsgrupper](../virtual-network/diagnose-network-traffic-filter-problem.md) dokumentet.

        > [!IMPORTANT]  
        > Regler för nätverkssäkerhetsgrupper tillämpas i ordning baserat på regelprioritet. Den första regeln som matchar trafikmönstret har tillämpats och inga andra tillämpas efter den trafiken. Ordning regler från mest Tillåtande till ger. Mer information finns i den [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) dokumentet.

    * Användardefinierade vägar

        Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller det virtuella nätverket och ange sedan kommando:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Mer information finns i den [Felsökningsvägar](../virtual-network/diagnose-network-routing-problem.md) dokumentet.

4. Skapa ett HDInsight-kluster och välj det virtuella Azure-nätverk under konfigurationen. Använd stegen i följande dokument för att förstå klusterskapningsprocessen:

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) (Skapa HDInsight med hjälp av Azure-portalen)
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Skapa HDInsight med hjälp av Azure PowerShell)
    * [Skapa HDInsight med klassiska Azure-CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Skapa HDInsight med en Azure Resource Manager-mall](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Att lägga till HDInsight i ett virtuellt nätverk är ett valfritt konfigurationssteg. Var noga med att välja det virtuella nätverket när du konfigurerar klustret.

## <a id="multinet"></a>Ansluta flera virtuella nätverk

Den största utmaningen med flera nätverkskonfigurationer är namnmatchning mellan nätverk.

Azure ger namnmatchning för Azure-tjänster som är installerade i ett virtuellt nätverk. Den här inbyggda namnmatchning kan HDInsight att ansluta till följande resurser med hjälp av ett fullständigt kvalificerat domännamn (FQDN):

* Alla resurser som är tillgänglig på internet. Till exempel microsoft.com, windowsupdate.com.

* Alla resurser som tillhör samma Azure Virtual Network, med hjälp av den __interna DNS-namnet__ för resursen. Till exempel när du använder standard-namnmatchning, är följande exempel interna DNS-namn som tilldelats till HDInsight-arbetsnoder:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Båda dessa noder kan kommunicera direkt med varandra och andra noder i HDInsight, med hjälp av interna DNS-namn.

Standard-namnmatchning har __inte__ Tillåt HDInsight att matcha namnen på resurser i nätverk som är anslutna till det virtuella nätverket. Det är till exempel vanligt att ansluta till ditt lokala nätverk till det virtuella nätverket. HDInsight med endast standard namnmatchningen, inte de åtkomst till resurser i det lokala nätverket efter namn. Motsatt gäller även, resurser i ditt lokala nätverk inte kan komma åt resurser i det virtuella nätverket efter namn.

> [!WARNING]  
> Du måste skapa den anpassa DNS-servern och konfigurerar det virtuella nätverket för att använda den innan du skapar HDInsight-kluster.

Om du vill aktivera namnmatchningen mellan det virtuella nätverket och resurser i anslutna nätverk, måste du utföra följande åtgärder:

1. Skapa en anpassad DNS-server i Azure-nätverk där du planerar att installera HDInsight.

2. Konfigurera det virtuella nätverket om du vill använda anpassade DNS-servern.

3. Hitta Azure tilldelade DNS-suffixet för det virtuella nätverket. Det här värdet liknar `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Läs om hur du hittar DNS-suffix i [exempel: Anpassad DNS](#example-dns) avsnittet.

4. Konfigurera vidarebefordran mellan DNS-servrar. Konfigurationen beror på vilken typ av fjärrnätverket.

   * Om fjärrnätverket är ett lokalt nätverk kan du konfigurera DNS på följande sätt:
        
     * __Anpassad DNS__ (i det virtuella nätverket):

         * Vidarebefordra begäranden för DNS-suffixet för det virtuella nätverket till Azures rekursiva matchare (168.63.129.16). Azure hanterar begäranden för resurser i det virtuella nätverket

         * Vidarebefordra alla övriga förfrågningar till den lokala DNS-servern. Den lokala DNS hanterar alla andra namnmatchning, även begäranden om Internetresurser, till exempel Microsoft.com.

     * __Den lokala DNS__: Vidarebefordra begäranden för DNS-suffixet för virtuellt nätverk till anpassade DNS-servern. Den anpassa DNS-servern vidarebefordrar sedan till Azures rekursiva matchare.

       Den här konfigurationen vägar begäranden för fullständigt kvalificerade domännamn som innehåller DNS-suffixet för det virtuella nätverket till anpassade DNS-servern. Alla övriga förfrågningar (även för offentliga internet-adresser) hanteras av den lokala DNS-servern.

   * Om fjärrnätverket är ett annat Azure virtuellt nätverk kan du konfigurera DNS enligt följande:

     * __Anpassad DNS__ (i varje virtuellt nätverk):

         * Begäranden för DNS-suffixet för de virtuella nätverken vidarebefordras till de anpassade DNS-servrarna. DNS i varje virtuellt nätverk är ansvarig för att lösa resurser inom sitt nätverk.

         * Vidarebefordra alla övriga förfrågningar till Azures rekursiva matchare. Rekursiva matchare ansvarar för att lösa lokala och internet-resurser.

       DNS-servern baserat för varje nätverk vidarebefordrar begäranden till en annan på DNS-suffix. Andra förfrågningar har åtgärdats med hjälp av Azures rekursiva matchare.

     Ett exempel på varje konfiguration finns i den [exempel: Anpassad DNS](#example-dns) avsnittet.

Mer information finns i den [namnmatchning för virtuella datorer och Rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentet.

## <a name="directly-connect-to-apache-hadoop-services"></a>Ansluta direkt till Apache Hadoop-tjänster

Du kan ansluta till klustret på `https://CLUSTERNAME.azurehdinsight.net`. Den här adressen använder en offentlig IP-adress som kanske inte kan nås om du har använt NSG: er för att begränsa inkommande trafik från internet. Dessutom när du distribuerar klustret i ett virtuellt nätverk du har åtkomst till den med hjälp av privata slutpunkt `https://CLUSTERNAME-int.azurehdinsight.net`. Den här slutpunkten motsvarar en privat IP-adress i virtuellt nätverk för åtkomst till klustret.

Använd följande steg för att ansluta till Apache Ambari och andra webbsidor via det virtuella nätverket:

1. Använd någon av följande metoder för att identifiera de interna fullständigt kvalificerade domännamn (FQDN) för noderna i HDInsight:

    Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller det virtuella nätverket och ange sedan kommando:

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

    I listan över noder som returnerade, hitta det fullständiga Domännamnet för huvudnoderna och Använd FQDN: er för att ansluta till Ambari och andra webbtjänster. Till exempel använda `http://<headnode-fqdn>:8080` att komma åt Ambari.

    > [!IMPORTANT]  
    > Vissa tjänster som finns på huvudnoderna är bara aktiva på en nod i taget. Om du försöker få åtkomst till en tjänst på en huvudnod och den returnerar ett 404-fel, växla till andra huvudnoden.

2. Information om den noden och porten som en tjänst är tillgänglig på finns i [portar som används av Hadoop-tjänster på HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentet.

## <a id="networktraffic"></a> Kontrollera nätverkstrafik

Nätverkstrafik i en Azure-nätverk kan kontrolleras med hjälp av följande metoder:

* **Nätverkssäkerhetsgrupper** (NSG) du kan filtrera inkommande och utgående trafik till nätverket. Mer information finns i den [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) dokumentet.

    > [!WARNING]  
    > HDInsight har inte stöd för att begränsa utgående trafik. All utgående trafik ska tillåtas.

* **Användardefinierade vägar** (UDR) definiera hur trafiken flödar mellan resurser i nätverket. Mer information finns i den [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md) dokumentet.

* **Virtuella nätverksinstallationer** replikera funktionerna på enheter som brandväggar och routrar. Mer information finns i den [nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances) dokumentet.

HDInsight kräver obegränsad åtkomst till HDInsight-hälsa och management services både inkommande och utgående trafik från det virtuella nätverket som en hanterad tjänst. När du använder NSG: er och udr: er, måste du kontrollera att dessa tjänster fortfarande kan kommunicera med HDInsight-kluster.

### <a id="hdinsight-ip"></a> HDInsight med nätverkssäkerhetsgrupper och användardefinierade vägar

Om du tänker använda **nätverkssäkerhetsgrupper** eller **användardefinierade vägar** till nätverkstrafik genom att utföra följande åtgärder innan du installerar HDInsight:

1. Identifiera Azure-regionen som du planerar att använda för HDInsight.

2. Identifiera IP-adresser som krävs av HDInsight. Mer information finns i den [IP-adresser som krävs av HDInsight](#hdinsight-ip) avsnittet.

3. Skapa eller ändra nätverkssäkerhetsgrupper eller användardefinierade vägar för det undernät som du planerar att installera HDInsight i.

    * __Nätverkssäkerhetsgrupper__: Tillåt __inkommande__ trafik på port __443__ från IP-adresser. Det säkerställer att HDI hanteringstjänster kan nå klustret från utanför VNET.
    * __Användardefinierade vägar__: Om du planerar att använda udr: er, skapa en väg för varje IP-adress och ange den __nästa hopptyp__ till __Internet__. Du bör också tillåta all utgående trafik från det virtuella nätverket utan begränsning. Exempelvis kan du dirigera all annan trafik till din Azure eller en network virtuella installation (installation i Azure) för övervakning, men den utgående trafiken inte ska blockeras.

Mer information om nätverkssäkerhetsgrupper eller användardefinierade vägar finns i följande dokumentation:

* [Nätverkssäkerhetsgrupp](../virtual-network/security-overview.md)

* [Användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling-to-on-premise"></a>Tvingad tunneltrafik till lokala

Tvingad tunneltrafik är en användardefinierad konfiguration där all trafik från ett undernät tvingas att ett visst nätverk eller plats, till exempel ditt lokala nätverk. HDInsight har __inte__ support Tvingad tunneltrafik till lokala nätverk. Om du använder Azure brandvägg eller en virtuell nätverksinstallation som finns i Azure kan använda du udr: er att dirigera trafiken till den för övervakning och tillåter all utgående trafik.

## <a id="hdinsight-ip"></a> Den begärda IP-adresser

> [!IMPORTANT]  
> Azure hälso- och management-tjänster måste kunna kommunicera med HDInsight. Om du använder nätverkssäkerhetsgrupper eller användardefinierade vägar, Tillåt trafik från IP-adresser för dessa tjänster att nå HDInsight.
>
> Om du inte använder nätverkssäkerhetsgrupper eller användardefinierade vägar Kontrollera trafik, kan du ignorera det här avsnittet.

Om du använder nätverkssäkerhetsgrupper måste du tillåta trafik från azuretjänsterna för hälso- och nå HDInsight-kluster på port 443. Du måste även tillåta trafik mellan virtuella datorer i undernätet. Använd följande steg för att hitta IP-adresser som måste tillåtas:

1. Du måste alltid tillåta trafik från följande IP-adresser:

    | Källans IP-adress | Målport | Direction |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Inkommande |
    | 23.99.5.239 | 443 | Inkommande |
    | 168.61.48.131 | 443 | Inkommande |
    | 138.91.141.162 | 443 | Inkommande |

2. Om ditt HDInsight-kluster är i något av följande regioner, måste du tillåta trafik från IP-adresser som visas för regionen:

    > [!IMPORTANT]  
    > Om du använder den Azure-region inte visas kan sedan bara använda fyra IP-adresser från steg 1.

    | Land/region | Region | Tillåtna käll-IP-adresser | Tillåtna målport | Direction |
    | ---- | ---- | ---- | ---- | ----- |
    | Asien | Östasien | 23.102.235.122</br>52.175.38.134 | 443 | Inkommande |
    | &nbsp; | Sydostasien | 13.76.245.160</br>13.76.136.249 | 443 | Inkommande |
    | Australien | Östra Australien | 104.210.84.115</br>13.75.152.195 | 443 | Inkommande |
    | &nbsp; | Sydöstra Australien | 13.77.2.56</br>13.77.2.94 | 443 | Inkommande |
    | Brasilien | Södra Brasilien | 191.235.84.104</br>191.235.87.113 | 443 | Inkommande |
    | Kanada | Östra Kanada | 52.229.127.96</br>52.229.123.172 | 443 | Inkommande |
    | &nbsp; | Centrala Kanada | 52.228.37.66</br>52.228.45.222 | 443 | Inkommande |
    | Kina | Norra Kina | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | 443 | Inkommande |
    | &nbsp; | Östra Kina | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | 443 | Inkommande |
    | &nbsp; | Kina, norra 2 | 40.73.37.141</br>40.73.38.172 | 443 | Inkommande |
    | &nbsp; | Kina, östra 2 | 139.217.227.106</br>139.217.228.187 | 443 | Inkommande |
    | Europa | Norra Europa | 52.164.210.96</br>13.74.153.132 | 443 | Inkommande |
    | &nbsp; | Västra Europa| 52.166.243.90</br>52.174.36.244 | 443 | Inkommande |
    | Frankrike | Frankrike, centrala| 20.188.39.64</br>40.89.157.135 | 443 | Inkommande |
    | Tyskland | Centrala Tyskland | 51.4.146.68</br>51.4.146.80 | 443 | Inkommande |
    | &nbsp; | Nordöstra Tyskland | 51.5.150.132</br>51.5.144.101 | 443 | Inkommande |
    | Indien | Indien, centrala | 52.172.153.209</br>52.172.152.49 | 443 | Inkommande |
    | &nbsp; | Södra Indien | 104.211.223.67<br/>104.211.216.210 | 443 | Inkommande |
    | Japan | Östra Japan | 13.78.125.90</br>13.78.89.60 | 443 | Inkommande |
    | &nbsp; | Västra Japan | 40.74.125.69</br>138.91.29.150 | 443 | Inkommande |
    | Korea | Sydkorea, centrala | 52.231.39.142</br>52.231.36.209 | 433 | Inkommande |
    | &nbsp; | Sydkorea, södra | 52.231.203.16</br>52.231.205.214 | 443 | Inkommande
    | Storbritannien | Storbritannien, västra | 51.141.13.110</br>51.141.7.20 | 443 | Inkommande |
    | &nbsp; | Storbritannien, södra | 51.140.47.39</br>51.140.52.16 | 443 | Inkommande |
    | USA | Centrala USA | 13.67.223.215</br>40.86.83.253 | 443 | Inkommande |
    | &nbsp; | Östra USA | 13.82.225.233</br>40.71.175.99 | 443 | Inkommande |
    | &nbsp; | Norra centrala USA | 157.56.8.38</br>157.55.213.99 | 443 | Inkommande |
    | &nbsp; | Västra centrala USA | 52.161.23.15</br>52.161.10.167 | 443 | Inkommande |
    | &nbsp; | Västra USA | 13.64.254.98</br>23.101.196.19 | 443 | Inkommande |
    | &nbsp; | Västra USA 2 | 52.175.211.210</br>52.175.222.222 | 443 | Inkommande |

    Information om IP-adresser för Azure Government finns i den [Azure Government information + analys](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) dokumentet.

3. Du måste även tillåta åtkomst från __168.63.129.16__. Den här adressen är Azures rekursiva matchare. Mer information finns i den [namnmatchning för virtuella datorer och rollen instanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentet.

Mer information finns i den [styra nätverkstrafiken](#networktraffic) avsnittet.

Om du använder användardefinierade routes(UDRs), bör du ange en väg och tillåta utgående trafik från det virtuella nätverket till IP-adresserna som ovan med nästa hopp inställd på ”Internet”.
    
## <a id="hdinsight-ports"></a> Portar som krävs

Om du tänker använda en **brandväggen** och få åtkomst till klustret från utanför på vissa portar måste du kanske att tillåta trafik på dessa portar som krävs för ditt scenario. Som standard krävs ingen särskild lista över tillåtna portar så länge azure management-trafiken som beskrivs i föregående avsnitt tillåts att nå klustret på port 443.

En lista över portar för specifika tjänster finns i den [portar som används av Apache Hadoop-tjänster på HDInsight](hdinsight-hadoop-port-settings-for-services.md) dokumentet.

Mer information om brandväggsregler för virtuella installationer finns på [virtuella utrustningsscenario](../virtual-network/virtual-network-scenario-udr-gw-nva.md) dokumentet.

## <a id="hdinsight-nsg"></a>Exempel: nätverkssäkerhetsgrupper med HDInsight

Exemplen i det här avsnittet visar hur du skapar regler som tillåter att HDInsight ska kunna kommunicera med Azure-hanteringstjänster för nätverkssäkerhet. Innan du använder exemplen, justerar du IP-adresser för att matcha värdena för den Azure-region du använder. Du hittar den här informationen i den [HDInsight med nätverkssäkerhetsgrupper och användardefinierade vägar](#hdinsight-ip) avsnittet.

### <a name="azure-resource-management-template"></a>Azure Resource Manager-mall

Följande Resource Manager-mallen skapar ett virtuellt nätverk som begränsar inkommande trafik, men tillåter trafik från IP-adresser som krävs av HDInsight. Den här mallen skapar även ett HDInsight-kluster i det virtuella nätverket.

* [Distribuera ett skyddat virtuellt Azure-nätverk och ett HDInsight Hadoop-kluster](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]  
> Ändra IP-adresser som används i det här exemplet för att matcha den Azure-region du använder. Du hittar den här informationen i den [HDInsight med nätverkssäkerhetsgrupper och användardefinierade vägar](#hdinsight-ip) avsnittet.

### <a name="azure-powershell"></a>Azure PowerShell

Använd följande PowerShell-skript för att skapa ett virtuellt nätverk som begränsar inkommande trafik och tillåter trafik från IP-adresser för regionen Europa, norra.

> [!IMPORTANT]  
> Ändra IP-adresser som används i det här exemplet för att matcha den Azure-region du använder. Du hittar den här informationen i den [HDInsight med nätverkssäkerhetsgrupper och användardefinierade vägar](#hdinsight-ip) avsnittet.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `
# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

> [!IMPORTANT]  
> Det här exemplet visar hur du lägger till regler för att tillåta inkommande trafik på de begärda IP-adresserna. Den innehåller inte en regel för att begränsa inkommande åtkomst från andra källor.
>
> I följande exempel visar hur du aktiverar SSH-åtkomst från Internet:
>
> ```powershell
> Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>Azure CLI

Använd följande steg för att skapa ett virtuellt nätverk som begränsar inkommande trafik, men tillåter trafik från IP-adresser som krävs av HDInsight.

1. Använd följande kommando för att skapa en ny grupp med namnet `hdisecure`. Ersätt `RESOURCEGROUP` med resursgruppen som innehåller Azure virtuellt nätverk. Ersätt `LOCATION` med plats (region) som gruppen har skapats i.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    När gruppen har skapats kan få du information om den nya gruppen.

2. Använd följande för att lägga till regler i den nya nätverkssäkerhetsgrupp som tillåter inkommande kommunikation på port 443 från Azure HDInsight-tjänsten för hälsotillstånd och hantering. Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller Azure virtuellt nätverk.

    > [!IMPORTANT]  
    > Ändra IP-adresser som används i det här exemplet för att matcha den Azure-region du använder. Du hittar den här informationen i den [HDInsight med nätverkssäkerhetsgrupper och användardefinierade vägar](#hdinsight-ip) avsnittet.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Om du vill hämta den unika identifieraren för den här nätverkssäkerhetsgruppen, använder du följande kommando:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query 'id'
    ```

    Det här kommandot returnerar ett värde som liknar följande text:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Använd dubbla citattecken runt `id` i kommandot om du inte får de förväntade resultaten.

4. Använd följande kommando för att tillämpa nätverkssäkerhetsgruppen till ett undernät. Ersätt den `GUID` och `RESOURCEGROUP` med dem som returnerats från föregående steg. Ersätt `VNETNAME` och `SUBNETNAME` med namn på virtuellt nätverk och undernätsnamn som du vill skapa.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    När det här kommandot har slutförts kan installera du HDInsight i det virtuella nätverket.

> [!IMPORTANT]  
> De här stegen Öppna endast åtkomst till HDInsight hälso- och management-tjänsten på Azure-molnet. Alla andra åtkomst till HDInsight-kluster från utanför det virtuella nätverket är blockerad. Om du vill aktivera åtkomst från utanför det virtuella nätverket måste du lägga till ytterligare regler för Nätverkssäkerhetsgruppen.
>
> I följande exempel visar hur du aktiverar SSH-åtkomst från Internet:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Exempel: DNS-konfiguration

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Namnmatchning mellan ett virtuellt nätverk och ett anslutna lokalt nätverk

Det här exemplet gör följande antaganden:

* Du har ett Azure-nätverk som är ansluten till ett lokalt virtuellt nätverk med en VPN-gateway.

* Anpassad DNS-servern i det virtuella nätverket kör Linux eller Unix som operativsystem.

* [Binda](https://www.isc.org/downloads/bind/) är installerad på den anpassa DNS-servern.

Anpassad DNS-servern i det virtuella nätverket:

1. Använd Azure PowerShell eller Azure CLI för att hitta det virtuella nätverket DNS-suffix:

    Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller det virtuella nätverket och ange sedan kommando:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Anpassad DNS-servern för det virtuella nätverket, Använd följande text som innehållet i den `/etc/bind/named.conf.local` fil:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Ersätt den `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` värde med DNS-suffixet för det virtuella nätverket.

    Den här konfigurationen dirigerar alla DNS-förfrågningar för DNS-suffixet för det virtuella nätverket till Azures rekursiva matchare.

2. Anpassad DNS-servern för det virtuella nätverket, Använd följande text som innehållet i den `/etc/bind/named.conf.options` fil:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Ersätt den `10.0.0.0/16` värdet med IP-adressintervallet för det virtuella nätverket. Den här posten kan namnet lösning begäranden adresser inom det här intervallet.

    * Lägg till IP-adressintervallet för det lokala nätverket till den `acl goodclients { ... }` avsnittet.  posten kan namnmatchning från resurser i det lokala nätverket.
    
    * Ersätt värdet `192.168.0.1` med IP-adressen för den lokala DNS-servern. Den här posten dirigerar alla andra DNS-begäranden till den lokala DNS-servern.

3. Om du vill använda konfigurationen startar du om bindning. Till exempel `sudo service bind9 restart`.

4. Lägg till en villkorlig vidarebefordrare till den lokala DNS-servern. Konfigurera villkorlig vidarebefordrare för att skicka begäranden för DNS-suffix från steg 1 till den anpassa DNS-servern.

    > [!NOTE]  
    > I dokumentationen för din DNS-programvara ge specifik information om hur du lägger till en villkorlig vidarebefordrare.

När du har slutfört de här stegen kan du ansluta till resurser i nätverken med fullständigt kvalificerade domännamn (FQDN). Du kan nu installera HDInsight till det virtuella nätverket.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Namnmatchningen mellan två anslutna virtuella nätverk

Det här exemplet gör följande antaganden:

* Du har två virtuella Azure-nätverk som är anslutna med hjälp av en VPN-gateway eller peering.

* Anpassade DNS-servern i båda nätverken kör Linux eller Unix som operativsystem.

* [Binda](https://www.isc.org/downloads/bind/) är installerad på de anpassade DNS-servrarna.

1. Använd Azure PowerShell eller Azure CLI för att hitta båda virtuella nätverken DNS-suffix:

    Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller det virtuella nätverket och ange sedan kommando:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Använd följande text som innehållet i den `/etc/bind/named.config.local` filen på den anpassa DNS-servern. Gör den här ändringen anpassade DNS-servern i båda virtuella nätverken.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Ersätt den `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` värdet med DNS-suffix i __andra__ virtuellt nätverk. Den här posten dirigerar begäranden för fjärrnätverket DNS-suffix till anpassad DNS i nätverket.

3. På de anpassade DNS-servrarna i båda virtuella nätverken använder du följande text som innehållet i den `/etc/bind/named.conf.options` fil:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
   Ersätt den `10.0.0.0/16` och `10.1.0.0/16` värden med IP-adressintervall för dina virtuella nätverk. Den här posten kan resurser i varje nätverk för att göra förfrågningar från DNS-servrar.

    Alla förfrågningar som inte är för DNS-suffix för de virtuella nätverken (till exempel microsoft.com) hanteras av Azures rekursiva matchare.

4. Om du vill använda konfigurationen startar du om bindning. Till exempel `sudo service bind9 restart` på båda DNS-servrar.

När du har slutfört de här stegen kan du ansluta till resurser i det virtuella nätverket med fullständigt kvalificerade domännamn (FQDN). Du kan nu installera HDInsight till det virtuella nätverket.

## <a name="next-steps"></a>Nästa steg

* Slutpunkt till slutpunkt-exempel på hur du konfigurerar HDInsight för att ansluta till ett lokalt nätverk finns i [ansluta HDInsight till ett lokalt nätverk](./connect-on-premises-network.md).
* Konfigurera Apache Hbase-kluster i Azure-nätverk, finns i [skapa Apache HBase-kluster i HDInsight i Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Konfigurerar Apache HBase-geo-replikering finns i [Konfigurera replikering för Apache HBase-kluster i Azure-nätverk](hbase/apache-hbase-replication.md).
* Mer information om virtuella Azure-nätverk finns i den [översikt över Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Mer information om nätverkssäkerhetsgrupper finns i [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

* Mer information om användardefinierade vägar finns i [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md).
