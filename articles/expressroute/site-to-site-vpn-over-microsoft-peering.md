---
title: Konfigurera en plats-till-plats-VPN över Microsoft Azure ExpressRoute-peering | Microsoft Docs
description: Konfigurera IPsec/IKE-anslutningen till Azure via en peering Microsoft ExpressRoute-krets med hjälp av en plats-till-plats VPN-gateway.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: cherylmc
ms.openlocfilehash: 64203e2cbac1206224f0e0ad8b7d364f19ad0332
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
ms.locfileid: "26357736"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurera en plats-till-plats VPN-anslutning via Microsoft ExpressRoute-peering

Den här artikeln hjälper dig att konfigurera säker krypterad anslutning mellan ditt lokala nätverk och dina virtuella Azure-nätverk (Vnet) via en privat ExpressRoute-anslutning. Konfigurera en säker tunnel över ExpressRoute kan för datautbyte med sekretess, skydd mot omsändning, äkthetsbeviset och integritet.

## <a name="architecture"></a>Arkitektur

Du kan använda Microsoft-peering för att upprätta en plats-till-plats IPsec/IKE VPN-tunnel mellan din valda lokala nätverk och virtuella Azure-nätverk.

  ![Översikt över anslutning](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)

>[!NOTE]
>När du konfigurerar VPN för plats-till-plats över Microsoft-peering, debiteras du för VPN-gateway och VPN-utgång. Mer information finns i [priser för VPN-Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

Du kan konfigurera flera tunnlar via en ExpressRoute-krets två MSEE PE-par och aktivera belastningsutjämning mellan tunnlar för hög tillgänglighet och redundans.

  ![Alternativ för hög tillgänglighet](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

VPN-tunnlar över Microsoft-peering kan avslutas med hjälp av VPN-gateway eller använda en lämplig nätverket virtuell installation (NVA) tillgängliga via Azure Marketplace. Du kan utbyta vägar statiskt eller dynamiskt via krypterade tunnlar utan att exponera väg exchange till den underliggande Microsoft-peering. I exemplen i den här artikeln används BGP (skiljer sig från BGP-sessionen som används för att skapa Microsoft-peering) för att utbyta prefix dynamiskt via krypterade tunnlar.

>[!IMPORTANT]
>För den lokala sidan vanligtvis Microsoft-peering termineras på Perimeternätverket och privat peering termineras på zonen core nätverk. Två zoner separeras med brandväggar. Om du konfigurerar Microsoft exklusivt för att aktivera säker tunnelanslutning via ExpressRoute-peering, Kom ihåg att filtrera via endast den offentliga IP-adresser av intresse komma annonseras via Microsoft-peering.
>
>

## <a name="workflow"></a>Arbetsflöde

1. Konfigurera Microsoft-peering för ExpressRoute-kretsen.
2. Annonsera valda Azure regionala offentliga prefix till ditt lokala nätverk via Microsoft-peering.
3. Konfigurera en VPN-gateway och upprätta IPsec-tunnlar
4. Konfigurera lokala VPN-enhet.
5. Skapa plats-till-plats IPsec/IKE-anslutning.
6. (Valfritt) Konfigurera brandväggar/filtrering på lokala VPN-enhet.
7. Testa och validera IPsec-kommunikation via ExpressRoute-kretsen.

## <a name="peering"></a>1. Konfigurera Microsoft-peering

Om du vill konfigurera en plats-till-plats VPN-anslutning via ExpressRoute, måste du använda Microsoft ExpressRoute-peering.

* Om du vill konfigurera en ny ExpressRoute-krets, börja med den [ExpressRoute krav](expressroute-prerequisites.md) artikel, och sedan [skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-arm.md).

* Om du redan har en ExpressRoute-krets, men har inte konfigurerats för Microsoft-peering, konfigurerar du Microsoft-peering med hjälp av den [skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-arm.md#msft) artikel.

När du har konfigurerat dina krets och Microsoft-peering kan du lätt kan visa den med hjälp av den **översikt** sida i Azure-portalen.

![kretsen](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. Konfigurera filter för Routning

En vägfilter kan du identifiera tjänster du vill använda via ExpressRoute-krets Microsoft-peering. Det är i grunden en godkänd lista över alla värden för BGP-communityn. 

![vägfilter](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

I det här exemplet distributionen är endast i den *Azure West US 2* region. En väg filterregeln läggs så att endast en annons Azure West US 2 regionala prefix som har BGP community-värde *12076:51026*. Du anger de regionala prefix som du vill tillåta genom att välja **hantera regeln**.

I vägfiltret även måste du välja ExpressRoute-kretsar som vägen filtret gäller. Du kan välja ExpressRoute-kretsar genom att välja **lägga till krets**. Vägfiltret är kopplad till exempel ExpressRoute-krets i föregående bild.

### <a name="configfilter"></a>2.1 konfigurera route-filter

Konfigurera ett filter för vägen. Anvisningar finns [konfigurera route filter för Microsoft-peering](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2 Kontrollera BGP-vägar

När du har skapat Microsoft-peering över ExpressRoute-krets och tillhör kretsen vägfilter, kan du kontrollera BGP-vägar som togs emot från MSEEs på PE-enheter som peering med MSEEs. Kommandot verifiering varierar beroende på operativsystemet på PE-enheter.

#### <a name="cisco-examples"></a>Cisco-exempel

Det här exemplet används en Cisco IOS-XE-kommando. I det här exemplet används virtuella Routning och vidarebefordran av instansen (VRF) för att isolera trafiken peering.

```
show ip bgp vpnv4 vrf 10 summary
```

Följande partiella utdata visar att 68 prefix har tagits emot från grannen *.243.229.34 med ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Om du vill se en lista över prefix som togs emot från grannen, Använd följande exempel:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

För att bekräfta att du får rätt uppsättning prefix, kan du cross-kontrollera. Följande Azure PowerShell-kommandot innehåller prefix som annonseras via Microsoft-peering för varje tjänst och för varje Azure-regionen:

```powershell
Get-AzureRmBgpServiceCommunity
```

## <a name="vpngateway"></a>3. Konfigurera VPN-gateway och IPsec-tunnlar

I det här avsnittet skapas IPsec VPN-tunnlar mellan Azure VPN-gateway och lokala VPN-enhet. Exemplen använder Cisco Cloud Service Router (CSR1000) VPN-enheter.

Följande diagram visar IPsec VPN tunnlar mellan lokala VPN-enhet 1 och Azure VPN gateway-instans-par. Två IPsec VPN-tunnlar upprättas mellan lokala VPN-enhet 2 Azure VPN gateway-instans paret visas inte i diagram och konfigurationsinformationen visas inte. Men förbättrar med ytterligare VPN-tunnlar hög tillgänglighet.

  ![VPN-tunnlar](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

En eBGP-sessionen har upprättats för att utbyta privata nätverksvägar via IPSec-tunneln-par. Följande diagram visar den eBGP-session som upprättats via IPSec-tunneln paret:

  ![eBGP sessioner över tunnel par](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Följande diagram visar abstracted översikt över exempelnätverket:

  ![exempel-nätverk](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Om exemplen för Azure Resource Manager-mall

I exemplen, VPN-gateway och IPSec-tunneln uppsägningar har konfigurerats med en Azure Resource Manager-mall. Om du har använt Resource Manager-mallar eller att förstå grunderna för Resource Manager mallen, se [förstå struktur och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md). I det här avsnittet skapas en greenfield Azure-miljön (VNet). Om du har ett existerande VNet, kan du dock använda det i mallen. Om du inte är bekant med konfigurationer som plats-till-plats VPN-gateway IPsec/IKE finns [skapa en plats-till-plats-anslutning](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Du behöver inte använda Azure Resource Manager-mallar för att skapa den här konfigurationen. Du kan skapa den här konfigurationen med hjälp av Azure-portalen eller PowerShell.
>
>

### <a name="variables3"></a>3.1 deklarera variablerna

I det här exemplet motsvarar variabeldeklarationer exempelnätverket. När deklarera variabler, ändringar i det här avsnittet för att avspegla din miljö.

* Variabeln **localAddressPrefix** är en matris med lokala IP-adresser för att avsluta IPsec-tunnlar.
* Den **gatewaySku** anger VPN-genomströmning. Läs mer om gatewaySku och vpnType [konfigurationsinställningar för VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Priser, finns i [priser för VPN-Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Ange den **vpnType** till **RouteBased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premise configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="vnet"></a>3.2 Skapa virtuellt nätverk (VNet)

Om du kopplar ett befintligt virtuellt nätverk till VPN-tunnlar, kan du hoppa över det här steget.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="ip"></a>3.3 tilldela offentliga IP-adresser till VPN-gateway-instanser
 
Tilldela en offentlig IP-adress för varje instans av en VPN-gateway.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="termination"></a>3.4 Ange lokala VPN-tunnel avslutning (lokal nätverksgateway)

VPN-enheter lokalt kallas den **lokal nätverksgateway**. Följande json-fragment anger också fjärranslutna BGP-peer-information:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="creategw"></a>3.5 skapa VPN-gateway

Det här avsnittet av mallen konfigurerar VPN-gateway med de nödvändiga inställningarna för en aktiv-aktiv konfiguration. Tänk på följande krav:

* Skapa en VPN-gateway med en **”RouteBased”** VpnType. Den här inställningen är obligatoriskt om du vill aktivera BGP-routning mellan VPN-gateway och den VPN-lokalt.
* Upprätta VPN-tunnlar mellan två instanser av VPN-gateway och en viss lokal enhet i aktivt-aktivt läge i **”activeActive”** parametern anges till **SANT** i Resource Manager-mall . Om du vill veta mer om hög tillgänglighet VPN-gatewayer finns [anslutningar med hög tillgänglighet VPN-gateway](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Du måste ange två olika ASN: er på endera sidan om du vill konfigurera eBGP-sessioner mellan VPN-tunnlar. Det är bättre att ange privata ASN-nummer. Mer information finns i [översikt för BGP och Azure VPN-gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="ipsectunnel"></a>3,6 upprätta IPsec-tunnlar

Den sista åtgärden av skriptet skapar IPsec-tunnlar mellan Azure VPN-gateway och lokala VPN-enhet.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="device"></a>4. Konfigurera lokala VPN-enhet

Azure VPN-gatewayen är kompatibel med många VPN-enheter från olika leverantörer. Information om konfiguration och enheter som har godkänts för att fungera med VPN-gateway finns [om VPN-enheter](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

När du konfigurerar VPN-enhet behöver du följande:

* En delad nyckel. Det här är samma delade nyckel som du anger när du skapar din plats-till-plats VPN-anslutning. Exemplen använder en grundläggande delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
* Offentliga IP-adressen för VPN-gateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. Navigera till virtuella nätverksgatewayer för att hitta offentliga IP-adressen för din VPN-gateway med hjälp av Azure portal och klicka på namnet på din gateway.

Vanligtvis är eBGP peer-datorer anslutna direkt (ofta via en WAN-anslutning). När du konfigurerar eBGP via IPsec-VPN-tunnlar via Microsoft ExpressRoute-peering, finns det dock flera routningsdomäner mellan eBGP peer-datorer. Använd den **ebgp Multihopp** kommando för att upprätta en eBGP granne relation mellan två inte-direktanslutna peer-datorer. Heltal som följer ebgp Multihopp kommando anger TTL-värdet i BGP-paket. Kommandot **maximalt sökvägar eibgp 2** möjliggör belastningsutjämning för trafik mellan de två sökvägarna BGP.

### <a name="cisco1"></a>Cisco CSR1000 exempel

I följande exempel visas konfigurationen för Cisco CSR1000 i en virtuell dator för Hyper-V som lokala VPN-enhet:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="firewalls"></a>5. Konfigurera VPN-filtrering och brandväggar (valfritt)

Konfigurera brandväggen och filtrera enligt dina krav.

## <a name="testipsec"></a>6. Testa och validera IPsec-tunneln

Status för IPsec-tunnlar kan verifieras på Azure VPN-gatewayen av Powershell-kommandon:

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Exempel på utdata:

```powershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Om du vill kontrollera status för tunnlar på Azure VPN-gateway-instanser oberoende av varandra, använder du följande exempel:

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Exempel på utdata:

```powershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Du kan också kontrollera status för tunnel på din lokala VPN-enhet.

Cisco CSR1000 exempel:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Exempel på utdata:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Protokollet rad på virtuella Tunnel gränssnitt (VTI) ändras inte ”upp” tills IKE fas 2 har slutförts. Följande kommando verifierar säkerhetsassociationen:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verifye2e"></a>Kontrollera anslutningen för slutpunkt till slutpunkt mellan insidan lokalt och Azure VNet

Om IPsec-tunnlar är igång och att de statiska vägarna är korrekt inställda, bör du kunna pinga fjärranslutna BGP-peer IP-adress:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verifybgp"></a>Kontrollera BGP-sessioner via IPsec

Kontrollera status för BGP-peer på Azure VPN-gateway:

```powershell
Get-AzureRmVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Exempel på utdata:

```powershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Du kan filtrera efter attributet ”ursprung” för att kontrollera listan över nätverksprefix som tagits emot via eBGP från den VPN-koncentrator lokalt:

```powershell
Get-AzureRmVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

I exempel-utdata är ASN 65010 BGP autonomt nummer i den VPN-lokalt.

```powershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Om du vill se en lista över annonserade vägar:

```powershell
Get-AzureRmVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Exempel på utdata:

```powershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Exempel på lokalt Cisco CSR1000:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

Listan över nätverk annonserade från lokala Cisco CSR1000 till Azure VPN-gateway kan visas med följande kommando:

```powershell
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Nästa steg

* [Konfigurera övervakare av nätverksprestanda för ExpressRoute](how-to-npm.md)

* [Lägga till en plats-till-plats-anslutning till ett virtuellt nätverk med en befintlig anslutning för VPN-gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)