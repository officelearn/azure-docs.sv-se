---
title: Konfigurera Azure Files nätverks slut punkter | Microsoft Docs
description: En översikt över nätverks alternativ för Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4695164e7bcbc63b852f2f4364cdccbc8ea7d8c4
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849322"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurera Azure Files nätverks slut punkter
Azure Files tillhandahåller två huvud typer av slut punkter för åtkomst till Azure-fil resurser: 
- Offentliga slut punkter som har en offentlig IP-adress och som kan nås från var som helst i världen.
- Privata slut punkter, som finns i ett virtuellt nätverk och har en privat IP-adress inom det virtuella nätverkets adress utrymme.

Offentliga och privata slut punkter finns på Azure Storage-kontot. Ett lagrings konto är en hanterings konstruktion som representerar en delad pool av lagring där du kan distribuera flera fil resurser, samt andra lagrings resurser, till exempel BLOB-behållare eller köer.

Den här artikeln fokuserar på hur du konfigurerar ett lagrings kontos slut punkter för åtkomst till Azure-filresursen direkt. De flesta Detaljer som tillhandahålls i det här dokumentet gäller också hur Azure File Sync interagerar med offentliga och privata slut punkter för lagrings kontot, men för mer information om nätverks överväganden för en Azure File Sync distribution, se [konfigurera Azure File Sync proxy-och brand Väggs inställningar](storage-sync-files-firewall-and-proxy.md).

Vi rekommenderar att du läser [Azure Files nätverks överväganden](storage-files-networking-overview.md) innan du läser den här guiden.

