---
title: "Ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk: VPN från plats till plats: CLI | Microsoft Docs"
description: "Steg för att skapa en IPsec-anslutning från ditt lokala nätverk till ett virtuellt Azure-nätverk via offentligt Internet. Dessa steg hjälper dig att skapa en plats-till-plats-anslutning med VPN-gateway med hjälp av CLI."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2017
ms.author: cherylmc
ms.openlocfilehash: 64c08400c39013f2bfc5bcc57eb21839ad69490b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med CLI

Den här artikeln visar hur du kan använda Azure CLI för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:<br>

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>


![Diagram över plats-till-plats-anslutning med VPN-gateway](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-diagram.png)

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Kontrollera att du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till.
* Verifiera att du har installerat den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI 2.0](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

### <a name="example"></a>Exempelvärden

Du kan använda följande värden till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.0.0/24 
GatewaySubnet           = 10.11.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. Ansluta till din prenumeration

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="rg"></a>2. Skapa en resursgrupp

I följande exempel skapas en resursgrupp med namnet ”TestRG1” på platsen ”eastus”. Om du redan har en resursgrupp i regionen där du vill skapa ditt virtuella nätverk kan du använda den i stället.

```azurecli
az group create --name TestRG1 --location eastus
```

## <a name="VNet"></a>3. Skapa ett virtuellt nätverk

Om du inte redan har ett virtuellt nätverk skapar du ett med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). När du skapar ett virtuellt nätverk ska du kontrollera att de adressutrymmen du anger inte överlappar några adressutrymmen som du har i det lokala nätverket.

>[!NOTE]
>För att detta VNet ska anslutas till en lokal plats måste du kontakta den lokala nätverksadministratören för att få ett intervall med IP-adresser som du kan använda specifikt för det här virtuella nätverket. Annars dirigeras inte trafiken rätt om det finns ett duplicerat adressintervall på båda sidorna av VPN-anslutningen.
>
>

I följande exempel skapas ett virtuellt nätverk med namnet ”TestVNet1” och ett undernät, ”Subnet1”.

```azurecli
az network vnet create --name TestVNet1 --resource-group TestRG1 --address-prefix 10.11.0.0/16 --location eastus --subnet-name Subnet1 --subnet-prefix 10.11.0.0/24
```

## 4. <a name="gwsub"></a>Skapa gateway-undernätet

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

För den här konfigurationen behöver du också ett gateway-undernät. Den virtuella nätverksgatewayen använder ett gatewayundernät som innehåller de IP-adresser som VPN-gatewaytjänsterna använder. När du skapar ett gateway-undernät måste det få namnet ”GatewaySubnet”. Om du ger det något annat namn kan du skapa undernätet, men Azure behandlar det inte som ett gateway-undernät.

Storleken på gatewayundernätet du anger beror på konfigurationen av VPN-gatewayen du vill skapa. Även om det är möjligt att skapa ett gateway-undernät som är så litet som /29 så rekommenderar vi att du skapar ett större undernät som inkluderar fler adresser genom att välja /27 eller /28. Om du använder det större nätverksundernätet får du tillräckligt många IP-adresser för att hantera möjliga framtida konfigurationer.

Använd kommandot [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) för att skapa ett gateway-undernätet.

```azurecli
az network vnet subnet create --address-prefix 10.11.255.0/27 --name GatewaySubnet --resource-group TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5. Skapa den lokala nätverksgatewayen

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. Du namnger webbplatsen så att Azure kan referera till den och sedan anger du IP-adressen för den lokala VPN-enhet som du skapar en anslutning till. Du anger också IP-adressprefixen som ska dirigeras via VPN-gatewayen till VPN-enheten. Adressprefixen du anger är de prefix som finns på det lokala nätverket. Det är enkelt att uppdatera dessa prefix om det lokala nätverket ändras.

Ange följande värden:

* *--gateway-ip-address* är IP-adressen till den lokala VPN-enheten. VPN-enheten får inte finnas bakom en NAT.
* *--local-address-prefixes* är dina lokala adressutrymmen.

Använd kommandot [az network local-gateway create](/cli/azure/network/local-gateway#az_network_local_gateway_create) för att lägga till en lokal nätverksgateway med flera adressprefix:

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --resource-group TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. Begär en offentlig IP-adress

En VPN-gateway måste ha en offentlig IP-adress. Först begär du IP-adressresursen och sedan hänvisar du till den när du skapar din virtuella nätverksgateway. IP-adressen tilldelas dynamiskt till resursen när en VPN-gateway har skapats. VPN-gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Du kan inte begära en statisk offentlig IP-adresstilldelning. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

Använd kommandot [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) för att begära en dynamisk offentlig IP-adress.

```azurecli
az network public-ip create --name VNet1GWIP --resource-group TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. Skapa VPN-gatewayen

