---
title: 'Konfigurera BGP på en Azure VPN-gateway: Resource Manager och CLI | Microsoft Docs'
description: Den här artikeln vägleder dig genom att konfigurera BGP med en Azure VPN-gateway med hjälp av Azure Resource Manager och CLI.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: 98cd606ce930624ec5c591ffd8f13e0feae1a6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884043"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Hur du konfigurerar BGP på en Azure VPN-gateway med hjälp av CLI

Den här artikeln hjälper dig att aktivera BGP på plats-till-plats (S2S) VPN-anslutning mellan platser och VNet-till-VNet-anslutning (det vill säga en anslutning mellan virtuella nätverk) med hjälp av Azure Resource Manager-distributionsmodellen och Azure CLI.

## <a name="about-bgp"></a>Om BGP

BGP är standard routing protocol som ofta används på internet för att utbyta Routning och reachability information mellan två eller flera nätverk. BGP gör det möjligt för VPN-gatewayer och din lokala VPN-enheter som kallas BGP-peers eller grannar, för att utbyta vägar. Vägar informera båda gateways om tillgänglighet och tillgängligheten för prefix som ska gå igenom gateways eller routrar som ingår. BGP kan också aktivera transitroutning mellan flera nätverk genom att sprida de vägar som en gateway med BGP lär sig från en BGP-peer till andra BGP-peers.

Mer information om fördelarna med BGP och att förstå de tekniska krav och överväganden för att använda BGP finns [översikt av BGP med Azure VPN-gatewayer](vpn-gateway-bgp-overview.md).

Den här artikeln får du hjälp med följande uppgifter:

