---
title: Översikt över BGP och Azure VPN-gatewayer | Microsoft Docs
description: Den här artikeln innehåller en översikt över BGP med Azure VPN Gateway.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/12/2017
ms.author: yushwang
ms.openlocfilehash: 91e9fe1eb6b3df0b64d05f2b1e300403a9e01db9
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306530"
---
# <a name="about-bgp-with-azure-vpn-gateway"></a>Om BGP med Azure VPN Gateway
Den här artikeln innehåller en översikt över BGP (Border Gateway Protocol)-stöd i Azure VPN Gateway.

BGP är ett standardroutningsprotokoll som vanligen används på Internet för att utbyta information om routning och åtkomst mellan två eller flera nätverk. När det används i virtuella Azure-nätverk möjliggör BGP att Azure VPN Gateway och dina lokala VPN-enheter (kallas BGP-peer eller grannar) kan utbyta ”vägar” som informerar båda gatewayerna om åtkomsten för de prefix som ska passera genom de gatewayer eller routrar som berörs. BGP kan också möjliggöra överföringsroutning mellan flera nätverk genom att sprida vägar som BGP-gatewayen får information om från en BGP-peer till alla andra BGP-peers. 

## <a name="why"></a>Varför ska jag använda BGP?
BGP är en valfri funktion som du kan använda med Azures routningsbaserade VPN-gateways. Du bör också kontrollera att dina lokala VPN-enheter stöder BGP innan du aktiverar funktionen. Du kan fortsätta att använda Azures VPN-gatewayer och lokala VPN-enheter utan BGP. Det motsvarar att använda statiska vägar (utan BGP) *kontra* dynamisk routning med BGP mellan dina nätverk och Azure.

Det finns flera fördelar och nya funktioner med BGP:

### <a name="prefix"></a>Stöd för automatiska och flexibla prefixuppdateringar
Med BGP behöver du bara deklarera ett minsta prefix till en specifik BGP-peer via IPsec S2S VPN-tunneln. Det kan vara så litet som ett värdprefix (/32) för BGP-peerens IP-adressen till din lokala VPN-enhet. Du kan styra vilka lokala nätverksprefix som du vill annonsera till Azure för att tillåta åtkomst för Azure Virtual Network.

Du kan också annonsera ett större prefix som kan innehålla några av dina VNet-adressprefix, till exempel ett stort privat IP-adressutrymme (t.ex, 10.0.0.0/8). Observera även om prefix inte får vara identiskt med några av dina VNet-prefix. De vägar som är identiska med dina VNet-prefix kommer att avvisas.

### <a name="multitunnel"></a>Stöd för flera tunnlar mellan ett virtuellt nätverk och en lokal plats med automatisk redundans baserat på BGP
Du kan upprätta flera anslutningar mellan ditt Azure-VNet och dina lokala VPN-enheter på samma plats. Den här funktionen innehåller flera tunnlar (sökvägar) mellan två nätverk i en aktiv-aktiv konfiguration. Om en av tunnlarna kopplas motsvarande vägar dras tillbaka via BGP och trafiken flyttas automatiskt till de återstående tunnlarna.

I följande diagram visas ett enkelt exempel på den här installationen med hög tillgänglighet:

![Flera aktiva sökvägar](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="transitrouting"></a>Stöd för överföringsroutning mellan ditt lokala nätverk och flera virtuella Azure-nätverk
Med BGP kan flera gateways lära sig och sprida prefix från olika nätverk, oavsett om de är direkt eller indirekt anslutna. Detta kan möjliggöra överföringsroutning med Azures VPN- gatewayer mellan lokala platser eller över flera virtuella Azure-nätverk.

I följande diagram visas ett exempel på en multihopptopologi med flera sökvägar som kan passera mellan de två lokala nätverken via Azures VPN-gatewayer inom Microsoft Networks:

![Multihoppöverföring](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="faq"></a>BGP VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>Nästa steg
Se i [Komma igång med BGP på Azures VPN-gatewayer](vpn-gateway-bgp-resource-manager-ps.md) hur du konfigurerar BGP för flera lokala platser och VNet-till-VNet-anslutningar.

