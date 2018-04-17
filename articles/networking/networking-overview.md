---
title: Azure-nätverk | Microsoft Docs
description: Lär dig mer om Azure nätverkstjänster och funktioner.
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
ms.openlocfilehash: a4f1c7c3342a2a78a2a5b765bd688bed4c85392f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-networking"></a>Azure-nätverk

Azure tillhandahåller en mängd olika nätverksfunktioner som kan användas tillsammans eller separat. Klicka på någon av följande viktiga funktioner för mer information om dem:
- [Anslutning mellan Azure-resurser](#connectivity): ansluta Azure-resurser tillsammans i en säker, privat virtuellt nätverk i molnet.
- [Internetanslutning](#internet-connectivity): kommunikation till och från Azure-resurser via Internet.
- [Lokal anslutning](#on-premises-connectivity): ansluta ett lokalt nätverk till Azure-resurser via ett virtuellt privat nätverk (VPN) via Internet eller via en dedikerad anslutning till Azure.
- [Läsa in belastningsutjämning och trafik riktning](#load-balancing): Läs in Utjämna trafiken till servrar i samma plats och trafiken till servrar på olika platser.
- [Säkerhet](#security): Filtrera nätverkstrafiken mellan undernät eller enskilda virtuella datorer (VM).
- [Routning](#routing): Använd standardroutning eller helt styra routning mellan Azure och lokala resurser.
- [Hanterbarhet](#manageability): övervaka och hantera dina Azure nätverksresurser.
- [Verktyg för distribution och konfiguration av](#tools): använda en webbaserad portal eller plattformsoberoende kommandoradsverktyg för att distribuera och konfigurera nätverksresurser.

## <a name="Connectivity"></a>Anslutning mellan Azure-resurser

Azure-resurser som virtuella datorer, Cloud Services, Skalningsuppsättningar i virtuella datorer och Azure App Service-miljöer kan privat kommunicerar med varandra via ett Azure Virtual Network (VNet). Ett virtuellt nätverk är en logisk isolering av Azure-molnet dedikerad till din [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Du kan implementera flera Vnet inom varje Azure-prenumeration och Azure [region](https://azure.microsoft.com/regions). Varje virtuellt nätverk är isolerad från andra Vnet. För varje virtuellt nätverk kan du:

- Ange en egen privata IP-adressutrymmet med hjälp av offentliga och privata (RFC 1918)-adresser. Azure tilldelar resurser som ansluten till VNet en privat IP-adress från det adressutrymme som du tilldelar.
- Segmentera VNet i en eller flera undernät och tilldela en del av VNet-adressutrymmet för varje undernät.
- Använd Azure-tillhandahållna namnmatchning eller ange egna DNS-server för användning av resurser som är anslutna till ett virtuellt nätverk.

Mer information om tjänsten Azure Virtual Network den [översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel. Du kan ansluta Vnet till varandra, aktivera resurser som är anslutna till något virtuellt nätverk att kommunicera med varandra via Vnet. Du kan använda ett eller båda av följande alternativ för att ansluta Vnet till varandra:

- **Peering:** gör att resurser som anslutits till olika virtuella Azure-nätverk inom samma Azure-region att kommunicera med varandra. Bandbredd och fördröjning mellan till Vnet är samma som resurserna som är anslutna till samma virtuella nätverk. Mer information om peering den [peering översikt över virtuella nätverk](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- **VPN-Gateway:** gör att resurser som anslutits till olika virtuella Azure-nätverk inom olika Azure-regioner kan kommunicera med varandra. Trafik mellan Vnet går genom en Azure VPN-Gateway. Bandbredden mellan Vnet är begränsad till bandbredden för gatewayen. Mer information om hur du ansluter Vnet med en VPN-Gateway den [konfigurerar du en VNet-till-VNet-anslutning mellan regioner](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.

## <a name="internet-connectivity"></a>Internet-anslutning

Alla Azure-resurser som ansluten till ett virtuellt nätverk har utgående anslutning till Internet som standard. Privata IP-adressen för resursen är källan nätverksadress översättas (SNAT) till en offentlig IP-adress av Azure-infrastrukturen. Om du vill veta mer om utgående Internetanslutning kan du läsa den [förstå utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.

Att kommunicera inkommande Azure-resurser från Internet eller kommunicera utgående till Internet utan SNAT tilldelas en resurs en offentlig IP-adress. Mer information om den offentliga IP-adresser i [offentliga IP-adresser](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.

## <a name="on-premises-connectivity"></a>Lokal anslutning

Du kan komma åt resurser i ditt virtuella nätverk på ett säkert sätt via en VPN-anslutning eller en privat direktanslutning. Om du vill skicka nätverkstrafik mellan dina virtuella Azure-nätverket och ditt lokala nätverk, måste du skapa en virtuell nätverksgateway. Du kan konfigurera inställningar för gateway för att skapa typ av anslutning som du vill använda, VPN eller ExpressRoute.

Du kan ansluta dina lokala nätverk till ett virtuellt nätverk med hjälp av följande alternativ:

**Punkt-till-plats (VPN över SSTP)**

Följande bild visar separat punkt till plats-anslutningar mellan flera datorer och ett virtuellt nätverk:

![Punkt-till-plats](./media/networking-overview/point-to-site.png)

Den här anslutningen har upprättats mellan en dator och ett VNet. Den här anslutningen är bra om du precis har börjat med Azure, eller för utvecklare, eftersom det krävs lite eller ingen ändringar i ditt befintliga nätverk. Det är också praktiskt när du ansluter från en annan plats, till exempel en konferens eller hemma. Punkt-till-plats-anslutningar är ofta tillsammans med en plats-till-plats-anslutning via samma gateway för virtuellt nätverk. Anslutningen använder SSTP-protokollet för att tillhandahålla krypterad kommunikation via Internet mellan datorn och VNet. Svarstiden för en punkt-till-plats-VPN är oförutsägbart, eftersom trafiken färdas genom Internet.

**Plats-till-plats (IPsec/IKE VPN-tunnel)**

![Plats-till-plats](./media/networking-overview/site-to-site.png)

Den här anslutningen har upprättats mellan din lokala VPN-enhet och en Azure VPN-Gateway. Den här anslutningstypen gör att alla lokala resurser som du har behörighet att komma åt VNet. Anslutningen är en IPSec/IKE-VPN som tillhandahåller krypterad kommunikation via Internet mellan din lokala enhet och Azure VPN-gatewayen. Du kan ansluta flera lokala platser till samma VPN-gateway. Lokala VPN-enhet på varje plats måste ha en externt riktade-offentliga IP-adress som inte finns bakom en NAT Svarstiden för en plats-till-plats-anslutning är oförutsägbart, eftersom trafiken färdas genom Internet.

**ExpressRoute (dedikerad privata anslutning)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Den här typen av anslutning upprättas mellan ditt nätverk och Azure, via en ExpressRoute-partner. Den här anslutningen är privat. Trafik inte går över Internet. Svarstid för en ExpressRoute-anslutning är förutsägbar, eftersom trafiken inte sker via Internet. ExpressRoute kan kombineras med en plats-till-plats-anslutning.

Mer information om alla tidigare anslutningsalternativ den [anslutning Topologidiagram](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.

## <a name="load-balancing"></a>Läsa in belastningsutjämning och trafik riktning

Microsoft Azure tillhandahåller flera tjänster för att hantera hur nätverkstrafik distribueras och Utjämning av nätverksbelastning. Du kan använda någon av följande funktioner enskilt eller tillsammans:

**Belastningsutjämning med DNS**

Azure Traffic Manager-tjänsten tillhandahåller globala DNS belastningsutjämning. Traffic Manager svarar på klienter med IP-adressen för en felfri slutpunkt, baserat på en av följande metoder för routning:
- **Geografiska:** klienterna dirigeras till specifika slutpunkter (Azure externa eller kapslade) baserat på vilka geografiska plats deras DNS-fråga som kommer från. Den här metoden möjliggör scenarier där känna till en klient geografisk region och routning dem baserat på det, är viktig. Exempel: uppfyller data suveränitet uppdrag, lokalisering av innehåll & användarens upplevelse och mäta trafik från olika regioner.
- **Prestanda:** IP-adressen som returneras till klienten är ”närmaste” till klienten. 'Närmaste' slutpunkt är inte nödvändigtvis närmaste mätt av geografiska avstånd. Den här metoden anger i stället närmaste slutpunkten genom att mäta Nätverksfördröjningen. Traffic Manager upprätthåller en Internet latens tabell för att spåra fram och åter tiden mellan IP-adressintervall och varje Azure-datacenter.
- **Prioritet:** trafik dirigeras till den primära (högsta prioritet)-slutpunkten. Om den primära slutpunkten inte är tillgänglig dirigerar Traffic Manager trafik till andra slutpunkten. Om både de primära och sekundära slutpunkterna inte är tillgängliga går trafiken till tredje och så vidare. Tillgängligheten för slutpunkten är baserat på konfigurerad status (aktiverade eller inaktiverade) och pågående slutpunktsövervakning.
- **Viktad resursallokering:** för varje begäran väljer Traffic Manager slumpmässigt en tillgänglig slutpunkt. Sannolikheten för att välja en slutpunkt som baseras på viktningar som tilldelats till alla tillgängliga slutpunkterna. Med hjälp av samma vikt mellan alla slutpunkter resultat i en även trafikfördelning. Med högre eller lägre vikterna på specifika slutpunkter gör dessa slutpunkter som ska returneras mer eller mindre ofta i DNS-svar.

Följande bild visar en begäran för ett webbprogram som dirigeras till en slutpunkt för webbprogram. Slutpunkter kan också finnas andra Azure-tjänster, till exempel virtuella datorer och molntjänster.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

Klienten ansluter direkt till denna slutpunkt. Azure Traffic Manager identifierar när en slutpunkt har dåligt hälsotillstånd och dirigerar om klienter till en annan, felfri slutpunkt. Mer information om Traffic Manager den [översikt över Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.

**Belastningsutjämning för program**

Tjänsten Azure Application Gateway ger programmet leverans domänkontrollant (ADC) som en tjänst. Application Gateway har olika Layer 7 (HTTP/HTTPS) belastningsutjämnande funktioner för ditt program, till exempel en brandvägg för webbaserade program för att skydda dina webbapplikationer från säkerhetsrisker och trojaner. Programgateway kan du optimera web servergruppen produktivitet genom att avlasta processorintensiva SSL-avslutning för programgatewayen. 

Andra lager 7 routningsfunktionerna inkluderar resursallokering distribution av inkommande trafik, cookie-baserad session tillhörighet, URL-sökväg-baserad Routning och möjligheten att vara värd för flera webbplatser bakom en enda Programgateway. Programgateway kan konfigureras som en mot Internet-gateway, en endast internt-gateway eller en kombination av båda. Programgateway är fullständigt Azure hanterade, skalbara och hög tillgänglighet. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner. Mer information om Application Gateway den [Programgateway översikt](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.

Följande bild visar Webbadressen sökväg-baserade routning med Application Gateway:

![Application Gateway](./media/networking-overview/application-gateway.png)

**Utjämning av nätverksbelastning**

Azure belastningsutjämnare ger hög prestanda, låg latens lager 4 belastningsutjämning för alla UDP och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slutpunkter. Du kan definiera regler för att mappa inkommande anslutningar till backend-adresspool mål med hjälp av TCP- och HTTP-avsökning av hälsotillstånd alternativ för att hantera tjänsttillgänglighet. Mer information om belastningsutjämning i [belastningsutjämnaren översikt](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.

Följande bild visar ett Internet-riktade flera nivåer program som använder både externa och interna belastningsutjämnare:

![Belastningsutjämnare](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Säkerhet

Du kan filtrera trafik till och från Azure-resurser med hjälp av följande alternativ:

- **Nätverk:** du kan implementera Azure nätverkssäkerhetsgrupper (NSG: er) för att filtrera inkommande och utgående trafik till Azure-resurser. Varje NSG innehåller en eller flera regler för inkommande och utgående. Varje regel anger käll-IP-adresser, mål-IP-adresser, port och protokoll som trafik filtreras med. NSG: er kan tillämpas på enskilda undernät och enskilda virtuella datorer. Mer information om NSG: er i [Network security groups översikt](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- **Program:** med hjälp av en Programgateway med Brandvägg för webbaserade program kan du skydda webbprogrammen från säkerhetsrisker och trojaner. Vanliga exempel är SQL injection attacker, över flera webbplatser och felaktig huvuden. Programgateway filtrerar ut den här trafiken och stoppas från att nå webbservrar. Du kan konfigurera vilka regler som du vill aktiverad. Möjligheten att konfigurera principer för SSL-förhandling tillhandahålls så att vissa principer är inaktiverad. Mer information om Brandvägg för webbaserade program i [Brandvägg för webbaserade program](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.

Om du behöver nätverkskapacitet Azure inte ge eller vill använda nätverksprogram som du använder lokalt kan du implementera produkterna i virtuella datorer och Anslut dem till ditt VNet. Den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) innehåller flera olika virtuella datorer förkonfigurerade med nätverksprogram som du kan använda. Dessa förkonfigurerade virtuella datorer kallas vanligtvis virtuella nätverksinstallationer (NVA). NVAs finns tillgängliga för program som brandvägg och WAN-optimering.

## <a name="routing"></a>Routning

Azure skapar standard routningstabeller som gör att resurser som är anslutna till alla undernät i alla virtuella nätverk för att kommunicera med varandra. Du kan implementera ett eller båda av följande typer av vägar att åsidosätta standardvägar Azure skapar:
- **Användardefinierade:** kan du skapa anpassade routningstabeller med vägar som styr där trafik dirigeras till för varje undernät. Mer information om användardefinierade vägar finns i artikeln [Användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Border gateway protocol (BGP):** om du ansluter ditt VNet till ditt lokala nätverk med hjälp av en Azure VPN-Gateway eller ExpressRoute-anslutning du sprida BGP-vägar till din Vnet. BGP är ett standardroutningsprotokoll som vanligen används på Internet för att utbyta information om routning och åtkomst mellan två eller flera nätverk. När den används i samband med virtuella Azure-nätverk, kan BGP Azure VPN-gatewayer och din lokala VPN-enheter som kallas BGP-peers eller grannar, för att utbyta ”dirigerar” som information om båda gateways på tillgängligheten och tillgängligheten för de prefix som ska gå igenom gateways eller routrar som ingår. BGP kan också aktivera transitroutning mellan flera nätverk genom att sprida vägar som en gateway med BGP lär sig från en BGP-peer till andra BGP-peers. Mer information om BGP finns på [BGP med Azure VPN-gatewayer översikt](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.

## <a name="manageability"></a>Hanterbarhet

Azure tillhandahåller följande verktyg för att övervaka och hantera nätverk:
- **Aktivitetsloggar:** alla Azure-resurser har aktivitetsloggar som ger information om åtgärder som vidtagits, status för åtgärder och vem som initierat åtgärden. Mer information om aktivitetsloggar den [aktivitet loggar översikt](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- **Diagnostikloggar:** Periodisk och eget initiativ händelser har skapats av nätverksresurser och loggas i Azure storage-konton, skickas till en Azure-Händelsehubb eller skickas till Azure logganalys. Diagnostikloggar ger information om hälsotillståndet för en resurs. Diagnostikloggar har angetts för belastningsutjämnaren (Internet-inriktad), Nätverkssäkerhetsgrupper, vägar och Application Gateway. Mer information om diagnostiska loggar den [diagnostikloggar översikt](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- **Mått:** är prestandamått och räknare som samlas in under en tidsperiod på resurser. Mått kan användas för att utlösa varningar baserat på tröskelvärden. Mått är för närvarande tillgängliga på Application Gateway. Mer information om mått på [mått översikt](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- **Felsökning:** felsökningsinformation är tillgänglig direkt i Azure-portalen. Informationen kan felsöka vanliga problem med ExpressRoute, VPN-Gateway, Programgateway, säkerhetsloggar i nätverket, vägar, DNS, belastningsutjämnare och Traffic Manager.
- **Rollbaserad åtkomstkontroll (RBAC):** styra vem som kan skapa och hantera nätverksresurser med rollbaserad åtkomstkontroll (RBAC). Mer information om RBAC genom att läsa den [Kom igång med RBAC](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel. 
- **Paketinsamling:** Nätverksbevakaren i Azure service ger dig möjlighet att köra en paketinsamling på en virtuell dator via ett tillägg i den virtuella datorn. Den här funktionen är tillgänglig för Linux och Windows-datorer. Mer information om paketinsamling den [paket avbilda översikt](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- **Kontrollera IP-flöden:** Nätverksbevakaren kan du kontrollera IP-flöden mellan en Azure VM och en fjärresurs för att fastställa om paket tillåts eller nekas. Den här funktionen ger administratörer möjlighet att snabbt diagnostisera problem med nätverksanslutningen. Mer information om hur du kontrollerar IP-flöden på [IP-flöde Kontrollera översikt](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- **Felsökning av VPN-anslutningar:** VPN-felsökaren möjligheterna för Nätverksbevakaren ger möjlighet att fråga en anslutning eller gateway och kontrollera hälsotillståndet för resurser. Mer information om felsökning av VPN-anslutningar i [felsökning översikt över VPN-anslutning](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- **Visa nätverkstopologi:** visar en grafisk representation av nätverksresurser i ett VNet med Nätverksbevakaren. Mer information om hur du visar nätverkstopologi den [labbtopologi](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.

## <a name="tools"></a>Verktyg för distribution och konfiguration

Du kan distribuera och konfigurera Azure nätverksresurser med någon av följande verktyg:

- **Azure-portalen:** ett grafiskt användargränssnitt som körs i en webbläsare. Öppna [Azure-portalen](http://portal.azure.com).
- **Azure PowerShell:** kommandoradsverktyg för att hantera Azure från Windows-datorer. Lär dig mer om Azure PowerShell genom att läsa den [översikt över Azure PowerShell](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- **Azure-kommandoradsgränssnittet (CLI):** kommandoradsverktyg för att hantera Azure från Linux, macOS eller Windows-datorer. Lär dig mer om Azure CLI genom att läsa den [översikt över Azure CLI](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- **Azure Resource Manager-mallar:** en fil (i JSON-format) som definierar infrastrukturen och konfigurationen av en Azure-lösning. Genom att använda en mall kan du distribuera lösningen flera gånger under dess livscykel och vara säker på att dina resurser distribueras konsekvent. Mer information om att redigera mallar på [bästa praxis för att skapa mallar](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel. Mallar kan distribueras med Azure-portalen, CLI eller PowerShell. Om du vill komma igång med mallar direkt, distribuera någon av de många förkonfigurerade mallarna i den [Azure Quickstart mallar](https://azure.microsoft.com/resources/templates/?term=network) bibliotek. 

## <a name="pricing"></a>Prissättning

Några av Azure nätverkstjänster finns en kostnad, medan andra är ledigt. Visa den [för virtuella nätverk](https://azure.microsoft.com/pricing/details/virtual-network), [VPN-Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway), [Programgateway](https://azure.microsoft.com/en-us/pricing/details/application-gateway/), [belastningsutjämnaren](https://azure.microsoft.com/pricing/details/load-balancer), [Nätverksbevakaren](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) och [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) priser sidor för mer information.

## <a name="next-steps"></a>Nästa steg

- Skapa din första virtuella nätverk och ansluta ett fåtal virtuella datorer, genom att slutföra stegen i den [skapa din första virtuella nätverket](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- Ansluta datorn till ett virtuellt nätverk genom att slutföra stegen i den [konfigurerar en punkt-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
- Belastningsutjämna Internet-trafik till offentliga servrar genom att slutföra stegen i den [skapa en Internetriktade belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artikel.
