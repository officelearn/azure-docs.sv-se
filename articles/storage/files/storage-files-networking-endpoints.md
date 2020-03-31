---
title: Konfigurera slutpunkter för Azure Files-nätverk | Microsoft-dokument
description: En översikt över nätverksalternativ för Azure-filer.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cc487e8def180735606aa010651dde40ef93908e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082509"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurera slutpunkter för Azure Files-nätverk
Azure Files innehåller två huvudtyper av slutpunkter för åtkomst till Azure-filresurser: 
- Offentliga slutpunkter, som har en offentlig IP-adress och kan nås från var som helst i världen.
- Privata slutpunkter, som finns i ett virtuellt nätverk och har en privat IP-adress inifrån adressutrymmet i det virtuella nätverket.

Offentliga och privata slutpunkter finns på Azure-lagringskontot. Ett lagringskonto är en hanteringskonstruktion som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blob-behållare eller köer.

Den här artikeln fokuserar på hur du konfigurerar slutpunkterna för ett lagringskonto för åtkomst till Azure-filresursen direkt. De flesta detaljer som finns i det här dokumentet gäller även för hur Azure File Sync interoperates med offentliga och privata slutpunkter för lagringskontot, men mer information om nätverksöverväganden för en Azure File Sync-distribution finns i [konfigurera Azure File Sync proxy och brandväggsinställningar](storage-sync-files-firewall-and-proxy.md).

Vi rekommenderar att du läser [Azure Files-nätverksöverväganden](storage-files-networking-overview.md) innan du läser det här för att vägleda.

