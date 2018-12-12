---
title: Konfigurera en plats-till-plats-VPN över Microsoft-peering - ExpressRoute – Azure | Microsoft Docs
description: Konfigurera IPsec/IKE-anslutningar till Azure över en peering Microsoft ExpressRoute-krets med hjälp av en plats-till-plats VPN-gateway.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 3ba9d7ab9e05c3c5480e1832cc5ddd0ce91a3ae1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094210"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurera en VPN för plats-till-plats via ExpressRoute Microsoft-peering

Den här artikeln hjälper dig att konfigurera säker krypterad anslutning mellan ditt lokala nätverk och din Azure-nätverk (Vnet) via en privat ExpressRoute-anslutning. Du kan använda Microsoft-peering för att upprätta en plats-till-plats IPsec/IKE VPN-tunnel mellan dina valda lokala nätverk och virtuella Azure-nätverk. Konfigurera en säker tunnel via ExpressRoute tillåter för datautbyte med konfidentialitet, anti repetitionsattacker, äkthetsbeviset och integritet.

>[!NOTE]
>När du konfigurerar VPN för plats-till-plats via Microsoft-peering, debiteras du för VPN-gateway och VPN-utgående. Mer information finns i [prissättning för VPN-Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

## <a name="architecture"></a>Arkitektur


  ![Översikt över anslutning](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


För hög tillgänglighet och redundans kan du konfigurera flera tunnlar via en ExpressRoute-krets två msee: N PE-par och aktivera belastningsutjämning mellan tunnlarna.

  ![alternativ för hög tillgänglighet](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

VPN-tunnlar via Microsoft-peering kan avbrytas antingen med hjälp av VPN-gateway eller använda en lämplig virtuell nätverksinstallation (NVA) tillgängliga via Azure Marketplace. Du kan utbyta vägar statiskt eller dynamiskt via krypterade tunnlar utan att exponera routningsutbyte till de underliggande Microsoft-peering. I exemplen i den här artikeln används BGP (skiljer sig från BGP-sessionen som används för att skapa Microsoft-peering) för att utbyta dynamiskt prefix via krypterade tunnlarna.

>[!IMPORTANT]
>För den lokala sidan normalt Microsoft-peering avslutas på Perimeternätverket och privat peering termineras på zonen core nätverk. Två zoner separeras med brandväggar. Om du konfigurerar Microsoft-peering exklusivt för att aktivera säker tunnel via ExpressRoute, Kom ihåg att filtrera via bara den offentliga IP-adresser av intresse som komma annonseras via Microsoft-peering.
>
>

## <a name="workflow"></a>Arbetsflöde

1. Konfigurera Microsoft-peering för din ExpressRoute-krets.
2. Annonsera valda Azure regionala offentliga prefix till ditt lokala nätverk via Microsoft-peering.
3. Konfigurera en VPN-gateway och upprätta IPsec-tunnlar
4. Konfigurera lokala VPN-enhet.
5. Skapa anslutningen för plats-till-plats-IPsec/IKE.
6. (Valfritt) Konfigurera brandväggar /-filtrering på den lokala VPN-enheten.
7. Testa och validera en IPsec-kommunikation via ExpressRoute-krets.

## <a name="peering"></a>1. Konfigurera Microsoft-peering

Om du vill konfigurera en plats-till-plats VPN-anslutning via ExpressRoute, måste du använda ExpressRoute Microsoft-peering.

* Om du vill konfigurera en ny ExpressRoute-krets, börja med den [ExpressRoute-krav](expressroute-prerequisites.md) artikeln och sedan [skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-arm.md).

* Om du redan har en ExpressRoute-krets, men har inte konfigurerats för Microsoft-peering, konfigurera Microsoft-peering med hjälp av den [skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-arm.md#msft) artikeln.

När du har konfigurerat din krets och Microsoft-peering kan du lätt kan visa den med hjälp av den **översikt** sidan på Azure portal.

![krets](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. Konfigurera routningsfilter

Med ett flödesfilter kan du identifiera tjänster som du vill använda via Microsoft-peering för din ExpressRoute-krets. Det är i grunden en godkänd lista över alla värden för BGP-community. 

![flödesfilter](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

I det här exemplet distributionen är endast i den *Azure västra USA 2* region. En flödesfilterregeln har lagts till för att tillåta endast annonsen dit BGP community-värde med Azure västra USA 2 regionala prefix *12076:51026*. Du anger de regionala prefix som du vill tillåta genom att välja **hantera regel**.

I flödesfiltret måste du också väljer ExpressRoute-kretsar som flödesfiltret gäller. Du kan välja ExpressRoute-kretsar genom att välja **lägga till krets**. Flödesfiltret är kopplad till exempel ExpressRoute-krets i föregående bild.

### <a name="configfilter"></a>2.1 konfigurera route-filter

Konfigurera ett flödesfilter. Anvisningar finns i [konfigurera routningsfilter för Microsoft-peering](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2 Kontrollera BGP-vägar

När du har skapat Microsoft-peering över ExpressRoute-kretsen och kopplade till ett flödesfilter kretsen, kan du kontrollera BGP-vägar som togs emot från msee på PE-enheter som peeringen med msee: erna. Kommandot verifiering varierar beroende på operativsystemet på dina PE-enheter.

#### <a name="cisco-examples"></a>Cisco-exempel

Det här exemplet används ett Cisco IOS-XE-kommando. I det här exemplet används en virtuell Routning och vidarebefordra (VRF) instans att isolera peering trafiken.

```
show ip bgp vpnv4 vrf 10 summary
```

Följande partiella utdata visar att 68 prefix togs emot från intilliggande *.243.229.34 med ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Om du vill se en lista över prefix som togs emot från intilliggande, använder du följande exempel:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

För att bekräfta att du får rätt uppsättning prefix, kan du verifierar. Utdata från följande Azure PowerShell visar en lista över de prefix som annonseras via Microsoft-peering för varje tjänst och för varje Azure-region:

```azurepowershell-interactive
Get-AzureRmBgpServiceCommunity
```

## <a name="vpngateway"></a>3. Konfigurera VPN-gateway och IPsec-tunnlar

I det här avsnittet skapas IPsec VPN-tunnlar mellan Azure VPN-gateway och lokal VPN-enhet. I exemplen används Cisco Cloud Service-Router (CSR1000) VPN-enheter.

I följande diagram visas IPsec VPN tunnlar upprättas mellan den lokala VPN-enhet 1 och Azure VPN gateway-instans paret. Två IPsec VPN-tunnlar upprättas mellan den lokala VPN-enheten 2 och Azure VPN gateway-instans paret visas inte i diagrammet och konfigurationsinformationen visas inte. Men förbättrar med ytterligare VPN-tunnlar hög tillgänglighet.

  ![VPN-tunnlar](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Över IPsec-tunnel par upprättas en eBGP-session för att utbyta privata nätverksvägar. Följande diagram visar eBGP när sessionen har upprättats över IPsec-tunnel paret:

  ![eBGP-sessioner via tunnel par](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Följande diagram visar en abstraherad översikt över exempelnätverket:

  ![exempel-nätverk](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Om Azure Resource Manager-mallexempel

I exemplen är VPN-gatewayen och IPsec-tunnel uppsägningar har konfigurerats med en Azure Resource Manager-mall. Om du är van vid att använda Resource Manager-mallar eller att förstå grunderna för Resource Manager-mall finns i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md). I det här avsnittet skapas en med grönt fält Azure-miljö (VNet). Om du har ett befintligt VNet, kan du dock använda det i mallen. Om du inte är bekant med VPN gateway-IPsec/IKE plats-till-plats-konfigurationer finns i [skapa en plats-till-plats-anslutning](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Du behöver inte använda Azure Resource Manager-mallar för att skapa den här konfigurationen. Du kan skapa den här konfigurationen med hjälp av Azure portal eller PowerShell.
>
>

### <a name="variables3"></a>3.1 deklarera variablerna

I det här exemplet motsvarar variabeldeklarationer exempel-nätverk. När deklarera variabler, kan du ändra det här avsnittet för att avspegla din miljö.

* Variabeln **localAddressPrefix** är en matris med den lokala IP-adresser för att avsluta IPsec-tunnlar.
* Den **gatewaySku** anger VPN-dataflöde. Läs mer om gatewaySku och vpnType [konfigurationsinställningar för VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Om priser finns i [prissättning för VPN-Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
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
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
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

Om du associerar ett befintligt VNet med VPN-tunnlar, kan du hoppa över det här steget.

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

### <a name="ip"></a>3.3 tilldela offentliga IP-adresser till VPN gateway-instanser
 
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

### <a name="termination"></a>3.4 Ange den lokala VPN-tunnel avslutning (lokal nätverksgateway)

Den lokala VPN-enheter kallas den **lokal nätverksgateway**. Följande json-kodfragmentet anger också fjärranslutna BGP-peer-information:

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

Det här avsnittet av mallen konfigurerar VPN-gatewayen med de nödvändiga inställningarna för en aktiv-aktiv konfiguration. Tänk på följande krav:

* Skapa VPN-gateway med en **”RouteBased”** VpnType. Den här inställningen är obligatorisk om du vill aktivera BGP-routning mellan VPN-gatewayen och VPN-lokala platser.
* Att upprätta VPN-tunnlar mellan de två instanserna av VPN-gateway och en viss lokal enhet i aktivt-aktivt läge i **”activeActive”** parametern är inställd på **SANT** i Resource Manager-mall . Mer information om VPN-gatewayer med hög tillgänglighet i [med hög tillgänglighet VPN gateway-anslutningar](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Om du vill konfigurera eBGP-sessioner mellan VPN-tunnlar, måste du ange två olika ASN: er på endera sidan. Det är bättre att ange privata ASN-nummer. Mer information finns i [översikt över BGP och Azure VPN-gatewayer](../vpn-gateway/vpn-gateway-bgp-overview.md).

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

### <a name="ipsectunnel"></a>3.6 upprätta IPsec-tunnlar

Den sista åtgärden av skriptet skapar IPsec-tunnlar mellan Azure VPN-gateway och lokal VPN-enhet.

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

Azure VPN-gatewayen är kompatibel med många VPN-enheter från olika leverantörer. Konfigurationsinformation och enheter som har godkänts för att fungera med VPN-gateway finns i [om VPN-enheter](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

När du konfigurerar din VPN-enheten behöver du följande objekt:

* En delad nyckel. Det här är samma delade nyckel som du anger när du skapar plats-till-plats VPN-anslutningen. I exemplen används en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
* Offentliga IP-adressen för din VPN-gateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. Gå till virtuella nätverksgatewayer för att hitta offentliga IP-adressen för din VPN-gateway med Azure portal och sedan på namnet på din gateway.

Vanligtvis är eBGP peer-datorer anslutna direkt (ofta via en WAN-anslutning). När du konfigurerar eBGP över IPsec-VPN-tunnlar via ExpressRoute Microsoft-peering, finns det dock flera routningsdomäner mellan eBGP peer-datorer. Använd den **ebgp-Multihopp** kommando för att upprätta en eBGP intilliggande relation mellan två inte-direktanslutna peer-datorer. Det heltal som följer ebgp-Multihopp kommando anger TTL-värdet i BGP-paket. Kommandot **maximalt sökvägar eibgp 2** aktiverar belastningsutjämning av trafik mellan de två sökvägarna för BGP.

### <a name="cisco1"></a>Cisco CSR1000 exempel

I följande exempel visar konfigurationen för Cisco CSR1000 på en Hyper-V virtuell dator som den lokala VPN-enhet:

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

Konfigurera brandväggen och filtrering baserat på dina krav.

## <a name="testipsec"></a>6. Testa och validera en IPsec-tunneln

Status för IPsec-tunnlar kan verifieras på Azure VPN-gateway med Powershell-kommandon:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Exempel på utdata:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Om du vill kontrollera status för tunnlar på Azure VPN-gateway-instanser oberoende av varandra, använder du exemplet nedan:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Exempel på utdata:

```azurepowershell
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

Rad-protokollet på virtuella Tunnel gränssnitt (VTI) ändras inte om du vill ”upp” tills IKE fas 2 är klar. Följande kommando verifierar säkerhetsassociationen:

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

### <a name="verifye2e"></a>Kontrollera anslutningen för slutpunkt till slutpunkt mellan insidan lokala och Azure VNet

Om IPsec-tunnlar är igång och de statiska vägarna är korrekt inställda, bör du kunna pinga IP-adressen för den fjärranslutna BGP-peer:

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

### <a name="verifybgp"></a>Kontrollera BGP-sessionerna via IPsec

Kontrollera status för BGP-peer på Azure VPN-gatewayen:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Exempel på utdata:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Du kan filtrera efter attributet ”ursprung” för att kontrollera listan över nätverksprefix som tagits emot via eBGP från VPN koncentrator lokala platser:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

I exempel-utdata är ASN-65010 BGP autonomt systemnummer i VPN-lokala platser.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Om du vill se en lista över annonseras vägar:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Exempel på utdata:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Exempel för lokala Cisco CSR1000:

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

Listan med nätverk som annonseras från den lokala Cisco CSR1000 till Azure VPN-gatewayen kan anges med följande kommando:

```
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

* [Lägg till en plats-till-plats-anslutning till ett virtuellt nätverk med en befintlig anslutning för VPN-gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)