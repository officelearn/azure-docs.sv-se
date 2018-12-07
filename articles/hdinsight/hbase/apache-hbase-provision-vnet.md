---
title: Skapa HBase-kluster i ett virtuellt nätverk – Azure
description: Kom igång med HBase i Azure HDInsight. Lär dig hur du skapar HDInsight HBase-kluster i Azure Virtual Network.
services: hdinsight,virtual-network
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: cf037000a047b02f3874c3bccc9678f2ea18ecec
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011206"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Skapa Apache HBase-kluster i HDInsight i Azure-nätverk
Lär dig hur du skapar Azure HDInsight Apache HBase-kluster i en [Azure Virtual Network][1].

Med virtual network-integration, kan Apache HBase-kluster bara distribueras till samma virtuella nätverk som dina program så att program kan kommunicera direkt med HBase. Fördelarna innefattar:

* Direktanslutning av webbprogrammet på noderna i HBase-kluster, vilket möjliggör kommunikation via HBase Java fjärranrop anropa API: er (RPC).
* Förbättrad prestanda genom att inte låta trafiken går över flera gatewayer och belastningsutjämnare.
* Möjligheten att bearbeta känslig information på ett säkrare sätt utan att exponera en offentlig slutpunkt.

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar den här självstudiekursen behöver du följande:

