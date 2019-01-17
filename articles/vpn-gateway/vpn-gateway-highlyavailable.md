---
title: Översikt över konfigurationer med hög tillgänglighet med Azure VPN gateway | Microsoft Docs
description: Den här artikeln ger en översikt över konfigurationsalternativen för hög tillgänglighet med Azure VPN Gateway.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2016
ms.author: yushwang
ms.openlocfilehash: 623ed10e155012780f039bf7b9148be34143454d
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353285"
---
# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Anslutning med hög tillgänglighet på flera platser och VNet-till-VNet-anslutning
Den här artikeln ger en översikt över konfigurationsalternativen för anslutning på flera platser och VNet-till-VNet-anslutning med Azure VPN-gateways.

## <a name = "activestandby"></a>Om Azure VPN gateway-redundans
Varje Azure VPN-gateway består av två instanser i en aktiv-standby-konfiguration. När det utförs ett planerat underhåll (eller uppstår ett oplanerat avbrott) för en aktiv instans tar standby-instansen över (redundans) automatiskt och S2S VPN- eller VNet-till-VNet-anslutningarna återupptas. Växlingen mellan instanserna orsakar ett kort avbrott. Vid ett planerat underhåll återställs anslutningen inom 10 till 15 sekunder. Vid ett oplanerat avbrott tar det lite längre tid att återställa anslutningen. Det kan ta ungefär en minut eller en och en halv minut i värsta fall. När det gäller P2S VPN-klientanslutningar till gatewayen kopplas P2S-anslutningarna från och användarna måste ansluta på nytt från klientdatorerna.