## <a name="prerequisites"></a>Krav
- Den här artikeln förutsätter att du redan har skapat en Azure-prenumeration. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Den här artikeln förutsätter att du redan har skapat en Azure-filresurs i ett lagrings konto som du vill ansluta till lokalt. Information om hur du skapar en Azure-filresurs finns i [skapa en Azure-fil resurs](storage-how-to-create-file-share.md).
- Om du tänker använda Azure PowerShell [installerar du den senaste versionen](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Om du tänker använda Azure CLI [installerar du den senaste versionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt
Om du skapar en privat slut punkt för ditt lagrings konto leder det till att följande Azure-resurser distribueras:

- **En privat slut punkt**: en Azure-resurs som representerar lagrings kontots privata slut punkt. Du kan tänka på detta som en resurs som ansluter ett lagrings konto och ett nätverks gränssnitt.
- **Ett nätverks gränssnitt (NIC)**: det nätverks gränssnitt som upprätthåller en privat IP-adress inom det angivna virtuella nätverket/under nätet. Det här är exakt samma resurs som distribueras när du distribuerar en virtuell dator, men i stället för att tilldelas en virtuell dator, ägs den av den privata slut punkten.
- **En privat DNS-zon**: om du aldrig har distribuerat en privat slut punkt för det här virtuella nätverket tidigare, kommer en ny privat DNS-zon att distribueras för det virtuella nätverket. En DNS A-post kommer också att skapas för lagrings kontot i den här DNS-zonen. Om du redan har distribuerat en privat slut punkt i det här virtuella nätverket kommer en ny A-post för lagrings kontot att läggas till i den befintliga DNS-zonen. Distribution av en DNS-zon är valfritt, rekommenderas, och krävs om du monterar dina Azure-filresurser med ett huvud namn för AD-tjänsten eller med det fileraste API: et.

> [!Note]  
> I den här artikeln används DNS-suffixet för lagrings kontot för Azures offentliga regioner `core.windows.net` . Den här kommentarer gäller också för Azures suveräna moln, till exempel Azure-molnet för amerikanska myndigheter och molnet i molnet, och ersätter bara de nödvändiga suffixen för din miljö. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigera till lagrings kontot som du vill skapa en privat slut punkt för. I innehålls förteckningen för lagrings kontot väljer du **anslutningar för privata slut punkter**och sedan **+ privat slut punkt** för att skapa en ny privat slut punkt. 

![En skärm bild av objektet privata slut punkts anslutningar i innehålls förteckningen för lagrings kontot](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

Den resulterande guiden har flera sidor att slutföra.

På bladet **grundläggande** väljer du önskad resurs grupp, namn och region för din privata slut punkt. Det kan vara vad du vill, de behöver inte matcha lagrings kontot i alla fall, men du måste skapa den privata slut punkten i samma region som det virtuella nätverk som du vill skapa den privata slut punkten i.

![En skärm bild av grunderna i avsnittet Skapa privat slut punkt](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

På **resurs** bladet väljer du alternativ knappen för **att ansluta till en Azure-resurs i min katalog**. Under **resurs typ**väljer du **Microsoft. Storage/storageAccounts** som resurs typ. **Resurs** fältet är lagrings kontot med den Azure-filresurs som du vill ansluta till. Mål under resurs är **fil**, eftersom det är för Azure Files.

På bladet **konfiguration** kan du välja det angivna virtuella nätverk och undernät som du vill lägga till din privata slut punkt till. Välj det virtuella nätverk som du skapade ovan. Du måste välja ett distinkt undernät från under nätet som du har lagt till tjänst slut punkten till ovan. Konfigurations bladet innehåller också information om hur du skapar/uppdaterar den privata DNS-zonen. Vi rekommenderar att du använder standard `privatelink.file.core.windows.net` zonen.

![En skärm bild av konfigurations avsnittet](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Klicka på **Granska + skapa** för att skapa den privata slut punkten. 

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen [här](storage-files-networking-dns.md), kan du testa att den privata slut punkten har kon figurer ATS korrekt genom att köra följande kommandon från PowerShell, kommando raden eller terminalen (Works för Windows, Linux eller MacOS). Du måste ersätta `<storage-account-name>` med lämpligt lagrings konto namn:

```
nslookup <storage-account-name>.file.core.windows.net
```

Om allt har fungerat korrekt bör du se följande utdata, där `192.168.0.5` är den privata IP-adressen för den privata slut punkten i det virtuella nätverket (utdata som visas för Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill skapa en privat slut punkt för ditt lagrings konto måste du först hämta en referens till ditt lagrings konto och det virtuella nätverks under nätet där du vill lägga till den privata slut punkten. Ersätt `<storage-account-resource-group-name>` ,,, `<storage-account-name>` `<vnet-resource-group-name>` `<vnet-name>` och `<vnet-subnet-name>` nedan:

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

Om du vill skapa en privat slut punkt måste du skapa en anslutning till ett privat länk-tjänst till lagrings kontot. Den privata länk tjänst anslutningen är inmatad för att skapa den privata slut punkten. 

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

När du skapar en privat DNS-zon för Azure kan du använda det ursprungliga namnet på lagrings kontot, till exempel `storageaccount.file.core.windows.net` för att matcha den privata IP-adressen i det virtuella nätverket. Även om det är valfritt från perspektivet för att skapa en privat slut punkt krävs det uttryckligen för att montera Azure-filresursen med hjälp av ett huvud för AD-användare eller åtkomst via REST API.  

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

Nu när du har en referens till den privata DNS-zonen måste du skapa en A-post för ditt lagrings konto.

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

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen [här](storage-files-networking-dns.md), kan du testa att den privata slut punkten har kon figurer ATS korrekt med följande kommandon:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Om allt har fungerat korrekt bör du se följande utdata, där `192.168.0.5` är den privata IP-adressen för den privata slut punkten i det virtuella nätverket:

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
Om du vill skapa en privat slut punkt för ditt lagrings konto måste du först hämta en referens till ditt lagrings konto och det virtuella nätverks under nätet där du vill lägga till den privata slut punkten. Ersätt `<storage-account-resource-group-name>` ,,, `<storage-account-name>` `<vnet-resource-group-name>` `<vnet-name>` och `<vnet-subnet-name>` nedan:

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

Om du vill skapa en privat slut punkt måste du först se till att under nätets nätverks princip är inställd på inaktive rad. Sedan kan du skapa en privat slut punkt med `az network private-endpoint create` kommandot

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

När du skapar en privat DNS-zon för Azure kan du använda det ursprungliga namnet på lagrings kontot, till exempel `storageaccount.file.core.windows.net` för att matcha den privata IP-adressen i det virtuella nätverket. Även om det är valfritt från perspektivet för att skapa en privat slut punkt krävs det uttryckligen för att montera Azure-filresursen med hjälp av ett huvud för AD-användare eller åtkomst via REST API.  

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

Nu när du har en referens till den privata DNS-zonen måste du skapa en A-post för ditt lagrings konto.

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

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen [här](storage-files-networking-dns.md), kan du testa att den privata slut punkten har kon figurer ATS korrekt med följande kommandon:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Om allt har fungerat korrekt bör du se följande utdata, där `192.168.0.5` är den privata IP-adressen för den privata slut punkten i det virtuella nätverket. Observera att du fortfarande bör använda storageaccount.file.core.windows.net för att räkna till fil resursen insread för privatelink-sökvägen.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>Begränsa åtkomsten till den offentliga slut punkten
Du kan begränsa åtkomsten till den offentliga slut punkten med hjälp av brand Väggs inställningarna för lagrings kontot. I allmänhet kommer de flesta brand Väggs principer för ett lagrings konto att begränsa nätverks åtkomsten till ett eller flera virtuella nätverk. Det finns två sätt att begränsa åtkomsten till ett lagrings konto till ett virtuellt nätverk:

- [Skapa en eller flera privata slut punkter för lagrings kontot](#create-a-private-endpoint) och begränsa all åtkomst till den offentliga slut punkten. Detta säkerställer att endast trafik som härstammar från de önskade virtuella nätverken kan komma åt Azure-filresurserna i lagrings kontot.
- Begränsa den offentliga slut punkten till ett eller flera virtuella nätverk. Detta fungerar med hjälp av en funktion i det virtuella nätverket med namnet *tjänst slut punkter*. När du begränsar trafiken till ett lagrings konto via en tjänst slut punkt har du fortfarande åtkomst till lagrings kontot via den offentliga IP-adressen.

### <a name="restrict-all-access-to-the-public-endpoint"></a>Begränsa all åtkomst till den offentliga slut punkten
När all åtkomst till den offentliga slut punkten är begränsad kan lagrings kontot fortfarande nås via den privata slut punkten. Annars avvisas giltiga begär anden till lagrings kontots offentliga slut punkt. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigera till det lagrings konto som du vill begränsa all åtkomst till den offentliga slut punkten. I innehålls förteckningen för lagrings kontot väljer du **brand väggar och virtuella nätverk**.

Längst upp på sidan väljer du alternativ knappen **valda nätverk** . Detta gör att det inte går att dölja ett antal inställningar för att kontrol lera begränsningen för den offentliga slut punkten. Markera **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här tjänst kontot** för att tillåta betrodda Microsoft-tjänster från första part, till exempel Azure File Sync att komma åt lagrings kontot.

![Skärm bild av bladet brand väggar och virtuella nätverk med lämplig begränsning på plats](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Följande PowerShell-kommando kommer att neka all trafik till lagrings kontots offentliga slut punkt. Observera att det här kommandot har `-Bypass` parametern inställd på `AzureServices` . Detta ger betrodda tjänster från första part, till exempel Azure File Sync att komma åt lagrings kontot via den offentliga slut punkten.

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
Följande CLI-kommando kommer att neka all trafik till lagrings kontots offentliga slut punkt. Observera att det här kommandot har `-bypass` parametern inställd på `AzureServices` . Detta ger betrodda tjänster från första part, till exempel Azure File Sync att komma åt lagrings kontot via den offentliga slut punkten.

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

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Begränsa åtkomsten till den offentliga slut punkten till vissa virtuella nätverk
När du begränsar lagrings kontot till specifika virtuella nätverk kan du tillåta begär anden till den offentliga slut punkten från de angivna virtuella nätverken. Detta fungerar med hjälp av en funktion i det virtuella nätverket med namnet *tjänst slut punkter*. Detta kan användas med eller utan privata slut punkter.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigera till det lagrings konto för vilket du vill begränsa den offentliga slut punkten till vissa virtuella nätverk. I innehålls förteckningen för lagrings kontot väljer du **brand väggar och virtuella nätverk**. 

Längst upp på sidan väljer du alternativ knappen **valda nätverk** . Detta gör att det inte går att dölja ett antal inställningar för att kontrol lera begränsningen för den offentliga slut punkten. Klicka på **+ Lägg till befintligt virtuellt nätverk** för att välja det enskilda virtuella nätverk som ska få åtkomst till lagrings kontot via den offentliga slut punkten. Detta kräver att du väljer ett virtuellt nätverk och ett undernät för det virtuella nätverket. 

Markera **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här tjänst kontot** för att tillåta betrodda Microsoft-tjänster från första part, till exempel Azure File Sync att komma åt lagrings kontot.

![Skärm bild av bladet brand väggar och virtuella nätverk med ett särskilt virtuellt nätverk som har behörighet att komma åt lagrings kontot via den offentliga slut punkten](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
För att begränsa åtkomsten till lagrings kontots offentliga slut punkt till vissa virtuella nätverk som använder tjänst slut punkter, behöver vi först samla in information om lagrings kontot och det virtuella nätverket. Fyll i `<storage-account-resource-group>` ,,, `<storage-account-name>` `<vnet-resource-group-name>` `<vnet-name>` och `<subnet-name>` för att samla in den här informationen.

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

För att trafik från det virtuella nätverket ska tillåtas av Azure Network Fabric för att komma till lagrings kontots offentliga slut punkt måste tjänst slut punkten vara utsatt för det virtuella nätverkets undernät `Microsoft.Storage` . Följande PowerShell-kommandon kommer att lägga till `Microsoft.Storage` tjänstens slut punkt i under nätet om den inte redan finns där.

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

Det sista steget i att begränsa trafiken till lagrings kontot är att skapa en nätverks regel och lägga till lagrings kontots nätverks regel uppsättning.

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
För att begränsa åtkomsten till lagrings kontots offentliga slut punkt till vissa virtuella nätverk som använder tjänst slut punkter, behöver vi först samla in information om lagrings kontot och det virtuella nätverket. Fyll i `<storage-account-resource-group>` ,,, `<storage-account-name>` `<vnet-resource-group-name>` `<vnet-name>` och `<subnet-name>` för att samla in den här informationen.

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

För att trafik från det virtuella nätverket ska tillåtas av Azure Network Fabric för att komma till lagrings kontots offentliga slut punkt måste tjänst slut punkten vara utsatt för det virtuella nätverkets undernät `Microsoft.Storage` . Följande CLI-kommandon lägger till `Microsoft.Storage` tjänstens slut punkt i under nätet om den inte redan finns där.

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

Det sista steget i att begränsa trafiken till lagrings kontot är att skapa en nätverks regel och lägga till lagrings kontots nätverks regel uppsättning.

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
- [Azure Files nätverks överväganden](storage-files-networking-overview.md)
- [Konfigurera DNS-vidarebefordring för Azure Files](storage-files-networking-dns.md)
- [Konfigurerar S2S VPN för Azure Files](storage-files-configure-s2s-vpn.md)
