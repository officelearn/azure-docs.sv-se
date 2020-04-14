---
title: Konfigurera ett P2S-VPN (Point-to-Site) i Windows för användning med Azure Files | Microsoft-dokument
description: Konfigurera ett P2S-VPN (Point-to-Site) i Windows för användning med Azure-filer
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95386af4522adca1d65e04b01c2a349a80e9ab8a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273485"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Konfigurera ett P2S-VPN (Point-to-Site) i Windows för användning med Azure-filer
Du kan använda en P2S-VPN-anslutning (Point-to-Site) för att montera dina Azure-filresurser via SMB utanför Azure, utan att öppna port 445. En Punkt-till-plats VPN-anslutning är en VPN-anslutning mellan Azure och en enskild klient. Om du vill använda en P2S VPN-anslutning med Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. Om du har många klienter som behöver ansluta till dina Azure-filresurser från ditt lokala nätverk kan du använda en S2S-anslutning (Site-to-Site) i stället för en Point-to-Site-anslutning för varje klient. Mer information finns i [Konfigurera ett plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).

Vi rekommenderar starkt att du läser [Nätverksöverväganden för direkt Azure-filresursåtkomst](storage-files-networking-overview.md) innan du fortsätter med den här artikeln för en fullständig diskussion om nätverksalternativen som är tillgängliga för Azure-filer.

Artikeln beskriver stegen för att konfigurera en Point-to-Site VPN på Windows (Windows-klient och Windows Server) för att montera Azure-filresurser direkt lokalt. Om du vill dirigera Azure File Sync-trafik via en VPN läser du [konfigurera proxy- och brandväggsinställningar för Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Krav
- Den senaste versionen av Azure PowerShell-modulen. Mer information om hur du installerar Azure PowerShell finns i [Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps) och välj operativsystem. Om du föredrar att använda Azure CLI på Windows kan du, men instruktionerna nedan presenteras för Azure PowerShell.

- En Azure-filresurs som du vill montera lokalt. Azure-filresurser distribueras i lagringskonton, som är hanteringskonstruktioner som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blob-behållare eller köer. Du kan läsa mer om hur du distribuerar Azure-filresurser och lagringskonton i [Skapa en Azure-filresurs](storage-how-to-create-file-share.md).

- En privat slutpunkt för lagringskontot som innehåller den Azure-filresurs som du vill montera lokalt. Mer information om hur du skapar en privat slutpunkt finns i [Konfigurera slutpunkter för Azure Files-nätverk](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Distribuera ett virtuellt nätverk
Om du vill komma åt din Azure-filresurs och andra Azure-resurser från lokala via ett Point-to-Site VPN måste du skapa ett virtuellt nätverk eller ett virtuellt nätverk. Den P2S VPN-anslutning som du automatiskt skapar är en brygga mellan din lokala Windows-dator och det virtuella Azure-nätverket.

Följande PowerShell skapar ett virtuellt Azure-nätverk med tre undernät: ett för ditt lagringskontos tjänstslutpunkt, en för ditt lagringskontos privata slutpunkt, som krävs för att komma åt lagringskontot lokalt utan att skapa anpassad routning för den offentliga IP-adressen för lagringskontot som kan ändras och en för din virtuella nätverksgateway som tillhandahåller VPN-tjänsten. 

Kom ihåg `<region>` `<resource-group>`att `<desired-vnet-name>` ersätta , och med lämpliga värden för din miljö.

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

## <a name="create-root-certificate-for-vpn-authentication"></a>Skapa rotcertifikat för VPN-autentisering
För att VPN-anslutningar från dina lokala Windows-datorer ska autentiseras för att komma åt det virtuella nätverket måste du skapa två certifikat: ett rotcertifikat som ska tillhandahållas till den virtuella datorns gateway och ett klientcertifikat som signeras med rotcertifikatet. Följande PowerShell skapar rotcertifikatet. Klientcertifikatet skapas när Azure-gatewayen för virtuella nätverk har skapats med information från gatewayen. 

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
Azure-gatewayen för virtuella nätverk är den tjänst som dina lokala Windows-datorer ansluter till. Distribuera den här tjänsten kräver två grundläggande komponenter: en offentlig IP som identifierar gatewayen till dina klienter var de än är i världen och ett rotcertifikat som du skapade tidigare som kommer att användas för att autentisera dina klienter.

Kom ihåg `<desired-vpn-name-here>` att ersätta med det namn du vill ha för dessa resurser.

> [!Note]  
> Det kan ta upp till 45 minuter att distribuera azure-gatewayen för virtuella nätverk. Medan den här resursen distribueras blockerar det här PowerShell-skriptet för att distributionen ska slutföras. Detta är normalt.

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

## <a name="create-client-certificate"></a>Skapa klientcertifikat
Klientcertifikatet skapas med URI för den virtuella nätverksgatewayen. Certifikatet är signerat med rotcertifikatet som du skapade tidigare.

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
Azure-gatewayen för virtuella nätverk skapar ett nedladdningsbart paket med konfigurationsfiler som krävs för att initiera VPN-anslutningen på din lokala Windows-dator. Vi konfigurerar VPN-anslutningen med funktionen [Always On VPN i](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) Windows 10/Windows Server 2016+. Det här paketet innehåller även körbara paket som konfigurerar den äldre Windows VPN-klienten, om så önskas. Den här guiden använder Alltid på VPN i stället för den äldre Windows VPN-klienten eftersom Alltid på VPN-klienten tillåter slutanvändare att ansluta/koppla från Azure VPN utan att ha administratörsbehörighet till sin dator. 

Följande skript installerar klientcertifikatet som krävs för autentisering mot den virtuella nätverksgatewayen, hämtning och installation av VPN-paketet. Kom ihåg `<computer1>` `<computer2>` att byta ut och med önskade datorer. Du kan köra skriptet på så många datorer du vill `$sessions` genom att lägga till fler PowerShell-sessioner i matrisen. Ditt användningskonto måste vara administratör på var och en av dessa datorer. Om en av dessa datorer är den lokala datorn som du kör skriptet från måste du köra skriptet från en upphöjd PowerShell-session. 

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
            $virtualNetworkName `
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
Nu när du har konfigurerat vpn för punkt till plats kan du använda den för att montera Azure-filresursen på de datorer du konfigurerar via PowerShell. I följande exempel monteras resursen, resursens rotkatalog listas för att bevisa att resursen faktiskt är monterad och resursen avmonteras. Tyvärr är det inte möjligt att montera aktien ihållande över PowerShell-ommotning. Information om hur du monterar beständigt finns i [Använda en Azure-filresurs med Windows](storage-how-to-use-files-windows.md). 

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

## <a name="see-also"></a>Se även
- [Nätverksöverväganden för direkt azure-filresursåtkomst](storage-files-networking-overview.md)
- [Konfigurera ett P2S-VPN (Point-to-Site) på Linux för användning med Azure-filer](storage-files-configure-p2s-vpn-linux.md)
- [Konfigurera ett S2S-VPN (Site-to-Site) för användning med Azure-filer](storage-files-configure-s2s-vpn.md)