## <a name="prerequisites"></a>Krav
- Den här artikeln förutsätter att du redan har skapat en Azure-prenumeration. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Den här artikeln förutsätter att du redan har skapat en Azure-filresurs i ett lagringskonto som du vill ansluta till från lokala. Mer information om hur du skapar en Azure-filresurs finns i [Skapa en Azure-filresurs](storage-how-to-create-file-share.md).
- Om du tänker använda Azure PowerShell [installerar du den senaste versionen](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Om du tänker använda Azure CLI [installerar du den senaste versionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt
Om du skapar en privat slutpunkt för ditt lagringskonto kommer följande Azure-resurser att distribueras:

- **En privat slutpunkt:** En Azure-resurs som representerar lagringskontots privata slutpunkt. Du kan se detta som en resurs som ansluter ett lagringskonto och ett nätverksgränssnitt.
- **Ett nätverksgränssnitt (NIC)**: Nätverksgränssnittet som underhåller en privat IP-adress i det angivna virtuella nätverket/undernätet. Det här är exakt samma resurs som distribueras när du distribuerar en virtuell dator, men i stället för att tilldelas en virtuell dator ägs den av den privata slutpunkten.
- **En privat DNS-zon:** Om du aldrig har distribuerat en privat slutpunkt för det här virtuella nätverket tidigare distribueras en ny privat DNS-zon för det virtuella nätverket. En DNS A-post skapas också för lagringskontot i den här DNS-zonen. Om du redan har distribuerat en privat slutpunkt i det här virtuella nätverket läggs en ny A-post för lagringskontot till i den befintliga DNS-zonen. Det är valfritt att distribuera en DNS-zon, men rekommenderas starkt, och krävs om du monterar dina Azure-filresurser med ett AD-tjänsthuvudnamn eller använder FileREST API.Deploying a DNS zone is optional, however highly recommended, and required if you are mounting your Azure file shares with an AD service principal or using the FileREST API.

> [!Note]  
> Den här artikeln använder DNS-suffixet för `core.windows.net`lagringskontot för Azure Public-regionerna . Den här kommentaren gäller även Azure Sovereign-moln som Azure US Government-molnet och Azure China-molnet – bara ersätta lämpliga suffix för din miljö. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigera till det lagringskonto som du vill skapa en privat slutpunkt för. I innehållsförteckningen för lagringskontot väljer du **Privata slutpunktsanslutningar**och sedan **+ Privat slutpunkt** för att skapa en ny privat slutpunkt. 

![En skärmbild av objektet för privata slutpunktsanslutningar i innehållsförteckningen för lagringskonto](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

Den resulterande guiden har flera sidor att slutföra.

I bladet **Grunderna** väljer du önskad resursgrupp, namn och region för din privata slutpunkt. Dessa kan vara vad du vill, de behöver inte matcha lagringskontot i alla fall, även om du måste skapa den privata slutpunkten i samma region som det virtuella nätverk som du vill skapa den privata slutpunkten i.

![En skärmbild av avsnittet Grunderna i avsnittet skapa privat slutpunkt](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

I **resursbladet** väljer du alternativknappen för **Anslut till en Azure-resurs i katalogen**. Under **Resurstyp**väljer du **Microsoft.Storage/storageAccounts** för resurstypen. Fältet **Resurs** är lagringskontot med den Azure-filresurs som du vill ansluta till. Målunderresurs är **fil**, eftersom detta är för Azure-filer.

**Med bladet Konfiguration** kan du välja det specifika virtuella nätverk och det undernät som du vill lägga till den privata slutpunkten i. Välj det virtuella nätverk som du skapade ovan. Du måste välja ett distinkt undernät från undernätet som du lade till tjänstslutpunkten i ovan. Konfigurationsbladet innehåller också information för att skapa/uppdatera den privata DNS-zonen. Vi rekommenderar att `privatelink.file.core.windows.net` du använder standardzonen.

![En skärmbild av avsnittet Konfiguration](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Klicka på **Granska + skapa** för att skapa den privata slutpunkten. 

Om du har en virtuell dator inuti det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen [här,](storage-files-networking-dns.md)kan du testa att din privata slutpunkt har konfigurerats korrekt genom att köra följande kommandon från PowerShell, kommandoraden eller terminalen (fungerar för Windows, Linux eller macOS). Du måste `<storage-account-name>` ersätta med lämpligt lagringskontonamn:

```
nslookup <storage-account-name>.file.core.windows.net
```

Om allt har fungerat, bör du se `192.168.0.5` följande utdata, var är den privata IP-adressen för den privata slutpunkten i ditt virtuella nätverk (utdata som visas för Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Om du vill skapa en privat slutpunkt för ditt lagringskonto måste du först hämta en referens till ditt lagringskonto och det virtuella nätverksundernätet som du vill lägga till den privata slutpunkten till. Ersätt `<storage-account-resource-group-name>` `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, `<vnet-subnet-name>` , och nedan:

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group-name>"
$storageAccountName = "<storage-account-name>"
$virtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$virtualNetworkName = "<vnet-name>"
$subnetName = "<vnet-subnet-name>"

# Get storage account reference, and throw error if it doesn't exist
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageAccount) {
    $errorMessage = "Storage account $storageAccountName not found "
    $errorMessage += "in resource group $storageAccountResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get virtual network reference, and throw error if it doesn't exist
$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $virtualNetworkName `
        -ErrorAction SilentlyContinue

if ($null -eq $virtualNetwork) {
    $errorMessage = "Virtual network $virtualNetworkName not found "
    $errorMessage += "in resource group $virtualNetworkResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get reference to virtual network subnet, and throw error if it doesn't exist
$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $virtualNetworkName." `
            -ErrorAction Stop
}
```

Om du vill skapa en privat slutpunkt måste du skapa en privat länktjänstanslutning till lagringskontot. Den privata länktjänstanslutningen är en indata till skapandet av den privata slutpunkten. 

```PowerShell
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork | Out-Null

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageAccountName-Connection" `
        -PrivateLinkServiceId $storageAccount.Id `
        -GroupId "file"

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name "$storageAccountName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

Genom att skapa en privat AZURE DNS-zon kan `storageaccount.file.core.windows.net` det ursprungliga namnet på lagringskontot matchas till den privata IP-adressen i det virtuella nätverket. Även om det är valfritt med tanke på att skapa en privat slutpunkt, krävs det uttryckligen för att montera Azure-filresursen med hjälp av ett AD-användarobjekt eller åtkomst via REST API.  

```PowerShell
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$storageAccountSuffix = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
$dnsZoneName = "privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
$dnsZone = Get-AzPrivateDnsZone | `
    Where-Object { $_.Name -eq $dnsZoneName } | `
    Where-Object {
        $privateDnsLink = Get-AzPrivateDnsVirtualNetworkLink `
                -ResourceGroupName $_.ResourceGroupName `
                -ZoneName $_.Name `
                -ErrorAction SilentlyContinue
        
        $privateDnsLink.VirtualNetworkId -eq $virtualNetwork.Id
    }

if ($null -eq $dnsZone) {
    # No matching DNS zone attached to virtual network, so create new one.
    $dnsZone = New-AzPrivateDnsZone `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsZoneName `
            -ErrorAction Stop

    $privateDnsLink = New-AzPrivateDnsVirtualNetworkLink `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name "$virtualNetworkName-DnsLink" `
            -VirtualNetworkId $virtualNetwork.Id `
            -ErrorAction Stop
}
```

Nu när du har en referens till den privata DNS-zonen måste du skapa en A-post för ditt lagringskonto.

```PowerShell
$privateEndpointIP = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object @{ 
        Name = "NetworkInterfaces"; 
        Expression = { Get-AzNetworkInterface -ResourceId $_.Id } 
    } | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateIpAddress

$privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $privateEndpointIP

New-AzPrivateDnsRecordSet `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $storageAccountName `
        -RecordType A `
        -ZoneName $dnsZoneName `
        -Ttl 600 `
        -PrivateDnsRecords $privateDnsRecordConfig `
        -ErrorAction Stop | `
    Out-Null
```

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen [här,](storage-files-networking-dns.md)kan du testa att den privata slutpunkten har konfigurerats korrekt med följande kommandon:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.File) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Om allt har fungerat har fungerat bör du `192.168.0.5` se följande utdata, där är den privata IP-adressen för den privata slutpunkten i det virtuella nätverket:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill skapa en privat slutpunkt för ditt lagringskonto måste du först hämta en referens till ditt lagringskonto och det virtuella nätverksundernätet som du vill lägga till den privata slutpunkten till. Ersätt `<storage-account-resource-group-name>` `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, `<vnet-subnet-name>` , och nedan:

```bash
storageAccountResourceGroupName="<storage-account-resource-group-name>"
storageAccountName="<storage-account-name>"
virtualNetworkResourceGroupName="<vnet-resource-group-name>"
virtualNetworkName="<vnet-name>"
subnetName="<vnet-subnet-name>"

# Get storage account ID 
storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

# Get virtual network ID
virtualNetwork=$(az network vnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --name $virtualNetworkName \
        --query "id" | \
    tr -d '"')

# Get subnet ID
subnet=$(az network vnet subnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --vnet-name $virtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Om du vill skapa en privat slutpunkt måste du först se till att undernätets privata slutpunktsnätverksprincip är inaktiverad. Sedan kan du skapa en `az network private-endpoint create` privat slutpunkt med kommandot

```bash
# Disable private endpoint network policies
az network vnet subnet update \
        --ids $subnet \
        --disable-private-endpoint-network-policies \
        --output none

# Get virtual network location
region=$(az network vnet show \
        --ids $virtualNetwork \
        --query "location" | \
    tr -d '"')

# Create a private endpoint
privateEndpoint=$(az network private-endpoint create \
        --resource-group $virtualNetworkResourceGroupName \
        --name "$storageAccountName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageAccount \
        --group-ids "file" \
        --connection-name "$storageAccountName-Connection" \
        --query "id" | \
    tr -d '"')
```

Genom att skapa en privat AZURE DNS-zon kan `storageaccount.file.core.windows.net` det ursprungliga namnet på lagringskontot matchas till den privata IP-adressen i det virtuella nätverket. Även om det är valfritt med tanke på att skapa en privat slutpunkt, krävs det uttryckligen för att montera Azure-filresursen med hjälp av ett AD-användarobjekt eller åtkomst via REST API.  

```bash
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
storageAccountSuffix=$(az cloud show \
        --query "suffixes.storageEndpoint" | \
    tr -d '"')

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
dnsZoneName="privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

for possibleDnsZone in $possibleDnsZones
do
    possibleResourceGroupName=$(az resource show \
            --ids $possibleDnsZone \
            --query "resourceGroup" | \
        tr -d '"')
    
    link=$(az network private-dns link vnet list \
            --resource-group $possibleResourceGroupName \
            --zone-name $dnsZoneName \
            --query "[?virtualNetwork.id == '$virtualNetwork'].id" \
            --output tsv)
    
    if [ -z $link ]
    then
        1 > /dev/null
    else 
        dnsZoneResourceGroup=$possibleResourceGroupName
        dnsZone=$possibleDnsZone
        break
    fi  
done

if [ -z $dnsZone ]
then
    # No matching DNS zone attached to virtual network, so create a new one
    dnsZone=$(az network private-dns zone create \
            --resource-group $virtualNetworkResourceGroupName \
            --name $dnsZoneName \
            --query "id" | \
        tr -d '"')
    
    az network private-dns link vnet create \
            --resource-group $resourceGroupName \
            --zone-name $zoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
fi
```

Nu när du har en referens till den privata DNS-zonen måste du skapa en A-post för ditt lagringskonto.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateEndpointIP=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[0].privateIpAddress" | \
    tr -d '"')

az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name $storageAccountName \
        --output none

az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name $storageAccountName \
        --ipv4-address $privateEndpointIP \
        --output none
```

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen [här,](storage-files-networking-dns.md)kan du testa att den privata slutpunkten har konfigurerats korrekt med följande kommandon:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.File" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Om allt har fungerat har fungerat bör du `192.168.0.5` se följande utdata, där är den privata IP-adressen för den privata slutpunkten i det virtuella nätverket:

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>Begränsa åtkomsten till den offentliga slutpunkten
Du kan begränsa åtkomsten till den offentliga slutpunkten med hjälp av brandväggsinställningarna för lagringskontot. I allmänhet begränsar de flesta brandväggsprinciper för ett lagringskonto nätverksåtkomst till ett eller flera virtuella nätverk. Det finns två metoder för att begränsa åtkomsten till ett lagringskonto till ett virtuellt nätverk:

- [Skapa en eller flera privata slutpunkter för lagringskontot](#create-a-private-endpoint) och begränsa all åtkomst till den offentliga slutpunkten. Detta säkerställer att endast trafik som kommer inifrån önskade virtuella nätverk kan komma åt Azure-filresurserna i lagringskontot.
- Begränsa den offentliga slutpunkten till ett eller flera virtuella nätverk. Detta fungerar med hjälp av en funktion i det virtuella nätverket som kallas *tjänstslutpunkter*. När du begränsar trafiken till ett lagringskonto via en tjänstslutpunkt, är du fortfarande tillgång till lagringskontot via den offentliga IP-adressen.

### <a name="restrict-all-access-to-the-public-endpoint"></a>Begränsa all åtkomst till den offentliga slutpunkten
När all åtkomst till den offentliga slutpunkten är begränsad kan lagringskontot fortfarande nås via det privata slutpunkterna. Annars avvisas giltiga begäranden till lagringskontots offentliga slutpunkt. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigera till det lagringskonto som du vill begränsa all åtkomst till den offentliga slutpunkten för. I innehållsförteckningen för lagringskontot väljer du **Brandväggar och virtuella nätverk**.

Högst upp på sidan väljer du alternativknappen **Markerade nätverk.** Detta kommer att dölja ett antal inställningar för att styra begränsningen av den offentliga slutpunkten. Kontrollera **Tillåt betrodda Microsoft-tjänster att komma åt det här tjänstkontot** så att betrodda Microsoft-tjänster från första part, till exempel Azure File Sync, kan komma åt lagringskontot.

![Skärmbild av bladet Brandväggar och virtuella nätverk med lämpliga begränsningar på plats](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Följande PowerShell-kommando nekar all trafik till lagringskontots offentliga slutpunkt. Observera att det `-Bypass` här kommandot `AzureServices`har parametern inställd på . Detta gör det möjligt för betrodda förstapartstjänster som Azure File Sync att komma åt lagringskontot via den offentliga slutpunkten.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Följande CLI-kommando nekar all trafik till lagringskontots offentliga slutpunkt. Observera att det `-bypass` här kommandot `AzureServices`har parametern inställd på . Detta gör det möjligt för betrodda förstapartstjänster som Azure File Sync att komma åt lagringskontot via den offentliga slutpunkten.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```
---

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Begränsa åtkomsten till den offentliga slutpunkten till specifika virtuella nätverk
När du begränsar lagringskontot till specifika virtuella nätverk tillåter du begäranden till den offentliga slutpunkten inifrån de angivna virtuella nätverken. Detta fungerar med hjälp av en funktion i det virtuella nätverket som kallas *tjänstslutpunkter*. Detta kan användas med eller utan privata slutpunkter.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigera till det lagringskonto som du vill begränsa den offentliga slutpunkten till specifika virtuella nätverk för. I innehållsförteckningen för lagringskontot väljer du **Brandväggar och virtuella nätverk**. 

Högst upp på sidan väljer du alternativknappen **Markerade nätverk.** Detta kommer att dölja ett antal inställningar för att styra begränsningen av den offentliga slutpunkten. Klicka på **+Lägg till befintligt virtuellt nätverk** för att välja det specifika virtuella nätverk som ska tillåtas att komma åt lagringskontot via den offentliga slutpunkten. Detta kräver att du väljer ett virtuellt nätverk och ett undernät för det virtuella nätverket. 

Kontrollera **Tillåt betrodda Microsoft-tjänster att komma åt det här tjänstkontot** så att betrodda Microsoft-tjänster från första part, till exempel Azure File Sync, kan komma åt lagringskontot.

![Skärmbild av bladet Brandväggar och virtuella nätverk med ett specifikt virtuellt nätverk som får åtkomst till lagringskontot via den offentliga slutpunkten](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Om du vill begränsa åtkomsten till lagringskontots offentliga slutpunkt till specifika virtuella nätverk med hjälp av tjänstslutpunkter måste vi först samla in information om lagringskontot och det virtuella nätverket. Fyll `<storage-account-resource-group>`i `<storage-account-name>` `<vnet-resource-group-name>`, `<vnet-name>`, `<subnet-name>` , och för att samla in den här informationen.

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group>"
$storageAccountName = "<storage-account-name>"
$restrictToVirtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$restrictToVirtualNetworkName = "<vnet-name>"
$subnetName = "<subnet-name>"

$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction Stop

$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $restrictToVirtualNetworkResourceGroupName `
        -Name $restrictToVirtualNetworkName `
        -ErrorAction Stop

$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $restrictToVirtualNetworkName." `
            -ErrorAction Stop
}
```

För att trafik från det virtuella nätverket ska tillåtas av Azure-nätverksinfrastrukturen för att komma till den `Microsoft.Storage` offentliga slutpunkten för lagringskontot måste det virtuella nätverkets undernät ha tjänstslutpunkten exponerat. Följande PowerShell-kommandon lägger `Microsoft.Storage` till tjänstslutpunkten i undernätet om den inte redan finns där.

```PowerShell
$serviceEndpoints = $subnet | `
    Select-Object -ExpandProperty ServiceEndpoints | `
    Select-Object -ExpandProperty Service

if ($serviceEndpoints -notcontains "Microsoft.Storage") {
    if ($null -eq $serviceEndpoints) {
        $serviceEndpoints = @("Microsoft.Storage")
    } elseif ($serviceEndpoints -is [string]) {
        $serviceEndpoints = @($serviceEndpoints, "Microsoft.Storage")
    } else {
        $serviceEndpoints += "Microsoft.Storage"
    }

    $virtualNetwork = $virtualNetwork | Set-AzVirtualNetworkSubnetConfig `
            -Name $subnetName `
            -AddressPrefix $subnet.AddressPrefix `
            -ServiceEndpoint $serviceEndpoints `
            -WarningAction SilentlyContinue `
            -ErrorAction Stop | `
        Set-AzVirtualNetwork `
            -ErrorAction Stop
}
```

Det sista steget i att begränsa trafiken till lagringskontot är att skapa en nätverksregel och lägga till lagringskontots nätverksregeluppsättning.

```PowerShell
$networkRule = $storageAccount | Add-AzStorageAccountNetworkRule `
    -VirtualNetworkResourceId $subnet.Id `
    -ErrorAction Stop

$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -VirtualNetworkRule $networkRule `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill begränsa åtkomsten till lagringskontots offentliga slutpunkt till specifika virtuella nätverk med hjälp av tjänstslutpunkter måste vi först samla in information om lagringskontot och det virtuella nätverket. Fyll `<storage-account-resource-group>`i `<storage-account-name>` `<vnet-resource-group-name>`, `<vnet-name>`, `<subnet-name>` , och för att samla in den här informationen.

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

För att trafik från det virtuella nätverket ska tillåtas av Azure-nätverksinfrastrukturen för att komma till den `Microsoft.Storage` offentliga slutpunkten för lagringskontot måste det virtuella nätverkets undernät ha tjänstslutpunkten exponerat. Följande CLI-kommandon lägger `Microsoft.Storage` till tjänstslutpunkten i undernätet om den inte redan finns där.

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

Det sista steget i att begränsa trafiken till lagringskontot är att skapa en nätverksregel och lägga till lagringskontots nätverksregeluppsättning.

```bash
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```

---

## <a name="see-also"></a>Se även
- [Överväganden för Azure Files-nätverk](storage-files-networking-overview.md)
- [Konfigurera DNS-vidarebefordran för Azure-filer](storage-files-networking-dns.md)
- [Konfigurera S2S VPN för Azure-filer](storage-files-configure-s2s-vpn.md)