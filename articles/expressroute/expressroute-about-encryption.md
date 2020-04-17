---
title: 'Azure ExpressRoute: Om kryptering'
description: Läs mer om ExpressRoute-kryptering.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: d48f83df4e83fd1d1fe38eea76432e1b1c711b76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461471"
---
# <a name="expressroute-encryption"></a>ExpressRoute-kryptering
 
ExpressRoute stöder ett par krypteringstekniker för att säkerställa sekretess och integritet för de data som passerar mellan nätverket och Microsofts nätverk.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Vanliga frågor om punkt-till-punkt-kryptering från MACsec
MACsec är en [IEEE-standard](https://1.ieee802.org/security/802-1ae/). Den krypterar data på MAC-nivå (Media Access control) eller Nätverkslagret 2. Du kan använda MACsec för att kryptera de fysiska länkarna mellan nätverksenheterna och Microsofts nätverksenheter när du ansluter till Microsoft via [ExpressRoute Direct](expressroute-erdirect-about.md). MACsec är inaktiverat på ExpressRoute Direct-portar som standard. Du tar med din egen MACsec-nyckel för kryptering och lagrar den i [Azure Key Vault](../key-vault/general/overview.md). Du bestämmer när du ska rotera nyckeln. Se andra vanliga frågor nedan.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Kan jag aktivera MACsec på min ExpressRoute-krets som etablerats av en ExpressRoute-leverantör?
Nej. MACsec krypterar all trafik på en fysisk länk med en nyckel som ägs av en entitet (dvs. kund). Därför är det endast tillgängligt på ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Kan jag kryptera några av ExpressRoute-kretsarna på mina ExpressRoute Direct-portar och lämna andra kretsar på samma portar okrypterade? 
Nej. När MACsec har aktiverats krypteras all nätverkskontrolltrafik, till exempel BGP-datatrafik och kunddatatrafik. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>När jag aktiverar/inaktiverar MACsec eller uppdaterar MACsec-nyckeln förlorar mitt lokala nätverk anslutningen till Microsoft via ExpressRoute?
Ja. För MACsec-konfigurationen stöder vi endast det fördelade nyckelläget. Det innebär att du måste uppdatera nyckeln på både dina enheter och på Microsofts (via vårt API). Den här ändringen är inte atomär, så du förlorar anslutningen när det finns en viktig obalans mellan de två sidorna. Vi rekommenderar starkt att du schemalägger ett underhållsfönster för konfigurationsändringen. För att minimera driftstoppet föreslår vi att du uppdaterar konfigurationen på en länk i ExpressRoute Direct i taget efter att du har växlat nätverkstrafiken till den andra länken.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Kommer trafiken att fortsätta att flöda om det finns en obalans i MACsec-nyckeln mellan mina enheter och Microsofts?
Nej. Om MACsec är konfigurerat och en nyckelmatchning uppstår förlorar du anslutningen till Microsoft. Med andra ord kommer vi inte att falla tillbaka till en okrypterad anslutning, utsätta dina data. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Försämrar du nätverksprestanda genom att aktivera MACsec på ExpressRoute Direct?
MACsec kryptering och dekryptering sker i hårdvara på routrar vi använder. Det finns ingen prestandapåverkan på vår sida. Du bör dock kontrollera med nätverksleverantören för de enheter du använder och se om MACsec har någon prestandakonsekvens.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>vilka chiffersviter stöds för kryptering?
Vi stödjer AES128 och AES256.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Vanliga frågor och svar från slutpunkt till slutpunkt efter kryptering från IPsec
IPsec är en [IETF-standard](https://tools.ietf.org/html/rfc6071). Den krypterar data på IP-nivå eller nätverkslager 3. Du kan använda IPsec för att kryptera en heltäckande anslutning mellan det lokala nätverket och ditt virtuella nätverk (VNET) på Azure. Se andra vanliga frågor nedan.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Kan jag aktivera IPsec utöver MACsec på mina ExpressRoute Direct-portar?
Ja. MACsec säkrar de fysiska anslutningarna mellan dig och Microsoft. IPsec skyddar slutanslutningen mellan dig och dina virtuella nätverk på Azure. Du kan aktivera dem oberoende av dem. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Kan jag använda Azure VPN-gateway för att konfigurera IPsec-tunneln mellan mitt lokala nätverk och mitt virtuella Azure-nätverk?
Ja. Du kan ställa in den här IPsec-tunneln via Microsoft Peering av din ExpressRoute-krets. Följ vår [konfigurationsguide](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Kan jag använda Azure VPN-gateway för att konfigurera IPsec-tunneln via Azure Private Peering?
Om du använder Azure Virtual WAN kan du följa [dessa steg](../virtual-wan/vpn-over-expressroute.md) för att kryptera slutpunkt till slutpunkt-anslutning. Om du har vanliga Azure VNET kan du distribuera en VPN-gateway från tredje part i ditt VNET och upprätta en IPsec-tunnel mellan den och din lokala VPN-gateway.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Vad får jag för dataflöde när jag har aktiverat IPsec på min ExpressRoute-anslutning?
Om Azure VPN-gateway används kontrollerar du [prestandanumren här](../vpn-gateway/vpn-gateway-about-vpngateways.md). Om en VPN-gateway från tredje part används kontrollerar du med leverantören om prestandanumren.

## <a name="next-steps"></a>Nästa steg
Mer information om MACsec-konfigurationen finns i [Konfigurera MACsec.](expressroute-howto-macsec.md)

Mer information om IPsec-konfigurationen finns i [Konfigurera IPsec.](site-to-site-vpn-over-microsoft-peering.md)