Skapa den virtuella VPN-nätverksgatewayen. Det kan ta upp till 45 minuter att skapa en VPN-gateway.

Ange följande värden:

* *--gateway-type* för en plats-till-plats-konfiguration är *Vpn*. Gateway-typen gäller alltid den konfiguration som du implementerar. Se [Gatewaytyper](vpn-gateway-about-vpn-gateway-settings.md#gwtype) för mer information.
* *--vpn-type* kan vara *RouteBased* (kallas en dynamisk gateway i viss dokumentation) eller *PolicyBased* (kallas en statisk gateway i viss dokumentation). Inställningen gäller krav på den enhet som du ansluter till. Mer information om VPN-gatewaytyper finns i [Om konfigurationsinställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* Markera den gateway SKU som du vill använda. Det finns konfigurationsbegränsningar för vissa SKU:er. Se [Gateway SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för mer information.

Skapa en VPN-gateway med kommandot [az network vnet-gateway create](/cli/azure/network/vnet-gateway#az_network_vnet_gateway_create). Om du kör det här kommandot med parametern ”--no-wait” ser du ingen feedback eller utdata. Den här parametern gör att gatewayen kan skapas i bakgrunden. Det tar cirka 45 minuter att skapa en gateway.

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWIP --resource-group TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait 
```

## <a name="VPNDevice"></a>8. Konfigurera din VPN-enhet

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. I det här steget konfigurerar du VPN-enheten. När du konfigurerar VPN-enheten behöver du följande:

- En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
- Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. Använd kommandot [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list) för att hitta den offentliga IP-adressen till din virtuella nätverksgateway. För att läsningen ska gå lätt är utdata formaterade för att visa listan över offentliga IP-adresser i tabellformat.

  ```azurecli
  az network public-ip list --resource-group TestRG1 --output table
  ```


[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>9. Skapa VPN-anslutningen

Skapa VPN-anslutningen för plats-till-plats mellan din virtuella nätverksgateway och din lokala VPN-enhet. Var extra uppmärksam till värdet för den delade nyckeln, som måste matcha det konfigurerade värde för delad nyckel för din VPN-enhet.

Skapa anslutningen med kommandot [az network vpn-connection create](/cli/azure/network/vpn-connection#az_network_vpn_connection_create).

```azurecli
az network vpn-connection create --name VNet1toSite2 -resource-group TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Efter en kort stund har anslutningen upprättats.

## <a name="toverify"></a>10. Verifiera VPN-anslutningen

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

Om du vill använda en annan metod för att verifiera anslutningen kan du läsa [Verifiera en anslutning till VPN-gateway](vpn-gateway-verify-connection-resource-manager.md).

## <a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="tasks"></a>Vanliga åtgärder

Det här avsnittet tar upp vanliga kommandon som är användbara när du arbetar med plats-till-plats-konfigurationer. En fullständig lista över CLI-nätverkskommandon finns i [Azure CLI - Networking](/cli/azure/network) (Azure CLI – nätverk).

[!INCLUDE [local network gateway common tasks](../../includes/vpn-gateway-common-tasks-cli-include.md)]

## <a name="next-steps"></a>Nästa steg

* När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Information om tvingad tunneltrafik finns i [Om forcerade tunnlar](vpn-gateway-forced-tunneling-rm.md).
* Mer information om aktiv-aktiv-anslutningar med hög tillgänglighet finns i [Anslutning med hög tillgänglighet på flera platser och VNet-till-VNet-anslutning](vpn-gateway-highlyavailable.md).
* Om du vill ha en lista över Azure CLI-nätverkskommandon finns det i [Azure CLI](https://docs.microsoft.com/cli/azure/network).
* Information om hur du skapar en VPN-anslutning från plats till plats med en Azure Resource Manager-mall finns i [Skapa en plats-till-plats-anslutning via VPN](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Information om hur du skapar en VPN-anslutning mellan två virtuella nätverk med en Azure Resource Manager-mall finns i [Distribuera HBase-georeplikering](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
