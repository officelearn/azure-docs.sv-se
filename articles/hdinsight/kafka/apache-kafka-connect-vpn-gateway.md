---
title: Ansluta till Kafka med virtuella nätverk - Azure HDInsight
description: Lär dig hur du ansluter direkt till Kafka på HDInsight via ett virtuellt Azure-nätverk. Lär dig hur du ansluter till Kafka från utvecklingsklienter med hjälp av en VPN-gateway eller från klienter i ditt lokala nätverk med hjälp av en VPN-gatewayenhet.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 36ff0d5f1fc96b2013555d37a869ebf629a22be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272128"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Ansluta till Apache Kafka på HDInsight via Azure Virtual Network

Lär dig hur du ansluter direkt till Apache Kafka på HDInsight via ett virtuellt Azure-nätverk. Det här dokumentet innehåller information om hur du ansluter till Kafka med hjälp av följande konfigurationer:

* Från resurser i ett lokalt nätverk. Den här anslutningen upprättas med hjälp av en VPN-enhet (programvara eller maskinvara) i det lokala nätverket.
* Från en utvecklingsmiljö med hjälp av en VPN-programvaruklient.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Arkitektur och planering

HDInsight tillåter inte direkt anslutning till Kafka via det offentliga internet. I stället måste Kafka-kunder (producenter och konsumenter) använda någon av följande anslutningsmetoder:

* Kör klienten i samma virtuella nätverk som Kafka på HDInsight. Den här konfigurationen används i [dokumentet Start with Apache Kafka på HDInsight.](apache-kafka-get-started.md) Klienten körs direkt på HDInsight-klusternoderna eller på en annan virtuell dator i samma nätverk.

