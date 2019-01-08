---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/14/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 36b3fcfa90b5b1de9c9d3262da1f3e519cc99c19
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "53444411"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>Stöds anpassade IPsec/IKE-principer på alla Azure VPN Gateway-SKU: er?
Anpassade IPsec/IKE-principer stöds på Azure **VpnGw1, VpnGw2, VpnGw3, Standard** och **HighPerformance** VPN-gatewayer. **Basic** SKU stöds **inte**.

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>Hur många principer kan jag ställa in för en anslutning?
Du kan bara ange ***en*** principkombination för en viss anslutning.

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>Kan jag ange en partiell princip på en anslutning? (till exempel endast IKE-algoritmer, men inte IPsec)
Nej, du måste ange alla algoritmer och parametrar för både IKE (huvudläge) och IPsec (snabbläge). Partiell principspecifikationen tillåts inte.

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>Vilka är de algoritmer och viktiga fördelar som stöds i den anpassade principen?
Tabellen nedan innehåller de krypteringsalgoritmer och nyckellängder som stöds och som kan konfigureras av kunden. Du måste välja ett alternativ för varje fält.

| **IPsec/IKEv2**  | **Alternativ**                                                                   |
| ---              | ---                                                                           |
| IKEv2-kryptering | AES256, AES192, AES128, DES3, DES                                             |
| IKEv2 Integrity  | SHA384, SHA256, SHA1, MD5                                                     |
| DH-grupp         | DHGroup24, ECP384, ECP256, DHGroup14 (DHGroup2048), DHGroup2, DHGroup1, None |
| IPsec-kryptering | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None      |
| IPsec Integrity  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                            |
| PFS-grupp        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, None                              |
| QM SA-livstid   | Sekunder (heltal. **min. 300**/standard 27 000 sekunder)<br>Kilobyte (heltal. **min. 1024**/standard 102400000 kilobyte)           |
| Trafikväljare | UsePolicyBasedTrafficSelectors ($True/$False; default $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. DHGroup2048 och PFS2048 är samma som Diffie-Hellman-grupp **14** i IKE och IPsec PFS. De fullständiga mappningarna finns i avsnittet om [Diffie-Hellman-grupper](#DH).
> 2. För GCMAES-algoritmer måste du ange samma GCMAES-algoritm och nyckellängd för både IPsec-kryptering och -integritet.
> 3. IKEv2 Main Mode SA har en livslängd på högst 28 800 sekunder på Azure VPN-gatewayer
> 4. QM SA-livslängder är valfria parametrar. Om inget har angetts används standardvärdena på 27 000 sekunder (7,5 timmar) och 102 400 000 kB (102 GB).
> 5. UsePolicyBasedTrafficSelector är en valfri parameter för anslutningen. Läs nästa fråga från vanliga frågor och svar för ”UsePolicyBasedTrafficSelectors”

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Behöver allt matcha mellan Azure VPN gateway-principen och mina lokala konfigurationer för VPN-enheter?
Din lokala konfiguration för VPN-enheten måste stämma överens med eller innehålla följande algoritmer och parametrar som du anger på Azure IPsec/IKE-principen:

* IKE-krypteringsalgoritm
* IKE-integritetsalgoritm
* DH-grupp
* IPsec-krypteringsalgoritm
* IPsec-integritetsalgoritm
* PFS-grupp
* Trafikväljare (*)

SA-livstider är lokala specifikationer och behöver inte matcha.

Om du aktiverar **UsePolicyBasedTrafficSelectors**, måste du se till att din VPN-enhet har matchande trafikväljare som har definierats med alla prefixkombinationer i ditt lokala nätverk (lokal nätverksgateway) till eller från de virtuella Azure-nätverksprefixen, i stället för alla-till-alla. Om ditt prefix för det lokala nätverket är 10.1.0.0/16 och 10.2.0.0/16 och ditt prefix för det virtuella nätverket är 192.168.0.0/16 och 172.16.0.0/16, måste du ange följande trafik väljare:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Mer information finns i avsnittet [Connect multiple on-premises policy-based VPN devices](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Ansluta flera lokala principbaserade VPN-enheter).

### <a name ="DH"></a>Vilka Diffie-Hellman-grupper stöds?
Tabellen nedan visar de Diffie-Hellman-grupper som stöds för IKE (DHGroup) och IPsec (PFSGroup):

| **Diffie-Hellman-grupp**  | **DHGroup**              | **PFSGroup** | **Nyckellängd** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | 768-bitars MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024-bitars MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048-bitars MODP  |
| 19                        | ECP256                   | ECP256       | 256-bitars ECP    |
| 20                        | ECP384                   | ECP384       | 384-bitars ECP    |
| 24                        | DHGroup24                | PFS24        | 2048-bitars MODP  |
|                           |                          |              |                |

Mer information finns i [RFC3526](https://tools.ietf.org/html/rfc3526) och [RFC5114](https://tools.ietf.org/html/rfc5114).

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>Ersätter den anpassade principen standardprincipuppsättningar för IPsec/IKE för Azure VPN-gatewayer?
Ja, när en anpassad princip har angetts på en anslutning kommer Azure VPN-gatewayen bara använda principen på anslutningen, både som IKE-initierare och IKE-responder.

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>Om jag tar bort en anpassad princip för IPsec/IKE, blir anslutningen mindre säker?
Nej, anslutningen kommer att skyddas av IPsec/IKE. När du tar bort den anpassade principen från en anslutning återgår Azure VPN-gatewayen till [standardlistan med IPsec/IKE-förslag](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) och startar om IKE-handskakningen med din lokala VPN-enhet.

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>Kan det störa VPN-anslutningen att lägga till eller uppdatera en IPsec/IKE-princip?
Ja, det kan uppstå ett kort avbrott (några sekunder) när Azure VPN-gatewayen bryter den befintliga anslutningen och startar om IKE-handskakningen för att återupprätta IPsec-tunneln med de nya krypteringsalgoritmerna och parametrarna. Kontrollera att din lokala VPN-enhet har konfigurerats med matchande algoritmer och nyckellängder för att minimera avbrottet.

### <a name="can-i-use-different-policies-on-different-connections"></a>Kan jag använda olika principer för olika anslutningar?
Ja. Anpassade principer tillämpats på per-anslutningbasis. Du kan skapa och tillämpa olika IPsec/IKE-principer på olika anslutningar. Du kan också välja att använda anpassade principer för en delmängd av anslutningar. Övriga principer använder de fördefinierade IPsec/IKE-principuppsättningarna i Azure.

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>Kan jag använda den anpassade principen på VNet-till-VNet-anslutningen?
Ja, du kan använda anpassade principen på både IPSec-anslutningar mellan lokala anslutningar eller VNet-till-VNet-anslutningar.

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>Behöver jag ange samma princip på båda resurserna för VNet-till-VNet-anslutningen?
Ja. En VNet-till-VNet-tunnel består av två anslutningsresurser i Azure, en för varje riktning. Kontrollera att båda anslutningsresurserna har samma princip, annars upprättas inte anslutningen mellan de virtuella nätverken.

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>Fungerar en anpassad IPsec/IKE-princip på ExpressRoute-anslutningen?
Nej. IPSec-/ princip fungerar bara på S2S VPN- och VNet-till-VNet-anslutningar via Azure VPN-gatewayer.

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>Var hittar jag mer information om IPsec?
Se [konfigurera IPsec/IKE-principer för S2S eller VNet-till-VNet-anslutningar](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md)
