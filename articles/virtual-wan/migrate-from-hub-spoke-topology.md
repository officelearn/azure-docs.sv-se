---
title: Migrera till Azure Virtual WAN
description: Lär dig mer om hur du migrerar till Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: c50f2ad0a15e9793fd5a799d44bf34fc2b48e14b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851272"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrera till Azure Virtual WAN
Med Azure Virtual WAN kan företag förenkla sin globala anslutning och dra nytta av Microsofts globala nätverks skala. Den här white paper ger teknisk information för företag som vill migrera från en befintlig kundhanterad hubb-och-eker-topologi till en design som använder Microsoft-hanterade virtuella WAN-hubbar.

Den [globala nätverks arkitekturen och den virtuella WAN](virtual-wan-global-transit-network-architecture.md) -artikeln visar de fördelar som Azure Virtual WAN gör det möjligt för företag att använda ett molnbaseradt, modernt Enterprise globalt nätverk.

![hubb och eker](./media/migrate-from-hub-spoke-topology/figure1.png)
**figur 1: Azure Virtual WAN**

Azure Virtual Data Center (VDC) Hub-och-eker-anslutningen har antagits av tusentals kunder för att utnyttja standard beteendet för transitivt beteende i Azure-nätverk för att bygga enkla och skalbara moln nätverk. Azure Virtual WAN bygger på dessa begrepp och introducerar nya funktioner som tillåter globala anslutnings topologier, inte bara mellan lokala platser och Azure, utan också att kunderna kan utnyttja skalan i Microsoft Network för att öka sin befintliga globala nätverk.

I den här artikeln beskrivs hur du migrerar en befintlig hybrid miljö till ett virtuellt WAN.

## <a name="scenario"></a>Scenario

Contoso är en global finansiell organisation med kontor i både Europa och Asien. De planerar att flytta sina befintliga program från ett lokalt Data Center i till Azure och har skapat en grund design baserat på VDC-arkitekturen, inklusive regionala virtuella nätverk med regionala Kundhanterade nav för Hybrid anslutning. Som en del av förflyttningen till molnbaserade tekniker har nätverks teamet varit inloggade med att säkerställa att deras anslutning är optimerad för att företaget ska kunna flytta framåt.

Bild 2 visar en övergripande vy över det befintliga globala nätverket, inklusive anslutning till flera Azure-regioner.

![contoso befintlig nätverkstopologi](./media/migrate-from-hub-spoke-topology/figure2.png)
**bild 2: contoso befintlig** nätverkstopologi

Följande punkter kan förstås från den befintliga nätverk sto pol Ogin:
 
- En nav-och-eker-topologi används i flera regioner, inklusive ExpressRoute Premium-kretsar för anslutning tillbaka till ett gemensamt privat WAN.
- Vissa av dessa platser har också VPN-tunnlar direkt i Azure för att uppnå program som finns i Microsoft-molnet.

## <a name="requirements"></a>Krav
Nätverks teamet har fått en uppgift om att leverera en global nätverks modell som har stöd för Contoso-migrering till molnet och måste optimera i områdena kostnad, skala och prestanda. I sammanfattning måste följande krav uppfyllas:
- Ange både Head-fjärdedel (HQ) och avdelnings kontor med optimerade vägar till program som körs i molnet. 
- Ta bort beroendet av befintliga lokala data Center (DC) för VPN-avslutning samtidigt som du behåller följande anslutnings vägar:
    - **Branch-till-VNet**: VPN-anslutna kontor måste kunna komma åt program som migrerats till molnet i den lokala Azure-regionen.
    - **Gren-till-hubb**-till-VNet: VPN-anslutna kontor måste kunna komma åt program som migrerats till molnet i den fjärranslutna Azure-regionen. 
    - **Gren-till-gren**: regionala VPN-anslutna kontor måste kunna kommunicera med varandra och EXPRESSROUTE anslutna HQ/DC-platser. 
    - **Gren-** till-nav-till-gren: globalt ÅTSKILDa VPN-anslutna kontor måste kunna kommunicera med varandra och alla EXPRESSROUTE anslutna HQ/DC-platser.
    - **Gren-till-Internet**: anslutna platser måste kunna kommunicera med Internet och den här trafiken måste filtreras och loggas.
    - **VNet-till-VNet**: ekrar virtuella nätverk i samma region måste kunna kommunicera med varandra.
    - **VNet-till**-hubb-till-VNet: ekrar virtuella nätverk i olika regioner måste kunna kommunicera med varandra.
