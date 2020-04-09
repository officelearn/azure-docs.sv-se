---
title: Koppla samman med Kina med Azure Virtual WAN och secure Hub
description: Lär dig mer om Virtual WAN-automatisk skalbar anslutning mellan för gren till gren, tillgängliga regioner och partner.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985634"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Koppla samman med Kina med Azure Virtual WAN och Secure Hub

När man tittar på vanliga fordonsindustrin, tillverkningsindustrin, logistikindustrin eller andra institut som ambassader, finns det ofta frågan om hur man kan förbättra samtrafik med Kina. Dessa förbättringar är oftast relevanta för användning av molntjänster som Office 365, Azure Global Services eller sammanlänkade filialer i Kina med en kundstamnät.

I de flesta fall, kunder kämpar med hög latens, låg bandbredd, instabil anslutning, och höga kostnader ansluter till utanför Kina (till exempel, Europa eller USA).

En anledning till dessa kamper är "Great Firewall of China", som skyddar den kinesiska delen av Internet och filtrerar trafik till Kina. Nästan all trafik som går från Kina fastlandet till utanför Kina, utom den särskilda administrationen zoner som Hong Kong och Macau, passerar den stora brandväggen. Trafiken som går genom Hong Kong och Macau träffar inte great firewall med full kraft, den hanteras av en delmängd av Great Firewall.

![Sammanlänkning av leverantör](./media/interconnect-china/provider.png)

Med Virtual WAN kan en kund upprätta en mer högpresterande och stabil anslutning till Microsoft Cloud Services och en anslutning till företagets nätverk utan att bryta mot den kinesiska cybersäkerhetslagen.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Krav och arbetsflöde

Om du vill hålla dig kompatibel med den kinesiska cybersäkerhetslagen måste du uppfylla en uppsättning vissa villkor.

Först måste du arbeta tillsammans med ett nätverk och Internet-leverantören som äger en ICP-licens (Internet Content Provider) för Kina. I de flesta fall får du en av följande leverantörer:

* China Telecom Global Ltd.
* Kina Mobile Ltd.
* Kina Unicom Ltd.
* PCCW Global Ltd.
* Hongkong Telecom Ltd.

Beroende på leverantören och dina behov måste du nu köpa en av följande nätverksanslutningstjänster för att koppla samman dina filialer i Kina.

* Ett MPLS/IPVPN-nätverk 
* Ett programvarudefinierat WAN (SDWAN)
* Dedikerad internetåtkomst

Därefter måste du hålla med den leverantören för att ge en breakout till Microsoft Global Network och dess Edge Network i Hong Kong, inte i Peking eller Shanghai. I detta fall är Hong Kong mycket viktigt på grund av dess fysiska anslutning och plats till Kina.

Medan de flesta kunder tror att använda Singapore för sammankoppling är det bästa fallet eftersom det ser närmare Kina när man tittar på kartan, detta är inte sant. När du följer nätverksfiberkartor går nästan alla nätverksanslutningar via Peking, Shanghai och Hong Kong. Detta gör Hong Kong ett bättre läge val att koppla till Kina.

Beroende på leverantör kan du få olika tjänsteerbjudanden. Tabellen nedan visar ett exempel på leverantörer och den tjänst de erbjuder, baserat på information vid den tidpunkt då den här artikeln skrevs.

| Tjänst | Exempel på leverantör |
| --- | --- |
| MPLS/IPVPN-nätverk |PCCW, China Telecom Global |
|SDWAN (SDWAN)| PCCW, China Telecom Global|
| Dedikerad internetåtkomst | PCCW, Hong Kong Telecom, Kina Mobil|

Med din leverantör kan du komma överens om vilka av följande två lösningar som ska användas för att nå Microsofts globala stamnät:

