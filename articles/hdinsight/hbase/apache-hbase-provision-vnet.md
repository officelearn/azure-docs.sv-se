---
title: Skapa HBase-kluster i en Virtual Network – Azure
description: Kom igång med HBase i Azure HDInsight. Lär dig hur du skapar HDInsight HBase-kluster på Azure Virtual Network.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: c8fc7c931f31e1ff58f41faa9a29f7e77e9655fd
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917322"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Skapa Apache HBase-kluster på HDInsight i Azure Virtual Network
Lär dig hur du skapar Azure HDInsight Apache HBase-kluster i ett [Azure-Virtual Network][1].

Med Virtual Network-integrering kan Apache HBase-kluster distribueras till samma virtuella nätverk som dina program så att program kan kommunicera med HBase direkt. Fördelarna är:

* Direkt anslutning av webb programmet till noderna i HBase-klustret, vilket möjliggör kommunikation via HBase Java API för RPC (Remote Procedure Call).
* Bättre prestanda genom att inte låta trafiken gå över flera gatewayer och belastnings utjämning.
* Möjligheten att bearbeta känslig information på ett säkrare sätt utan att exponera en offentlig slut punkt.

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar den här artikeln måste du ha följande objekt:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **En arbetsstation med Azure PowerShell**. Se [Installera och använda Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Skapa Apache HBase-kluster i ett virtuellt nätverk
I det här avsnittet skapar du ett Linux-baserat Apache HBase-kluster med det beroende Azure Storage-kontot i ett virtuellt Azure-nätverk med hjälp av en [Azure Resource Manager-mall](../../azure-resource-manager/resource-group-template-deploy.md). För andra metoder för att skapa kluster och förstå inställningarna, se [skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur du använder en mall för att skapa Apache Hadoop kluster i HDInsight finns i [skapa Apache Hadoop kluster i HDInsight med Azure Resource Manager mallar](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Vissa egenskaper är hårdkodade i mallen. Exempel:
>
> * **Plats**: USA, östra 2
> * **Kluster version**: 3.6
> * **Antal kluster arbets noder**: 2
> * **Standard lagrings konto**: en unik sträng
> * **Namn på virtuellt nätverk**: &lt;Kluster namn >-VNet
> * **Adress utrymme för virtuellt nätverk**: 10.0.0.0/16
> * **Under näts namn**: subnet1
> * **Adressintervall för undernätet**: 10.0.0.0/24
>
> &lt;Kluster namnet > ersätts med det kluster namn som du anger när du använder mallen.


1. Klicka på följande bild för att öppna mallen i Azure Portal. Mallen finns i [snabb starts mallar för Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>
2. Från bladet **Anpassad distribution** anger du följande egenskaper:

   * **Prenumeration**: Välj en Azure-prenumeration som används för att skapa HDInsight-klustret, det beroende lagrings kontot och det virtuella Azure-nätverket.
   * **Resursgrupp**: Välj **Skapa ny**och ange ett nytt resurs grupp namn.
   * **Plats**: Välj en plats för resursgruppen.
   * **Kluster**namn: Ange ett namn för det Hadoop-kluster som ska skapas.
   * **Kluster inloggnings namn och lösen ord**: Standardinloggningsnamnet är **admin**.
   * **SSH-användarnamn och-lösen ord**: Standardanvändarnamnet är **sshuser**.  Du kan byta namn.
   * **Jag samtycker till villkoren och de villkor som anges ovan**: Select
3. Klicka på **Köp**. Det tar cirka 20 minuter att skapa ett kluster. När klustret har skapats kan du öppna det genom att klicka på kluster bladet på portalen.

När du har slutfört artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Instruktioner för hur du tar bort ett kluster finns i [hantera Apache Hadoop kluster i HDInsight med hjälp av Azure Portal](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Om du vill börja arbeta med ditt nya HBase-kluster kan du använda de procedurer som finns i [Kom igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Ansluta till Apache HBase-klustret med Apache HBase Java RPC API: er
1. Skapa en virtuell IaaS-dator (Infrastructure as a Service) i samma virtuella Azure-nätverk och samma undernät. Instruktioner för hur du skapar en ny virtuell IaaS-dator finns i [skapa en virtuell dator som kör Windows Server](../../virtual-machines/windows/quick-create-portal.md). När du följer stegen i det här dokumentet måste du använda följande värden för nätverks konfigurationen:

   * **Virtuellt nätverk**: &lt;Kluster namn >-VNet
   * **Undernät**: subnet1

   > [!IMPORTANT]  
   > Ersätt &lt;kluster namnet > med det namn som du använde när du skapade HDInsight-klustret i föregående steg.

   Med dessa värden placeras den virtuella datorn i samma virtuella nätverk och undernät som HDInsight-klustret. Med den här konfigurationen kan de kommunicera direkt med varandra. Det finns ett sätt att skapa ett HDInsight-kluster med en tom Edge-nod. Edge-noden kan användas för att hantera klustret.  Mer information finns i [använda tomma Edge-noder i HDInsight](../hdinsight-apps-use-edge-node.md).

2. När du använder ett Java-program för att fjärrans luta till HBase måste du använda det fullständigt kvalificerade domän namnet (FQDN). För att fastställa detta måste du hämta det anslutningsspecifika DNS-suffixet för HBase-klustret. Om du vill göra det kan du använda någon av följande metoder:

   * Använda en webbläsare för att göra ett [Apache Ambari](https://ambari.apache.org/) -anrop:

     Bläddra till https://&lt;kluster namn >. azurehdinsight. NET/API/v1/Clusters&lt;/kluster namn >/hosts? minimal_response = True. Den aktiverar en JSON-fil med DNS-suffix.
   * Använd Ambari webbplats:

     1. Bläddra till https://&lt;kluster namn >. azurehdinsight. net.
     2. Klicka på **värdar** på den översta menyn.
   * Använd sväng för att göra REST-anrop:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     Leta upp posten "host_name" i de JavaScript Object Notation (JSON) som returneras. Den innehåller FQDN för noderna i klustret. Exempel:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Den del av domän namnet som börjar med kluster namnet är DNS-suffixet. Till exempel mycluster.b1.cloudapp.net.
   * Använda Azure PowerShell

     Använd följande Azure PowerShell-skript för att registrera funktionen **Get-ClusterDetail** , som kan användas för att returnera DNS-suffixet:

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     När du har kört Azure PowerShell-skriptet använder du följande kommando för att returnera DNS-suffixet med hjälp av funktionen **Get-ClusterDetail** . Ange ditt HDInsight HBase-kluster namn, administratörs namn och administratörs lösen ord när du använder det här kommandot.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     Det här kommandot returnerar DNS-suffixet. Till exempel **yourclustername.B4.Internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Du kan kontrol lera att den virtuella datorn kan kommunicera med HBase-klustret genom att `ping headnode0.<dns suffix>` använda kommandot från den virtuella datorn. Pinga till exempel headnode0.mycluster.b1.cloudapp.net.

Om du vill använda den här informationen i ett Java-program kan du följa stegen i [använda Apache Maven för att skapa Java-program som använder Apache HBase med HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) för att skapa ett program. Om du vill att programmet ska ansluta till en fjärran sluten HBase-Server ändrar du filen **HBase-site. XML** i det här exemplet för att använda det fullständiga domän namnet för Zookeeper. Exempel:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Mer information om namn matchning i virtuella Azure-nätverk, inklusive hur du använder din egen DNS-server, finns i [namn matchning (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du skapar ett Apache HBase-kluster. Du kan läsa mer här:

* [Kom igång med HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Använd tomma Edge-noder i HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurera Apache HBase-replikering i HDInsight](apache-hbase-replication.md)
* [Skapa Apache Hadoop kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Kom igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