- Ge användarna till gång till företags resurser på företags nätverket, till exempel för Contoso-centrala användare (bärbar dator och telefon).

## <a name="azure-virtual-wan-architecture"></a>Azure Virtual WAN-arkitektur

Bild 3 visar en övergripande vy av den uppdaterade måldomänkontrollanten med Azure Virtual WAN för att uppfylla kraven som beskrivs i föregående avsnitt.

![Contosos virtuella WAN-arkitektur](./media/migrate-from-hub-spoke-topology/figure3.png)
**bild 3: Azure Virtual WAN-arkitektur**

Sammanfattning: 
- HQ i Europa förblir ExpressRoute ansluten, den lokala Europa-DOMÄNKONTROLLANTen migreras fullständigt till Azure och tas nu ur bruk.
- Asien och HQ förblir anslutna till privat WAN. Azure Virtual WAN används nu för att utöka det lokala operatörs nätverket och tillhandahålla global anslutning 
- Azure virtuella WAN-hubbar som distribueras i både Västeuropa och Asien, sydöstra Azure-regioner för att tillhandahålla anslutnings nav för ExpressRoute-och VPN-anslutna enheter. 
- Hubbar ger också VPN-avslutning för centrala användare över flera klient typer som använder OpenVPN-anslutning till det globala nät nätverket, vilket ger åtkomst till inte bara program som migrerats till Azure, utan även eventuella resurser som återstår lokalt. 
- Internet anslutning för resurser i ett virtuellt nätverk som tillhandahålls av Azure Virtual WAN. Internet anslutning för fjärranslutna platser tillhandahålls även av Azure Virtual WAN. Lokala Internet-grupp stöds via partner integration för optimerad åtkomst till SaaS-tjänster som Office 365.

## <a name="migrate-to-azure-virtual-wan"></a>Migrera till Azure Virtual WAN

I det här avsnittet beskrivs de olika stegen för att migrera till Azure Virtual WAN.
 
### <a name="vdc-hub-and-spoke-single-region"></a>VDC Hub-och-eker-enkel region

Följande bild visar en topologi för en enda region för Contoso innan du inför distributionen av Azure Virtual WAN.

![Distribuera virtuella WAN-hubbar](./media/migrate-from-hub-spoke-topology/figure4.png)

 **Bild 4: VDC Hub-och-eker-enkel region – steg 1**

I linje med VDC-metoden (Virtual Data Center) innehåller det virtuella kund hanterade Hubbs nätverket flera funktions block:
- Delade tjänster (alla vanliga funktioner som krävs av flera ekrar) ett exempel på vilka Contoso använder är Windows Server-domänkontrollanter på virtuella IaaS-datorer (Infrastructure-as-a-Service).
- IP/routing Firewall-tjänster tillhandahålls av en virtuell nätverks installation från tredje part, vilket aktiverar eker-till-ekrar Layer-3 IP-routning. 
- Ingångs-och utgångs tjänster för Internet, inklusive Azure Application Gateway för inkommande HTTPS-begäranden och tredjeparts Proxy-tjänster som körs på virtuella datorer för filtrerad utgående åtkomst till Internet resurser.
- ExpressRoute och VPN Virtual Network Gateway för anslutning till lokala nätverk.

### <a name="deploy-virtual-wan-hubs"></a>Distribuera virtuella WAN-hubbar

Det första steget för att distribuera en virtuell WAN-hubb i varje region. Konfigurera den virtuella WAN-hubben med VPN Gateway and Express Route Gateway enligt beskrivningen i följande artiklar:  
- [Självstudie: skapa en plats-till-plats-anslutning med Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Självstudie: skapa en ExpressRoute-Association med Azure Virtual WAN](virtual-wan-expressroute-portal.md) 

