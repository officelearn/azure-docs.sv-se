---
title: Konfigurera en punkt-till-plats (P2S) VPN i Windows för användning med Azure Files | Microsoft Docs
description: Så här konfigurerar du en punkt-till-plats (P2S) VPN i Windows för användning med Azure Files
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 90995b1c9d10c7b589706f5abf37f92d76e4362b
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75560359"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Konfigurera en punkt-till-plats (P2S) VPN i Windows för användning med Azure Files
Du kan använda en punkt-till-plats (P2S) VPN-anslutning för att montera dina Azure-filresurser över SMB utanför Azure, utan att öppna port 445. En punkt-till-plats-VPN-anslutning är en VPN-anslutning mellan Azure och en enskild klient. Om du vill använda en P2S VPN-anslutning med Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. Om du har många klienter som behöver ansluta till dina Azure-filresurser från ditt lokala nätverk kan du använda en plats-till-plats (S2S) VPN-anslutning i stället för en punkt-till-plats-anslutning för varje klient. Mer information finns i [Konfigurera en plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).

Vi rekommenderar starkt att du läser [nätverks överväganden för direkt åtkomst till Azure-filresurser](storage-files-networking-overview.md) innan du fortsätter med den här artikeln för en fullständig diskussion av de nätverks alternativ som är tillgängliga för Azure Files.

Artikeln beskriver stegen för att konfigurera en punkt-till-plats-VPN i Windows (Windows-klient och Windows Server) för att montera Azure-filresurser direkt lokalt. Om du vill dirigera Azure File Sync trafik via en VPN-anslutning kan du läsa [konfigurera Azure File Sync proxy-och brand Väggs inställningar](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Krav
- Den senaste versionen av Azure PowerShell-modulen. Mer information om hur du installerar Azure PowerShell finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps) och välja operativ system. Om du föredrar att använda Azure CLI i Windows kan du se till att anvisningarna nedan visas för Azure PowerShell.

- Azure Privat DNS PowerShell-modulen. Detta distribueras för närvarande inte som en del av Azure PowerShell-modulen, så det kan installeras med följande metod:
    ```PowerShell
    if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
        Install-Module -Name Az.PrivateDns -AllowClobber -AllowPrerelease
    } else {
        Install-Module -Name Az.PrivateDns -RequiredVersion "0.1.3"
    }

    Import-Module -Name Az.PrivateDns
    ```  

- En Azure-filresurs som du vill montera lokalt. Du kan använda antingen en [standard](storage-how-to-create-file-share.md) -eller [Premium Azure-filresurs](storage-how-to-create-premium-fileshare.md) med punkt-till-plats-VPN.

## <a name="deploy-a-virtual-network"></a>Distribuera ett virtuellt nätverk
För att få åtkomst till Azure-filresursen och andra Azure-resurser från lokala platser via en punkt-till-plats-VPN måste du skapa ett virtuellt nätverk eller ett virtuellt nätverk. P2S VPN-anslutningen som du skapar automatiskt är en bro mellan den lokala Windows-datorn och det virtuella Azure-nätverket.

Följande PowerShell skapar ett virtuellt Azure-nätverk med tre undernät: ett för lagrings kontots tjänst slut punkt, ett för lagrings kontots privata slut punkt, vilket krävs för att komma åt lagrings kontot lokalt utan att skapa anpassad routning för den offentliga IP-adressen för det lagrings konto som kan ändras och en för din virtuella nätverksgateway som tillhandahåller VPN-tjänsten. 

Kom ihåg att ersätta `<region>`, `<resource-group>`och `<desired-vnet-name>` med lämpliga värden för din miljö.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>Begränsa lagrings kontot till det virtuella nätverket
Som standard när du skapar ett lagrings konto kan du komma åt det från var som helst i världen, förutsatt att du har möjlighet att autentisera din begäran (till exempel med din Active Directory identitet eller med lagrings konto nyckeln). Om du vill begränsa åtkomsten till det här lagrings kontot till det virtuella nätverk som du nyss skapade måste du skapa en nätverks regel uppsättning som tillåter åtkomst i det virtuella nätverket och nekar all annan åtkomst.

Att begränsa lagrings kontot till det virtuella nätverket kräver att en tjänst slut punkt används. Tjänst slut punkten är en nätverks konstruktion som den offentliga DNS/offentliga IP-adressen endast kan nås från i det virtuella nätverket. Eftersom den offentliga IP-adressen inte garanteras vara densamma, vill vi i slut ändan använda en privat slut punkt i stället för en tjänst slut punkt för lagrings kontot, men det går inte att begränsa lagrings kontot om inte en tjänst slut punkt också exponeras.

Kom ihåg att ersätta `<storage-account-name>` med det lagrings konto som du vill komma åt.

```PowerShell
$storageAccountName = "<storage-account-name>"

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName

$networkRule = Add-AzStorageAccountNetworkRule `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -VirtualNetworkResourceId $serviceEndpointSubnet.Id

Update-AzStorageAccountNetworkRuleSet `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Bypass AzureServices `
    -DefaultAction Deny `
    -VirtualNetworkRule $networkRule | Out-Null
``` 

## <a name="create-a-private-endpoint-preview"></a>Skapa en privat slut punkt (förhands granskning)
Att skapa en privat slut punkt för ditt lagrings konto ger ditt lagrings konto en IP-adress inom IP-adressutrymmet för det virtuella nätverket. När du monterar en Azure-filresurs från lokala datorer med den här privata IP-adressen, kommer de routningsregler som definierats automatiskt av VPN-installationen att skicka din monterings förfrågan till lagrings kontot via VPN. 

