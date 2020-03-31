---
title: 'Arkitektur: Migrera till Virtuellt WAN i Azure'
description: Lär dig hur du migrerar till Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8aa4fe143c78d2053ce8c48e4866a5522057aa0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063029"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrera till Virtuellt WAN i Azure

Med Azure Virtual WAN kan företag förenkla sin globala anslutning för att dra nytta av omfattningen av Microsofts globala nätverk. Den här artikeln innehåller teknisk information för företag som vill migrera från en befintlig kundhanterad hub-and-spoke-topologi till en design som utnyttjar Microsoft-hanterade virtuella WAN-hubbar.

Information om fördelarna med Azure Virtual WAN gör det möjligt för företag som använder ett molncentrerat modernt globalt företagsnätverk finns i [Global transitnätverksarkitektur och Virtuellt WAN](virtual-wan-global-transit-network-architecture.md).

![hubb](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
och eker**Figur: Azure Virtual WAN**

Vdc-hub-and-spoke-anslutningsmodellen (Azure Virtual Datacenter) har antagits av tusentals av våra kunder för att utnyttja standardtransitiva routningsbeteendet för Azure Networking för att skapa enkla och skalbara molnnätverk. Azure Virtual WAN bygger vidare på dessa begrepp och introducerar nya funktioner som möjliggör globala anslutningstopologier, inte bara mellan lokala platser och Azure, utan också gör det möjligt för kunderna att utnyttja omfattningen av Microsoft-nätverket för att öka sina befintliga globala nätverk.

Den här artikeln visar hur du migrerar en befintlig hybridmiljö till VirtuellT WAN.

## <a name="scenario"></a>Scenario

Contoso är en global finansiell organisation med kontor i både Europa och Asien. De planerar att flytta sina befintliga program från ett lokalt datacenter till Azure och har byggt ut en grunddesign baserad på VDC-arkitekturen, inklusive regionala kundhanterade virtuella hubbnätverk för hybridanslutning. Som en del av övergången till molnbaserad teknik har nätverksteamet fått i uppdrag att se till att deras anslutning är optimerad för att verksamheten ska gå vidare.

Följande bild visar en vy på hög nivå av det befintliga globala nätverket, inklusive anslutning till flera Azure-regioner.

![Contoso befintlig nätverkstopologi](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
**Bild: Contoso befintlig nätverkstopologi**

Följande punkter kan förstås från den befintliga nätverkstopologin:

- En hub-and-spoke topologi används i flera regioner, inklusive ExpressRoute Premium-kretsar för anslutning tillbaka till ett gemensamt privat WAN.

- Vissa av dessa platser har också VPN-tunnlar direkt till Azure för att nå program som finns i Microsoft-molnet.

## <a name="requirements"></a>Krav

Nätverksteamet har fått i uppdrag att leverera en global nätverksmodell som kan stödja Contoso-migreringen till molnet och måste optimera inom områdena kostnad, skala och prestanda. Sammanfattningsvis skall följande krav vara uppfyllda:

- Förse både huvudkontor (HQ) och filialkontor med optimerad väg till molnbaserade program.
- Ta bort beroendet av befintliga lokala datacenter (DC) för VPN-avslutning samtidigt som du behåller följande anslutningsvägar:
  - **Branch -till- VNet**: VPN-anslutna kontor måste kunna komma åt program som migrerats till molnet i den lokala Azure-regionen.
  - **Branch -till- Hub -to- Hub -till- VNet**: VPN-anslutna kontor måste kunna komma åt program som migrerats till molnet i fjärr-Azure-regionen.
  - **Filial -till-gren:** Regionala VPN-anslutna kontor måste kunna kommunicera med varandra och ExpressRoute anslutna HQ / DC-platser.
  - **Branch -to- Hub -to- Hub -to-branch**: Globalt separerade VPN-anslutna kontor måste kunna kommunicera med varandra och alla ExpressRoute-anslutna HQ/DC-platser.
  - **Branch -to-Internet**: Anslutna webbplatser måste kunna kommunicera med Internet. Den här trafiken måste filtreras och loggas.
  - **VNet -till- VNet**: Ekriga virtuella nätverk i samma region måste kunna kommunicera med varandra.
  - **VNet -till- Hub -to- Hub -to- VNet**: Eker virtuella nätverk i de olika regionerna måste kunna kommunicera med varandra.
- Ge Contoso-roaminganvändare (bärbar dator och telefon) möjlighet att komma åt företagets resurser utan att i företagsnätverket.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure Virtual WAN-arkitektur

Följande bild visar en vy på hög nivå av den uppdaterade måltopologin med Azure Virtual WAN för att uppfylla kraven i föregående avsnitt.

![Contoso virtuell](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
WAN-arkitektur**Figur: Azure Virtual WAN-arkitektur**

Sammanfattning:

- HUVUDKONTOR i Europa förblir ExpressRoute ansluten, Europa lokalt DC migreras helt till Azure och nu avvecklas.
- Asia DC och HQ förblir anslutna till Private WAN. Azure Virtual WAN används nu för att utöka det lokala operatörsnätverket och tillhandahålla global anslutning.
- Azure Virtual WAN-hubbar som distribueras i azure-regioner i både Västeuropa och Sydostasien för att tillhandahålla anslutningsnav för ExpressRoute- och VPN-anslutna enheter.
- Hubbar tillhandahåller också VPN-avslutning för centrala användare över flera klienttyper med OpenVPN-anslutning till det globala mesh-nätverket, vilket ger åtkomst till inte bara program som migreras till Azure, utan även alla resurser som finns kvar lokalt.
- Internet-anslutning för resurser inom ett virtuellt nätverk som tillhandahålls av Azure Virtual WAN.

Internet-anslutning för fjärrplatser som också tillhandahålls av Azure Virtual WAN. Lokal internet breakout stöds via partnerintegration för optimerad åtkomst till SaaS-tjänster som Office 365.

## <a name="migrate-to-virtual-wan"></a>Migrera till Virtual WAN

I det här avsnittet visas de olika stegen för att migrera till Azure Virtual WAN.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>Steg 1: VDC-hub-and-spoke enda region

Granska arkitekturen. Följande bild visar en enda regiontopologi för Contoso före distributionen av Azure Virtual WAN:

![Enkel regiontopologi](./media/migrate-from-hub-spoke-topology/figure1.png)
**Bild 1: VDC-hub-and-spoke enda region**

I enlighet med metoden Virtual Data Center (VDC) innehåller det kundhanterade virtuella navnätverket flera funktionsblock:

- Delade tjänster (alla vanliga funktioner som krävs av flera ekrar). Exempel: Contoso använder Windows Server-domänkontrollanter på virtuella IaaS-datorer (Infrastructure-as-a-service).
- IP/Routning brandväggstjänster tillhandahålls av en virtuell nätverksinstallation från tredje part, vilket möjliggör eker-till-ekrad layer-3 IP-routning.
- Internet ingress/utgående tjänster, inklusive Azure Application Gateway för inkommande HTTPS-begäranden och proxytjänster från tredje part som körs på virtuella datorer för filtrerad utgående åtkomst till internetresurser.
- ExpressRoute och VPN virtuellt nätverk gateway för anslutning till lokala nätverk.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Steg 2: Distribuera virtuella WAN-hubbar

Distribuera en virtuell WAN-hubb i varje region. Konfigurera den virtuella WAN-hubben med VPN Gateway och ExpressRoute Gateway enligt beskrivningen i följande artiklar:

- [Självstudie: Skapa en plats-till-plats-anslutning med Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Självstudiekurs: Skapa en ExpressRoute-association med Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Azure Virtual WAN måste använda Standard SKU för att aktivera några av de trafiksökvägar som visas i den här artikeln.

![Distribuera virtuella](./media/migrate-from-hub-spoke-topology/figure2.png)
**WAN-hubbar Bild 2: VDC-hubb-och-talade till Virtual WAN-migrering**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Steg 3: Anslut fjärrplatser (ExpressRoute och VPN) till Virtual WAN

Anslut virtual WAN-hubben till de befintliga ExpressRoute-kretsarna och konfigurera VPN-nätverk från plats till plats via Internet till alla fjärrgrenar.

> [!NOTE]
> Expressrutterkretsar måste uppgraderas till Premium SKU-typ för att ansluta till Virtual WAN-hubben.

![Anslut fjärrplatser till](./media/migrate-from-hub-spoke-topology/figure3.png)
Virtual WAN**Bild 3: VDC-hubb-och-talade till Virtual WAN-migrering**

Nu börjar den lokala nätverksutrustningen ta emot vägar som återspeglar DET IP-adressutrymme som tilldelats det virtuella WAN-hanterade navet VNet. Fjärr-VPN-anslutna grenar i detta skede kommer att se två vägar till alla befintliga program i ekrar virtuella nätverk. Dessa enheter bör konfigureras för att fortsätta att använda tunneln till VDC-hubben för att säkerställa symmetrisk routning under övergångsfasen.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Steg 4: Testa hybridanslutning via Virtuellt WAN

Innan du använder den hanterade Virtual WAN-hubben för produktionsanslutning rekommenderar vi att du konfigurerar ett virtuellt testektalt nätverk och Virtual WAN VNet-anslutning. Verifiera att anslutningar till den här testmiljön fungerar via ExpressRoute och Site to Site VPN innan du fortsätter med nästa steg.

![Testa hybridanslutning via](./media/migrate-from-hub-spoke-topology/figure4.png)
Virtual WAN**Figur 4: VDC-hubb-och-talade till Virtual WAN-migrering**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Steg 5: Övergångsanslutning till virtuell WAN-hubb

![Övergångsanslutning till Virtual](./media/migrate-from-hub-spoke-topology/figure5.png)
**WAN-hubb Bild 5: VDC-hubb-och-talade till Virtual WAN-migrering**

**a**. Ta bort befintliga peering-anslutningar från eker virtuella nätverk till den gamla VDC-hubben. Åtkomst till program i ekriga virtuella nätverk är inte tillgänglig förrän steg a-c är klara.

**b.** Anslut de ekriska virtuella nätverken till Virtual WAN-hubben via virtuella nätverk.

**c**. Ta bort alla användardefinierade vägar (UDR) som tidigare använts i ekriga virtuella nätverk för eker-till-ekrare-kommunikation. Den här sökvägen aktiveras nu genom dynamisk routning som är tillgänglig i virtual WAN-hubben.

**d**. Befintliga ExpressRoute- och VPN-gateways i VDC-hubben inaktiveras nu för att tillåta nästa steg (e).

**e**. Anslut den gamla VDC-hubben (hubbvirt nätverk) till Virtual WAN-hubben via en ny VNet-anslutning.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Steg 6: Gamla navet blir delade tjänster ekriga

Vi har nu gjort om vårt Azure-nätverk för att göra Virtual WAN-hubben till den centrala punkten i vår nya topologi.

![Gamla navet blir](./media/migrate-from-hub-spoke-topology/figure6.png)
Shared Services spoke**Figur 6: VDC hub-and-spoke to Virtual WAN migration**

Eftersom Virtual WAN-hubben är en hanterad entitet och inte tillåter distribution av anpassade resurser som virtuella datorer, finns det nu blockering av delade tjänster som ett ekervirt virtuellt nätverk och värdfunktioner som internetinträngning via Azure Application Gateway eller virtualiserad nätverksinstallation. Trafik mellan den delade tjänstmiljön och serverdels virtuella datorer transiterar nu det virtuella WAN-hanterade navet.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Steg 7: Optimera lokal anslutning för att fullt ut utnyttja Virtual WAN

I det här skedet har Contoso mestadels slutfört sina migreringar av affärsprogram i Microsoft Cloud, med endast ett fåtal äldre program kvar i den lokala domänkontrollanten.

![Optimera lokal anslutning för att fullt](./media/migrate-from-hub-spoke-topology/figure7.png)
ut utnyttja Virtual WAN**Figur 7: VDC-hub-and-spoke till Virtual WAN-migrering**

För att utnyttja alla funktioner i Azure Virtual WAN bestämmer contoso att inaktivera sina äldre lokala VPN-anslutningar. Alla grenar som fortsätter att komma åt HQ- eller DC-nätverk kan transitera Microsofts globala nätverk med den inbyggda transitroutningen av Azure Virtual WAN.

> [!NOTE]
> ExpressRoute Global Reach är ett alternativt val för kunder som vill utnyttja Microsofts ryggrad för att komplettera sina befintliga privata SURFNÄT.

## <a name="end-state-architecture-and-traffic-paths"></a>End-state arkitektur och trafikvägar

![Sluttillståndsarkitektur och trafikvägar](./media/migrate-from-hub-spoke-topology/figure8.png)
**Figur: Virtuellt WAN med dubbla regioner**

Det här avsnittet innehåller en sammanfattning av hur den här topologin uppfyller de ursprungliga kraven genom att titta på några exempel trafikflöden.

### <a name="path-1"></a>Bana 1

Sökväg 1 visar trafikflödet från en S2S VPN-ansluten gren i Asien till ett Azure VNet i regionen Sydostasien.

Trafiken dirigeras enligt följande:

- Asien gren är ansluten via fjädrande S2S BGP-aktiverade tunnlar i Sydostasien Virtual WAN hub.

- Asien Virtual WAN-hubb dirigerar trafik lokalt till anslutet virtuellt nätverk.

![Flöde 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Bana 2

Sökväg 2 visar trafikflödet från Det ExpressRoute-anslutna europeiska högkvarteret till ett Azure VNet i regionen Sydostasien.

Trafiken dirigeras enligt följande:

- Europeiska HQ ansluts via premium ExpressRoute krets i Västeuropa Virtual WAN nav.

- Virtuell WAN-hub-to-hub-global anslutning möjliggör överföring av trafik till VNet som är anslutet i fjärrregion.

![Flöde 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Bana 3

Väg 3 visar trafikflödet från Den lokala asien-DC-anslutna till Private WAN till en europeisk S2S-ansluten gren.

Trafiken dirigeras enligt följande:

- Asia DC är anslutet till den lokala privata WAN-operatören.

- ExpressRoute-kretsen avslutas lokalt i Private WAN-anslutning till virtuell WAN-hubb för Sydostasien.

- Virtuell WAN-hub-to-hub-global anslutning möjliggör överföring av trafik.

![Flöde 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Bana 4

Sökväg 4 visar trafikflödet från ett Azure VNet i regionen Sydostasien till ett Azure VNet i Västeuropa.

Trafiken dirigeras enligt följande:

- Virtuell WAN-hub-to-hub-global anslutning möjliggör intern överföring av alla anslutna Virtuella Azure-nätverk utan ytterligare användarkonfiguration.

![Flöde 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Bana 5

Sökväg 5 visar trafikflödet från centrala VPN-användare (P2S) till ett Azure-nätverk i regionen Västeuropa.

Trafiken dirigeras enligt följande:

- Användare av bärbara och mobila enheter använder OpenVPN-klienten för transparent anslutning till P2S VPN-gatewayen i Västeuropa.

- Västeuropa Virtuella WAN-hubbvägar trafikerar lokalt till anslutet virtuellt nätverk.

![Flöde 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Säkerhets- och principkontroll via Azure-brandväggen

Contoso har nu validerat anslutningen mellan alla grenar och virtuella nätverk i enlighet med de krav som diskuterades tidigare i den här artikeln. För att uppfylla sina krav på säkerhetskontroll och nätverksisolering måste de fortsätta att separera och logga trafik via navnätverket. Tidigare utfördes den här funktionen av en virtuell nätverksinstallation (NVA). Contoso vill också inaktivera sina befintliga proxytjänster och använda inbyggda Azure-tjänster för utgående Internet-filtrering.

![Säkerhets- och principkontroll](./media/migrate-from-hub-spoke-topology/security-policy.png)
via Azure-brandväggsfigur:**Azure-brandväggen i VirtuellT WAN (Skyddat virtuellt nav)**

Följande steg på hög nivå krävs för att introducera Azure-brandväggen i virtuella WAN-hubbar för att aktivera en enhetlig principkontroll. Mer information om den här processen och begreppet Säkra virtuella hubbar finns i [Azure Firewall Manager](../firewall-manager/index.yml).

1. Skapa Azure-brandväggsprincipen.
2. Länka brandväggsprincipen till Azure Virtual WAN-hubben. Med det här steget kan den befintliga virtuella WAN-hubben fungera som ett skyddat virtuellt nav och distribuerar de nödvändiga Azure-brandväggsresurserna.

> [!NOTE]
> Om Azure-brandväggen distribueras i en standard virtuell WAN-hubb (SKU: Standard): V2V-, B2V-, V2I- och B2I FW-principer tillämpas endast på trafiken som kommer från de virtuella nätverk och grenar som är anslutna till det specifika navet där Azure FW distribueras (Secured Hub). Trafik som kommer från fjärr-virtuella nätverk och grenar som är anslutna till andra virtuella WAN-hubbar i samma virtuella WAN kommer inte att "brandvägg", även om fjärrgrenar och virtuella nätverk är sammankopplade via Virtual WAN-hubb till hubblänkar. Stöd för brandväggar mellan flera hubben finns på översikten för Azure Virtual WAN och Firewall Manager.

Följande sökvägar visar anslutningssökvägarna som aktiveras med hjälp av Azure-skyddade virtuella hubbar:

### <a name="path-6"></a>Bana 6

Sökväg 6 visar säkert trafikflöde mellan virtuella nätverk inom samma region.

Trafiken dirigeras enligt följande:

- Virtuella nätverk som är anslutna till samma säkra virtuella hubb dirigerar nu trafik till via Azure-brandväggen.

- Azure-brandväggen kan tillämpa principen på dessa flöden.

![Flöde 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Bana 7

Sökväg 7 visar trafikflödet från ett Azure-nätverk till Internet eller säkerhetstjänst från tredje part.

Trafiken dirigeras enligt följande:

- Virtuella nätverk som är anslutna till secure virtual hub kan skicka trafik till offentliga, destinationer på Internet, med hjälp av Secure Hub som en central punkt för Internet-åtkomst.

- Den här trafiken kan filtreras lokalt med hjälp av Azure Firewall FQDN-regler eller skickas till en säkerhetstjänst från tredje part för inspektion.

![Flöde 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Bana 8

Sökväg 8 visar trafikflödet från branch-till-Internet eller säkerhetstjänst från tredje part.

Trafiken dirigeras enligt följande:

- Grenar som är anslutna till secure virtual hub kan skicka trafik till offentliga destinationer på Internet genom att använda Secure Hub som en central åtkomstpunkt för Internet.

- Den här trafiken kan filtreras lokalt med hjälp av Azure Firewall FQDN-regler eller skickas till en säkerhetstjänst från tredje part för inspektion.

![Flöde 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Virtual WAN](virtual-wan-about.md)