* **en Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **En arbetsstation med Azure PowerShell**. Se [installera och använda Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Skapa Apache HBase-kluster i virtuellt nätverk
I det här avsnittet skapar du ett Linux-baserade Apache HBase-kluster med beroende Azure Storage-konto i ett virtuellt Azure-nätverk med hjälp av en [Azure Resource Manager-mall](../../azure-resource-manager/resource-group-template-deploy.md). Information om andra metoder för att skapa-kluster och förstå inställningarna finns i [skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur du använder en mall för att skapa Apache Hadoop-kluster i HDInsight finns i [skapa Apache Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]
> Vissa egenskaper är hårdkodad i mallen. Exempel:
>
> * **Plats**: östra USA 2
> * **Klusterversion**: 3.6
> * **Kluster worker nodantal**: 2
> * **Standard storage-konto**: en unik sträng
> * **Namn på virtuellt nätverk**: &lt;klustrets namn > – virtuellt nätverk
> * **Virtuella nätverkets adressutrymme**: 10.0.0.0/16
> * **Undernätsnamn**: subnet1
> * **Adressintervall för undernätet**: 10.0.0.0/24
>
> &lt;Klusternamn > ersätts med klustrets namn som du anger när du använder mallen.
>
>

1. Klicka på följande bild för att öppna mallen i Azure Portal. Mallen finns i [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Från den **anpassad distribution** bladet, ange följande egenskaper:

   * **Prenumeration**: Välj en Azure-prenumeration används för att skapa HDInsight-klustret, det beroende lagringskontot och virtuella Azure-nätverket.
   * **Resursgrupp**: Välj **Skapa nytt**, och ange ett nytt resursgruppnamn.
   * **Plats**: Välj en plats för resursgruppen.
   * **Klusternamn**: Ange ett namn för Hadoop-kluster som ska skapas.
   * **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard **admin**.
   * **SSH-användarnamn och lösenord**: Standardanvändarnamnet är **sshuser**.  Du kan byta namn.
   * **Jag godkänner villkoren och de villkor som anges ovan**: (Välj)
3. Klicka på **Köp**. Det tar cirka 20 minuter att skapa ett kluster. När klustret har skapats klickar du på klusterbladet i portalen för att öppna den.

När du har slutfört självstudien kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Anvisningar för att ta bort ett kluster finns i [hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure portal](../hdinsight-administer-use-management-portal.md#delete-clusters).

Om du vill börja arbeta med din nya HBase-kluster måste du använda de procedurer som finns i [komma igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Ansluta till Apache HBase-kluster med Apache HBase Java RPC-API: er
1. Skapa en infrastruktur som en tjänst (IaaS) virtuell dator i samma Azure-nätverket och samma undernät. Anvisningar om hur du skapar en ny virtuell IaaS-dator finns i [skapa en virtuell dator som kör Windows Server](../../virtual-machines/windows/quick-create-portal.md). När du följer stegen i det här dokumentet, måste du använda följande värden för nätverkskonfigurationen:

   * **Virtuellt nätverk**: &lt;klustrets namn > – virtuellt nätverk
   * **Undernät**: subnet1

   > [!IMPORTANT]
   > Ersätt &lt;klustrets namn > med namnet du använde när du skapar HDInsight-kluster i föregående steg.
   >
   >

   Med hjälp av dessa värden, placeras den virtuella datorn i samma virtuella nätverk och undernät som HDInsight-klustret. Den här konfigurationen gör att de kan kommunicera direkt med varandra. Det är ett sätt att skapa ett HDInsight-kluster med en tom edge-nod. Kantnoden kan användas för att hantera klustret.  Mer information finns i [använda tomma kantnoder i HDInsight](../hdinsight-apps-use-edge-node.md).

2. När du använder ett Java-program för att fjärransluta till HBase, måste du använda det fullständigt kvalificerade domännamnet (FQDN). Om du vill ta reda på detta, måste du hämta det anslutningsspecifika DNS-suffixet för HBase-kluster. Gör att kan du använda någon av följande metoder:

   * Använd en webbläsare för att göra en [Apache Ambari](https://ambari.apache.org/) anropa:

     Bläddra till https://&lt;klusternamn >.azurehdinsight.net/api/v1/clusters/&lt;klusternamn > / värd? minimal_response = true. Det har visat en JSON-fil med DNS-suffix.
   * Använda Ambari-webbplats:

     1. Bläddra till https://&lt;klusternamn >. azurehdinsight.net.
     2. Klicka på **värdar** på den översta menyn.
   * Använd Curl för att göra REST-anrop:

    ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
    ```

     Hitta posten ”värddatornamn” i de JavaScript Object Notation (JSON) data som returneras. Den innehåller det fullständiga Domännamnet för noder i klustret. Exempel:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Del av den domän namn som början med klusternamnet är DNS-suffixet. Till exempel mycluster.b1.cloudapp.net.
   * Använda Azure PowerShell

     Använd följande Azure PowerShell-skript för att registrera den **Get-ClusterDetail** som kan användas för att returnera DNS-suffix:

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

     När Azure PowerShell-skriptet har körts, kan du använda kommandot för att returnera DNS-suffix genom att använda den **Get-ClusterDetail** funktion. Ange HDInsight HBase-klusternamnet, namn på serveradministratör och lösenordet för serveradministratören när du använder det här kommandot.

    ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
    ```

     Det här kommandot returnerar DNS-suffixet. Till exempel **yourclustername.b4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Kontrollera att den virtuella datorn kan kommunicera med HBase-kluster genom att använda kommandot `ping headnode0.<dns suffix>` från den virtuella datorn. Exempel: ping headnode0.mycluster.b1.cloudapp.net.

Om du vill använda den här informationen i ett Java-program, kan du följa stegen i [använder Apache Maven för att skapa Java-program som använder Apache HBase med HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) att skapa ett program. Om du vill att programmet ansluta till en fjärrserver HBase, ändra den **hbase-site.xml** filen i det här exemplet använder det fullständiga Domännamnet för Zookeeper. Exempel:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]
> Mer information om namnmatchning i Azure virtuella nätverk, inklusive hur du använder en egen DNS-server finns i [Name Resolution (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
>
>

## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att skapa ett Apache HBase-kluster. Du kan läsa mer här:

* [Kom igång med HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Använda tomma kantnoder i HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurera Apache HBase-replikering i HDInsight](apache-hbase-replication.md)
* [Skapa Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Kom igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

