---
title: Om kryptering – Azure-ExpressRoute | Microsoft Docs
description: Lär dig mer om ExpressRoute-kryptering.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 650e45ca9092b9c81b2127eb995a0297745410a4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244144"
---
# <a name="expressroute-encryption"></a>ExpressRoute-kryptering
 
ExpressRoute stöder en par krypterings tekniker som garanterar konfidentialitet och integritet för data som passerar mellan ditt nätverk och Microsofts nätverk.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Vanliga frågor och svar om punkt-till-punkt-kryptering av MACsec
MACsec är en [IEEE-standard](https://1.ieee802.org/security/802-1ae/). Den krypterar data på MAC-nivån (Media Access Control) eller nätverks nivå 2. Du kan använda MACsec för att kryptera de fysiska länkarna mellan dina nätverks enheter och Microsofts nätverks enheter när du ansluter till Microsoft via [ExpressRoute Direct](expressroute-erdirect-about.md). MACsec är inaktiverat på ExpressRoute Direct-portar som standard. Du tar med din egen MACsec-nyckel för kryptering och lagrar den i [Azure Key Vault](../key-vault/key-vault-overview.md). Du bestämmer när du vill rotera nyckeln. Se andra vanliga frågor och svar nedan.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Kan jag aktivera MACsec på min ExpressRoute-krets som tillhandahålls av en ExpressRoute-Provider?
Nej. MACsec krypterar all trafik på en fysisk länk med en nyckel som ägs av en entitet (d.v.s. kund). Därför är den bara tillgänglig på ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Kan jag kryptera några av ExpressRoute-kretsarna på mina ExpressRoute Direct-portar och lämna andra kretsar på samma portar okrypterade? 
Nej. När MACsec har Aktiver ATS är all nätverks kontroll trafik, t. ex. BGP-datatrafiken och kund data trafiken krypterad. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>När jag aktiverar/inaktiverar MACsec eller uppdaterar MACsec nyckel kommer mitt lokala nätverk att förlora anslutningen till Microsoft via ExpressRoute?
Ja. För MACsec-konfigurationen stöder vi bara det i förväg delade nyckel läget. Det innebär att du måste uppdatera nyckeln på både enheterna och på Microsofts (via vår API). Den här ändringen är inte atomisk, så du förlorar anslutningen när det finns ett nyckel matchnings fel mellan de två sidorna. Vi rekommenderar starkt att du schemalägger en underhålls period för konfigurations ändringen. För att minimera stillestånds tiden rekommenderar vi att du uppdaterar konfigurationen på en länk av ExpressRoute direkt i taget när du har växlat nätverks trafiken till den andra länken.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Kommer trafiken fortsätta att flöda om det finns ett matchnings fel i MACsec-nyckeln mellan mina enheter och Microsoft?
Nej. Om MACsec har kon figurer ATS och en nyckel avvikelse uppstår förlorar du anslutningen till Microsoft. Det innebär att det inte går att återgå till en okrypterad anslutning, vilket visar dina data. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Kommer MACsec att aktivera på ExpressRoute direkt försämra nätverks prestanda?
MACsec kryptering och dekryptering sker i maskin vara på de routrar som vi använder. Det finns ingen inverkan på vår sida. Du bör dock kontrol lera med nätverks leverantören för de enheter du använder och se om MACsec har några prestanda indirekt.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>vilka chiffersviter stöds för kryptering?
Vi stöder AES128 (GCM – AES-128) och AES256 (GCM – AES – 256).

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Vanliga frågor och svar från slut punkt till slut punkt för IPsec
IPsec är en [IETF-standard](https://tools.ietf.org/html/rfc6071). Den krypterar data på Internet Protocol (IP) eller nätverks nivå 3. Du kan använda IPsec för att kryptera en slutpunkt-till-slutpunkt-anslutning mellan ditt lokala nätverk och ditt virtuella nätverk (VNET) i Azure. Se andra vanliga frågor och svar nedan.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Kan jag aktivera IPsec förutom MACsec på mina ExpressRoute Direct-portar?
Ja. MACsec säkrar de fysiska anslutningarna mellan dig och Microsoft. IPsec skyddar slutpunkt-till-slutpunkt-anslutningen mellan dig och dina virtuella nätverk i Azure. Du kan aktivera dem oberoende av varandra. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Kan jag använda Azure VPN gateway för att konfigurera IPsec-tunneln mellan mitt lokala nätverk och mitt virtuella Azure-nätverk?
Ja. Du kan konfigurera den här IPsec-tunneln för Microsoft-peering av ExpressRoute-kretsen. Följ vår [konfigurations guide](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Kan jag använda Azure VPN gateway för att konfigurera IPsec-tunneln via privat Azure-peering?
Nej. Du måste distribuera en VPN-gateway från tredje part i ditt virtuella Azure-nätverk och upprätta en IPsec-tunnel mellan den och din lokala VPN-gateway.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Vad kommer genomflödet att få när du har aktiverat IPsec på min ExpressRoute-anslutning?
Om Azure VPN gateway används kontrollerar du [prestanda numren här](../vpn-gateway/vpn-gateway-about-vpngateways.md). Om en VPN-gateway från tredje part används, kontrollerar du med leverantören för prestanda numren.

## <a name="next-steps"></a>Nästa steg
Mer information om MACsec-konfigurationen finns i [Konfigurera MACsec](expressroute-howto-macsec.md) .

Se [Konfigurera IPSec](site-to-site-vpn-over-microsoft-peering.md) för mer information om IPSec-konfigurationen.