* Anslut ett privat nätverk, till exempel det lokala nätverket, till det virtuella nätverket. Med den här konfigurationen kan klienter i det lokala nätverket arbeta direkt med Kafka. Så här aktiverar du den här konfigurationen:

  1. Skapa ett virtuellt nätverk.
  2. Skapa en VPN-gateway som använder en plats-till-plats-konfiguration. Konfigurationen som används i det här dokumentet ansluter till en VPN-gatewayenhet i ditt lokala nätverk.
  3. Skapa en DNS-server i det virtuella nätverket.
  4. Konfigurera vidarebefordran mellan DNS-servern i varje nätverk.
  5. Skapa en Kafka på HDInsight-kluster i det virtuella nätverket.

     Mer information finns i [avsnittet Anslut till Apache Kafka från ett lokalt nätverk.](#on-premises)

* Anslut enskilda datorer till det virtuella nätverket med hjälp av en VPN-gateway och VPN-klient. Så här aktiverar du den här konfigurationen:

  1. Skapa ett virtuellt nätverk.
  2. Skapa en VPN-gateway som använder en point-to-site-konfiguration. Den här konfigurationen kan användas med både Windows- och MacOS-klienter.
  3. Skapa en Kafka på HDInsight-kluster i det virtuella nätverket.
  4. Konfigurera Kafka för IP-annonsering. Med den här konfigurationen kan klienten ansluta med hjälp av mäklar-IP-adresser i stället för domännamn.
  5. Ladda ner och använd VPN-klienten på utvecklingssystemet.

     Mer information finns i avsnittet [Anslut till Apache Kafka med en VPN-klientsektion.](#vpnclient)

     > [!WARNING]  
     > Den här konfigurationen rekommenderas endast för utvecklingsändamål på grund av följande begränsningar:
     >
     > * Varje klient måste ansluta med hjälp av en VPN-programvaruklient.
     > * VPN-klienten skickar inte namnmatchningsbegäranden till det virtuella nätverket, så du måste använda IP-adressering för att kommunicera med Kafka. IP-kommunikation kräver ytterligare konfiguration på Kafka-klustret.

Mer information om hur du använder HDInsight i ett virtuellt nätverk finns i [Planera ett virtuellt nätverk för Azure HDInsight-kluster](../hdinsight-plan-virtual-network-deployment.md).

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a>Anslut till Apache Kafka från ett lokalt nätverk

Om du vill skapa ett Kafka-kluster som kommunicerar med ditt lokala nätverk följer du stegen i [Connect HDInsight till ditt lokala nätverksdokument.](./../connect-on-premises-network.md)

> [!IMPORTANT]  
> När du skapar HDInsight-klustret väljer du __Kafka-klustertypen.__

De här stegen skapar följande konfiguration:

* Azure Virtual Network
* VPN-gateway för plats till plats
* Azure Storage-konto (används av HDInsight)
* Kafka på HDInsight

Om du vill kontrollera att en Kafka-klient kan ansluta till klustret lokalt använder du stegen i avsnittet [Exempel: Python-klient.](#python-client)

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a>Anslut till Apache Kafka med en VPN-klient

Använd stegen i det här avsnittet för att skapa följande konfiguration:

* Azure Virtual Network
* VPN-gateway för punkt till plats
* Azure Storage-konto (används av HDInsight)
* Kafka på HDInsight

1. Följ stegen i dokumentet [Arbeta med självsignerade certifikat för point-to-site-anslutningar.](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) Det här dokumentet skapar de certifikat som behövs för gatewayen.

2. Öppna en PowerShell-prompt och använd följande kod för att logga in på din Azure-prenumeration:

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

4. Använd följande kod för att skapa Azure-resursgruppen och det virtuella nätverket:

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
    > Det kan ta flera minuter innan den här processen har slutförts.

5. Använd följande kod för att skapa Azure Storage Account och blob-behållaren:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

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
   > Den här processen tar cirka 15 minuter att slutföra.

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurera Kafka för IP-annonsering

Som standard returnerar Apache Zookeeper domännamnet för Kafka-mäklare till klienter. Den här konfigurationen fungerar inte med VPN-programvaruklienten, eftersom den inte kan använda namnmatchning för entiteter i det virtuella nätverket. För den här konfigurationen använder du följande steg för att konfigurera Kafka för att annonsera IP-adresser i stället för domännamn:

1. Via en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net`. Ersätt `CLUSTERNAME` med namnet på Kafka på HDInsight-klustret.

    När du uppmanas till det använder du användarnamnet och lösenordet för HTTPS för klustret. Ambari-webbgränssnittet för klustret visas.

2. Om du vill visa information om Kafka väljer du __Kafka__ i listan till vänster.

    ![Servicelista med Kafka markerad](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Om du vill visa Kafka-konfigurationen väljer du __Configs__ uppifrån mitten.

    ![Konfiguration av Apache Ambari-tjänster](./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png)

4. Om du vill hitta __kafka-env-konfigurationen__ anger du `kafka-env` fältet __Filter__ längst upp till höger.

    ![Kafka konfiguration, för kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Om du vill konfigurera Kafka för att annonsera IP-adresser lägger du till följande text längst ned i __kafka-env-mallfältet:__

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Om du vill konfigurera gränssnittet som Kafka lyssnar på anger du `listeners` i fältet __Filter__ längst upp till höger.

7. Om du vill konfigurera Kafka för att lyssna på alla `PLAINTEXT://0.0.0.0:9092`nätverksgränssnitt ändrar du värdet i __lyssnarfältet__ till .

8. Om du vill spara konfigurationsändringarna använder du knappen __Spara.__ Ange ett textmeddelande som beskriver ändringarna. Välj __OK__ när ändringarna har sparats.

    ![Apache Ambari spara konfiguration](./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png)

9. Om du vill förhindra fel vid omstart av Kafka använder du knappen __Serviceåtgärder__ och väljer __Aktivera underhållsläge__. Välj OK för att slutföra den här åtgärden.

    ![Serviceåtgärder, med aktiverat underhåll markerat](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Om du vill starta om Kafka använder du knappen __Starta om__ och väljer Starta om alla __som påverkas__. Bekräfta omstarten och använd sedan __OK-knappen__ när åtgärden är klar.

    ![Knappen Starta om med omstart markerad](./media/apache-kafka-connect-vpn-gateway/restart-required-button.png)

11. Om du vill inaktivera underhållsläge använder du knappen __Serviceåtgärder__ och väljer __Stäng av underhållsläge__. Välj **OK** för att slutföra den här åtgärden.

### <a name="connect-to-the-vpn-gateway"></a>Ansluta till VPN-gatewayen

Om du vill ansluta till VPN-gatewayen använder du avsnittet __Anslut till Azure__ i [anslutningsdokumentet Konfigurera en punkt till plats.](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect)

## <a name="example-python-client"></a><a id="python-client"></a>Exempel: Python-klient

Om du vill validera anslutningen till Kafka använder du följande steg för att skapa och köra en Python-producent och konsument:

1. Använd någon av följande metoder för att hämta det fullständigt kvalificerade domännamnet (FQDN) och IP-adresserna för noderna i Kafka-klustret:

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

    Det här skriptet förutsätter att det `$resourceGroupName` är namnet på Azure-resursgruppen som innehåller det virtuella nätverket.

    Spara den returnerade informationen för användning i nästa steg.

2. Använd följande för att installera [kafka-python-klienten:](https://kafka-python.readthedocs.io/)

    ```bash
    pip install kafka-python
    ```

3. Om du vill skicka data till Kafka använder du följande Python-kod:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Ersätt `'kafka_broker'` posterna med adresserna som returneras från steg 1 i det här avsnittet:

   * Om du använder en __VPN-klient för programvara__ersätter du posterna `kafka_broker` med ARBETSnodernas IP-adress.

   * Om du har __aktiverat namnmatchning via en anpassad DNS-server__ersätter du `kafka_broker` posterna med FQDN för arbetsnoderna.

     > [!NOTE]
     > Den här koden `test message` skickar `testtopic`strängen till ämnet . Standardkonfigurationen för Kafka på HDInsight är att skapa ämnet om det inte finns.

4. Om du vill hämta meddelandena från Kafka använder du följande Python-kod:

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

    Ersätt `'kafka_broker'` posterna med adresserna som returneras från steg 1 i det här avsnittet:

    * Om du använder en __VPN-klient för programvara__ersätter du posterna `kafka_broker` med ARBETSnodernas IP-adress.

    * Om du har __aktiverat namnmatchning via en anpassad DNS-server__ersätter du `kafka_broker` posterna med FQDN för arbetsnoderna.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder HDInsight med ett virtuellt nätverk finns i dokumentet [Planera en virtuell nätverksdistribution för Azure HDInsight-kluster.](../hdinsight-plan-virtual-network-deployment.md)

Mer information om hur du skapar ett Virtuellt Azure-nätverk med VPN-gateway för punkt till plats finns i följande dokument:

* [Konfigurera en point-to-site-anslutning med Azure-portalen](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurera en point-to-site-anslutning med Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Mer information om hur du arbetar med Apache Kafka på HDInsight finns i följande dokument:

* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)
* [Använd spegling med Apache Kafka på HDInsight](apache-kafka-mirroring.md)
