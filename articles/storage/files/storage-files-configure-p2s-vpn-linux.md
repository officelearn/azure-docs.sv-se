---
title: Konfigurera en punkt-till-plats (P2S) VPN på Linux som ska användas med Azure Files | Microsoft Docs
description: Så här konfigurerar du en punkt-till-plats (P2S) VPN i Linux för användning med Azure Files
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b988435fc6928d52321cb427e2412e7ca81680d2
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141747"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Konfigurera en punkt-till-plats (P2S) VPN på Linux som ska användas med Azure Files
Du kan använda en punkt-till-plats (P2S) VPN-anslutning för att montera dina Azure-filresurser över SMB utanför Azure, utan att öppna port 445. En punkt-till-plats-VPN-anslutning är en VPN-anslutning mellan Azure och en enskild klient. Om du vill använda en P2S VPN-anslutning med Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. Om du har många klienter som behöver ansluta till dina Azure-filresurser från ditt lokala nätverk kan du använda en plats-till-plats (S2S) VPN-anslutning i stället för en punkt-till-plats-anslutning för varje klient. Mer information finns i [Konfigurera en plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).

Vi rekommenderar starkt att du läser [Azure Files nätverks översikt](storage-files-networking-overview.md) innan du fortsätter med den här artikeln för en fullständig diskussion av de nätverks alternativ som är tillgängliga för Azure Files.