* Få en Microsoft Azure ExpressRoute avslutad i Hong Kong. Detta skulle vara fallet vid användning av MPLS/IPVPN. För närvarande är endast den enda ICP-licensleverantören med ExpressRoute till Hong Kong China Telecom Global. De kan dock också prata med andra leverantörer om de utnyttjar Cloud Exchange Providers som Megaport eller InterCloud. Mer information finns i [ExpressRoute-anslutningsleverantörer](../expressroute/expressroute-locations-providers.md#partners).

* Använda en dedikerad Internet-åtkomst direkt på någon av följande Internet Exchange-punkter eller använda en privat nätverksanslutning.

Följande lista visar Internet-börser möjligt i Hong Kong:

* AMS-IX Hongkong
* BBIX Hongkong
* Equinix Hongkong
* HKIX (AVSIX)

När du använder den här anslutningen måste nästa BGP-hopp för Microsoft Services vara Microsoft Autonomous System Number (AS#) 8075. Om du använder en enda plats eller SDWAN-lösning, skulle det vara valet av anslutning.

Hursomhelst, rekommenderar vi fortfarande att du har en andra och regelbunden Internet Breakout i det kinesiska fastlandet. Detta är att dela upp trafiken mellan företagstrafik till molntjänster som Microsoft 365 och Azure, och lagreglerad reglerad Internettrafik.

En kompatibel nätverksarkitektur i Kina kan se ut som följande exempel:

![Flera grenar](./media/interconnect-china/multi-branch.png)

I det här exemplet kan du ha en sammankoppling med Microsoft Global Network i Hong Kong nu börja utnyttja [Azure Virtual WAN Global Transit Architecture](virtual-wan-global-transit-network-architecture.md) och ytterligare tjänster, till exempel Azure secure Virtual WAN hub, för att använda tjänster och koppla till dina filialer och datacenter utanför Kina.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Hub-till-hub-kommunikation

I det här avsnittet använder vi Virtual WAN hub-to-hub-kommunikation för att koppla samman. I det här fallet skapar du en ny virtuell WAN-hubb för att ansluta till en virtuell WAN-hubb i Hong Kong, andra regioner som du föredrar, en region där du redan har Azure-resurser eller där du vill ansluta.

En exempelarkitektur kan se ut som följande exempel:

![Exempel på WAN](./media/interconnect-china/sample.png)

I det här exemplet ansluter Kina-grenarna till Azure Cloud China och varandra med hjälp av VPN- eller MPLS-anslutningar. Filialer som måste anslutas till Global Services använder MPLS eller Internetbaserade tjänster som är anslutna direkt till Hong Kong. Om du vill använda ExpressRoute i Hong Kong och i den andra regionen måste du konfigurera [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) för att koppla samman båda ExpressRoute Circuits.

ExpressRoute Global Reach är inte tillgängligt i vissa regioner. Om du till exempel behöver koppla samman med Brasilien eller Indien måste du använda [Cloud Exchange Providers](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) för att tillhandahålla routningstjänsterna.

Bilden nedan visar båda exemplen för det här scenariot.

![Global räckvidd](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Säker Internet breakout för Office 365

En annan faktor är nätverkssäkerhet samt loggning för ingångspunkten mellan Kina och Virtual WAN etablerade stamnät komponent, och kunden ryggraden. I de flesta fall finns det ett behov av att bryta ut på Internet i Hong Kong för att direkt nå Microsoft Edge-nätverket och, med det, Azure Front Door Servers som används för Microsoft 365 Services.

För båda scenarierna med Virtuellt WAN använder du [azure virtual WAN-skyddade hubben](../firewall-manager/secured-virtual-hub.md). Med Hjälp av Azure Firewall Manager kan du ändra en vanlig Virtuell WAN-hubb till en säker hubb och sedan distribuera och hantera en Azure-brandvägg inom den hubben.

Följande bild visar ett exempel på det här scenariot:

![Internet breakout för webb- och Microsoft-tjänster trafik](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Arkitektur och trafikflöden

Beroende på ditt val när det gäller anslutningen till Hong Kong kan den övergripande arkitekturen ändras något. Det här avsnittet visar tre tillgängliga arkitekturer i olika kombinationer med VPN eller SDWAN och/eller ExpressRoute.

Alla dessa alternativ använder Azure Virtual WAN-säker hubb för direkt M365-anslutning i Hong Kong. Dessa arkitekturer stöder också efterlevnadskraven för [Office 365 Multi-Geo](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) och håller trafiken nära nästa Office 365-klientplats. Som ett resultat, Det är också en förbättring för användningen av Microsoft 365 från Kina.

När du använder Azure Virtual WAN tillsammans med Internet-anslutningar kan varje anslutning dra nytta av ytterligare tjänster som [Microsoft Azure Peering Services (MAPS)](https://docs.microsoft.com/azure/peering-service/about). MAPS byggdes för att optimera trafiken som kommer till Microsoft Global Network från tredje parts Internet-leverantörer.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Alternativ 1: SDWAN eller VPN

I det här avsnittet beskrivs en design som använder SDWAN eller VPN till Hong Kong och andra grenar. Det här alternativet visar användning och trafikflöde när du använder ren Internetanslutning på båda platserna i virtual WAN-stamnätet. I det här fallet överförs anslutningen till Hong Kong med dedikerad Internet-åtkomst eller en ICP-leverantör SDWAN-lösning. Andra grenar använder rent Internet eller SDWAN Solutions också.

![Kina till Hongkong trafik](./media/interconnect-china/china-traffic.png)

I den här arkitekturen är varje plats ansluten till Microsofts globala nätverk med hjälp av VPN och Azure Virtual WAN. Trafiken mellan platserna och Hong Kong överförs genom Microsoft Network och använder endast regelbunden Internetanslutning på den sista milen.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Alternativ 2: ExpressRoute och SDWAN eller VPN

I det här avsnittet beskrivs en design som använder ExpressRoute i Hong Kong och andra grenar med VPN/SDWAN-grenar. Det här alternativet visar användningen av och ExpressRoute avslutas i Hong Kong och andra grenar som är anslutna via SDWAN eller VPN. ExpressRoute i Hong Kong är för närvarande begränsad till en kort lista över leverantörer, som du hittar i listan över [Express Route Partners](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![Kina till Hong Kong trafikEr ExpressRoute](./media/interconnect-china/expressroute.png)

Det finns också alternativ för att avsluta ExpressRoute från Kina, till exempel i Sydkorea eller Japan. Men med tanke på efterlevnad, reglering och latens är Hongkong för närvarande det bästa valet.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Alternativ 3: Endast ExpressRoute

I det här avsnittet beskrivs en design där ExpressRoute används för Hong Kong och andra grenar. Det här alternativet visar sammankopplingen med ExpressRoute i båda ändar. Här har du ett annat trafikflöde än det andra. Microsoft 365-trafiken flödar till azure-navet för virtuellt WAN-skyddat och därifrån till Microsoft Edge Network och Internet.

Den trafik som går till de sammankopplade grenarna eller från dem till platserna i Kina kommer att följa en annan strategi inom denna arkitektur. För närvarande stöder virtuella WAN inte ExpressRoute till ExpressRoute-överföring. Trafiken kommer att utnyttja ExpressRoute Global Reach eller 3: e parts sammanlänkning utan att passera den virtuella WAN Hub. Det kommer direkt att flöda från en Microsoft Enterprise Edge (MSEE) till en annan.

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

För närvarande är ExpressRoute Global Reach inte tillgängligt i alla länder, men du kan konfigurera en lösning med Azure Virtual WAN.

Du kan till exempel konfigurera en ExpressRoute med Microsoft Peering och ansluta en VPN-tunnel via den peering till Azure Virtual WAN. Nu har du aktiverat, återigen, transiteringen mellan VPN och ExpressRoute utan Global Reach och tredjepartsleverantör och tjänst, till exempel Megaport Cloud.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

* [Global transitnätverksarkitektur med Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

* [Skapa ett virtuellt WAN-nav](virtual-wan-site-to-site-portal.md)

* [Konfigurera ett virtuellt WAN-skyddat nav](../firewall-manager/secure-cloud-network.md)

* [Översikt över förhandsversionen av Azure Peering-tjänsten](https://docs.microsoft.com/azure/peering-service/about)
