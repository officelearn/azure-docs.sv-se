---
title: Konfigurera BGP på Azure VPN Gateway:CLI
description: I den här artikeln får du hjälp med att konfigurera BGP med en Azure VPN-gateway med hjälp av Azure Resource Manager och CLI.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: 42a07ac00fd8a26918164f6547bf57c2b021d14c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863622"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Konfigurera BGP på en Azure VPN-gateway med CLI

Den här artikeln hjälper dig att aktivera BGP på en extra lokal S2S-anslutning (Plats-till-plats) och en VNet-till-VNet-anslutning (det vill säga en anslutning mellan virtuella nätverk) med hjälp av Distributionsmodellen för Azure Resource Manager och Azure CLI.

## <a name="about-bgp"></a>Om BGP

BGP är det standardroutningsprotokoll som vanligen används på Internet för att utbyta routning och information om räckvidd mellan två eller flera nätverk. BGP gör det möjligt för VPN-gateways och dina lokala VPN-enheter, så kallade BGP-peer- eller grannar, att utbyta rutter. Rutterna informerar båda gateways om tillgänglighet och nåbarhet för prefix för att gå igenom de inblandade gateways eller routrarna. BGP kan också aktivera transitroutning mellan flera nätverk genom att sprida de vägar som en BGP-gateway lär sig från en BGP-peer, till alla andra BGP-peer-datorer.

Mer information om fördelarna med BGP och för att förstå de tekniska kraven och övervägandena med att använda BGP finns i [Översikt över BGP med Azure VPN-gateways](vpn-gateway-bgp-overview.md).

Den här artikeln hjälper dig med följande uppgifter:

