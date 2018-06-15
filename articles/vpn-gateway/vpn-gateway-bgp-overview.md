---
title: Översikt över BGP med Azure VPN-gatewayer | Microsoft Docs
description: Den här artikeln innehåller en översikt över BGP med Azures VPN-gatewayer.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: f8c3985c-c128-4f34-835c-0e88742bf36e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/12/2017
ms.author: yushwang
ms.openlocfilehash: 13a17eb3d78e70a09864bf218f1027d6e98486a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23883511"
---
# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Översikt över BGP med Azures VPN-gatewayer
Den här artikeln innehåller en översikt över BGP-stöd (Border Gateway Protocol) i Azures VPN-gatewayer.

BGP är ett standardroutningsprotokoll som vanligen används på Internet för att utbyta information om routning och åtkomst mellan två eller flera nätverk. När det används i virtuella Azure-nätverk möjliggör BGP att Azures VPN-gatewayer och dina lokala VPN-enheter (kallas BGP-peer eller grannar) kan utbyta ”vägar” som informerar båda gatewayerna om åtkomsten för de prefix som ska passera genom de gateways eller routrar som berörs. BGP kan också möjliggöra överföringsroutning mellan flera nätverk genom att sprida vägar som BGP-gatewayen får information om från en BGP-peer till alla andra BGP-peers. 

## <a name="why"></a>Varför ska jag använda BGP?
BGP är en valfri funktion som du kan använda med Azures routningsbaserade VPN-gateways. Du bör också kontrollera att dina lokala VPN-enheter stöder BGP innan du aktiverar funktionen. Du kan fortsätta att använda Azures VPN-gatewayer och lokala VPN-enheter utan BGP. Det motsvarar att använda statiska vägar (utan BGP) *kontra* dynamisk routning med BGP mellan dina nätverk och Azure.

Det finns flera fördelar och nya funktioner med BGP:

### <a name="prefix"></a>Stöd för automatisk och flexibel prefix uppdateringar
Med BGP behöver du bara deklarera ett minsta prefix till en specifik BGP-peer via IPsec S2S VPN-tunneln. Det kan vara så litet som ett värdprefix (/32) för BGP-peerens IP-adressen till din lokala VPN-enhet. Du kan styra vilka lokala nätverksprefix som du vill annonsera till Azure för att tillåta åtkomst för Azure Virtual Network.

Du kan också annonsera större prefix som kan innehålla några av dina VNet-adressprefixer, till exempel en stor privata IP-adressutrymme (till exempel 10.0.0.0/8). Observera att prefix inte får vara identiskt med något av VNet-prefix. De vägar som är identiska med dina VNet-prefix kommer att avvisas.

### <a name="multitunnel"></a>Stöd för flera tunnlar mellan ett virtuellt nätverk och en lokal plats med automatisk redundans baserat på BGP
Du kan upprätta flera anslutningar mellan ditt Azure-VNet och dina lokala VPN-enheter på samma plats. Den här funktionen innehåller flera tunnlar (sökvägar) mellan två nätverk i en aktiv-aktiv konfiguration. Om något av tunnlarna kopplas från, kommer att tas motsvarande vägar via BGP och trafiken flyttas automatiskt till återstående tunnlar.

I följande diagram visas ett enkelt exempel på den här installationen med hög tillgänglighet:

![Flera aktiva sökvägar](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="transitrouting"></a>Stöd för transitroutning mellan ditt lokala nätverk och flera virtuella Azure-nätverk
Med BGP kan flera gateways lära sig och sprida prefix från olika nätverk, oavsett om de är direkt eller indirekt anslutna. Detta kan möjliggöra överföringsroutning med Azures VPN- gatewayer mellan lokala platser eller över flera virtuella Azure-nätverk.

I följande diagram visas ett exempel på en multihopptopologi med flera sökvägar som kan passera mellan de två lokala nätverken via Azures VPN-gatewayer inom Microsoft Networks:

![Multihoppöverföring](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="faq"></a>BGP VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>Nästa steg
Se i [Komma igång med BGP på Azures VPN-gatewayer](vpn-gateway-bgp-resource-manager-ps.md) hur du konfigurerar BGP för flera lokala platser och VNet-till-VNet-anslutningar.