![Aktiv-standby](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Anslutning med hög tillgänglighet på flera platser
Det finns ett par alternativ för att få bättre tillgänglighet för anslutningar på flera platser:

* Flera lokala VPN-enheter
* Aktiv-aktiv Azure VPN gateway
* En kombination av båda

### <a name = "activeactiveonprem"></a>Flera lokala VPN-enheter
Du kan använda flera VPN-enheter från ditt lokala nätverk för att ansluta till din Azure VPN-gateway enligt följande diagram:

![Flera lokala VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Med den här konfigurationen får du flera aktiva tunnlar från samma Azure VPN-gateway till dina lokala enheter på samma plats. Det finns vissa krav och begränsningar:

1. Du måste skapa flera S2S VPN-anslutningar från dina VPN-enheter till Azure. När du ansluter flera VPN-enheter från samma lokala nätverk till Azure, måste du skapa en lokal nätverksgateway för varje VPN-enhet och en anslutning från din Azure VPN-gateway för varje lokal nätverksgateway.
2. De lokala nätverkgateways som motsvarar dina VPN-enheter måste ha unika offentliga IP-adresser i GatewayIpAddress-egenskapen.
3. BGP krävs för den här konfigurationen. Varje lokal nätverksgateway som representerar en VPN-enhet måste ha en unik IP-adress för BGP-peer angiven i BgpPeerIpAddress-egenskapen.
4. AddressPrefix-egenskapsfältet i varje lokal nätverksgateway får inte överlappa varandra. Du måste ange "BgpPeerIpAddress" i /32 CIDR-format i fältet AddressPrefix, till exempel 10.200.200.254/32.
5. Du bör använda BGP så att samma prefix visas för samma lokala nätverksprefix för Azure VPN-gatewayen, och trafiken vidarebefordras samtidigt genom dessa tunnlar.
6. Du måste använda Equal-cost Multipath routning (ECMP).
7. Varje anslutning räknas av mot det maximala antalet tunnlar för din Azure VPN-gateway: 10 för Basic- och Standard-SKU:er och 30 för HighPerformance-SKU. 

I den här konfigurationen är Azure VPN-gatewayen fortfarande i aktiv-standby-läge, vilket innebär att det redundansbeteende och korta avbrott som beskrivs [ovan](#activestandby) fortfarande gäller. Men konfigurationen skyddar mot fel och avbrott i ditt lokala nätverk och dina VPN-enheter.

### <a name="active-active-azure-vpn-gateway"></a>Aktiv-aktiv Azure VPN gateway
Nu kan du skapa en Azure VPN-gateway i en aktiv-aktiv-konfiguration, där båda instanserna av de virtuella datorernas gateway etablerar S2S VPN-tunnlar till din lokala VPN-enhet enligt följande diagram:

![Aktiv-aktiv](./media/vpn-gateway-highlyavailable/active-active.png)

I den här konfigurationen har varje Azure-gatewayinstans en unik offentlig IP-adress, och varje instans etablerar en IPsec/IKE S2S VPN-tunnel till den lokala VPN-enhet som du har angett i din lokala nätverksgateway och anslutning. Observera att båda VPN-tunnlarna tillhör samma anslutning. Du måste ändå konfigurera din lokala VPN-enhet för att godkänna eller etablera S2S VPN-tunnlar till de två offentliga IP-adresserna för Azure VPN-gatewayen.

Eftersom Azure-gatewayinstanserna är i en aktiv-aktiv-konfiguration dirigeras trafiken från ditt virtuella Azure-nätverk till ditt lokala nätverk samtidigt via båda tunnlarna, även om din lokala VPN-enhet prioriterar en tunnel framför den andra. Observera att TCP- eller UDP-flödet alltid går igenom samma tunnel eller väg, såvida inte en underhållshändelse inträffar på någon av instanserna.

När ett planerat underhåll utförs för en gatewayinstans (eller en oplanerad händelse inträffar) frånkopplas IPsec-tunneln från den instansen till din lokala VPN-enhet. Motsvarande vägar på dina VPN-enheter måste tas bort eller dras tillbaka automatiskt så att trafiken växlar över till den andra aktiva IPsec-tunneln. På Azure-sidan sker växlingen automatiskt från den berörda instansen till den aktiva instansen.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Dubbel redundans: aktiv-aktiv VPN-gateways för både Azure och lokala nätverk
Det mest tillförlitliga alternativet är att kombinera aktiv-aktiv-gateways i både nätverket och Azure (se diagrammet nedan).

![Dubbel redundans](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

Här skapar du och konfigurerar Azure VPN-gatewayen i en aktiv-aktiv-konfiguration, och skapar två lokala nätverksgateways och två anslutningar för dina två lokala VPN-enheter enligt beskrivningen ovan. Resultatet är en anslutning med ett helt nät med 4 IPSec-tunnlar mellan ditt virtuella Azure-nätverk och ditt lokala nätverk.

Alla gateways och tunnlar är aktiva från Azure-sidan, vilket gör att trafiken sprids samtidigt mellan alla fyra tunnlar. Varje TCP- eller UDP-flöde går genom samma tunnel eller väg från Azure-sidan. Genom att sprida trafiken kan du få bättre genomströmning via IPsec-tunnlarna, men huvudsyftet med konfigurationen är att uppnå hög tillgänglighet. På grund av spridningens statistiska egenskaper är det svårt att ge något mått på hur olika programtrafikförhållanden påverkar den totala genomströmningen.

För den här topologin krävs två lokala nätverksgateways och två anslutningar för de två lokala VPN-enheterna, och BGP krävs för att kunna använda dessa två anslutningar till samma lokala nätverk. Kraven är desamma som de som anges [ovan](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Anslutningar mellan virtuella nätverk med hög tillgänglighet via Azure VPN Gateway
Samma aktiv-aktiv-konfiguration gäller även för Azure VNet-till-VNet-anslutningar. Du kan skapa aktiv-aktiv VPN-gateways för de båda virtuella nätverken och koppla samman dem för att skapa samma anslutningar med ett helt nät med 4 tunnlar mellan två VNets, enligt diagrammet nedan:

![VNet-till-VNet](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

Detta garanterar att det alltid finns ett tunnelpar mellan de två virtuella nätverken för planerade underhållshändelser, och det ger också ökad tillgänglighet. Trots att samma topologi för anslutning på flera platser kräver två anslutningar behöver VNet-till-VNet-topologin ovan bara en anslutning för varje gateway. Dessutom är BGP valfritt, om det inte krävs överföringsroutning över VNet-till-VNet-anslutningen.

## <a name="next-steps"></a>Nästa steg
Anvisningar om hur du konfigurerar aktiv-aktiv VPN Gateway för flera platser och anslutningar mellan virtuella nätverk finns i [Konfigurera aktiv-aktiv VPN Gateway för flera platser och anslutningar mellan virtuella nätverk](vpn-gateway-activeactive-rm-powershell.md).