```PowerShell
$internalVnet = Get-AzResource `
    -ResourceId $virtualNetwork.Id `
    -ApiVersion "2019-04-01"

$internalVnet.Properties.subnets[1].properties.privateEndpointNetworkPolicies = "Disabled"
$internalVnet | Set-AzResource -Force | Out-Null

$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
    -Name "myConnection" `
    -PrivateLinkServiceId $storageAccount.Id `
    -GroupId "file"

$privateEndpoint = New-AzPrivateEndpoint `
    -ResourceGroupName $resourceGroupName `
    -Name "$storageAccountName-privateEndpoint" `
    -Location $region `
    -Subnet $privateEndpointSubnet `
    -PrivateLinkServiceConnection $privateEndpointConnection

$zone = Get-AzPrivateDnsZone -ResourceGroupName $resourceGroupName
if ($null -eq $zone) {
    $zone = New-AzPrivateDnsZone `
        -ResourceGroupName $resourceGroupName `
        -Name "privatelink.file.core.windows.net"
} else {
    $zone = $zone[0]
}

$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName $resourceGroupName `
    -ZoneName $zone.Name `
    -Name ($virtualNetwork.Name + "-link") `
    -VirtualNetworkId $virtualNetwork.Id

$internalNic = Get-AzResource `
    -ResourceId $privateEndpoint.NetworkInterfaces[0].Id `
    -ApiVersion "2019-04-01"

foreach($ipconfig in $internalNic.Properties.ipConfigurations) {
    foreach($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.', 2)[0]
        $dnsZone = $fqdn.split('.', 2)[1]
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $resourceGroupName `
            -Name $recordName `
            -RecordType A `
            -ZoneName $zone.Name `
            -Ttl 600 `
            -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
                -IPv4Address $ipconfig.properties.privateIPAddress) | Out-Null
    }
}
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Skapa rot certifikat för VPN-autentisering
För att VPN-anslutningar från dina lokala Windows-datorer ska kunna autentiseras för åtkomst till ditt virtuella nätverk måste du skapa två certifikat: ett rot certifikat som kommer att tillhandahållas till den virtuella datorns gateway och ett klient certifikat, vilket kommer signeras med rot certifikatet. Följande PowerShell skapar rot certifikatet. klient certifikatet skapas efter det att Azures virtuella nätverksgateway har skapats med information från gatewayen. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Distribuera virtuell nätverksgateway
Den virtuella Azure-Nätverksgatewayen är den tjänst som dina lokala Windows-datorer kommer att ansluta till. Att distribuera den här tjänsten kräver två grundläggande komponenter: en offentlig IP-adress som identifierar gatewayen för dina klienter oavsett var de finns i världen och ett rot certifikat som du skapade tidigare och som ska användas för att autentisera dina klienter.

Kom ihåg att ersätta `<desired-vpn-name-here>` med det namn som du vill ha för dessa resurser.

> [!Note]  
> Det kan ta upp till 45 minuter att distribuera den virtuella Azure-Nätverksgatewayen. Även om den här resursen distribueras, kommer det här PowerShell-skriptet att blockeras för att distributionen ska slutföras. Detta är normalt.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName ` 
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Skapa klient certifikat
Klient certifikatet skapas med URI: n för den virtuella Nätverksgatewayen. Certifikatet är signerat med rot certifikatet som du skapade tidigare.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>Konfigurera VPN-klienten
Den virtuella Azure-Nätverksgatewayen kommer att skapa ett nedladdnings Bart paket med konfigurationsfiler som krävs för att initiera VPN-anslutningen på din lokala Windows-dator. Vi konfigurerar VPN-anslutningen med funktionen [Always on VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) i Windows 10/Windows Server 2016 +. Det här paketet innehåller också körbara paket som konfigurerar den äldre Windows VPN-klienten, om så önskas. Den här guiden använder alltid VPN i stället för den äldre Windows VPN-klienten som Always on VPN-klienten, vilket gör att slutanvändarna kan ansluta eller koppla från Azure VPN utan administratörs behörighet till datorn. 

Följande skript kommer att installera det klient certifikat som krävs för autentisering mot den virtuella Nätverksgatewayen, ladda ned och installera VPN-paketet. Kom ihåg att ersätta `<computer1>` och `<computer2>` med önskade datorer. Du kan köra det här skriptet på så många datorer som du vill genom att lägga till fler PowerShell-sessioner i `$sessions` matrisen. Ditt användnings konto måste vara administratör på var och en av dessa datorer. Om någon av dessa datorer är den lokala dator som du kör skriptet från, måste du köra skriptet från en upphöjd PowerShell-session. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer ` 
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Montera Azure-filresurs
Nu när du har konfigurerat din punkt-till-plats-VPN kan du använda den för att montera Azure-filresursen på de datorer som du installerar via PowerShell. I följande exempel monteras resursen, en lista visas i resursens rot Katalog för att bevisa att resursen faktiskt monteras och demontera resursen. Det går tyvärr inte att montera resursen beständigt över PowerShell-fjärrkommunikation. Information om hur du monterar permanent finns i [använda en Azure-filresurs med Windows](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Se också
- [Nätverks överväganden för direkt åtkomst till Azure-filresurser](storage-files-networking-overview.md)
- [Konfigurera en punkt-till-plats (P2S) VPN på Linux som ska användas med Azure Files](storage-files-configure-p2s-vpn-linux.md)
- [Konfigurera ett VPN för plats-till-plats (S2S) som ska användas med Azure Files](storage-files-configure-s2s-vpn.md)
