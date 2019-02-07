---
title: Exempel på konfiguration för att ansluta Cisco ASA-enheter till Azure VPN-gatewayer | Microsoft Docs
description: Den här artikeln innehåller en exempelkonfiguration för att ansluta Cisco ASA-enheter till Azure VPN-gatewayer.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/19/2018
ms.author: yushwang
ms.openlocfilehash: e575fac61a1c5d9351391d39d200b87e34ff26cd
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817248"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Exempel på konfiguration: Cisco ASA-enhet (IKEv2/inget BGP)
Den här artikeln innehåller exempel konfigurationer för enheter som ansluter Cisco anpassningsbar Security installation (ASA) till Azure VPN-gatewayer. I exemplet avser Cisco ASA-enheter som kör IKEv2 utan Border Gateway Protocol (BGP). 

## <a name="device-at-a-glance"></a>Enheten på ett ögonblick

|                        |                                   |
| ---                    | ---                               |
| Enhetsleverantör          | Cisco                             |
| Enhetsmodell           | ASA                               |
| Målversion         | 8.4 och senare                     |
| Testade modell           | ASA 5505                          |
| Testade version         | 9.2                               |
| IKE-version            | IKEv2                             |
| BGP                    | Nej                                |
| Azure VPN gateway-typ | Ruttbaserad VPN-gateway           |
|                        |                                   |

