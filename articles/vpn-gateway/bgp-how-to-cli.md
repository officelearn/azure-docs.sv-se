---
title: 'Konfigurera BGP på en Azure VPN-gateway: Resource Manager och CLI | Microsoft Docs'
description: Den här artikeln beskriver hur du konfigurerar BGP med Azure VPN gateway med hjälp av Azure Resource Manager och CLI.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38688565"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Hur du konfigurerar BGP på Azure VPN gateway med hjälp av CLI

Den här artikeln hjälper dig att aktivera BGP på en plats-till-plats (S2S) VPN-anslutning för flera platser och VNet-till-VNet-anslutning (det vill säga en anslutning mellan virtuella nätverk) med hjälp av Azure Resource Manager-distributionsmodellen och Azure CLI.

## <a name="about-bgp"></a>Om BGP

BGP är standard routningsprotokoll som ofta används på internet för att utbyta Routning och nätverksåtkomst information mellan två eller flera nätverk. BGP kan VPN-gatewayer och din lokala VPN-enheter kallas BGP-peer eller grannar, för att utbyta vägar. Vägarna informerar båda gatewayerna om åtkomsten för prefix som ska passera genom de gatewayer eller routrar som ingår. BGP kan också möjliggöra överföringsroutning mellan flera nätverk genom att sprida vägar som BGP-gatewayen får från en BGP-peer till alla andra BGP-peers.

Läs mer om fördelarna med BGP och för att förstå de tekniska krav och överväganden för att använda BGP, [översikt över BGP med Azure VPN gateway](vpn-gateway-bgp-overview.md).

Den här artikeln hjälper dig med följande uppgifter:

