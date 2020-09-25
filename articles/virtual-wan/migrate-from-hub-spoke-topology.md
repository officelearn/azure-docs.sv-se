---
title: 'Arkitektur: migrera till Azure Virtual WAN'
description: Lär dig hur du migrerar till Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 4c39a24664d36e4a69a44b7fe06835572e0cfe75
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329252"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrera till Azure Virtual WAN

Med Azure Virtual WAN kan företag förenkla sin globala anslutning för att dra nytta av Microsofts globala nätverks skala. Den här artikeln innehåller teknisk information för företag som vill migrera från en befintlig kundhanterad nav-och-eker-topologi till en design som använder Microsoft-hanterade virtuella WAN-hubbar.

För information om de fördelar som Azure Virtual WAN möjliggör för företag som använder ett molnbaseradt globalt Enterprise-nätverk, se [Global överföring nätverks arkitektur och virtuellt WAN](virtual-wan-global-transit-network-architecture.md).

![hubb och eker- ](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
 **bild: Azure Virtual WAN**

Azure Hub-och-eker-nätverksanslutningen har antagits av tusentals kunder för att utnyttja standard beteendet för transitivt beteende i Azure-nätverk för att bygga enkla och skalbara moln nätverk. Azure Virtual WAN bygger på dessa begrepp och introducerar nya funktioner som tillåter globala anslutnings topologier, inte bara mellan lokala platser och Azure, utan också att kunderna kan utnyttja skalan i Microsoft Network för att utöka sina befintliga globala nätverk.

Den här artikeln visar hur du migrerar en befintlig hybrid miljö till ett virtuellt WAN-nätverk.

## <a name="scenario"></a>Scenario

Contoso är en global finansiell organisation med kontor i både Europa och Asien. De planerar att flytta sina befintliga program från ett lokalt Data Center i till Azure och har skapat en grund design baserat på den manuella nav-och-eker-arkitekturen, inklusive regionala virtuella nätverk med regionala Kundhanterade nav för Hybrid anslutning. Som en del av förflyttningen till molnbaserade tekniker har nätverks teamet varit inloggade med att se till att deras anslutning är optimerad för att företaget ska flytta framåt.

Följande bild visar en övergripande vy över det befintliga globala nätverket, inklusive anslutning till flera Azure-regioner.

![Contosos befintliga nätverkstopologi ](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
 **: contoso befintlig** nätverkstopologi

Följande punkter kan förstås från den befintliga nätverk sto pol Ogin:

- En nav-och-eker-topologi används i flera regioner, inklusive ExpressRoute Premium-kretsar för anslutning tillbaka till ett gemensamt privat WAN.

- Vissa av dessa platser har också VPN-tunnlar direkt i Azure för att uppnå program som finns i Microsoft-molnet.

## <a name="requirements"></a>Krav

Nätverks teamet har fått en uppgift om att leverera en global nätverks modell som har stöd för Contoso-migrering till molnet och måste optimera i områdena kostnad, skala och prestanda. I sammanfattning måste följande krav uppfyllas:

- Ange både Head-fjärdedel (HQ) och avdelnings kontor med optimerade vägar till program som körs i molnet.
- Ta bort beroendet av befintliga lokala data Center (DC) för VPN-avslutning och Behåll följande anslutnings vägar:
  - **Branch-till-VNet**: VPN-anslutna kontor måste kunna komma åt program som migrerats till molnet i den lokala Azure-regionen.
  - **Gren-till-hubb**-till-VNet: VPN-anslutna kontor måste kunna komma åt program som migrerats till molnet i den fjärranslutna Azure-regionen.
  - **Gren-till-gren**: regionala VPN-anslutna kontor måste kunna kommunicera med varandra och EXPRESSROUTE anslutna HQ/DC-platser.
  - **Gren-** till-nav-till-gren: globalt ÅTSKILDa VPN-anslutna kontor måste kunna kommunicera med varandra och alla EXPRESSROUTE anslutna HQ/DC-platser.
  - **Gren-till-Internet**: anslutna platser måste kunna kommunicera med Internet. Den här trafiken måste filtreras och loggas.
  - **VNet-till-VNet**: ekrar virtuella nätverk i samma region måste kunna kommunicera med varandra.
  - **VNet-till**-hubb-till-VNet: ekrar virtuella nätverk i olika regioner måste kunna kommunicera med varandra.
- Ange möjligheten för Contoso-centrala användare (bärbar dator och telefon) för att få åtkomst till företags resurser när de inte finns i företags nätverket.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure Virtual WAN-arkitektur

Följande bild visar en övergripande vy av den uppdaterade måldomänkontrollanten med Azure Virtual WAN för att uppfylla kraven som beskrivs i föregående avsnitt.

![Contoso Virtual WAN-arkitektur ](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
 **bild: Azure Virtual WAN-arkitektur**

Sammanfattning:

- HQ i Europa förblir ExpressRoute ansluten, den lokala Europa-DOMÄNKONTROLLANTen migreras fullständigt till Azure och tas nu ur bruk.
- Asien och HQ förblir anslutna till privat WAN. Azure Virtual WAN används nu för att utöka det lokala bärvåg nätverket och tillhandahålla global anslutning.
- Azure virtuella WAN-hubbar som distribueras i både Västeuropa och Asien, sydöstra Azure-regioner för att tillhandahålla anslutnings nav för ExpressRoute-och VPN-anslutna enheter.
- Hubbar ger också VPN-avslutning för centrala användare över flera klient typer som använder OpenVPN-anslutning till det globala nät nätverket, vilket ger åtkomst till inte bara program som migrerats till Azure, utan även eventuella resurser som återstår lokalt.
- Internet anslutning för resurser i ett virtuellt nätverk som tillhandahålls av Azure Virtual WAN.

Internet anslutning för fjärranslutna platser tillhandahålls även av Azure Virtual WAN. Lokala Internet-grupp stöds via partner integration för optimerad åtkomst till SaaS-tjänster som Office 365.

## <a name="migrate-to-virtual-wan"></a>Migrera till Virtual WAN

Det här avsnittet visar de olika stegen för att migrera till Azure Virtual WAN.

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>Steg 1: en kundhanterad hubb-och-eker-region

Följande bild visar en topologi för en enda region för Contoso innan du inför distributionen av Azure Virtual WAN:

![Topologi för en enda region ](./media/migrate-from-hub-spoke-topology/figure1.png)
 **bild 1: enkel region manuell hubb-och-eker**

I enlighet med hubb-och eker-metoden innehåller det virtuella kund hanterade Hubbs nätverket flera funktions block:

- Delade tjänster (alla vanliga funktioner som krävs av flera ekrar). Exempel: Contoso använder Windows Server-domänkontrollanter på IaaS-datorer (Infrastructure-as-a-Service).
- IP/routing Firewall-tjänster tillhandahålls av en virtuell nätverks installation från tredje part, vilket aktiverar eker-till-ekrar Layer-3 IP-routning.
- Ingångs-och utgångs tjänster för Internet, inklusive Azure Application Gateway för inkommande HTTPS-begäranden och tredjeparts Proxy-tjänster som körs på virtuella datorer för filtrerad utgående åtkomst till Internet resurser.
- ExpressRoute och VPN-gateway för anslutning till lokala nätverk.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Steg 2: distribuera virtuella WAN-hubbar

Distribuera en virtuell WAN-hubb i varje region. Konfigurera den virtuella WAN-hubben med VPN Gateway-och ExpressRoute-Gateway enligt beskrivningen i följande artiklar:

- [Självstudie: Skapa en plats-till-plats-anslutning med Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Självstudie: skapa en ExpressRoute-Association med Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Azure Virtual WAN måste använda standard-SKU: n för att aktivera vissa trafik Sök vägar som visas i den här artikeln.

![Distribuera virtuella WAN Hub ](./media/migrate-from-hub-spoke-topology/figure2.png)
 **-bild 2: Kundhanterade nav-och-eker till virtuell WAN-migrering**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Steg 3: ansluta fjärranslutna platser (ExpressRoute och VPN) till virtuellt WAN

Anslut den virtuella WAN-hubben till de befintliga ExpressRoute-kretsarna och Ställ in plats-till-plats-VPN via Internet till alla fjärranslutna grenar.

> [!NOTE]
> ExpressRoute-kretsar måste uppgraderas till Premium SKU-typ för att kunna ansluta till den virtuella WAN-hubben.

![Ansluta fjärrplatser till virtuellt WAN ](./media/migrate-from-hub-spoke-topology/figure3.png)
 **-bild 3: Kundhanterade nav-och-eker till virtuell WAN-migrering**

I det här läget börjar den lokala nätverks utrustningen ta emot vägar som motsvarar IP-adressutrymmet som tilldelats det virtuella WAN-hanterade hubb-nätverket. Fjärr-VPN-anslutna grenar i det här skedet kommer att se två sökvägar till alla befintliga program i ekrarnas virtuella nätverk. De här enheterna bör konfigureras att fortsätta använda tunneln till den Kundhanterade hubben för att säkerställa symmetrisk routning under över gångs fasen.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Steg 4: testa hybrid anslutning via virtuellt WAN

Innan du använder den hanterade virtuella WAN-hubben för produktions anslutning, rekommenderar vi att du skapar ett virtuellt nätverk för testning av ekrar och virtuell WAN-anslutning. Verifiera att anslutningar till den här test miljön fungerar via ExpressRoute och plats till plats-VPN innan du fortsätter med nästa steg.

![Testa hybrid anslutning via virtuellt WAN- ](./media/migrate-from-hub-spoke-topology/figure4.png)
 **bild 4: Kundhanterade nav-och-eker till virtuell WAN-migrering**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Steg 5: över gångs anslutning till Virtual WAN Hub

![Över gångs anslutning till virtuellt WAN Hub ](./media/migrate-from-hub-spoke-topology/figure5.png)
 **-bild 5: kundhanterad hubb-och-eker till virtuell WAN-migrering**

**a**. Ta bort befintliga peering-anslutningar från ekrar virtuella nätverk till den gamla kund hanterade hubben. Åtkomst till program i ekrar virtuella nätverk är inte tillgänglig förrän steg a-c har slutförts.

**b**. Anslut de eker-virtuella nätverken till den virtuella WAN-hubben via VNet-anslutningar.

**c**. Ta bort alla användardefinierade vägar (UDR) som tidigare använts i ekrar för virtuella nätverk för eker-till-ekrar-kommunikation. Den här sökvägen är nu aktive rad genom dynamisk routning tillgänglig i den virtuella WAN-hubben.

**d**. Befintliga ExpressRoute-och VPN-gatewayer i det Kundhanterade navet har nu inaktiverats för att tillåta nästa steg (e).

**e**. Anslut den gamla kund hanterade hubben (hubb virtuellt nätverk) till den virtuella WAN-hubben via en ny VNet-anslutning.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Steg 6: den gamla hubben blir delade tjänster eker

Nu har vi gjort om vårt Azure-nätverk för att göra den virtuella WAN-hubben till den centrala punkten i vår nya topologi.

![Den gamla hubben blir delade tjänster eker ](./media/migrate-from-hub-spoke-topology/figure6.png)
 **figur 6: kundhanterad hubb-och-eker till virtuell WAN-migrering**

Eftersom den virtuella WAN-hubben är en hanterad entitet och inte tillåter distribution av anpassade resurser, t. ex. virtuella datorer, finns inte blocket Shared Services som ett eker-virtuellt nätverk och fungerar som Internet-inkommande trafik via Azure Application Gateway eller virtualiserad nätverks installation. Trafik mellan de delade tjänsterna och de virtuella datorerna i drifts miljön överför nu den virtuella WAN-hanterade hubben.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Steg 7: optimera lokal anslutning för att utnyttja virtuellt WAN

I det här skedet har contoso främst slutfört sina migreringar av affärs program i Microsoft Cloud, med bara några få äldre program kvar i den lokala DOMÄNKONTROLLANTen.

![Optimera lokal anslutning för att fullt ut använda virtuella WAN ](./media/migrate-from-hub-spoke-topology/figure7.png)
 **-bild 7: Kundhanterade nav-och-eker till virtuell WAN-migrering**

För att kunna utnyttja alla funktioner i Azure Virtual WAN väljer contoso att inaktivera sina äldre lokala VPN-anslutningar. Alla grenar som fortsätter att komma åt HQ eller DC-nätverk kan överföra Microsofts globala nätverk med hjälp av den inbyggda överförings dirigeringen för Azure Virtual WAN.

> [!NOTE]
> ExpressRoute Global Reach är ett alternativt alternativ för kunder som vill utnyttja Microsofts stamnät för att komplettera sina befintliga privata WAN-enheter.

## <a name="end-state-architecture-and-traffic-paths"></a>Slut tillstånds arkitektur och trafik Sök vägar

![Slut punkts arkitektur och trafik Sök vägar ](./media/migrate-from-hub-spoke-topology/figure8.png)
 **figur: dubbelt region virtuellt WAN**

Det här avsnittet innehåller en översikt över hur den här topologin uppfyller de ursprungliga kraven genom att titta på några exempel på trafikflöden.

### <a name="path-1"></a>Sökväg 1

Sökväg 1 visar trafikflöde från en S2S VPN-ansluten gren i Asien till ett Azure VNet i den Asien, sydöstra regionen.

Trafiken dirigeras enligt följande:

- Asiens gren är ansluten via elastiska S2S BGP-aktiverade tunnlar till Asien, sydöstra virtuell WAN-hubb.

- Virtuellt WAN-nav i Asien dirigerar trafik lokalt till anslutna VNet.

![Flöde 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Sökväg 2

Sökväg 2 visar trafikflöde från ExpressRoute anslutna europeiska HQ till ett Azure VNet i den Asien, sydöstra regionen.

Trafiken dirigeras enligt följande:

- Europa HQ är ansluten via Premium ExpressRoute-kretsar till den virtuella WAN-hubben i västra Europa.

- Global WAN Hub-till-hubb global anslutning möjliggör trafik överföring till VNet som är anslutet i fjärrregionen.

![Flöde 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Sökväg 3

Path 3 visar trafikflödet från den lokala Asien som är ansluten till privat WAN till en europeisk S2S-ansluten gren.

Trafiken dirigeras enligt följande:

- Asien DC är anslutet till lokalt privat WAN-bärvåg.

- ExpressRoute-kretsen slutar lokalt i privata WAN-anslutningar till den Asien, sydöstra virtuella WAN-hubben.

- Virtuell WAN Hub-till-hubb global anslutning möjliggör trafik överföring.

![Flöde 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Sökväg 4

Path 4 visar trafikflödet från ett Azure VNet i Asien, sydöstra region till ett Azure VNet i regionen Europa, västra.

Trafiken dirigeras enligt följande:

- Global WAN Hub-till-hubb global anslutning möjliggör inbyggd transitering av alla anslutna Azure-virtuella nätverk utan ytterligare användar konfiguration.

![Flöde 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Sökväg 5

Path 5 visar trafikflödet från centrala VPN-användare (P2S) till ett Azure VNet i regionen Europa, västra.

Trafiken dirigeras enligt följande:

- Användare av bärbara och mobila enheter använder OpenVPN-klienten för transparent anslutning i P2S VPN-gatewayen i Västeuropa.

- Den virtuella WAN-hubben i Västeuropa dirigerar trafik lokalt till anslutna VNet.

![Flöde 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Säkerhet och princip kontroll via Azure-brandväggen

Contoso har nu verifierat anslutningen mellan alla grenar och virtuella nätverk i enlighet med kraven som beskrivs ovan i den här artikeln. För att uppfylla kraven för säkerhets kontroll och nätverks isolering måste de fortsätta att separera och logga trafik via hubb nätverket. Tidigare utfördes den här funktionen av en virtuell nätverks installation (NVA). Contoso vill också inaktivera sina befintliga proxy Services och använda interna Azure-tjänster för utgående Internet filtrering.

![Säkerhet och princip kontroll via Azure Firewall- ](./media/migrate-from-hub-spoke-topology/security-policy.png)
 **bild: Azure-brandvägg i virtuellt WAN-nätverk (säker virtuell hubb)**

Följande avancerade steg krävs för att presentera Azure-brandväggen i de virtuella WAN-hubbarna för att aktivera en enhetlig punkt för princip kontroll. Mer information om den här processen och begreppet säkra virtuella hubbar finns i [Azure Firewall Manager](../firewall-manager/index.yml).

1. Skapa Azure Firewall-princip.
2. Länka brand Väggs princip till Azure Virtual WAN Hub. Det här steget gör att den befintliga virtuella WAN-hubben fungerar som en säker virtuell hubb och distribuerar de nödvändiga Azure Firewall-resurserna.

> [!NOTE]
> Om Azure-brandväggen distribueras i en standard virtuell WAN-hubb (SKU: standard): V2V, B2V, V2I och B2Is VB-principer tillämpas endast på trafiken från virtuella nätverk och grenarna som är anslutna till det angivna hubben där Azure-VB har distribuerats (säker hubb). Trafik från fjärr-virtuella nätverk och förgreningar som är kopplade till andra virtuella WAN-hubbar i samma virtuella WAN-nätverk är inte "brand Väggs", även om fjärranslutna grenar och VNet är sammankopplade via virtuell WAN-hubb till nav länkar. Stöd för brand väggar för brand väggar finns i Översikt över Azure Virtual WAN och Firewall Manager.

Följande sökvägar visar de anslutnings Sök vägar som Aktiver ATS med Azures säkra virtuella hubbar:

### <a name="path-6"></a>Sökväg 6

Path 6 visar ett säkert trafikflöde mellan virtuella nätverk inom samma region.

Trafiken dirigeras enligt följande:

- Virtuella nätverk som är anslutna till samma säkra virtuella hubb dirigerar nu trafik till via Azure-brandväggen.

- Azure-brandväggen kan använda principer för dessa flöden.

![Flöde 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Sökväg 7

Path 7 visar trafikflödet från ett virtuellt Azure-nätverk till Internet eller från en säkerhets tjänst från tredje part.

Trafiken dirigeras enligt följande:

- Virtuella nätverk som är anslutna till den säkra virtuella hubben kan skicka trafik till offentliga, destinationer på Internet, med hjälp av säker hubb som en central punkt för Internet åtkomst.

- Trafiken kan filtreras lokalt med hjälp av Azure Firewall FQDN-regler eller skickas till en säkerhets tjänst från tredje part för inspektion.

![Flow 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Sökväg 8

Path 8 visar trafikflödet från gren till Internet eller säkerhets tjänst från tredje part.

Trafiken dirigeras enligt följande:

- Grenar som är anslutna till den säkra virtuella hubben kan skicka trafik till offentliga destinationer på Internet med hjälp av säker hubb som en central punkt för Internet åtkomst.

- Trafiken kan filtreras lokalt med hjälp av Azure Firewall FQDN-regler eller skickas till en säkerhets tjänst från tredje part för inspektion.

![Flöde 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Virtual WAN](virtual-wan-about.md)