> [!NOTE]
> Azure Virtual WAN måste använda standard-SKU: n för att aktivera vissa trafik Sök vägar som beskrivs i den här artikeln.


![distribuera virtuella WAN-hubbar](./media/migrate-from-hub-spoke-topology/figure5.png)
**bild 5: VDC hubb-och-eker till virtuell WAN-migrering – steg 2**

### <a name="connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Ansluta fjärranslutna platser (ExpressRoute och VPN) till virtuellt WAN

Nu ansluter vi den virtuella WAN-hubben till de befintliga ExpressRoute-kretsarna och konfigurerar VPN för plats-till-plats via Internet till alla fjärranslutna grenar.

> [!NOTE]
> ExpressRoute-kretsar måste uppgraderas till Premium SKU-typ för att kunna ansluta till den virtuella WAN-hubben.


![ansluta fjärranslutna platser till det virtuella WAN-](./media/migrate-from-hub-spoke-topology/figure6.png)
**bild 6: VDC Hub-och-eker till virtuell WAN-migrering – steg 3**

Vid den här lokala nätverks utrustningen börjar ta emot vägar som motsvarar IP-adressutrymmet som tilldelats det virtuella WAN-hanterade hubb-nätverket. Fjärr-VPN-anslutna grenar i det här skedet kommer att se två sökvägar till alla befintliga program i ekrarnas virtuella nätverk. De här enheterna bör konfigureras att fortsätta använda tunneln till VDC-hubben för att säkerställa symmetrisk routning under över gångs fasen.

### <a name="test-hybrid-connectivity-via-virtual-wan"></a>Testa hybrid anslutning via virtuellt WAN

Innan du använder den hanterade virtuella WAN-hubben för produktions anslutningar rekommenderar vi att du konfigurerar ett virtuellt nätverk för testning av ekrar och virtuell WAN-anslutning. Verifiera att anslutningar till den här test miljön fungerar via ExpressRoute och plats till plats-VPN innan du fortsätter med nästa steg.

![hybrid anslutning via Virtual WAN](./media/migrate-from-hub-spoke-topology/figure7.png)
**bild 7: VDC Hub-och-eker till virtuell WAN-migrering – steg 4**

### <a name="transition-connectivity-to-virtual-wan-hub"></a>Över gångs anslutning till virtuell WAN-hubb


![över gångs anslutning till den virtuella WAN-hubben](./media/migrate-from-hub-spoke-topology/figure8.png)
**figur 8: VDC hubb-och-eker till virtuell WAN-migrering – steg 5**

**a**. Ta bort befintliga peering-anslutningar från ekrar virtuella nätverk till den gamla VDC-hubben. Åtkomst till program i ekrar virtuella nätverk är inte tillgänglig förrän steg a-c har slutförts.

**b**. Anslut de eker-virtuella nätverken till den virtuella WAN-hubben via VNet-anslutningar.

**c**. Ta bort alla användardefinierade vägar (UDR) som tidigare använts i ekrar för virtuella nätverk för eker-till-ekrar-kommunikation. Den här sökvägen är nu aktive rad genom dynamisk routning tillgänglig i den virtuella WAN-hubben.

**d**. Befintliga ExpressRoute-och VPN-gatewayer i VDC-hubben har nu inaktiverats för att tillåta nästa steg (e).

**e**. Anslut den gamla VDC-hubben (hubb virtuellt nätverk) till den virtuella WAN-hubben via en ny VNet-anslutning.

### <a name="old-hub-becomes-shared-services-spoke"></a>Den gamla hubben blir delade tjänster eker

Nu har vi gjort om vårt Azure-nätverk för att göra den virtuella WAN-hubben till den centrala punkten i vår nya topologi.

![gamla hubben blir delade tjänster ekrar](./media/migrate-from-hub-spoke-topology/figure9.png)
**bild 9: VDC hubb-och-eker till virtuell WAN-migrering – steg 6**