* [Aktivera BGP för din VPN-gateway](#enablebgp) (krävs)

  Du kan sedan slutföra någon av följande avsnitt eller båda:

* [Upprätta en anslutning mellan lokala BGP](#crossprembgp)
* [Upprätta en VNet-till-VNet-anslutning med BGP](#v2vbgp)

Var och en av dessa tre avsnitt utgör ett grundläggande byggblock för att aktivera BGP i nätverksanslutningen. Om du har slutfört alla tre avsnitt kan skapa du topologin som du ser i följande diagram:

![BGP-topologi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Du kan kombinera dessa avsnitt för att skapa en mer komplex multihop övergångsnätverket som uppfyller dina behov.

## <a name ="enablebgp"></a>Aktivera BGP för din VPN-gateway

Det här avsnittet krävs innan du utför stegen i de andra två konfigurationsavsnitt. Följande konfigurationssteg konfigurera BGP-parametrarna för Azure VPN-gatewayen som du ser i följande diagram:

![BGP-gatewayen](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Innan du börjar

Installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI 2.0](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Steg 1: Skapa och konfigurera TestVNet1

#### <a name="Login"></a>1. Ansluta till din prenumeration

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Skapa en resursgrupp

I följande exempel skapas en resursgrupp med namnet TestRG1 på platsen ”eastus”. Om du redan har en resursgrupp i regionen där du vill skapa det virtuella nätverket kan du använda den i stället.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Skapa TestVNet1

I följande exempel skapas ett virtuellt nätverk med namnet TestVNet1 och tre undernät: GatewaySubnet, FrontEnd och BackEnd. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

Det första kommandot skapar frontend adressutrymmet och undernätet på klientsidan. Det andra kommandot skapar en ytterligare adressutrymme för BackEnd-undernät. Tredje och fjärde kommandona skapar BackEnd-undernät och GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Steg 2: Skapa en VPN-gateway för TestVNet1 med BGP-parametrar

#### <a name="1-create-the-public-ip-address"></a>1. Skapa offentlig IP-adress

Begär en offentlig IP-adress. Offentliga IP-adress allokeras till VPN-gateway som du skapar för ditt virtuella nätverk.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Skapa en VPN-gateway med AS-nummer

Skapa den virtuella nätverksgatewayen för TestVNet1. BGP kräver en Ruttbaserad VPN-gateway. Du måste också ytterligare parametern `-Asn` att ställa in autonomt systemnummer (ASN) för TestVNet1. Kan ta en stund att skapa en gateway (45 minuter eller mer) att slutföra. 

Om du kör det här kommandot med hjälp av den `--no-wait` parameter, ser du ingen feedback eller utdata. Den `--no-wait` parametern gör att gatewayen kan skapas i bakgrunden. Det innebär inte att VPN-gatewayen har skapats direkt.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Skaffa Azure BGP-peer IP-adress

När gatewayen har skapats kan behöva du hämta BGP-peer IP-adressen på Azure VPN-gatewayen. Den här adressen behövs för att konfigurera VPN-gateway som en BGP-peer för dina lokala VPN-enheter.

Kör följande kommando och kontrollera den `bgpSettings` längst upp på utdata:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

När gatewayen har skapats kan använda du den här gatewayen för att upprätta en anslutning mellan olika platser eller en VNet-till-VNet-anslutning med BGP.

## <a name ="crossprembgp"></a>Upprätta en anslutning mellan lokala BGP

För att upprätta en anslutning mellan olika platser, måste du skapa en lokal nätverksgateway som representerar din lokala VPN-enhet. Sedan kan du ansluta Azure VPN-gateway med den lokala nätverksgatewayen. Även om de här stegen är liknande för att skapa andra anslutningar, inkluderar de ytterligare egenskaper som krävs för att ange parametrar för BGP-konfiguration.

![BGP för flera platser](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Steg 1: Skapa och konfigurera den lokala nätverksgatewayen

Den här övningen fortsätter att bygga den konfiguration som visas i diagrammet. Ersätt värdena med de som du vill använda för din konfiguration. När du arbetar med lokala nätverksgatewayer, Tänk på följande saker:

* Den lokala nätverksgatewayen kan vara i samma plats och resursgrupp som VPN-gateway eller det kan vara i en annan plats och resursgrupp. Det här exemplet visar gateway i olika resursgrupper på olika platser.
* Det minsta prefix som du behöver för att deklarera för den lokala nätverksgatewayen är värdadressen för BGP peer-IP-adress på VPN-enheten. I det här fallet är det en /32 prefixet för 10.52.255.254/32.
* Du måste använda olika BGP ASN: er mellan dina lokala nätverk och virtuella Azure-nätverket som en påminnelse. Om de är likadana, måste du ändra din VNet ASN om dina lokala VPN-enheter använder redan ASN att peer-kopplas med andra BGP-grannar.

Innan du fortsätter, kontrollera att du har slutfört den [aktivera BGP för din VPN-gateway](#enablebgp) avsnitt i den här övningen och att du fortfarande är ansluten till prenumeration 1. Observera att i det här exemplet skapar en ny resursgrupp. Lägg också märke till två ytterligare parametrar för den lokala nätverksgatewayen: `Asn` och `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Steg 2: Anslut VNet-gateway och lokal nätverksgateway

I det här steget skapar du anslutningen från TestVNet1 till Site5. Du måste ange den `--enable-bgp` parametern för att aktivera BGP för den här anslutningen. 

I det här exemplet är virtuella nätverksgateway och lokala nätverksgatewayen i olika resursgrupper. När gatewayer finns i olika resursgrupper, måste du ange hela resurs-ID för två gateway att upprätta en anslutning mellan virtuella nätverk.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Hämta resurs-ID för VNet1GW

Du kan använda utdata från följande kommando för att hämta resurs-ID för VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Utdata och hitta den `"id":` rad. Du behöver värden inom citattecken för att skapa anslutningen i nästa avsnitt.

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

Kopiera värdena efter `"id":` till en textredigerare, till exempel Anteckningar, så att du enkelt kan klistra in dem när du skapar anslutningen. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Hämta resurs-ID för Site5

Använd följande kommando för att hämta resurs-ID för Site5 från utdata:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Skapa TestVNet1-till-Site5-anslutning

I det här steget skapar du anslutningen från TestVNet1 till Site5. Som tidigare diskuterats, är det möjligt att ha både BGP och icke-BGP-anslutningar för samma Azure VPN-gateway. Om BGP är aktiverat i Anslutningsegenskapen kommer Azure inte aktivera BGP för anslutningen, även om BGP parametrar redan är inställda på båda gatewayerna. Ersätt prenumerations-ID: N med dina egna.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

I följande exempel visar en lista över parametrar för att ange i konfigurationsavsnittet BGP för din lokala VPN-enhet i den här övningen:

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

## <a name ="v2vbgp"></a>Upprätta en VNet-till-VNet-anslutning med BGP

Det här avsnittet lägger till en VNet-till-VNet-anslutning med BGP, enligt följande diagram: 

![BGP för VNet-till-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Anvisningarna fortsätter från stegen i föregående avsnitt. Om du vill skapa och konfigurera TestVNet1 och VPN-gateway med BGP, måste du slutföra de [aktivera BGP för din VPN-gateway](#enablebgp) avsnittet.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Steg 1: Skapa TestVNet2 och VPN-gateway

Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket TestVNet2, inte överlappar med något av dina VNet-intervall.

I det här exemplet är tillhöra de virtuella nätverken samma prenumeration. Du kan konfigurera en VNet-till-VNet-anslutningar mellan olika prenumerationer. Mer information finns i [konfigurera en VNet-till-VNet-anslutning](vpn-gateway-howto-vnet-vnet-cli.md). Se till att du lägger till `-EnableBgp $True` när du skapar anslutningar för att aktivera BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Skapa en ny resursgrupp

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Skapa TestVNet2 i den nya resursgruppen

Det första kommandot skapar frontend adressutrymmet och undernätet på klientsidan. Det andra kommandot skapar en ytterligare adressutrymme för BackEnd-undernät. Tredje och fjärde kommandona skapar BackEnd-undernät och GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Skapa offentlig IP-adress

Begär en offentlig IP-adress. Offentliga IP-adress allokeras till VPN-gateway som du skapar för ditt virtuella nätverk.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Skapa en VPN-gateway med AS-nummer

Skapa den virtuella nätverksgatewayen för TestVNet2. Du måste åsidosätta standard-ASN på Azure VPN-gatewayer. ASN: er för anslutna virtuella nätverk måste vara olika för att aktivera BGP och transit routning.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Steg 2: Anslut gateway för TestVNet1 och TestVNet2

I det här steget skapar du anslutningen från TestVNet1 till Site5. Om du vill aktivera BGP för den här anslutningen måste du ange den `--enable-bgp` parametern.

I följande exempel är gateway för virtuellt nätverk och lokala nätverksgatewayen i olika resursgrupper. När gatewayer finns i olika resursgrupper, måste du ange hela resurs-ID för två gateway att upprätta en anslutning mellan virtuella nätverk. 

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

Skapa anslutningen från TestVNet1 till TestVNet2 och anslutningen från TestVNet2 till TestVNet1. Ersätt prenumerations-ID: N med dina egna.

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

När du har slutfört de här stegen, upprätta en anslutning på några få minuter. BGP-peeringsessionen är upp när VNet-till-VNet-anslutningen har slutförts.

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Anvisningar finns i [skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