* [Aktivera BGP för din VPN-gateway](#enablebgp) (obligatoriskt)

  Du kan sedan fylla i något av följande avsnitt eller både och:

* [Upprätta en anslutning mellan lokaler med BGP](#crossprembgp)
* [Upprätta en VNet-till-VNet-anslutning med BGP](#v2vbgp)

Var och en av dessa tre avsnitt utgör en grundläggande byggsten för att aktivera BGP i nätverksanslutningen. Om du fyller i alla tre avsnitten skapar du topologin enligt följande diagram:

![BGP-topologi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Du kan kombinera dessa avsnitt för att skapa ett mer komplext multihoptransitnätverk som uppfyller dina behov.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>Aktivera BGP för din VPN-gateway

Det här avsnittet krävs innan du utför något av stegen i de andra två konfigurationsavsnitten. Följande konfigurationssteg ställer in BGP-parametrarna för Azure VPN-gatewayen som visas i följande diagram:

![BGP-gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Innan du börjar

Installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Steg 1: Skapa och konfigurera TestVNet1

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Anslut till din prenumeration

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Skapa en resursgrupp

I följande exempel skapas en resursgrupp med namnet TestRG1 på platsen "eastus". Om du redan har en resursgrupp i den region där du vill skapa det virtuella nätverket kan du använda den i stället.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Skapa TestVNet1

I följande exempel skapas ett virtuellt nätverk med namnet TestVNet1 och tre undernät: GatewaySubnet, FrontEnd och BackEnd. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

Det första kommandot skapar klientdelens adressutrymme och FrontEnd-undernätet. Det andra kommandot skapar ytterligare ett adressutrymme för BackEnd-undernätet. Det tredje och fjärde kommandona skapar BackEnd-undernätet och GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Steg 2: Skapa VPN-gatewayen för TestVNet1 med BGP-parametrar

#### <a name="1-create-the-public-ip-address"></a>1. Skapa den offentliga IP-adressen

Begär en offentlig IP-adress. Den offentliga IP-adressen kommer att tilldelas den VPN-gateway som du skapar för ditt virtuella nätverk.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Skapa VPN-gatewayen med AS-numret

Skapa den virtuella nätverksgatewayen för TestVNet1. BGP kräver en ruttbaserad VPN-gateway. Du behöver också `-Asn` ytterligare parameter för att ställa in det autonoma systemnumret (ASN) för TestVNet1. Det kan ta en stund (45 minuter eller mer) att skapa en gateway. 

Om du kör det `--no-wait` här kommandot med hjälp av parametern visas ingen feedback eller utdata. Parametern `--no-wait` gör att gatewayen kan skapas i bakgrunden. Det betyder inte att VPN-gatewayen skapas omedelbart.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Skaffa Azure BGP-peer IP-adress

När gatewayen har skapats måste du hämta BGP-peer IP-adressen på Azure VPN-gatewayen. Den här adressen behövs för att konfigurera VPN-gatewayen som en BGP-peer för dina lokala VPN-enheter.

Kör följande kommando och `bgpSettings` kontrollera avsnittet högst upp i utdata:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

När gatewayen har skapats kan du använda den här gatewayen för att upprätta en anslutning mellan lokala och lokala nätverk eller en VNet-till-VNet-anslutning med BGP.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Upprätta en anslutning mellan lokaler med BGP

Om du vill upprätta en anslutning över flera lokaler måste du skapa en lokal nätverksgateway för att representera din lokala VPN-enhet. Sedan ansluter du Azure VPN-gatewayen med den lokala nätverksgatewayen. Även om dessa steg liknar att skapa andra anslutningar, innehåller de ytterligare egenskaper som krävs för att ange BGP-konfigurationsparametrarna.

![BGP för gränsöverskridande](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Steg 1: Skapa och konfigurera den lokala nätverksgatewayen

Den här övningen fortsätter att skapa konfigurationen som visas i diagrammet. Ersätt värdena med de som du vill använda för din konfiguration. Tänk på följande när du arbetar med lokala nätverksgatewayer:

* Den lokala nätverksgatewayen kan finnas på samma plats och resursgrupp som VPN-gatewayen, eller så kan den finnas på en annan plats och resursgrupp. I det här exemplet visas gateways i olika resursgrupper på olika platser.
* Det minsta prefix som du behöver deklarera för den lokala nätverksgatewayen är värdadressen för din BGP-peer-IP-adress på din VPN-enhet. I det här fallet är det ett /32 prefix på 10.51.255.254/32.
* Som en påminnelse måste du använda olika BGP ASN mellan dina lokala nätverk och det virtuella Azure-nätverket. Om de är desamma måste du ändra ditt virtuella nätverks-ASN om dina lokala VPN-enheter redan använder ASN för att peer med andra BGP-grannar.

Innan du fortsätter kontrollerar du att du har slutfört avsnittet [Aktivera BGP för din VPN-gateway](#enablebgp) i den här övningen och att du fortfarande är ansluten till prenumeration 1. Observera att du i det här exemplet skapar en ny resursgrupp. Lägg också märke till de två ytterligare `Asn` `BgpPeerAddress`parametrarna för den lokala nätverksgatewayen: och .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Steg 2: Anslut VNet-gatewayen och den lokala nätverksgatewayen

I det här steget skapar du anslutningen från TestVNet1 till Site5. Du måste `--enable-bgp` ange parametern för att aktivera BGP för den här anslutningen. 

I det här exemplet finns den virtuella nätverksgatewayen och den lokala nätverksgatewayen i olika resursgrupper. När gatewayerna finns i olika resursgrupper måste du ange hela resurs-ID för de två gatewayerna för att ställa in en anslutning mellan de virtuella nätverken.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Hämta resurs-ID för VNet1GW

Använd utdata från följande kommando för att hämta resurs-ID för VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Leta reda på `"id":` raden i utdata. Du behöver värdena inom citattecknen för att skapa anslutningen i nästa avsnitt.

Exempel på utdata:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Kopiera värdena `"id":` efter till en textredigerare, till exempel Anteckningar, så att du enkelt kan klistra in dem när du skapar anslutningen. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Hämta resurs-ID för Site5

Använd följande kommando för att hämta resurs-ID:et för Site5 från utdata:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Skapa TestVNet1-till-Site5-anslutningen

I det här steget skapar du anslutningen från TestVNet1 till Site5. Som diskuterats tidigare är det möjligt att ha både BGP- och icke-BGP-anslutningar för samma Azure VPN-gateway. Om inte BGP är aktiverat i anslutningsegenskapen aktiverar Azure inte BGP för den här anslutningen, även om BGP-parametrar redan är konfigurerade på båda gatewayerna. Ersätt prenumerations-ID:na mot dina egna.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

I det här exemplet visas följande exempel de parametrar som ska anges i bgp-konfigurationsavsnittet på din lokala VPN-enhet:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Anslutningen bör vara upprättad efter några minuter. BGP-peering-sessionen startar när IPsec-anslutningen har upprättats.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Upprätta en VNet-till-VNet-anslutning med BGP

I det här avsnittet läggs en VNet-till-VNet-anslutning till BGP, som visas i följande diagram: 

![BGP för VNet-till-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Följande instruktioner fortsätter från stegen i föregående avsnitt. Om du vill skapa och konfigurera TestVNet1 och VPN-gatewayen med BGP måste du slutföra avsnittet [Aktivera BGP för din VPN-gateway.](#enablebgp)

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Steg 1: Skapa TestVNet2 och VPN-gatewayen

Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket, TestVNet2, inte överlappar något av dina virtuella nätverksintervall.

I det här exemplet tillhör de virtuella nätverken samma prenumeration. Du kan ställa in VNet-till-VNet-anslutningar mellan olika prenumerationer. Mer information finns i [Konfigurera en VNet-till-VNet-anslutning](vpn-gateway-howto-vnet-vnet-cli.md). Se till att `-EnableBgp $True` du lägger till när du skapar anslutningarna för att aktivera BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Skapa en ny resursgrupp

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Skapa TestVNet2 i den nya resursgruppen

Det första kommandot skapar klientdelens adressutrymme och FrontEnd-undernätet. Det andra kommandot skapar ytterligare ett adressutrymme för BackEnd-undernätet. Det tredje och fjärde kommandona skapar BackEnd-undernätet och GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Skapa den offentliga IP-adressen

Begär en offentlig IP-adress. Den offentliga IP-adressen kommer att tilldelas den VPN-gateway som du skapar för ditt virtuella nätverk.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Skapa VPN-gatewayen med AS-numret

Skapa den virtuella nätverksgatewayen för TestVNet2. Du måste åsidosätta standard-ASN på dina Azure VPN-gateways. ASN för anslutna virtuella nätverk måste vara olika för att aktivera BGP och transitroutning.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Steg 2: Anslut testVNet1- och TestVNet2-gatewayerna

I det här steget skapar du anslutningen från TestVNet1 till Site5. Om du vill aktivera BGP för `--enable-bgp` den här anslutningen måste du ange parametern.

I följande exempel finns den virtuella nätverksgatewayen och den lokala nätverksgatewayen i olika resursgrupper. När gatewayerna finns i olika resursgrupper måste du ange hela resurs-ID för de två gatewayerna för att ställa in en anslutning mellan de virtuella nätverken. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Hämta resurs-ID för VNet1GW 

Hämta resurs-ID för VNet1GW från utdata från följande kommando:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Hämta resurs-ID för VNet2GW

Hämta resurs-ID för VNet2GW från utdata från följande kommando:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Skapa anslutningarna

Skapa anslutningen från TestVNet1 till TestVNet2 och anslutningen från TestVNet2 till TestVNet1. Ersätt prenumerations-ID:na mot dina egna.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Aktivera BGP för *båda* anslutningarna.
> 
> 

När du har slutfört de här stegen upprättas anslutningen inom några minuter. BGP-peering-sessionen kommer att vara uppe när VNet-till-VNet-anslutningen har slutförts.

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer i dina virtuella nätverk. Steg för steg finns i [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
