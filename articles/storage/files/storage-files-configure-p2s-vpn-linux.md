---
title: Konfigurera ett P2S-VPN (Point-to-Site) på Linux för användning med Azure Files | Microsoft-dokument
description: Konfigurera en P2S-VPN (Point-to-Site) på Linux för användning med Azure-filer
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cfff05ed52258ee448d83a521b99dca7d356a0f9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061051"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Konfigurera ett P2S-VPN (Point-to-Site) på Linux för användning med Azure-filer
Du kan använda en P2S-VPN-anslutning (Point-to-Site) för att montera dina Azure-filresurser via SMB utanför Azure, utan att öppna port 445. En Punkt-till-plats VPN-anslutning är en VPN-anslutning mellan Azure och en enskild klient. Om du vill använda en P2S VPN-anslutning med Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. Om du har många klienter som behöver ansluta till dina Azure-filresurser från ditt lokala nätverk kan du använda en S2S-anslutning (Site-to-Site) i stället för en Point-to-Site-anslutning för varje klient. Mer information finns i [Konfigurera ett plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).

Vi rekommenderar starkt att du läser [Azure Files nätverksöversikt](storage-files-networking-overview.md) innan du fortsätter med den här artikeln för en fullständig diskussion om nätverksalternativen som är tillgängliga för Azure-filer.

Artikeln beskriver stegen för att konfigurera en Point-to-Site VPN på Linux för att montera Azure-filresurser direkt lokalt. Om du vill dirigera Azure File Sync-trafik via en VPN läser du [konfigurera proxy- och brandväggsinställningar för Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Krav
- Den senaste versionen av Azure CLI. Mer information om hur du installerar Azure CLI finns i [Installera Azure PowerShell CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och välj operativsystem. Om du föredrar att använda Azure PowerShell-modulen på Linux kan du, men instruktionerna nedan presenteras för Azure CLI.

- En Azure-filresurs som du vill montera lokalt. Azure-filresurser distribueras i lagringskonton, som är hanteringskonstruktioner som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blob-behållare eller köer. Du kan läsa mer om hur du distribuerar Azure-filresurser och lagringskonton i [Skapa en Azure-filresurs](storage-how-to-create-file-share.md).

- En privat slutpunkt för lagringskontot som innehåller den Azure-filresurs som du vill montera lokalt. Mer information om hur du skapar en privat slutpunkt finns i [Konfigurera slutpunkter för Azure Files-nätverk](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>Installera nödvändig programvara
Azure-gatewayen för virtuella nätverk kan tillhandahålla VPN-anslutningar med flera VPN-protokoll, inklusive IPsec och OpenVPN. Den här guiden visar hur du använder IPsec och använder strongSwan-paketet för att ge stöd på Linux. 

> Verifierad med Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Distribuera ett virtuellt nätverk 
Om du vill komma åt din Azure-filresurs och andra Azure-resurser från lokala via ett Point-to-Site VPN måste du skapa ett virtuellt nätverk eller ett virtuellt nätverk. Den P2S VPN-anslutning som du automatiskt skapar är en brygga mellan din lokala Linux-dator och det virtuella Azure-nätverket.

Följande skript skapar ett virtuellt Azure-nätverk med tre undernät: ett för ditt lagringskontos tjänstslutpunkt, en för ditt lagringskontos privata slutpunkt, som krävs för att komma åt lagringskontot lokalt utan att skapa anpassad routning för den offentliga IP-adressen för lagringskontot som kan ändras och en för din virtuella nätverksgateway som tillhandahåller VPN-tjänsten. 

Kom ihåg `<region>` `<resource-group>`att `<desired-vnet-name>` ersätta , och med lämpliga värden för din miljö.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>Skapa certifikat för VPN-autentisering
För att VPN-anslutningar från dina lokala Linux-datorer ska autentiseras för att komma åt ditt virtuella nätverk måste du skapa två certifikat: ett rotcertifikat, som kommer att tillhandahållas till den virtuella datorn gateway, och ett klientcertifikat, som kommer att signerades med rotcertifikatet. Följande skript skapar de certifikat som krävs.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Distribuera virtuell nätverksgateway
Azure-gatewayen för virtuella nätverk är den tjänst som dina lokala Linux-datorer ansluter till. Distribuera den här tjänsten kräver två grundläggande komponenter: en offentlig IP som identifierar gatewayen till dina klienter var de än är i världen och ett rotcertifikat som du skapade tidigare som kommer att användas för att autentisera dina klienter.

Kom ihåg `<desired-vpn-name-here>` att ersätta med det namn du vill ha för dessa resurser.

> [!Note]  
> Det kan ta upp till 45 minuter att distribuera azure-gatewayen för virtuella nätverk. Medan den här resursen distribueras blockerar det här bash-skriptet för distributionen som ska slutföras. Detta är normalt.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Konfigurera VPN-klienten
Azure-gatewayen för virtuella nätverk skapar ett nedladdningsbart paket med konfigurationsfiler som krävs för att initiera VPN-anslutningen på din lokala Linux-dator. Följande skript placerar de certifikat som du skapade `ipsec.conf` på rätt plats och konfigurerar filen med rätt värden från konfigurationsfilen i det nedladdningsbara paketet.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Montera Azure-filresurs
Nu när du har konfigurerat vpn för punkt till plats kan du montera din Azure-filresurs. I följande exempel monteras resursen som inte är beständig. Information om hur du monterar beständigt finns i [Använda en Azure-filresurs med Linux](storage-how-to-use-files-linux.md). 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Se även
- [Översikt över Azure Files-nätverk](storage-files-networking-overview.md)
- [Konfigurera ett P2S-VPN (Point-to-Site) i Windows för användning med Azure-filer](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurera ett S2S-VPN (Site-to-Site) för användning med Azure-filer](storage-files-configure-s2s-vpn.md)