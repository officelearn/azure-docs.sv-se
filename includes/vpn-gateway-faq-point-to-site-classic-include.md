---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b8a785293c4e555b547c4b002f5ef5906a7633c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
De här vanliga frågorna gäller för P2S-anslutningar som använder den klassiska distributionsmodellen.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Vilka klientoperativsystem kan jag använda med punkt-till-plats?

Följande klientoperativsystem stöds:

* Windows 7 (32-bitars och 64-bitars)
* Windows Server 2008 R2 (endast 64-bitars)
* Windows 8 (32-bitars och 64-bitars)
* Windows 8.1 (32-bitars och 64-bitars)
* Windows Server 2012 (endast 64-bitars)
* Windows Server 2012 R2 (endast 64-bitars)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Kan jag använda alla VPN-programklienter för punkt-till-plats som har stöd för SSTP?

Nej. Stödet är enbart begränsat till de Windows-operativsystemversioner som anges ovan.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Hur många slutpunkter på VPN-klienter kan jag ha i min punkt-till-plats-konfiguration?

Vi stöder att upp till 128 VPN-klienter kan ansluta till ett virtuellt nätverk på samma gång.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Kan jag använda min egen interna PKI-rotcertifikatutfärdare för punkt-till-plats-anslutningen?

Ja. Tidigare kunde bara självsignerade rotcertifikat användas. Du kan fortfarande överföra 20 rotcertifikat.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Kan jag korsa proxyservrar och brandväggar med punkt-till-plats-funktionen?

Ja. Vi använder SSTP (Secure Socket Tunneling Protocol) för tunneltrafik genom brandväggar. Den här tunneln visas som en HTTPs-anslutning.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Om jag startar om en klientdator som konfigurerats för punkt-till-plats, kommer då VPN att återansluta automatiskt?

Som standard kommer klientdatorn inte återupprätta VPN-anslutningen automatiskt.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Har punkt-till-plats stöd för automatisk återanslutning och DDNS på VPN-klienterna?

Automatisk återanslutning och DDNS stöds för närvarande inte i punkt-till-plats-VPN:er.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Kan jag ha plats-till-plats- och punkt-till-plats-konfigurationer samtidigt på samma virtuella nätverk?

Ja. Båda dessa lösningar fungerar om du har en routningsbaserad VPN-typ för din gateway. I den klassiska distributionsmodellen måste du ha en dynamisk gateway. Vi har inte stöd för punkt-till-plats för VPN-gateways med statisk routning eller gateways som använder cmdleten `-VpnType PolicyBased`.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Kan jag konfigurera en punkt-till-plats-klient så att den ansluter till flera virtuella nätverk samtidigt?

Ja, det kan du. Men de virtuella nätverken får inte ha några överlappande IP-prefix, och adressutrymmen för punkt-till-plats får inte överlappa mellan de virtuella nätverken.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Hur högt dataflöde kan jag förvänta mig vid anslutningar för plats-till-plats eller punkt-till-plats?

Det är svårt att bibehålla ett exakt dataflöde i VPN-tunnlarna. IPsec och SSTP är kryptografifrekventa VPN-protokoll. Dataflödet är också begränsat av svarstiden och bandbredden mellan din lokala plats och Internet.
