<properties
   pageTitle="Översikt över BGP med Azures VPN-gatewayer | Microsoft Azure"
   description="Den här artikeln innehåller en översikt över BGP med Azures VPN-gatewayer."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/16/2016"
   ms.author="yushwang"/>


# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Översikt över BGP med Azures VPN-gatewayer

Den här artikeln innehåller en översikt över BGP-stöd (Border Gateway Protocol) i Azures VPN-gatewayer.

## <a name="about-bgp"></a>Om BGP

BGP är ett standardroutningsprotokoll som vanligen används på Internet för att utbyta information om routning och åtkomst mellan två eller flera nätverk. När det används i virtuella Azure-nätverk möjliggör BGP att Azures VPN-gatewayer och dina lokala VPN-enheter (kallas BGP-peer eller grannar) kan utbyta ”vägar” som informerar båda gatewayerna om åtkomsten för de prefix som ska passera genom de gateways eller routrar som berörs. BGP kan också möjliggöra överföringsroutning mellan flera nätverk genom att sprida vägar som BGP-gatewayen får information om från en BGP-peer till alla andra BGP-peers.
 
### <a name="why-use-bgp?"></a>Varför ska man använda BGP?

BGP är en valfri funktion som du kan använda med Azures routningsbaserade VPN-gateways. Du bör också kontrollera att dina lokala VPN-enheter stöder BGP innan du aktiverar funktionen. Du kan fortsätta att använda Azures VPN-gatewayer och lokala VPN-enheter utan BGP. Det motsvarar att använda statiska vägar (utan BGP) *kontra* dynamisk routning med BGP mellan dina nätverk och Azure.

Det finns flera fördelar och nya funktioner med BGP:

#### <a name="support-automatic-and-flexible-prefix-updates"></a>Stöd för automatiska och flexibla prefixuppdateringar

Med BGP behöver du bara deklarera ett minsta prefix till en specifik BGP-peer via IPsec S2S VPN-tunneln. Det kan vara så litet som ett värdprefix (/32) för BGP-peerens IP-adressen till din lokala VPN-enhet. Du kan styra vilka lokala nätverksprefix som du vill annonsera till Azure för att tillåta åtkomst för Azure Virtual Network.
    
Du kan också annonsera ett större prefix som kan innehålla några av dina VNet-adressprefix, som t.ex. ett stort privat IP-adressutrymme (t.ex. 10.0.0.0/8). Observera att prefixen inte får vara identiska med några av dina VNet-prefix. De vägar som är identiska med dina VNet-prefix kommer att avvisas.

>[AZURE.IMPORTANT] För närvarande kommer annonsering av standardvägen (0.0.0.0/0) till Azure VPN-gatewayer att blockeras. Ytterligare uppdatering tillhandahålls när funktionen aktiveras.

#### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>Stöd för flera tunnlar mellan ett VNet och en lokal plats med automatisk redundans baserat på BGP

Du kan upprätta flera anslutningar mellan ditt Azure-VNet och dina lokala VPN-enheter på samma plats. Den här funktionen innehåller flera tunnlar (sökvägar) mellan två nätverk i en aktiv-aktiv konfiguration. Om en av tunnlarna kopplas bort kommer motsvarande vägar dras tillbaka via BGP och trafiken flyttas automatiskt till de återstående tunnlarna.
    
I följande diagram visas ett enkelt exempel på den här installationen med hög tillgänglighet:
    
![Flera aktiva sökvägar](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>Stöd för överföringsroutning mellan dina lokala nätverk och flera Azure-VNets

Med BGP kan flera gateways lära sig och sprida prefix från olika nätverk, oavsett om de är direkt eller indirekt anslutna. Detta kan möjliggöra överföringsroutning med Azures VPN- gatewayer mellan lokala platser eller över flera virtuella Azure-nätverk.
    
I följande diagram visas ett exempel på en multihopptopologi med flera sökvägar som kan passera mellan de två lokala nätverken via Azures VPN-gatewayer inom Microsoft Networks:

![Multihoppöverföring](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faqs"></a>Vanliga frågor och svar om BGP


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 




## <a name="next-steps"></a>Nästa steg

Se i [Komma igång med BGP på Azures VPN-gatewayer](./vpn-gateway-bgp-resource-manager-ps.md) hur du konfigurerar BGP för flera lokala platser och VNet-till-VNet-anslutningar.




<!--HONumber=Oct16_HO3-->


