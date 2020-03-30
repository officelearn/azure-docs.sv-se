---
title: Azure VMware Solution by CloudSimple – Sträck ut ett Layer 2-nätverk lokalt till Private Cloud
description: Beskriver hur du konfigurerar ett Layer 2 VPN mellan NSX-T i ett CloudSimple Private Cloud och en lokal fristående NSX Edge-klient
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2ddfa9611143d5c3f823539e018c8afc885c6a46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083223"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrera arbetsbelastningar med hjälp av stretchade Layer 2-nätverk

I den här guiden får du lära dig hur du använder Layer 2 VPN (L2VPN) för att sträcka ut ett Layer 2-nätverk från din lokala miljö till ditt CloudSimple Private Cloud. Den här lösningen möjliggör migrering av arbetsbelastningar som körs i din lokala VMware-miljö till det privata molnet i Azure inom samma undernätsadressutrymme utan att behöva åter-IP-dina arbetsbelastningar.

L2VPN-baserad sträckning av Layer 2-nätverk kan fungera med eller utan NSX-baserade nätverk i din lokala VMware-miljö. Om du inte har NSX-baserade nätverk för arbetsbelastningar lokalt kan du använda en fristående NSX Edge Services Gateway.

> [!NOTE]
> Den här guiden beskriver scenariot där lokala datacenter och privata molndata är anslutna via Plats-till-plats-VPN.

## <a name="deployment-scenario"></a>Distributionsscenario

Om du vill sträcka ut det lokala nätverket med L2VPN måste du konfigurera en L2VPN-server (mål-NSX-T Tier0-router) och en L2VPN-klient (källa fristående klient).  

I det här distributionsscenariot är ditt privata moln anslutet till din lokala miljö via en PLATS-till-plats-VPN-tunnel som gör att lokala hanterings- och vMotion-undernät kan kommunicera med undernäten Private Cloud management och vMotion. Detta arrangemang är nödvändigt för Cross vCenter vMotion (xVC-vMotion). En NSX-T Tier0-router distribueras som en L2VPN-server i det privata molnet.

Fristående NSX Edge distribueras i din lokala miljö som en L2VPN-klient och paras därefter ihop med L2VPN-servern. En GRE-tunnelslutpunkt skapas på varje sida och konfigureras för att "sträcka ut" det lokala Layer 2-nätverket till ditt privata moln. Den här konfigurationen visas i följande bild.

![Distributionsscenario](media/l2vpn-deployment-scenario.png)

Mer information om migrering med L2 VPN finns i [Virtuella privata nätverk](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) i VMware-dokumentationen.

## <a name="prerequisites-for-deploying-the-solution"></a>Förutsättningar för att distribuera lösningen

Kontrollera att följande finns på plats innan du distribuerar och konfigurerar lösningen:

* Den lokala vSphere-versionen är 6.7U1+ eller 6.5P03+.
* Den lokala vSphere-licensen finns på Enterprise Plus-nivå (för vSphere Distributed Switch).
* Identifiera arbetsbelastningen Layer 2-nätverk som ska sträckas ut till ditt privata moln.
* Identifiera ett Layer 2-nätverk i din lokala miljö för distribution av L2VPN-klientinstallationen.
* [Ett privat moln har redan skapats](create-private-cloud.md).
* Versionen av den fristående NSX-T Edge-apparaten är kompatibel med NSX-T Manager-versionen (NSX-T 2.3.0) som används i din privata molnmiljö.
* En trunkportgrupp har skapats i det lokala vCenter med förfalskade sändning aktiverade.
* En offentlig IP-adress har reserverats för användning för den fristående klientupplänknings-IP-adressen NSX-T och 1:1 NAT finns för översättning mellan de två adresserna.
* DNS-vidarebefordran anges på de lokala DNS-servrarna för att den az.cloudsimple.io domänen ska peka på de privata DNS-servrarna i molnet.
* RTT-svarstiden är mindre än eller lika med 150 ms, som krävs för vMotion att fungera över de två platserna.