Eftersom den virtuella WAN-hubben är en hanterad entitet och inte tillåter distribution av anpassade resurser, t. ex. virtuella datorer, finns blocket Shared Services nu som ett eker-virtuellt nätverk, som fungerar som Internet-inkommande trafik via Azure Application Gateway eller nätverk virtualiserad apparat. Trafik mellan de delade tjänsterna och de virtuella datorerna i drifts miljön överför nu den virtuella WAN-hanterade hubben.

### <a name="optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Optimera lokal anslutning för att utnyttja virtuellt WAN

I det här skedet har contoso främst slutfört sina migreringar av affärs program i Microsoft Cloud, med bara några få äldre program kvar i den lokala DOMÄNKONTROLLANTen.

![optimera lokal anslutning för att utnyttja virtuella WAN-](./media/migrate-from-hub-spoke-topology/figure10.png)
**Bild 10: VDC Hub-och-eker till virtuell WAN-migrering – steg 7**

 För att kunna utnyttja alla funktioner i Azure Virtual WAN väljer contoso att inaktivera sina äldre lokala VPN-anslutningar. Alla grenar som fortsätter att komma åt HQ eller DC-nätverk kan överföra Microsofts globala nätverk med hjälp av den inbyggda överförings dirigeringen för Azure Virtual WAN. 

> [!NOTE]
> ExpressRoute Global Reach är ett alternativt alternativ för kunder som vill utnyttja Microsofts stamnät för att komplettera sina befintliga privata WAN-enheter.

## <a name="end-state-architecture-and-traffic-paths"></a>Slut tillstånds arkitektur och trafik Sök vägar


![slut tillstånds arkitektur och trafik Sök vägar](./media/migrate-from-hub-spoke-topology/figure11.png)
**Bild 11: Dual regions virtuella WAN-nätverk**

Det här avsnittet innehåller en översikt över hur den här topologin uppfyller de ursprungliga kraven genom att titta på några exempel på trafikflöden.

### <a name="path-1"></a>Sökväg 1

Sökväg 1 beskriver trafikflöde från en S2S VPN-ansluten gren i Asien till ett Azure VNet i den Asien, sydöstra regionen.

Trafiken dirigeras enligt följande:
- Asiens gren är ansluten via elastiska S2S BGP-aktiverade tunnlar till Asien, sydöstra virtuell WAN-hubb.
- Virtuellt WAN-nav i Asien dirigerar trafik lokalt till anslutna VNet.

