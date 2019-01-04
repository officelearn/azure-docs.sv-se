---
title: Azure-nätverk | Microsoft Docs
description: Läs mer om Azure-nätverk tjänster och funktioner.
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: f21d92dabfcfbe51cf8135388a1ab489c20593a4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537553"
---
# <a name="azure-networking"></a>Azure-nätverk

Azure erbjuder en mängd olika nätverksfunktioner som kan användas tillsammans eller separat. Klicka på någon av följande viktiga funktioner för mer information om dem:
- [Anslutning mellan Azure-resurser](#connectivity): Ansluta Azure-resurser tillsammans i en säker och privat virtuellt nätverk i molnet.
- [Internetanslutning](#internet-connectivity): Meddela och från Azure-resurser via Internet.
- [Lokal anslutning](#on-premises-connectivity): Ansluta ett lokalt nätverk till Azure-resurser via ett virtuellt privat nätverk (VPN) via Internet eller via en dedikerad anslutning till Azure.
- [Läsa in belastningsutjämning och traffic riktning](#load-balancing): Belastningsutjämna trafik till servrar i samma plats och dirigera trafik till servrar på olika platser.
- [Security](#security): Filtrera nätverkstrafik mellan undernät eller enskilda virtuella datorer (VM).
- [Routning](#routing): Använd standardroutning eller helt och hållet styr routning mellan dina Azure och lokala resurser.
- [Hanterbarhet](#manageability): Övervaka och hantera dina Azure-nätverksresurser.
- [Verktyg för distribution och konfiguration av](#tools): Använda en webbaserad portal eller plattformsoberoende kommandoradsverktyg för att distribuera och konfigurera nätverksresurser.

## <a name="Connectivity"></a>Anslutning mellan Azure-resurser

Azure-resurser som virtuella datorer, molntjänster, Skalningsuppsättningar för virtuella datorer och Azure App Service-miljöer kan kommunicera privat med varandra via Azure Virtual Network (VNet). Ett virtuellt nätverk är en logisk avgränsning av Azure-molnet för din [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Du kan implementera flera virtuella nätverk i varje Azure-prenumeration och Azure [region](https://azure.microsoft.com/regions). Varje virtuellt nätverk är isolerat från andra virtuella nätverk. För varje virtuellt nätverk kan du:

- Ange ett anpassat privat IP-adressutrymme med offentliga och privata adresser (RFC 1918). Azure tilldelar resurser är anslutna till det virtuella nätverket en privat IP-adress från det adressutrymme som du tilldelar.
- Segmentera det virtuella nätverket i en eller flera undernät och allokera en del av virtuella nätverkets adressutrymme till varje undernät.
- Använd Azure-tillhandahållen namnmatchning eller ange din egen DNS-server för användning av resurser som är anslutna till ett virtuellt nätverk.

Mer information om tjänsten Azure Virtual Network i [översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln. Du kan ansluta virtuella nätverk till varandra, så att resurser som är anslutna till något virtuellt nätverk kan kommunicera med varandra över virtuella nätverk. Du kan använda ett eller båda av följande alternativ för att ansluta virtuella nätverk till varandra:

- **Peering:** Gör det möjligt för resurser som är anslutna till olika virtuella Azure-nätverk inom samma Azure-region ska kunna kommunicera med varandra. Nätverksbandbredd och svarstid för de virtuella nätverken är samma som om resurserna som var anslutna till samma virtuella nätverk. Mer information om peering på [peering översikt över virtuella nätverk](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- **VPN-Gateway:** Gör det möjligt för resurser som är anslutna till olika virtuella Azure-nätverk i olika Azure-regioner kan kommunicera med varandra. Det flödar trafiken mellan virtuella nätverk via Azure VPN Gateway. Bandbredden mellan virtuella nätverk är begränsad till bandbredden för gatewayen. Mer information om hur du ansluter virtuella nätverk med en VPN-Gateway den [konfigurera en VNet-till-VNet-anslutning mellan regioner](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.

## <a name="internet-connectivity"></a>Internetanslutning

Alla Azure-resurser som är anslutna till ett virtuellt nätverk har utgående anslutning till Internet som standard. Privata IP-adressen för resursen är adress källnätverket översättas (SNAT) till en offentlig IP-adress via Azure-infrastrukturen. Mer information om utgående Internet-anslutning i [förstå utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.

Att kommunicera inkommande till Azure-resurser från Internet eller för att kommunicera utgående till Internet utan SNAT, måste en resurs tilldelas en offentlig IP-adress. Mer information om offentliga IP-adresser i [offentliga IP-adresser](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.

## <a name="on-premises-connectivity"></a>Lokal anslutning

Du kan komma åt resurser i ditt virtuella nätverk på ett säkert sätt via en VPN-anslutning eller en privat direktanslutning. Om du vill skicka nätverkstrafik mellan Azure-nätverk och ditt lokala nätverk, måste du skapa en virtuell nätverksgateway. Du kan konfigurera inställningar för gatewayen för att skapa typ av anslutning som du vill, VPN eller ExpressRoute.

Du kan ansluta ditt lokala nätverk till ett virtuellt nätverk med hjälp av följande alternativ:

**Punkt-till-plats (VPN över SSTP)**

Följande bild visar separat punkt till plats-anslutningar mellan flera datorer och ett virtuellt nätverk:

![Punkt-till-plats](./media/networking-overview/point-to-site.png)

Den här anslutningen har upprättats mellan en dator och ett virtuellt nätverk. Den här anslutningstypen är bra om du precis har börjat med Azure, eller för utvecklare, eftersom det krävs få eller inga ändringar i ditt befintliga nätverk. Det är också praktisk när du ansluter från en annan plats, till exempel en konferens eller hem. Punkt-till-plats-anslutningar är ofta tillsammans med en plats-till-plats-anslutning via samma gateway för virtuellt nätverk. Anslutningen använder SSTP-protokollet för att tillhandahålla krypterad kommunikation via Internet mellan datorn och det virtuella nätverket. Svarstiden för en punkt-till-plats-VPN är oförutsägbart, eftersom trafiken passerar via Internet.

**Plats-till-plats (IPsec/IKE VPN-tunnel)**

![Plats-till-plats](./media/networking-overview/site-to-site.png)

Den här anslutningen har upprättats mellan din lokala VPN-enhet och Azure VPN Gateway. Den här anslutningstypen kan alla lokala resurser som du ger behörighet att komma åt det virtuella nätverket. Anslutningen är en IPSec/IKE VPN som tillhandahåller krypterad kommunikation via Internet mellan din lokala enhet och Azure VPN-gatewayen. Du kan ansluta flera lokala platser till samma VPN-gateway. Den lokala VPN-enheten på varje plats måste ha en externt riktad mot-offentliga IP-adress som inte ligger bakom en NAT. Svarstiden för en plats-till-plats-anslutning är oförutsägbart, eftersom trafiken passerar via Internet.

**ExpressRoute (dedicerad privat anslutning)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Den här typen av anslutning har upprättats mellan ditt nätverk och Azure, via en ExpressRoute-partner. Den här anslutningen är privat. Trafiken sker inte via Internet. Svarstiden för en ExpressRoute-anslutning är förutsägbart, eftersom trafiken inte via Internet. ExpressRoute kan kombineras med en plats-till-plats-anslutning.

Mer information om alla tidigare anslutningsalternativ den [anslutningstopologi](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.

## <a name="load-balancing"></a>Läsa in belastningsutjämning och traffic riktning

Microsoft Azure tillhandahåller flera tjänster för att hantera hur nätverkstrafiken distribueras och belastningsutjämning. Du kan använda någon av följande funktioner enskilt eller tillsammans:

**Belastningsutjämning med DNS**

Azure Traffic Manager-tjänsten tillhandahåller globala DNS belastningsutjämning. Traffic Manager svarar på klienter med IP-adressen för en felfri slutpunkt, baserat på en av följande metoder för trafikroutning:
- **Geografisk:** Klienter dirigeras till specifika slutpunkter (Azure, externa eller kapslad) baserat på vilka geografiska plats deras DNS-fråga som samlas in från. Den här metoden möjliggör scenarier där du känner till en klients geografiska region och routning dem baserat på det, är viktig. Exempel: uppfyller data landsbaserad placering måste ett demonterat, lokalisering av innehåll och användardata upplevelse och mäta trafik från olika regioner.
- **Prestanda:** IP-adressen som returneras till klienten är ”närmaste” till klienten. ”Närmaste' slutpunkt är inte nödvändigtvis närmaste enligt geografisk avstånd. Den här metoden anger i stället närmaste slutpunkten genom att mäta svarstiden i nätverket. Traffic Manager har en tabell med Internet svarstid för att spåra mäts tiden mellan IP-adressintervall och varje Azure-datacenter.
- **Prioritet:** Trafiken börjar dirigeras till den primära slutpunkten (högsta prioritet). Om den primära slutpunkten inte är tillgänglig, dirigerar Traffic Manager trafiken till den andra slutpunkten. Om både de primära och sekundära slutpunkterna inte är tillgängliga går trafiken till den tredje och så vidare. Tillgängligheten för slutpunkten som baseras på den konfigurerade status (aktiverad eller inaktiverad) och pågående slutpunktsövervakning.
- **Viktad resursallokering:** Traffic Manager väljer slumpmässigt en tillgänglig slutpunkt för varje begäran. Sannolikheten för att välja en slutpunkt är baserad på vikterna som tilldelats till alla tillgängliga slutpunkter. Med hjälp av samma vikt för alla slutpunkter resultat i en jämn trafikfördelning. Med högre eller lägre vikterna på specifika slutpunkter gör de slutpunkterna som ska returneras oftare eller mer sällan i DNS-svar.

Följande bild visar en begäran för ett webbprogram som dirigeras till en slutpunkt för Web App. Slutpunkter kan också vara andra Azure-tjänster, till exempel virtuella datorer och molntjänster.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

Klienten ansluter direkt till denna slutpunkt. Med Azure Traffic Manager identifierar när en slutpunkt har dåligt hälsotillstånd och sedan dirigerar klienter till en annan, felfri slutpunkt. Mer information om Traffic Manager, den [översikt över Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.

**Belastningsutjämning för program**

Tjänsten Azure Application Gateway erbjuder application Deliver controller (ADC) som en tjänst. Application Gateway erbjuder olika Layer 7 (HTTP/HTTPS) belastningsutjämningsfunktioner för ditt program, inklusive en brandvägg för webbaserade program för att skydda dina webbprogram från säkerhetsrisker och trojaner. Application Gateway kan du optimera webbservergruppens produktivitet genom att avlasta CPU-intensiva SSL-avslutning till application gateway. 

Andra Layer 7-routningsfunktioner omfattar resursallokeringsdistribution av inkommande trafik, Cookiebaserad sessionstillhörighet, URL-sökvägsbaserad Routning och möjligheten att vara värd för flera webbplatser bakom en enda application gateway. Application Gateway kan konfigureras som en internetuppkopplad gateway, en endast intern gateway eller en kombination av båda. Application Gateway är helt Azure hanterad, skalbar och högtillgänglig. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner. Om du vill veta mer om Application Gateway kan läsa den [översikt över Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.

Följande bild visar URL-sökvägsbaserad routning med Application Gateway:

![Application Gateway](./media/networking-overview/application-gateway.png)

**Utjämning av nätverksbelastning**

Azure Load Balancer ger höga prestanda och låg latens Layer 4-belastningsutjämning för alla UDP och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slutpunkter. Du kan definiera regler för att mappa inkommande anslutningar till backend-poolen mål med hjälp av TCP- och HTTP-avsökning av hälsotillstånd alternativ för att hantera tjänstens tillgänglighet. Mer information om belastningsutjämnaren i [översikt över Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.

Följande bild visar ett Internet-riktade flerskiktade program som använder både externa och interna belastningsutjämnare:

![Lastbalanserare](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Säkerhet

Du kan filtrera trafik till och från Azure-resurser med hjälp av följande alternativ:

- **Nätverk:** Du kan implementera Azure nätverkssäkerhetsgrupper (NSG) för att filtrera inkommande och utgående trafik till Azure-resurser. Varje NSG innehåller en eller flera regler för inkommande och utgående. Varje regel anger källans IP-adresser, mål-IP-adresser, port och protokoll som trafik filtreras med. NSG: er kan tillämpas på enskilda undernät och enskilda virtuella datorer. Mer information om Nätverkssäkerhetsgrupper i [nätverkssäkerhetsöversikt](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- **Program:** Du kan skydda dina webbprogram från säkerhetsrisker och hot på Internet med hjälp av en Programgateway med brandväggen för webbaserade program. Vanliga exempel är SQL-inmatningsattacker mellan webbplatser och felaktiga huvuden. Application gateway som filtrerar ut den här trafiken och stoppar från att nå dina webbservrar. Du kan konfigurera vilka regler som du vill att aktiverad. Möjligheten att konfigurera SSL-förhandlingsprinciper tillhandahålls för att tillåta vissa principer kan inaktiveras. Mer information om Brandvägg för webbaserade program på [Brandvägg för webbaserade program](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.

Om du behöver nätverkskapacitet Azure inte tillhandahålla eller vill använda nätverksprogram som du använder lokalt kan du implementera produkterna i virtuella datorer och Anslut dem till ditt virtuella nätverk. Den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) innehåller flera olika virtuella datorer vara förkonfigurerad med nätverksprogram som du kan för närvarande använder. Dessa förkonfigurerade virtuella datorer kallas vanligtvis för virtuella nätverksinstallationer (NVA). Nva: er är tillgängliga med program som brandvägg och WAN-optimering.

## <a name="routing"></a>Routning

Azure skapar standard routningstabeller som gör att resurser som är anslutna till alla undernät i alla VNet kan kommunicera med varandra. Du kan implementera ett eller båda av följande typer av vägar för att åsidosätta de standardvägar som Azure skapar:
- **Användardefinierade:** Du kan skapa anpassade routningstabeller med vägar som styr vart trafik dirigeras för varje undernät. Mer information om användardefinierade vägar finns i artikeln [Användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **BGP (border gateway protocol):** Om du ansluter ditt virtuella nätverk till ditt lokala nätverk som använder en Azure VPN Gateway eller ExpressRoute-anslutning kan du sprida BGP-vägar till dina virtuella nätverk. BGP är ett standardroutningsprotokoll som vanligen används på Internet för att utbyta information om routning och åtkomst mellan två eller flera nätverk. När de används i samband med Azure-nätverk möjliggör BGP att Azure VPN gateway och dina lokala VPN-enheter kallas BGP-peer eller grannar, utbyta ”vägar” som informerar båda gatewayerna om åtkomsten för de prefix som ska gå genom de gatewayer eller routrar som ingår. BGP kan också möjliggöra överföringsroutning mellan flera nätverk genom att sprida vägar som BGP-gatewayen får från en BGP-peer till alla andra BGP-peers. Mer information om BGP finns den [BGP med Azure VPN gateway-översikt](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.

## <a name="manageability"></a>Hanterbarhet

Azure tillhandahåller följande verktyg för att övervaka och hantera nätverk:
- **Aktivitetsloggar:** Alla Azure-resurser har aktivitetsloggar som ger information om åtgärder som vidtagits, statusen för åtgärder och vem som initierade åtgärden. Mer information om aktivitetsloggar i [aktivitetsloggar översikt](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- **Diagnostikloggar:** Periodiska och spontant händelser skapas av nätverksresurser och loggat in Azure storage-konton, skickas till en Azure-Händelsehubb eller skickas till Azure Log Analytics. Diagnostikloggar ger information om hälsotillståndet för en resurs. Diagnostikloggar tillhandahålls för Load Balancer (internetuppkopplad), Nätverkssäkerhetsgrupper, vägar och Application Gateway. Mer information om diagnostiska loggar den [diagnostikloggar översikt](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- **Mått:** Mått är prestandamått och prestandaräknare som samlats in under en viss tidsperiod på resurser. Mått kan användas till att utlösa aviseringar baserat på tröskelvärden. Mått är för närvarande tillgängliga på Application Gateway. Läs mer om mätvärden i [översikt över mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- **Felsökning:** Information om felsökning är tillgänglig direkt i Azure-portalen. Innehåller information om diagnostisera vanliga problem med ExpressRoute, VPN-Gateway, Application Gateway, säkerhetsloggar i nätverket, vägar, DNS, belastningsutjämnare och Traffic Manager.
- **Rollbaserad åtkomstkontroll (RBAC):** Kontrollera vem som kan skapa och hantera nätverksresurser med rollbaserad åtkomstkontroll (RBAC). Mer information om RBAC genom att läsa den [Kom igång med RBAC](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln. 
- **Infångade paket:** Tjänsten Azure Network Watcher ger möjlighet att köra ett infångat paket på en virtuell dator via ett tillägg i den virtuella datorn. Den här funktionen är tillgänglig för Linux- och Windows-datorer. Mer information om infångade den [översikt över paket](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- **Kontrollera IP-flöden:** Network Watcher kan du kontrollera IP-flöden mellan en Azure-dator och en fjärransluten resurs för att avgöra om paket tillåts eller nekas. Den här funktionen ger administratörer möjlighet att snabbt diagnostisera problem med nätverksanslutningen. Mer information om hur du kontrollerar IP-flöden i [IP-flödesverifieringen översikt](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- **Felsök VPN-anslutningen:** VPN-felsökare möjligheterna för Network Watcher ger möjlighet att fråga en anslutning eller gateway och kontrollera hälsotillståndet för resurserna. Mer information om felsökning av VPN-anslutningar i [VPN-anslutning felsökning översikt](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- **Visa nätverkstopologi:** Visa en grafisk representation av nätverksresurser i ett virtuellt nätverk med Network Watcher. Mer information om hur du visar nätverkstopologi den [topologiöversikt](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.

## <a name="tools"></a>Verktyg för distribution och konfiguration

Du kan distribuera och konfigurera Azure-nätverksresurser med någon av följande verktyg:

- **Azure-portalen:** Ett grafiskt användargränssnitt som körs i en webbläsare. Öppna [Azure-portalen](http://portal.azure.com).
- **Azure PowerShell:** Kommandoradsverktyg för hantering av Azure från Windows-datorer. Läs mer om Azure PowerShell genom att läsa den [översikt över Azure PowerShell](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- **Azure-kommandoradsgränssnittet (CLI):** Kommandoradsverktyg för hantering av Azure från Linux, macOS och Windows-datorer. Läs mer om Azure CLI genom att läsa den [översikt över Azure CLI](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- **Azure Resource Manager-mallar:** En fil (i JSON-format) som definierar infrastrukturen och konfigurationen av en Azure-lösning. Genom att använda en mall kan du distribuera lösningen flera gånger under dess livscykel och vara säker på att dina resurser distribueras konsekvent. Mer information om att skapa mallar i [bästa praxis för att skapa mallar](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln. Mallar kan distribueras med Azure-portalen, CLI eller PowerShell. Om du vill komma igång med mallar direkt kan distribuera någon av de många förkonfigurerade mallarna i den [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/?term=network) biblioteket. 

## <a name="pricing"></a>Prissättning

En del av Azure nätverkstjänsterna har en kostnad, medan andra är kostnadsfria. Visa den [virtuellt nätverk](https://azure.microsoft.com/pricing/details/virtual-network), [VPN-Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway), [Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/), [belastningsutjämnaren](https://azure.microsoft.com/pricing/details/load-balancer), [Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) och [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) prissidor för mer information.

## <a name="next-steps"></a>Nästa steg

- Skapa ditt första virtuella nätverk och Anslut några virtuella datorer, genom att följa stegen i den [skapa din första virtuella nätverk](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- Ansluta datorn till ett virtuellt nätverk genom att följa stegen i den [konfigurera en punkt-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
- Belastningsutjämna Internettrafik till offentliga servrar genom att följa stegen i den [skapar en internetuppkopplad belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artikeln.