> [!NOTE]
> Konfigurationen av exemplet ansluter en Cisco ASA-enhet till en Azure **routningsbaserad** VPN-gateway. Anslutningen använder en anpassad IPsec/IKE-princip med de **UsePolicyBasedTrafficSelectors** enligt beskrivningen i [i den här artikeln](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> Exempelfilen kräver som ASA enheter använder den **IKEv2** princip med åtkomst-list-baserade konfigurationer inte VTI-baserade. Kontakta din VPN-leverantör enhetsspecifikationer för att kontrollera att IKEv2 principer stöds på din lokala VPN-enheter.


## <a name="vpn-device-requirements"></a>Krav för VPN-enheter
Azure VPN-gatewayer använder de standard IPsec/IKE-protokoll som för att upprätta plats-till-plats (S2S) VPN-tunnlar. Detaljerade parametrar för IPsec/IKE-protokoll och standard kryptografiska algoritmer för Azure VPN-gatewayer finns i [om VPN-enheter](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Alternativt kan du ange en exakt kombination av kryptografiska algoritmer och nyckellängder för ett särskilt projekt, enligt beskrivningen i [om kryptografikrav](vpn-gateway-about-compliance-crypto.md). Om du anger ett exakt kombination av algoritmer och nyckellängder, måste du använda motsvarande specifikationer på dina VPN-enheter.

## <a name="single-vpn-tunnel"></a>En VPN-tunnel
Den här konfigurationen består av en S2S VPN-tunnel mellan en Azure VPN-gateway och en lokal VPN-enhet. Du kan också konfigurera BGP via VPN-tunnel.

![En S2S VPN-tunnel](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Stegvisa anvisningar om hur du skapar Azure-konfigurationer finns i [enda VPN-tunnel installationsprogrammet](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Virtuellt nätverk och information om VPN-gateway
Det här avsnittet visas parametrarna för exemplet.

| **Parametern**                | **Värde**                    |
| ---                          | ---                          |
| Vnet-adressprefix        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN gateway-IP         | Azure_Gateway_Public_IP      |
| Lokala adressprefix | 10.51.0.0/16<br>10.52.0.0/16 |
| Den lokala VPN-enhetens IP    | OnPrem_Device_Public_IP     |
| * Virtual network BGP ASN                | 65010                        |
| * Azure BGP peer IP           | 10.12.255.30                 |
| * On-premises BGP ASN         | 65050                        |
| * On-premises BGP peer IP     | 10.52.255.254                |
|                              |                              |

\* Valfri parameter för BGP endast.

### <a name="ipsecike-policy-and-parameters"></a>IPsec/IKE-principer och parametrar
I följande tabell visas IPsec/IKE-algoritmer och parametrar som används i exemplet. Kontakta din VPN-enhetsspecifikationer för att verifiera de algoritmer som stöds för dina modeller för VPN-enheten och versioner av inbyggd programvara.

| **IPsec/IKEv2**  | **Värde**                            |
| ---              | ---                                  |
| IKEv2-kryptering | AES256                               |
| IKEv2 Integrity  | SHA384                               |
| DH-grupp         | DHGroup24                            |
| * IPsec-kryptering | AES256                               |
| * IPsec Integrity  | SHA1                                 |
| PFS-grupp        | PFS24                                |
| QM SA-livstid   | 7 200 sekunder                         |
| Trafikväljare | UsePolicyBasedTrafficSelectors $True |
| I förväg delad nyckel   | PreSharedKey                         |
|                  |                                      |

\* På vissa enheter måste IPsec integritet vara ett null-värde när algoritmen för IPSec-kryptering är AES-GCM.

### <a name="asa-device-support"></a>Stöd för ASA-enhet

* Stöd för IKEv2 kräver ASA version 8.4 och senare.

* Stöd för DH-grupp och PFS-grupp utöver gruppen 5 kräver ASA version 9.x.

* Stöd för IPSec-kryptering med AES-GCM och IPsec-integritet med SHA-256, SHA-384 och SHA-512, kräver ASA version 9.x. Detta stöd gäller för nya ASA-enheter. Vid tidpunkten för publiceringen stöder modeller för ASA 5505, 5510, 5520, 5540, 5550 och 5580 inte dessa algoritmer. Kontakta din VPN-enhetsspecifikationer för att verifiera de algoritmer som stöds för dina modeller för VPN-enheten och versioner av inbyggd programvara.


### <a name="sample-device-configuration"></a>Exemplet enhetskonfiguration
Skriptet innehåller ett exempel som baseras på konfigurationen och parametrar som beskrivs i föregående avsnitt. Konfigurationen av S2S VPN-tunnel består av följande delar:

1. Gränssnitt och vägar
2. Åtkomstlistor
3. IKE-principer och parametrar (fas 1 eller huvudläge)
4. IPsec-principen och parametrar (fas 2 eller snabbläge)
5. Andra parametrar, till exempel TCP MSS-ihopfogning

> [!IMPORTANT]
> Utför följande steg innan du använder exempelskriptet. Ersätt platshållarvärdena i skriptet med Enhetsinställningar för din konfiguration.

* Ange gränssnittskonfiguration för både i och utanför gränssnitt.
* Identifiera vägarna för dina i/privat och utanför/offentlig nätverk.
* Se till att alla namn och nummer är unika på din enhet.
* Se till att de kryptografiska algoritmerna stöds på din enhet.
* Ersätt följande **platshållarvärdena** med faktiska värden för din konfiguration:
  - Utanför gränssnittsnamn: **utanför**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Virtuellt nätverk och lokala gateway-namn: **VNetName** och **LNGName**
  - Virtuellt nätverk och en lokal nätverksadress **prefix**
  - Rätt **nätmasker**

#### <a name="sample-script"></a>Exempelskript

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Enkel felsökning kommandon

Använd följande kommandon för ASA för felsökning:

* Visa IPsec eller IKE säkerhetsassociation (SA):
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Ange felsökningsläget:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    Den `debug` kommandon kan generera betydande utdata på konsolen.

* Visa de aktuella konfigurationerna på enheten:
    ```
    show run
    ```
    Använd `show` underkommandona till listan över vissa delar av enhetskonfigurationen, till exempel:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Nästa steg
För att konfigurera aktiv-aktiv på flera platser och VNet-till-VNet-anslutningar, se [konfigurera aktiv-aktiv VPN-gatewayer](vpn-gateway-activeactive-rm-powershell.md).
