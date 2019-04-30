---
title: Ansluta till Kafka med hjälp av virtuella nätverk – Azure HDInsight
description: Lär dig mer om att ansluta direkt till Kafka på HDInsight med Azure Virtual Network. Lär dig hur du ansluter till Kafka från utveckling-klienter som använder en VPN-gateway eller från klienter i ditt lokala nätverk med hjälp av en VPN-gateway-enhet.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 93b5aeafafdc6ab7ee233f6360bb5e09f45b387f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115360"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Ansluta till Apache Kafka på HDInsight via Azure-nätverk

Lär dig mer om att ansluta direkt till Apache Kafka på HDInsight med Azure Virtual Network. Det här dokumentet innehåller information om hur du ansluter till Kafka med följande konfigurationer:

* Från resurser i ett lokalt nätverk. Den här anslutningen har upprättats med hjälp av en VPN-enhet (programvara eller maskinvara) i det lokala nätverket.
* Med hjälp av en VPN-klientprogrammet från en utvecklingsmiljö.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Arkitektur och planering

HDInsight tillåter inte direkt anslutning till Kafka via offentligt internet. Kafka-klienter (producenter och konsumenter) måste i stället använda en av följande anslutningsmetoder:

* Kör klienten i samma virtuella nätverk som Kafka på HDInsight. Den här konfigurationen används i den [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md) dokumentet. Klienten körs direkt på noderna i HDInsight-klustret eller på en annan virtuell dator i samma nätverk.