![Flöde 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Sökväg 2
Sökväg 2 beskriver trafikflöde från ExpressRoute anslutna europeiska HQ till ett Azure VNet i Asien, sydöstra regionen.

Trafiken dirigeras enligt följande:
- Europa HQ är ansluten via Premium ExpressRoute-kretsar till den virtuella WAN-hubben i västra Europa.
- Global WAN Hub-till-hubb global anslutning möjliggör trafik överföring till VNet som är anslutet i fjärrregionen.

![Flöde 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Sökväg 3
Path 3 beskriver trafikflödet från den lokala Asien som är ansluten till privat WAN till en europeisk S2S-ansluten gren.

Trafiken dirigeras enligt följande:
- Asien DC är anslutet till lokalt privat WAN-bärvåg.
- ExpressRoute-kretsen slutar lokalt i privata WAN-anslutningar till den Asien, sydöstra virtuella WAN-hubben.
- Virtuell WAN Hub-till-hubb global anslutning möjliggör trafik överföring.

![Flöde 3](./media/migrate-from-hub-spoke-topology/flow3.png)


### <a name="path-4"></a>Sökväg 4
Path 4 beskriver trafikflöde från ett Azure VNet i Asien, sydöstra region till ett Azure VNet i regionen Europa, västra.

Trafiken dirigeras enligt följande:
- Global WAN Hub-till-hubb global anslutning möjliggör inbyggd transitering av alla anslutna Azure-virtuella nätverk utan ytterligare användar konfiguration.

![Flöde 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Sökväg 5
Path 5 beskriver trafikflöde från centrala VPN-användare (P2S) till ett Azure VNet i regionen Europa, västra.

Trafiken dirigeras enligt följande:
- Bärbara datorer och mobila enheter använder OpenVPN-klienten för transparent anslutning i P2S VPN-gatewayen i Västeuropa.
- Den virtuella WAN-hubben i Västeuropa dirigerar trafik lokalt till anslutna VNet.

![Flöde 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Säkerhet och princip kontroll via Azure-brandväggen

Contoso har nu verifierat anslutningen mellan alla grenar och virtuella nätverk i enlighet med kraven som beskrivs tidigare i det här dokumentet. För att uppfylla kraven för säkerhets kontroll och nätverks isolering måste de fortsätta att separera och logga trafik via hubb nätverket, tidigare funktionen utfördes av en virtuell nätverks installation (NVA). Contoso vill också inaktivera sina befintliga proxy Services och använda interna Azure-tjänster för utgående Internet filtrering. 

![säkerhets-och princip kontroll via Azure Firewall](./media/migrate-from-hub-spoke-topology/figure12.png)
**figur 12: Azure-brandvägg i virtuellt WAN-nätverk (säker virtuell hubb)**

Följande avancerade steg krävs för att presentera Azure-brandväggen i de virtuella WAN-hubbarna för att aktivera en enhetlig punkt för princip kontroll. Den här processen och begreppet säkra virtuella hubbar beskrivs utförligt [här](https://go.microsoft.com/fwlink/?linkid=2107683).
- Skapa Azure Firewall-princip.
- Länka brand Väggs princip till Azure Virtual WAN Hub.
- Steget ovan tillåter att den befintliga virtuella WAN-hubben fungerar som en säker virtuell hubb och distribuerar de nödvändiga Azure Firewall-resurserna.

> [!NOTE]
> Om Azure-brandväggen distribueras i en standard virtuell WAN-hubb (SKU: standard): V2V, B2V, V2I och B2Is VB-principer tillämpas endast på trafiken från virtuella nätverk och grenarna som är anslutna till det angivna hubben där Azure-VB har distribuerats (säker hubb). Trafik från fjärr-virtuella nätverk och förgreningar som är kopplade till andra virtuella WAN-hubbar i samma virtuella WAN-nätverk är inte "brand Väggs", även om dessa fjärranslutna grenar och VNET är sammankopplade via virtuell WAN-hubb till nav länkar. Stöd för brand Väggs stöd för interhubb finns i Översikt över Azure Virtual WAN och Firewall Manager.

Följande sökvägar beskriver de anslutnings Sök vägar som Aktiver ATS genom att använda Azures säkra virtuella hubbar.

### <a name="path-6"></a>Sökväg 6
Path 6 beskriver säkert trafikflöde mellan virtuella nätverk inom samma region.

Trafiken dirigeras enligt följande:
- Virtuella nätverk som är anslutna till samma säkra virtuella hubb dirigerar nu trafik till via Azure-brandväggen.
- Azure-brandväggen kan använda principer för dessa flöden.

![Flöde 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Sökväg 7
Path 7 beskriver trafikflöde från ett Azure VNet till Internet eller från en säkerhets tjänst från tredje part.

Trafiken dirigeras enligt följande:
- Virtuella nätverk som är anslutna till den säkra virtuella hubben kan skicka trafik till offentliga, destinationer på Internet, med hjälp av säker hubb som en central punkt för Internet åtkomst.
- Trafiken kan filtreras lokalt med hjälp av Azure Firewall FQDN-regler eller skickas till en säkerhets tjänst från tredje part för inspektion.

![Flow 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Sökväg 8
Path 8 beskriver trafikflöde från filial till Internet eller säkerhets tjänst från tredje part.

Trafiken dirigeras enligt följande:
- Grenar som är anslutna till den säkra virtuella hubben kan skicka trafik till offentliga destinationer på Internet, med hjälp av säker hubb som en central punkt för Internet åtkomst.
- Trafiken kan filtreras lokalt med hjälp av Azure Firewall FQDN-regler eller skickas till en säkerhets tjänst från tredje part för inspektion.

![Flöde 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure Virtual WAN](virtual-wan-about.md)
