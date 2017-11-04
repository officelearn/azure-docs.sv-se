---
title: "Skapa HBase-kluster i ett virtuellt nätverk - Azure | Microsoft Docs"
description: "Komma igång med HBase i Azure HDInsight. Lär dig hur du skapar kluster i HDInsight HBase på Azure Virtual Network."
keywords: 
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 8de8e446-f818-4e61-8fad-e9d38421e80d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/03/2017
ms.author: jgao
ms.openlocfilehash: 1d3dba645acf51a7dcdd42fa23c82db962244b62
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="create-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Skapa HBase-kluster i HDInsight i Azure-nätverk
Lär dig hur du skapar Azure HDInsight HBase-kluster i en [Azure Virtual Network][1].

Med virtuell nätverksintegration kan HBase-kluster bara distribueras till samma virtuella nätverk som dina program så att program kan kommunicera med HBase direkt. Fördelarna innefattar:

* Direktanslutning av webbprogrammet för noder i HBase-kluster, vilket möjliggör kommunikation via HBase Java fjärrproceduranrop anropa API: er (RPC).
* Bättre prestanda genom att inte låta trafiken gå över flera gateways och belastningsutjämnare.
* Möjligheten att behandla känslig information på ett säkrare sätt utan att exponera en offentlig slutpunkt.

### <a name="prerequisites"></a>Krav
Innan du börjar den här självstudiekursen behöver du följande:

* **en Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **En arbetsstation med Azure PowerShell**. Se [installera och använda Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-hbase-cluster-into-virtual-network"></a>Skapa HBase-kluster i virtuellt nätverk
I det här avsnittet skapar du en Linux-baserade HBase-kluster med beroende Azure Storage-konto i ett virtuellt Azure-nätverk med hjälp av en [Azure Resource Manager-mall](../../azure-resource-manager/resource-group-template-deploy.md). För andra metoder för att skapa kluster och förstå inställningarna, se [skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur du använder en mall för att skapa Hadoop-kluster i HDInsight finns [skapa Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]
> Vissa egenskaper är hårdkodat i mallen. Exempel:
>
> * **Plats**: östra USA 2
> * **Kluster av version**: 3,6
> * **Klustret worker nodsantalet**: 2
> * **Storage-konto som standard**: en unik sträng
> * **Virtuella nätverksnamnet**: &lt;klusternamn >-vnet
> * **Virtuella nätverkets adressutrymme**: 10.0.0.0/16
> * **Undernätnamnet**: Undernät1
> * **Adressintervall för gatewayundernät**: 10.0.0.0/24
>
> &lt;Klusternamn > ersättas med klustrets namn som du anger när du använder mallen.
>
>

1. Klicka på följande bild för att öppna mallen i Azure Portal. Mallen finns i [Azure QuickStart mallar](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Från den **anpassad distribution** bladet, ange följande egenskaper:

   * **Prenumerationen**: Välj en Azure-prenumeration används för att skapa HDInsight-klustret, det beroende lagringskontot och virtuella Azure-nätverket.
   * **Resursgruppen**: Välj **Skapa nytt**, och ange namn på en ny resursgrupp.
   * **Plats**: Välj en plats för resursgruppen.
   * **Klusternamn**: Ange ett namn för det Hadoop-klustret som ska skapas.
   * **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard **admin**.
   * **SSH-användarnamn och lösenord**: Standardanvändarnamnet är **sshuser**.  Du kan byta namn.
   * **Jag samtycker till villkoren och de villkor som anges ovan**: (Välj)
3. Klicka på **Köp**. Det tar cirka 20 minuter att skapa ett kluster. När klustret har skapats klickar du på klusterbladet i portalen för att öppna den.

När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Instruktioner för att ta bort ett kluster finns i [hantera Hadoop-kluster i HDInsight med hjälp av Azure portal](../hdinsight-administer-use-management-portal.md#delete-clusters).

För att börja arbeta med din nya HBase-kluster måste du använda procedurer som påträffas i [komma igång med HBase med Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Ansluta till HBase-kluster med HBase Java RPC-API: er
1. Skapa en infrastruktur som en tjänst (IaaS) virtuell dator i samma virtuella Azure-nätverket och samma undernät. Anvisningar om hur du skapar en ny virtuell IaaS-dator finns [skapa en virtuell dator kör Windows Server](../../virtual-machines/windows/quick-create-portal.md). När följa stegen i det här dokumentet, måste du använda följande värden för nätverkskonfigurationen:

   * **Virtuellt nätverk**: &lt;klusternamn >-vnet
   * **Undernät**: Undernät1

   > [!IMPORTANT]
   > Ersätt &lt;klusternamn > med det namn som används när du skapar HDInsight-kluster i föregående steg.
   >
   >

   Med hjälp av dessa värden, placeras den virtuella datorn i samma virtuella nätverk och undernät som HDInsight-klustret. Den här konfigurationen gör att de kan kommunicera direkt med varandra. Det är ett sätt att skapa ett HDInsight-kluster med en tom kantnod. Kantnoden kan användas för att hantera klustret.  Mer information finns i [använda tom edge-noder i HDInsight](../hdinsight-apps-use-edge-node.md).

2. När du använder ett Java-program för att fjärransluta till HBase, måste du använda det fullständigt kvalificerade domännamnet (FQDN). För att fastställa detta måste du hämta det anslutningsspecifika DNS-suffixet i HBase-kluster. Du kan använda någon av följande metoder för att göra det:

   * Använd en webbläsare för att ringa Ambari:

     Bläddra till https://&lt;klusternamn >.azurehdinsight.net/api/v1/clusters/&lt;klusternamn > / värd? minimal_response = true. Den blir en JSON-fil med DNS-suffix.
   * Använda Ambari-webbplats:

     1. Bläddra till https://&lt;klusternamn >. azurehdinsight.net.
     2. Klicka på **värdar** på den översta menyn.
   * Använd Curl till REST-anrop:

    ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
    ```

     Hitta ”värddatornamn” post i de JavaScript Object Notation (JSON) data som returneras. Den innehåller FQDN för noder i klustret. Exempel:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Del av den domän namn som början med klustrets namn är DNS-suffix. Till exempel mycluster.b1.cloudapp.net.
   * Använda Azure PowerShell

     Använd följande Azure PowerShell-skript för att registrera den **Get-ClusterDetail** funktion som kan användas för att returnera DNS-suffix:

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

     När du har kört skriptet Azure PowerShell använder du följande kommando för att returnera DNS-suffix genom att använda den **Get-ClusterDetail** funktion. Ange klusternamnet i HDInsight HBase, admin namn och lösenord för administratör när du använder det här kommandot.

    ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
    ```

     Det här kommandot returnerar DNS-suffix. Till exempel **yourclustername.b4.internal.cloudapp.net**.


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

Kontrollera att den virtuella datorn kan kommunicera med HBase-kluster genom att använda kommandot `ping headnode0.<dns suffix>` från den virtuella datorn. Till exempel ping headnode0.mycluster.b1.cloudapp.net.

Om du vill använda den här informationen i ett Java-program, kan du följa stegen i [Använd Maven för att skapa Java-program som använder HBase med HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) att skapa ett program. Om du vill att programmet ansluter till en fjärrserver HBase, ändra den **hbase-site.xml** filen i det här exemplet använder det fullständiga Domännamnet för Zookeeper. Exempel:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]
> Mer information om namnmatchning i Azure virtuella nätverk, inklusive hur du använder egna DNS-servern finns [Name Resolution (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
>
>

## <a name="next-steps"></a>Nästa steg
I kursen får du har lärt dig hur du skapar ett HBase-kluster. Du kan läsa mer här:

* [Komma igång med HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Använd tom edge-noder i HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurera HBase-replikering i HDInsight](apache-hbase-replication.md)
* [Skapa Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Komma igång med HBase med Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md)

[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

