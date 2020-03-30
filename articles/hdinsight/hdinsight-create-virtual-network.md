---
title: Skapa virtuella nätverk för Azure HDInsight-kluster
description: Lär dig hur du skapar ett virtuellt Azure-nätverk för att ansluta HDInsight till andra molnresurser eller resurser i ditt datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6fd23e3d41dda15b1ec439c1e8b02073722b8871
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272544"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Skapa virtuella nätverk för Azure HDInsight-kluster

Den här artikeln innehåller exempel och kodexempel för att skapa och konfigurera [Virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md) för användning med Azure HDInsight-kluster. Detaljerade exempel på att skapa nätverkssäkerhetsgrupper (NSG) och konfigurera DNS presenteras. 

Bakgrundsinformation om hur du använder virtuella nätverk med Azure HDInsight finns i [Planera ett virtuellt nätverk för Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Förutsättningar för kodexempel och exempel

Innan du kör något av kodexemplen i den här artikeln bör du ha en förståelse för TCP/IP-nätverk. Om du inte är bekant med TCP/IP-nätverk kontaktar du någon som gör ändringar i produktionsnätverk.

Andra förutsättningar för exemplen i den här artikeln är följande:

* Om du använder PowerShell måste du installera [AZ-modulen](https://docs.microsoft.com/powershell/azure/overview).
* Om du vill använda Azure CLI och ännu inte har installerat det läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Om du letar efter steg för steg-vägledning om hur du ansluter HDInsight till ditt lokala nätverk med hjälp av ett Virtuellt Azure-nätverk läser du [Anslut HDInsight till ditt lokala nätverksdokument.](connect-on-premises-network.md)

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Exempel: nätverkssäkerhetsgrupper med HDInsight

Exemplen i det här avsnittet visar hur du skapar regler för nätverkssäkerhetsgrupper som gör att HDInsight kan kommunicera med Azure-hanteringstjänster. Innan du använder exemplen justerar du IP-adresserna så att de matchar de för Azure-regionen som du använder. Du hittar den här informationen i [IP-adresser för HDInsight-hantering](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Mall för Azure Resource Management

Följande Resource Management-mall skapar ett virtuellt nätverk som begränsar inkommande trafik, men tillåter trafik från IP-adresser som krävs av HDInsight. Den här mallen skapar också ett HDInsight-kluster i det virtuella nätverket.

* [Distribuera ett säkert Virtuellt Azure-nätverk och ett HDInsight Hadoop-kluster](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Använd följande PowerShell-skript för att skapa ett virtuellt nätverk som begränsar inkommande trafik och tillåter trafik från IP-adresserna för regionen Nordeuropa.

> [!IMPORTANT]  
> Ändra IP-adresserna för `hdirule1` och `hdirule2` i det här exemplet för att matcha den Azure-region som du använder. Du hittar den här informationen [HDInsight management IP-adresser](hdinsight-management-ip-addresses.md).

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

Det här exemplet visar hur du lägger till regler för att tillåta inkommande trafik på de IP-adresser som krävs. Den innehåller ingen regel för att begränsa inkommande åtkomst från andra källor. Följande kod visar hur du aktiverar SSH-åtkomst från Internet:

```powershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

Följ följande steg för att skapa ett virtuellt nätverk som begränsar inkommande trafik, men tillåter trafik från IP-adresser som krävs av HDInsight.

1. Använd följande kommando för att skapa `hdisecure`en ny nätverkssäkerhetsgrupp med namnet . Ersätt `RESOURCEGROUP` med resursgruppen som innehåller Azure Virtual Network. Ersätt `LOCATION` med den plats (region) som gruppen skapades i.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    När gruppen har skapats får du information om den nya gruppen.

2. Använd följande för att lägga till regler i den nya nätverkssäkerhetsgruppen som tillåter inkommande kommunikation på port 443 från hälso- och hanteringstjänsten Azure HDInsight. Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller Azure Virtual Network.

    > [!IMPORTANT]  
    > Ändra IP-adresserna för `hdirule1` och `hdirule2` i det här exemplet för att matcha den Azure-region som du använder. Du hittar den här informationen i [IP-adresser för HDInsight-hantering](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Om du vill hämta den unika identifieraren för den här nätverkssäkerhetsgruppen använder du följande kommando:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Det här kommandot returnerar ett värde som liknar följande text:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. Använd följande kommando för att tillämpa nätverkssäkerhetsgruppen på ett undernät. Ersätt `GUID` värdena och `RESOURCEGROUP` med de som returnerats från föregående steg. Ersätt `VNETNAME` `SUBNETNAME` och med det virtuella nätverksnamn och det undernätsnamn som du vill skapa.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    När det här kommandot är klart kan du installera HDInsight i det virtuella nätverket.


Dessa steg öppnar endast åtkomst till hälso- och hanteringstjänsten HDInsight i Azure-molnet. All annan åtkomst till HDInsight-klustret utanför det virtuella nätverket blockeras. Om du vill aktivera åtkomst utanför det virtuella nätverket måste du lägga till ytterligare regler för nätverkssäkerhetsgrupp.

Följande kod visar hur du aktiverar SSH-åtkomst från Internet:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a>Exempel: DNS-konfiguration

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Namnmatchning mellan ett virtuellt nätverk och ett anslutet lokalt nätverk

I det här exemplet görs följande antaganden:

* Du har ett Virtuellt Azure-nätverk som är anslutet till ett lokalt nätverk med hjälp av en VPN-gateway.

* Den anpassade DNS-servern i det virtuella nätverket kör Linux eller Unix som operativsystem.

* [Bindning](https://www.isc.org/downloads/bind/) är installerat på den anpassade DNS-servern.

På den anpassade DNS-servern i det virtuella nätverket:

1. Använd antingen Azure PowerShell eller Azure CLI för att hitta DNS-suffixet för det virtuella nätverket:

    Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller det virtuella nätverket och ange sedan kommandot:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. På den anpassade DNS-servern för det virtuella nätverket använder `/etc/bind/named.conf.local` du följande text som innehållet i filen:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Ersätt `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` värdet med DNS-suffixet i det virtuella nätverket.

    Den här konfigurationen dirigerar alla DNS-begäranden för DNS-suffixet i det virtuella nätverket till Azure rekursiv resolver.

2. På den anpassade DNS-servern för det virtuella nätverket använder `/etc/bind/named.conf.options` du följande text som innehållet i filen:

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
    
    * Ersätt `10.0.0.0/16` värdet med IP-adressintervallet för det virtuella nätverket. Med den här posten tillåts namnmatchningsbegäranden adresser inom det här intervallet.

    * Lägg till IP-adressintervallet för det `acl goodclients { ... }` lokala nätverket i avsnittet .  med posten tillåts namnmatchningsbegäranden från resurser i det lokala nätverket.
    
    * Ersätt värdet `192.168.0.1` med IP-adressen för den lokala DNS-servern. Den här posten dirigerar alla andra DNS-begäranden till den lokala DNS-servern.

3. Om du vill använda konfigurationen startar du om Bind. Till exempel `sudo service bind9 restart`.

4. Lägg till en villkorlig vidarebefordrare på den lokala DNS-servern. Konfigurera den villkorliga vidarebefordraren för att skicka begäranden för DNS-suffixet från steg 1 till den anpassade DNS-servern.

    > [!NOTE]  
    > Mer information om hur du lägger till en villkorlig vidarebefordrare finns i dokumentationen för DNS-programvaran.

När du har slutfört de här stegen kan du ansluta till resurser i båda nätverken med fullständigt kvalificerade domännamn (FQDN). Du kan nu installera HDInsight i det virtuella nätverket.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Namnmatchning mellan två anslutna virtuella nätverk

I det här exemplet görs följande antaganden:

* Du har två Virtuella Azure-nätverk som är anslutna med antingen en VPN-gateway eller peering.

* Den anpassade DNS-servern i båda nätverken kör Linux eller Unix som operativsystem.

* [Bindning](https://www.isc.org/downloads/bind/) är installerat på anpassade DNS-servrar.

1. Använd antingen Azure PowerShell eller Azure CLI för att hitta DNS-suffixet för båda virtuella nätverken:

    Ersätt `RESOURCEGROUP` med namnet på resursgruppen som innehåller det virtuella nätverket och ange sedan kommandot:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Använd följande text som innehållet `/etc/bind/named.config.local` i filen på den anpassade DNS-servern. Gör den här ändringen på den anpassade DNS-servern i båda virtuella nätverken.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Ersätt `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` värdet med DNS-suffixet i det __andra__ virtuella nätverket. Den här posten dirigerar begäranden för DNS-suffixet i fjärrnätverket till den anpassade DNS i nätverket.

3. På de anpassade DNS-servrarna i båda virtuella nätverk `/etc/bind/named.conf.options` använder du följande text som innehållet i filen:

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
    
   Ersätt `10.0.0.0/16` värdena och `10.1.0.0/16` med IP-adressintervallen för dina virtuella nätverk. Med den här posten kan resurser i varje nätverk göra begäranden av DNS-servrarna.

    Alla begäranden som inte är för DNS-suffixen i de virtuella nätverken (till exempel microsoft.com) hanteras av Azure rekursiv resolver.

4. Om du vill använda konfigurationen startar du om Bind. Till exempel `sudo service bind9 restart` på båda DNS-servrarna.

När du har slutfört de här stegen kan du ansluta till resurser i det virtuella nätverket med fullständigt kvalificerade domännamn (FQDN). Du kan nu installera HDInsight i det virtuella nätverket.

## <a name="next-steps"></a>Nästa steg

* Ett exempel från på sluten tid för att konfigurera HDInsight för att ansluta till ett lokalt nätverk finns i [Anslut HDInsight till ett lokalt nätverk](./connect-on-premises-network.md).
* Information om hur du konfigurerar Apache HBase-kluster i virtuella Azure-nätverk finns i [Skapa Apache HBase-kluster på HDInsight i Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Information om konfiguration av Apache HBase-georeplikering finns i [Konfigurera Apache HBase-klusterreplikering i virtuella Azure-nätverk](hbase/apache-hbase-replication.md).
* Mer information om virtuella Azure-nätverk finns i [översikten över Virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md).

* Mer information om nätverkssäkerhetsgrupper finns i [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

* Mer information om användardefinierade vägar finns i [Användardefinierade vägar och IP-vidarebefordran](../virtual-network/virtual-networks-udr-overview.md).