Artikeln beskriver stegen för att konfigurera en punkt-till-plats-VPN i Linux för att montera Azure-filresurser direkt lokalt. Om du vill dirigera Azure File Sync trafik via en VPN-anslutning kan du läsa [konfigurera Azure File Sync proxy-och brand Väggs inställningar](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Krav
- Den senaste versionen av Azure CLI. Mer information om hur du installerar Azure CLI finns i [installera Azure POWERSHELL CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och välj operativ system. Om du föredrar att använda Azure PowerShell-modulen i Linux, kan du följa anvisningarna nedan för Azure CLI.

- En Azure-filresurs som du vill montera lokalt. Du kan använda antingen en [standard](storage-how-to-create-file-share.md) -eller [Premium Azure-filresurs](storage-how-to-create-premium-fileshare.md) med punkt-till-plats-VPN.

## <a name="install-required-software"></a>Installera nödvändig program vara
Den virtuella Azure-Nätverksgatewayen kan tillhandahålla VPN-anslutningar med flera VPN-protokoll, inklusive IPsec och OpenVPN. Den här guiden visar hur du använder IPsec och använder strongSwan-paketet för att tillhandahålla support på Linux. 

> Verifierad med Ubuntu 18,10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Distribuera ett virtuellt nätverk 
För att få åtkomst till Azure-filresursen och andra Azure-resurser från lokala platser via en punkt-till-plats-VPN måste du skapa ett virtuellt nätverk eller ett virtuellt nätverk. P2S VPN-anslutningen som du skapar automatiskt är en brygga mellan den lokala Linux-datorn och det virtuella Azure-nätverket.

Följande skript skapar ett virtuellt Azure-nätverk med tre undernät: ett för lagrings kontots tjänst slut punkt, ett för lagrings kontots privata slut punkt, vilket krävs för att komma åt lagrings kontot lokalt utan att skapa anpassade routning för den offentliga IP-adressen för det lagrings konto som kan ändras och ett för din virtuella nätverksgateway som tillhandahåller VPN-tjänsten. 

Kom ihåg att ersätta `<region>`, `<resource-group>`och `<desired-vnet-name>` med lämpliga värden för din miljö.

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

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>Begränsa lagrings kontot till det virtuella nätverket
Som standard när du skapar ett lagrings konto kan du komma åt det från var som helst i världen, förutsatt att du har möjlighet att autentisera din begäran (till exempel med din Active Directory identitet eller med lagrings konto nyckeln). Om du vill begränsa åtkomsten till det här lagrings kontot till det virtuella nätverk som du nyss skapade måste du skapa en nätverks regel uppsättning som tillåter åtkomst i det virtuella nätverket och nekar all annan åtkomst.

Att begränsa lagrings kontot till det virtuella nätverket kräver att en tjänst slut punkt används. Tjänst slut punkten är en nätverks konstruktion som den offentliga DNS/offentliga IP-adressen endast kan nås från i det virtuella nätverket. Eftersom den offentliga IP-adressen inte garanteras vara densamma, vill vi i slut ändan använda en privat slut punkt i stället för en tjänst slut punkt för lagrings kontot, men det går inte att begränsa lagrings kontot om inte en tjänst slut punkt också exponeras.

Kom ihåg att ersätta `<storage-account-name>` med det lagrings konto som du vill komma åt.

```bash
storageAccountName="<storage-account-name>"

az storage account network-rule add \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --subnet $serviceEndpointSubnet > /dev/null

az storage account update \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" > /dev/null
```

## <a name="create-a-private-endpoint-preview"></a>Skapa en privat slut punkt (förhands granskning)
Att skapa en privat slut punkt för ditt lagrings konto ger ditt lagrings konto en IP-adress inom IP-adressutrymmet för det virtuella nätverket. När du monterar en Azure-filresurs från lokala datorer med den här privata IP-adressen, kommer de routningsregler som definierats automatiskt av VPN-installationen att skicka din monterings förfrågan till lagrings kontot via VPN. 

```bash
zoneName="privatelink.file.core.windows.net"

storageAccount=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "id" | tr -d '"')

az resource update \
    --ids $privateEndpointSubnet \
    --set properties.privateEndpointNetworkPolicies=Disabled > /dev/null

az network private-endpoint create \
    --resource-group $resourceGroupName \
    --name "$storageAccountName-PrivateEndpoint" \
    --location $region \
    --subnet $privateEndpointSubnet \
    --private-connection-resource-id $storageAccount \
    --group-ids "file" \
    --connection-name "privateEndpointConnection" > /dev/null

az network private-dns zone create \
    --resource-group $resourceGroupName \
    --name $zoneName > /dev/null

az network private-dns link vnet create \
    --resource-group $resourceGroupName \
    --zone-name $zoneName \
    --name "$virtualNetworkName-link" \
    --virtual-network $virtualNetworkName \
    --registration-enabled false > /dev/null

networkInterfaceId=$(az network private-endpoint show \
    --name "$storageAccountName-PrivateEndpoint" \
    --resource-group $resourceGroupName \
    --query 'networkInterfaces[0].id' | tr -d '"')
 
storageAccountPrivateIP=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query "properties.ipConfigurations[0].properties.privateIPAddress" | tr -d '"')

fqdnQuery="properties.ipConfigurations[0].properties.privateLinkConnectionProperties.fqdns[0]"
fqdn=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query $fqdnQuery | tr -d '"')

az network private-dns record-set a create \
    --name $storageAccountName \
    --zone-name $zoneName \
    --resource-group $resourceGroupName > /dev/null
```

## <a name="create-certificates-for-vpn-authentication"></a>Skapa certifikat för VPN-autentisering
För att VPN-anslutningar från dina lokala Linux-datorer ska kunna autentiseras för åtkomst till ditt virtuella nätverk måste du skapa två certifikat: ett rot certifikat som kommer att tillhandahållas till den virtuella datorns gateway och ett klient certifikat som kommer att signerat med rot certifikatet. Följande skript skapar de certifikat som krävs.

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
Den virtuella Azure-Nätverksgatewayen är den tjänst som dina lokala Linux-datorer kommer att ansluta till. Att distribuera den här tjänsten kräver två grundläggande komponenter: en offentlig IP-adress som identifierar gatewayen för dina klienter oavsett var de finns i världen och ett rot certifikat som du skapade tidigare och som ska användas för att autentisera dina klienter.

Kom ihåg att ersätta `<desired-vpn-name-here>` med det namn som du vill ha för dessa resurser.

> [!Note]  
> Det kan ta upp till 45 minuter att distribuera den virtuella Azure-Nätverksgatewayen. När den här resursen distribueras blockerar det här bash-skript skriptet för att distributionen ska slutföras. Detta är normalt.

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
Den virtuella Azure-Nätverksgatewayen kommer att skapa ett nedladdnings Bart paket med konfigurationsfiler som krävs för att initiera VPN-anslutningen på din lokala Linux-dator. Följande skript placerar de certifikat du skapade på rätt plats och konfigurerar `ipsec.conf`-filen med rätt värden från konfigurations filen i det nedladdnings bara paketet.

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
Nu när du har konfigurerat din punkt-till-plats-VPN kan du montera Azure-filresursen. I följande exempel monteras resursen icke-permanent. Information om hur du monterar permanent finns i [använda en Azure-filresurs med Linux](storage-how-to-use-files-linux.md). 

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

## <a name="see-also"></a>Se också
- [Översikt över Azure Files nätverk](storage-files-networking-overview.md)
- [Konfigurera en punkt-till-plats (P2S) VPN i Windows för användning med Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurera ett VPN för plats-till-plats (S2S) som ska användas med Azure Files](storage-files-configure-s2s-vpn.md)