* Ansluta ett privat nätverk, till exempel ditt lokala nätverk till det virtuella nätverket. Den här konfigurationen kan klienter i det lokala nätverket att arbeta direkt med Kafka. Om du vill aktivera den här konfigurationen måste du utföra följande uppgifter:

  1. Skapa ett virtuellt nätverk.
  2. Skapa en VPN-gateway som använder en plats-till-plats-konfiguration. Konfigurationen används i det här dokumentet ansluter till en VPN-gateway-enhet i ditt lokala nätverk.
  3. Skapa en DNS-server i det virtuella nätverket.
  4. Konfigurera vidarebefordran mellan DNS-servern i varje nätverk.
  5. Skapa en Kafka på HDInsight-kluster i det virtuella nätverket.

     Mer information finns i den [Anslut till Apache Kafka från ett lokalt nätverk](#on-premises) avsnittet. 

* Ansluta enskilda datorer till det virtuella nätverket med en VPN-gateway och VPN-klienten. Om du vill aktivera den här konfigurationen måste du utföra följande uppgifter:

  1. Skapa ett virtuellt nätverk.
  2. Skapa en VPN-gateway som använder en punkt-till-plats-konfiguration. Den här konfigurationen kan användas med både Windows och MacOS klienter.
  3. Skapa en Kafka på HDInsight-kluster i det virtuella nätverket.
  4. Konfigurera Kafka för IP-annonsering. Den här konfigurationen gör att klienten kan ansluta med broker IP-adresser i stället för domännamn.
  5. Ladda ned och använda VPN-klienten på utvecklingssystemet.

     Mer information finns i den [Anslut till Apache Kafka med en VPN-klient](#vpnclient) avsnittet.

     > [!WARNING]  
     > Den här konfigurationen rekommenderas endast för utveckling på grund av följande begränsningar:
     >
     > * Varje klient måste ansluta med hjälp av en VPN-klientprogrammet.
     > * VPN-klienten skickar inte namnmatchning till det virtuella nätverket, så du måste använda IP-adresser för att kommunicera med Kafka. IP-kommunikation kräver ytterligare konfiguration av Kafka-klustret.

Mer information om hur du använder HDInsight i ett virtuellt nätverk finns i [utöka HDInsight med hjälp av Azure Virtual Networks](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Ansluta till Apache Kafka från ett lokalt nätverk

Om du vill skapa ett Kafka-kluster som kommunicerar med ditt lokala nätverk, följer du stegen i den [ansluta HDInsight till det lokala nätverket](./../connect-on-premises-network.md) dokumentet.

> [!IMPORTANT]  
> När du skapar HDInsight-kluster, Välj den __Kafka__ typ av kluster.

De här stegen skapar följande konfiguration:

* Azure Virtual Network
* Plats-till-plats VPN-gateway
* Azure Storage-konto (används av HDInsight)
* Kafka på HDInsight

För att kontrollera att en Kafka-klient kan ansluta till klustret från en lokal plats, använder du stegen i den [exempel: Python-klienten](#python-client) avsnittet.

## <a id="vpnclient"></a> Anslut till Apache Kafka med en VPN-klient

Använd stegen i det här avsnittet för att skapa följande konfiguration:

* Azure Virtual Network
* Punkt-till-plats VPN-gateway
* Azure Storage-konto (används av HDInsight)
* Kafka på HDInsight

1. Följ stegen i den [arbeta med självsignerade certifikat för punkt-till-plats-anslutningar](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) dokumentet. Det här dokumentet skapar certifikat som krävs för gatewayen.

2. Öppna en PowerShell-kommandotolk och Använd följande kod för att logga in på din Azure-prenumeration:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Använd följande kod för att skapa variabler som innehåller konfigurationsinformation:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Använd följande kod för att skapa Azure-resursgrupp och virtuellt nätverk:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > Det kan ta flera minuter innan den här processen skulle slutföras.

5. Använd följande kod för att skapa Azure Storage-konto och blob-behållaren:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Använd följande kod för att skapa HDInsight-klustret:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Den här processen tar ungefär 15 minuter för att slutföra.

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurera Kafka för IP-annonsering

Som standard returnerar Apache Zookeeper domännamnet för asynkrona meddelandeköer i Kafka till klienter. Den här konfigurationen fungerar inte med VPN-klientprogrammet som den inte kan använda namnmatchning för entiteter i det virtuella nätverket. Använd följande steg för att konfigurera Kafka att annonsera IP-adresser i stället för domännamn för den här konfigurationen:

1. Via en webbläsare går du till https://CLUSTERNAME.azurehdinsight.net. Ersätt __CLUSTERNAME__ med namnet på Kafka på HDInsight-klustret.

    När du uppmanas, använda HTTPS-användarnamn och lösenord för klustret. Ambari-Webbgränssnittet för klustret visas.

2. Om du vill visa information om Kafka, Välj __Kafka__ i listan till vänster.

    ![Tjänstlista med Kafka markerat](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Om du vill visa Kafka konfiguration, Välj __Peeringkonfigurationer__ från överst i mitten.

    ![Peeringkonfigurationer länkar för Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Att hitta den __kafka-env__ konfiguration, ange `kafka-env` i den __Filter__ i det övre högra hörnet.

    ![Kafka-konfigurationen för kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Om du vill konfigurera Kafka att annonsera IP-adresser, lägger du till följande text till slutet av den __kafka-env-template__ fält:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Om du vill konfigurera det gränssnitt som Kafka lyssnar på, ange `listeners` i den __Filter__ i det övre högra hörnet.

7. Om du vill konfigurera Kafka för att lyssna på alla nätverksgränssnitt, ändra värdet i den __lyssnare__ fältet `PLAINTEXT://0.0.0.0:9092`.

8. Om du vill spara konfigurationsändringarna, använda den __spara__ knappen. Ange ett textmeddelande som beskriver ändringarna. Välj __OK__ när ändringarna har sparats.

    ![Spara konfiguration](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. För att förhindra fel när du startar om Kafka, använda den __tjänståtgärder__ och välj __aktivera på underhållsläge__. Välj OK om du vill slutföra den här åtgärden.

    ![Tjänståtgärder, med aktivera Underhåll markerat](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Om du vill starta om Kafka, använda den __starta om__ och välj __starta om alla påverkade__. Bekräfta omstarten, och sedan använda den __OK__ knappen när åtgärden har slutförts.

    ![Starta om knappen om alla påverkade markerad](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Om du vill inaktivera underhållsläge, använda den __tjänståtgärder__ och välj __stänga av underhållsläge__. Välj **OK** att slutföra åtgärden.

### <a name="connect-to-the-vpn-gateway"></a>Ansluta till VPN-gateway

Om du vill ansluta till VPN-gateway den __Anslut till Azure__ delen av den [konfigurera en punkt-till-plats-anslutning](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) dokumentet.

## <a id="python-client"></a> Exempel: Python-klienten

Använd följande steg för att verifiera anslutningarna till Kafka, att skapa och köra en Python-producent och konsument:

1. Använd någon av följande metoder för att hämta det fullständigt kvalificerade domännamnet (FQDN) och IP-adresser för noderna i Kafka-klustret:

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

    Det här skriptet förutsätter att `$resourceGroupName` är namnet på Azure-resursgrupp som innehåller det virtuella nätverket.

    Spara den returnerade informationen för användning i nästa steg.

2. Använd följande för att installera den [kafka-python](https://kafka-python.readthedocs.io/) klienten:

        pip install kafka-python

3. Använd följande Python-kod för att skicka data till Kafka:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Ersätt den `'kafka_broker'` poster med adresser som returnerades från steg 1 i det här avsnittet:

   * Om du använder en __programvara VPN-klienten__, ersätter den `kafka_broker` poster med IP-adressen för din arbetsnoder.

   * Om du har __aktiverat namnmatchning via en anpassad DNS-server__, ersätter den `kafka_broker` poster med det fullständiga Domännamnet för arbetsnoderna.

     > [!NOTE]
     > Den här koden skickar strängen `test message` till ämnet `testtopic`. Standardkonfigurationen av Kafka på HDInsight är att skapa ämnet om det inte finns.

4. Använd följande Python-kod för att hämta meddelanden från Kafka:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Ersätt den `'kafka_broker'` poster med adresser som returnerades från steg 1 i det här avsnittet:

    * Om du använder en __programvara VPN-klienten__, ersätter den `kafka_broker` poster med IP-adressen för din arbetsnoder.

    * Om du har __aktiverat namnmatchning via en anpassad DNS-server__, ersätter den `kafka_broker` poster med det fullständiga Domännamnet för arbetsnoderna.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder HDInsight med ett virtuellt nätverk finns i den [utöka Azure HDInsight med hjälp av Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentet.

Mer information om hur du skapar ett virtuellt Azure nätverk med punkt-till-plats-VPN-gateway finns i följande dokument:

* [Konfigurera en punkt-till-plats-anslutning med Azure-portalen](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurera en punkt-till-plats-anslutning med Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Mer information om hur du arbetar med Apache Kafka på HDInsight finns i följande dokument:

* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)
* [Använda spegling med Apache Kafka på HDInsight](apache-kafka-mirroring.md)