* [Aktivera BGP för VPN-gateway](#enablebgp) (krävs)

  Du kan sedan göra något av följande avsnitt eller båda:

* [Upprätta en anslutning mellan lokala BGP](#crossprembgp)
* [Upprätta en anslutning för VNet-till-VNet med BGP](#v2vbgp)

Var och en av dessa tre avsnitt utgör ett grundläggande byggblock för att aktivera BGP i nätverksanslutningen. Om du har slutfört alla tre avsnitt kan skapa du topologin som visas i följande diagram:

![BGP-topologi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Du kan kombinera dessa avsnitt för att skapa en mer komplex multihop övergångsnätverket som uppfyller dina behov.

## <a name ="enablebgp"></a>Aktivera BGP för VPN-gateway

Det här avsnittet krävs innan du utför stegen i de andra konfiguration med två avsnitt. Följande konfigurationssteg konfigurera BGP-parametrarna för Azure VPN-gateway som visas i följande diagram:

![BGP-gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Innan du börjar

Installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI 2.0](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Steg 1: Skapa och konfigurera TestVNet1

#### <a name="Login"></a>1. Ansluta till din prenumeration

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Skapa en resursgrupp

I följande exempel skapas en resursgrupp med namnet TestRG1 på plats ”eastus”. Om du redan har en resursgrupp i den region där du vill skapa det virtuella nätverket, kan du använda den i stället.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Skapa TestVNet1

I följande exempel skapas ett virtuellt nätverk med namnet TestVNet1 och tre undernät: GatewaySubnet FrontEnd och BackEnd. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

Det första kommandot skapar frontend-adressutrymme och undernätet FrontEnd. Det andra kommandot skapar en ytterligare adressutrymme för BackEnd-undernät. Kommandona tredje och fjärde skapa BackEnd-undernät och GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Steg 2: Skapa VPN-gateway med BGP parametrar för TestVNet1

#### <a name="1-create-the-public-ip-address"></a>1. Skapa offentlig IP-adress

Begär en offentlig IP-adress. Den offentliga IP-adressen allokeras till VPN-gateway som du skapar för det virtuella nätverket.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Skapa VPN-gateway med AS-nummer

Skapa den virtuella nätverksgatewayen för TestVNet1. BGP kräver en Ruttbaserad VPN-gateway. Du måste också ytterligare parametern `-Asn` ange autonoma systemnummer (ASN: er) för TestVNet1. Skapa en gateway kan ta ett tag (45 minuter eller mer) att slutföra. 

Om du kör kommandot med hjälp av den `--no-wait` parameter du inte ser några feedback eller utdata. Den `--no-wait` parameter gör det möjligt för en gateway som ska skapas i bakgrunden. Det innebär inte att VPN-gateway har skapats direkt.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Hämta Azure BGP-peer IP-adress

När gatewayen är skapad, måste du hämta BGP-peer IP-adress på Azure VPN-gatewayen. Den här adressen behövs för att konfigurera VPN-gateway som en BGP-peer för dina lokala VPN-enheter.

Kör följande kommando och kontrollera den `bgpSettings` avsnittet längst upp i utdata:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

När gatewayen har skapats kan använda du den här gatewayen för att upprätta en anslutning mellan platser eller en VNet-till-VNet-anslutning med BGP.

## <a name ="crossprembgp"></a>Upprätta en anslutning mellan lokala BGP

För att upprätta en anslutning mellan platser, måste du skapa en lokal nätverksgateway som representerar din lokala VPN-enhet. Sedan kan du ansluta Azure VPN-gateway med den lokala nätverksgatewayen. Men de här stegen är liknande skapa andra anslutningar, omfattar de ytterligare egenskaper som krävs för att ange parametrar för BGP-konfiguration.

![BGP för mellan platser](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Steg 1: Skapa och konfigurera den lokala nätverksgatewayen

Den här övningen fortsätter att skapa den konfiguration som visas i diagrammet. Ersätt värdena med de som du vill använda för din konfiguration. När du arbetar med lokala nätverksgatewayer tänka på följande saker:

* Den lokala nätverksgatewayen kan vara på samma plats och resursgruppen som VPN-gateway eller det kan vara i en annan plats och resursgruppen. Det här exemplet visar gateway i olika resursgrupper på olika platser.
* Det minsta prefix som du måste deklarera för den lokala nätverksgatewayen är värdadressen för BGP peer-IP-adress på VPN-enhet. I det här fallet är det en /32 prefixet 10.52.255.254/32.
* Du måste använda olika BGP ASN: er mellan dina lokala nätverk och virtuella Azure-nätverket som en påminnelse. Om de är samma som du behöver ändra ditt VNet ASN om lokala VPN-enheter redan använder det autonoma Systemnumret till peer-datorn med andra BGP-grannar.

Innan du går vidare kontrollerar du att du har slutfört den [aktivera BGP för VPN-gateway](#enablebgp) avsnitt i den här övningen och att du fortfarande är ansluten till prenumerationen 1. Observera att i det här exemplet skapar du en ny resursgrupp. Observera också två ytterligare parametrar för den lokala nätverksgatewayen: `Asn` och `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Steg 2: Anslut VNet gateway och lokal nätverksgateway

I det här steget skapar du anslutningen från TestVNet1 till Site5. Du måste ange den `--enable-bgp` parametern för att aktivera BGP för den här anslutningen. 

I det här exemplet är det virtuella nätverkets gateway och lokal nätverksgateway i olika resursgrupper. När gateway finns i olika resursgrupper, måste du ange hela resurs-ID för två gateways för att upprätta en anslutning mellan virtuella nätverk.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Hämta resurs-ID för VNet1GW

Du kan använda utdata från kommandot för att hämta resurs-ID för VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

I resultatet är att hitta den `"id":` rad. Du måste värden inom citattecken för att skapa anslutningen i nästa avsnitt.

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

Kopiera värdena efter `"id":` till en textredigerare, till exempel Anteckningar, så att du enkelt kan klistra in dem när du skapar din anslutning. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Hämta resurs-ID för Site5

Använd följande kommando för att hämta resurs-ID Site5 från utdata:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Skapa TestVNet1 till Site5-anslutning

I det här steget skapar du anslutningen från TestVNet1 till Site5. Som tidigare diskuterats, är det möjligt att ha både BGP och icke-BGP-anslutningar för samma Azure VPN-gateway. Om BGP är aktiverat i Anslutningsegenskapen kommer Azure inte aktivera BGP för anslutningen, även om BGP parametrar har redan konfigurerats på båda gateways. Ersätt prenumerations-ID: N med din egen.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

I följande exempel visar parametrar för att ange i konfigurationsavsnittet BGP i din lokala VPN-enhet för den här övningen:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Anslutningen bör vara upprättad efter några minuter. BGP-peeringsessionen startar när IPsec-anslutning har upprättats.

## <a name ="v2vbgp"></a>Upprätta en anslutning för VNet-till-VNet med BGP

Det här avsnittet lägger till en VNet-till-VNet-anslutning med BGP, som visas i följande diagram: 

![BGP för VNet-till-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Följande instruktioner fortsätta från stegen i föregående avsnitt. Om du vill skapa och konfigurera TestVNet1 och VPN-gateway med BGP, måste du slutföra de [aktivera BGP för VPN-gateway](#enablebgp) avsnitt.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Steg 1: Skapa TestVNet2 och VPN-gateway

Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket TestVNet2, inte överlappar med någon av VNet-intervall.

I det här exemplet tillhör de virtuella nätverken samma prenumeration. Du kan konfigurera VNet-till-VNet-anslutningar mellan olika prenumerationer. Läs mer i [konfigurera VNet-till-VNet-anslutningen](vpn-gateway-howto-vnet-vnet-cli.md). Kontrollera att du lägger till `-EnableBgp $True` när du skapar anslutningar för att aktivera BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Skapa en ny resursgrupp

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Skapa TestVNet2 i den nya resursgruppen

Det första kommandot skapar frontend-adressutrymme och undernätet FrontEnd. Det andra kommandot skapar en ytterligare adressutrymme för BackEnd-undernät. Kommandona tredje och fjärde skapa BackEnd-undernät och GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Skapa offentlig IP-adress

Begär en offentlig IP-adress. Den offentliga IP-adressen allokeras till VPN-gateway som du skapar för det virtuella nätverket.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Skapa VPN-gateway med AS-nummer

Skapa den virtuella nätverksgatewayen för TestVNet2. Du måste åsidosätta standardvärdet ASN på Azure VPN-gatewayer. ASN: er för anslutna virtuella nätverk måste vara olika för att aktivera BGP och transitroutning.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Steg 2: Anslut TestVNet1 och TestVNet2 gateway

I det här steget skapar du anslutningen från TestVNet1 till Site5. För att aktivera BGP för den här anslutningen måste du ange den `--enable-bgp` parameter.

I följande exempel är den virtuella nätverksgatewayen och lokal nätverksgateway i olika resursgrupper. När gateway finns i olika resursgrupper, måste du ange hela resurs-ID för två gateways för att upprätta en anslutning mellan virtuella nätverk. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Hämta resurs-ID för VNet1GW 

Hämta en resurs-ID VNet1GW från utdata från kommandot:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Hämta resurs-ID för VNet2GW

Hämta en resurs-ID VNet2GW från utdata från kommandot:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Skapa anslutningar

Skapa anslutningen från TestVNet1 till TestVNet2 och anslutningen från TestVNet2 till TestVNet1. Ersätt prenumerations-ID: N med din egen.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Aktivera BGP för *både* anslutningar.
> 
> 

När du har slutfört de här stegen kan upprätta en anslutning om några minuter. BGP-peeringsessionen är när VNet-till-VNet-anslutningen är klar.

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till din virtuella nätverk. Anvisningar finns [skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