## <a name="limitations-and-considerations"></a>Begränsningar och överväganden

I följande tabell visas vSphere-versioner och nätverksadaptertyper som stöds.  

| vSphere version | Källa vSwitch typ | Drivrutin för virtuellt nätverkskort | Mål vSwitch Typ | Stöds? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Alla | DVS (PÅ ANDRA) | Alla | DVS (PÅ ANDRA) | Ja |
| vSphere 6.7UI eller högre, 6.5P03 eller högre | DVS (PÅ ANDRA) | VMXNET3 | N-VDS | Ja |
| vSphere 6.7UI eller högre, 6.5P03 eller högre | DVS (PÅ ANDRA) | E1000 (på) | N-VDS | [Stöds inte per VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI eller 6.5P03, NSX-V eller versioner under NSX-T2.2, 6.5P03 eller senare | Alla | Alla | N-VDS | [Stöds inte per VWware](https://kb.vmware.com/s/article/56991) |

Från och med VMware NSX-T 2.3 release:

* Den logiska växeln på sidan Privat moln som sträcks ut till lokalt över L2VPN kan inte dirigeras samtidigt. Den utsträckta logiska växeln kan inte anslutas till en logisk router.
* L2VPN och vägbaserade IPSEC-VPN kan endast konfigureras med API-anrop.

Mer information finns i [Virtual Private Networks](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) i VMware-dokumentationen.

### <a name="sample-l2-vpn-deployment-addressing"></a>Exempel på L2 VPN-distributionsadress

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Lokalt nätverk där den fristående ESG(L2 VPN-klienten) distribueras

| **Objekt** | **Värde** |
|------------|-----------------|
| Nätverksnamn | MGMT_NET_VLAN469 |
| Vlan | 469 |
| Cidr| 10.250.0.0/24 |
| Fristående IP-adress för Edge-apparat | 10.250.0.111 |
| Fristående NAT-IP-adress för Edge-apparat | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Lokalt nätverk som ska sträckas ut

| **Objekt** | **Värde** |
|------------|-----------------|
| Vlan | 472 |
| Cidr| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>Privat MOLN-IP-schema för NSX-T Tier0-router (L2 VPN-serve)

| **Objekt** | **Värde** |
|------------|-----------------|
| Loopback-gränssnitt | 192.168.254.254/32 |
| Tunnelgränssnitt | 5.5.5.1/29 |
| Logisk växel (sträckt) | Stretch_LS |
| Loopback-gränssnitt (NAT IP-adress) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Privat molnnätverk som ska mappas till det utsträckta nätverket

| **Objekt** | **Värde** |
|------------|-----------------|
| Vlan | 712 |
| Cidr| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Hämta det logiska router-ID som behövs för L2VPN

Följande steg visar hur du hämtar logisk router-ID för logiska routerinstansen för logiska routerinstanser för IPsec- och L2VPN-tjänsterna. Logiskt router-ID behövs senare när L2VPN implementeras.

1. Logga in på NSX-T Manager https://*nsx-t-manager-ip-adress* och välj Översikt över > **nätverksroutrar** > **Provider-LR** > **.** **Networking** För **läget Hög tillgänglighet**väljer du Aktiv **vänteläge**. Den här åtgärden öppnar ett popup-fönster som visar edge-virtuella datorer där Tier0-routern är aktiv.

    ![Välj vänteläge med aktiv vänteläge](media/l2vpn-fetch01.png)

2. Välj > **Tygnoder** > **Kanter**. **Fabric** Anteckna hanterings-IP-adressen för den aktiva Edge VM (Edge VM1) som identifierades i föregående steg.

    ![IP-hantering av anteckningar](media/l2vpn-fetch02.png)

3. Öppna en SSH-session till hanterings-IP-adressen för Edge-virtuella datorn. Kör ```get logical-router``` kommandot med användarnamn **admin** och lösenord **CloudSimple 123!**.

    ![hämta utdata för logisk router](media/l2vpn-fetch03.png)

4. Om du inte ser posten "DR-Provider-LR" gör du följande steg.

5. Skapa två överläggsbaserade logiska växlar. En logisk växel sträcks ut till lokalt där de migrerade arbetsbelastningarna finns. En annan logisk switch är en dummy switch. Instruktioner finns i [Skapa en logisk växel](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) i VMware-dokumentationen.

    ![Skapa logisk växel](media/l2vpn-fetch04.png)

6. Koppla dummy-växeln till Tier1-routern med en länka lokal IP-adress eller ett icke-överlappande undernät från lokalt eller privat moln. Se [Lägga till en nedlänksport på en logisk router på nivå 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) i VMware-dokumentationen.

    ![Fäst dummy switch](media/l2vpn-fetch05.png)

7. Kör `get logical-router` kommandot igen på SSH-sessionen för Edge-virtuella datorn. UUID för den logiska routern "DR-Provider-LR" visas. Anteckna UUID, som krävs när du konfigurerar L2VPN.

    ![hämta utdata för logisk router](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Hämta det logiska växel-ID som behövs för L2VPN

1. Logga in på [NSX-T Manager](https://nsx-t-manager-ip-address).
2. Välj > **Nätverksväxling** > **växlar**  >  **Networking****<\Logisk växel\>** > **översikt**.
3. Anteckna UUID för den logiska stretchväxeln, vilket krävs när L2VPN konfigureras.

    ![hämta utdata för logisk router](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Routning och säkerhetsöverväganden för L2VPN

För att upprätta en IPsec-ruttbaserad VPN mellan NSX-T Tier0-routern och den fristående NSX Edge-klienten måste loopback-gränssnittet för NSX-T Tier0-routern kunna kommunicera med den offentliga IP-adressen för NSX fristående klient lokalt över UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>Tillåt UDP 500/4500 för IPsec

1. [Skapa en offentlig IP-adress](public-ips.md) för NSX-T Tier0 loopback-gränssnittet i CloudSimple-portalen.

2. [Skapa en brandväggstabell](firewall.md) med tillståndskänsliga regler som tillåter UDP 500/ 4500 inkommande trafik och bifoga brandväggstabellen till undernätet NSX-T HostTransport.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Annonsera loopback-gränssnitts-IP till underlagsnätverket

1. Skapa en null-väg för loopback-gränssnittets nätverk. Logga in på NSX-T Manager och välj >  **Nätverksrout** > **routningsroutrar** > **Provider-LR** > **Routning** > **statiska vägar**.**Routing** Klicka på **Lägg till**. För **Nätverk**anger du IP-adressen för loopback-gränssnittet. För **Nästa hopp**klickar du på Lägg **till**, anger Null för nästa hopp och behåller standardvärdet 1 för admin distance.

    ![Lägg till statisk väg](media/l2vpn-routing-security01.png)

2. Skapa en IP-prefixlista. Logga in på NSX-T Manager och välj **Nätverkande** >  >  > **routningsroutrar** > **Provider-LR-routnings-IP-prefixlistor** > **Routing**.**Routing****IP Prefix Lists** Klicka på **Lägg till**. Ange ett namn för att identifiera listan. För **prefix**klickar du på **Lägg till** två gånger. På den första raden anger du "0.0.0.0/0" för **Nätverk** och "Neka" för **åtgärd**. På den andra raden väljer du **Alla** för **nätverk** och **tillstånd** för **åtgärd**.
3. Bifoga IP-prefixlistan till båda BGP-grannarna (TOR). Om du ansluter IP-prefixlistan till BGP-grannen kan standardvägen annonseras i BGP till TOR-växlarna. Alla andra vägar som innehåller null-vägen kommer dock att annonsera startgränssnittets IP-adress till TOR-växlarna.

    ![Skapa IP-prefixlista](media/l2vpn-routing-security02.png)

4. Logga in på NSX-T Manager och välj > **Nätverksrout** > routningsroutrar**Routers** > **Provider-LR** > **Routing** > **BGP-grannar** > **Neighbors**. **Networking** Välj den första grannen. Klicka på **Redigera** > **adressfamiljer**. För IPv4-familjen redigerar du kolumnen **Utfilter** och väljer den IP-prefixlista som du skapade. Klicka på **Spara**. Upprepa det här steget för den andra grannen.

    ![Bifoga IP-prefixlista](media/l2vpn-routing-security03.png) ![1 Bifoga IP-prefixlista 2](media/l2vpn-routing-security04.png)

5. Distribuera null-statisk väg till BGP. Om du vill annonsera direkthjälpsgränssnittets väg till underlaget måste du distribuera null-statisk väg till BGP. Logga in på NSX-T Manager och välj > **Nätverksrout** > routningsroutrar **Networking****Routers** > **Provider-LR** > **Routningsruttomfördelning** > **Route Redistribution** > **grannar**. Välj **Provider-LR-Route_Redistribution** och klicka på **Redigera**. Markera kryssrutan **Statisk och** klicka på **Spara**.

    ![Distribuera null-statisk väg till BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Konfigurera en ruttbaserad VPN på NSX-T Tier0-routern

Använd följande mall för att fylla i alla detaljer för att konfigurera ett ruttbaserat VPN på NSX-T Tier0-routern. UUID:erna i varje POST-anrop krävs i efterföljande POST-anrop. IP-adresserna för loopback- och tunnelgränssnitten för L2VPN måste vara unika och inte överlappa de lokala nätverken eller privata molnnätverken.

DE IP-adresser som valts för loopback- och tunnelgränssnitt som används för L2VPN måste vara unika och inte överlappa de lokala nätverken eller privata molnnätverken. Loopback-gränssnittet måste alltid vara /32.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section “Steps to fetch Logical-Router ID needed for L2VPN”
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

För alla följande API-anrop ersätter du IP-adressen med din NSX-T Manager IP-adress. Du kan köra alla dessa API-anrop `curl` från POSTMAN-klienten eller med hjälp av kommandon.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Aktivera IPSec VPN-tjänsten på den logiska routern

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Skapa profiler: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Skapa profiler: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Skapa profiler: Tunnel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Skapa en lokal slutpunkt

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Skapa en peer-slutpunkt

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Skapa en ruttbaserad VPN-session

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Konfigurera L2VPN på NSX-T Tier0-router

Fyll i följande information efter varje POST-samtal. ID:na krävs i efterföljande POST-anrop.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Skapa L2VPN-tjänsten

Utdata från följande GET-kommando kommer att vara tom, eftersom konfigurationen inte är klar ännu.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

För följande POST-kommando är det logiska router-ID:et UUID för den logiska routrar på Nivå0 DR som erhölls tidigare.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Skapa L2VPN-sessionen

För följande POST-kommando är L2VPN-tjänst-ID det ID som du just fått och IPsec VPN-sessions-ID är det ID som erhölls i föregående avsnitt.

``` 
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Dessa anrop skapar en GRE-tunnelslutpunkt. Om du vill kontrollera status kör du följande kommando.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Skapa logisk port med det tunnel-ID som angetts

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Hämta peer-koden för L2VPN på NSX-T-sidan

Hämta peer-koden för NSX-T-slutpunkten. Peer-koden krävs när fjärrslutpunkten konfigureras. L2VPN <sessions-id> kan erhållas från föregående avsnitt. Mer information finns i [API-guiden för NSX-T 2.3](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Distribuera den fristående NSX-T-klienten (lokalt)

Innan du distribuerar kontrollerar du att dina lokala brandväggsregler tillåter inkommande och utgående UDP 500/4500-trafik från/till CloudSimples offentliga IP-adress som reserverades tidigare för NSX-T T0-routerloopback-gränssnittet. 

1. [Ladda ned den fristående NSX Edge-klienten](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF och Extrahera filerna från det nedladdade paketet till en mapp.

    ![Ladda ned fristående NSX Edge-klient](media/l2vpn-deploy-client01.png)

2. Gå till mappen med alla extraherade filer. Välj alla vmdks (NSX-l2t-client-large.mf och NSX-l2t-client-large.ovf för stor apparatstorlek eller NSX-l2t-client-Xlarge.mf och NSX-l2t-client-Xlarge.ovf för extra stor storlek apparat). Klicka på **Nästa**.

    ![Välj](media/l2vpn-deploy-client02.png) ![mall Välj mall](media/l2vpn-deploy-client03.png)

3. Ange ett namn på den fristående NSX-T-klienten och klicka på **Nästa**.

    ![Ange mallnamn](media/l2vpn-deploy-client04.png)

4. Klicka på **Nästa** efter behov för att nå inställningarna för datalager. Välj lämpligt datalager för fristående NSX-T-klient och klicka på **Nästa**.

    ![Välj datalager](media/l2vpn-deploy-client06.png)

5. Välj rätt portgrupper för Trunk (Trunk PG), Public (Uplink PG) och HA-gränssnittet (Uplink PG) för den fristående NSX-T-klienten. Klicka på **Nästa**.

    ![Välj portgrupper](media/l2vpn-deploy-client07.png)

6. Fyll i följande information på **skärmen Anpassa mall** och klicka på **Nästa:**

    Expandera L2T:

    * **Peer-adress**. Ange IP-adressen som är reserverad på Azure CloudSimple-portalen för NSX-T Tier0 Loopback-gränssnittet.
    * **Peer-kod**. Klistra in peer-koden som hämtats från det sista steget i L2VPN Server-distributionen.
    * **Sub Interfaces VLAN (Tunnel ID)**. Ange VLAN-ID:t som ska sträckas ut. Inom parentes (), ange det tunnel-ID som tidigare konfigurerats.

    Expandera upplänksgränssnitt:

    * **DNS IP-adress**. Ange den lokala DNS-IP-adressen.
    * **Standardgateway**.  Ange standardgatewayen för VLAN som fungerar som en standardgateway för den här klienten.
    * **IP-adress**. Ange den fristående klientens upplänk-IP-adress.
    * **Prefixlängd**. Ange prefixlängden för upplänken VLAN/undernät.
    * **CLI admin/enable/root User Password**. Ange lösenordet för admin /enable /root-konto.

      ![Anpassa](media/l2vpn-deploy-client08.png)
      ![mall Anpassa mall - mer](media/l2vpn-deploy-client09.png)

7. Granska inställningarna och klicka på **Slutför**.

    ![Fullständig konfiguration](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Konfigurera en lokal sink-port

Om en av VPN-platserna inte har NSX distribuerad kan du konfigurera en L2 VPN genom att distribuera en fristående NSX Edge på den platsen. En fristående NSX Edge distribueras med en OVF-fil på en värd som inte hanteras av NSX. Detta distribuerar en NSX Edge Services Gateway-enhet för att fungera som en L2 VPN-klient.

Om ett fristående kantstamv-nätverkskort är anslutet till en vSphere Distributed Switch krävs antingen promiskuöst läge eller en sink-port för L2 VPN-funktion. Om du använder promiskuöst läge kan det orsaka dubbla pingar och dubblettsvar. Av denna anledning kan du använda sink-portläge i L2 VPN-fristående NSX Edge-konfiguration. Se [konfigurera en sink-port](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) i VMware-dokumentationen.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN- och L2VPN-verifiering

Använd följande kommandon för att verifiera IPsec- och L2VPN-sessioner från fristående NSX-T Edge.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Använd följande kommandon för att verifiera IPsec- och L2VPN-sessioner från NSX-T Tier0-routern.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Använd följande kommandon för att verifiera sink-porten på ESXi-värden där den fristående klienten NSX-T finns i den lokala miljön.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
