---
title: "Azure nätverkssäkerhet | Microsoft Docs"
description: "Läs mer om molnbaserad databearbetning tjänster som omfattar ett brett urval av compute-instanser och tjänster som kan skalas upp och ned automatiskt så att den passar ditt program-eller enterprise."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f0cc1716daa70bf7c860373819568774cf6f95d9
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="azure-network-security"></a>Azure nätverkssäkerhet

Vi vet att säkerhet är ett jobb i molnet och det är viktigt att du hitta korrekt och rimlig information om säkerheten i Azure. Ett av de bästa skälen att använda Azure för dina program och tjänster är att dra nytta av Azures mängd säkerhetsverktyg och funktioner. Dessa verktyg och funktioner för att göra det möjligt att skapa säkra lösningar på Azure-plattformen.

Microsoft Azure tillhandahåller sekretess, integritet och tillgänglighet av kundinformation, samtidigt som transparent accountability. För att hjälpa dig att bättre förstå mängden nätverket säkerhetsåtgärder som implementerats i Microsoft Azure kundens ur skrivs den här artikeln ”nätverkssäkerhet för Azure”, till och ger en omfattande titt på nätverket säkerhetsåtgärder tillgängligt med Microsoft Azure.

Det här dokumentet är avsett att informera dig om mängd nätverkskontroller som du kan konfigurera för att förbättra säkerheten för de lösningar som du distribuerar i Azure. Om du är intresserad av vad Microsoft gör för att skydda nätverksinfrastruktur för själva Azure-plattformen finns i Azure-säkerhet i den [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/azure-security).

## <a name="azure-platform"></a>Azure-plattformen

Azure är en offentlig molntjänstplattform som har stöd för ett brett urval av operativsystem, programmeringsspråk, ramverk, verktyg, databaser, och enheter.  Det kan köras Linux behållare med Docker-integration. skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; build-servrar för iOS, Android och Windows enheter. Azure-molntjänster hantering av samma miljontals utvecklare och IT-proffs redan förlitar sig på och litar på.

När du bygger på, eller migrera IT tillgångar, en offentlig molntjänstleverantör, måste den organisationens förmåga att skydda dina program och data med tjänster och de kontroller som de tillhandahåller för att hantera säkerheten för din molnbaserade tillgångar.

Azures infrastruktur från funktionen är utformad för att program som värd för miljontals kunder samtidigt och ger en säker grund som företag kan uppfylla sina säkerhetskrav. Dessutom ger Azure dig en omfattande uppsättning konfigurerbara säkerhetsalternativ och möjlighet att styra dem så att du kan anpassa säkerhet för att uppfylla de specifika behoven för din organisation distributioner.

## <a name="abstract"></a>Abstrakt

Microsofts offentliga molntjänster leverera storskaliga tjänster och infrastruktur, funktioner i företagsklass och många alternativ för hybridanslutning. Du kan välja att komma åt dessa tjänster via Internet eller med Azure ExpressRoute som tillhandahåller anslutningar privata nätverk. Microsoft Azure-plattformen kan du utöka din infrastruktur i molnet och skapa flera nivåer arkitekturer sömlöst. Tredje part kan dessutom aktivera förbättrade funktioner genom att erbjuda tjänster och virtuella installationer.

Azures nätverkstjänster maximera flexibilitet, tillgänglighet, återhämtning, säkerhet och integritet avsiktligt. I det här dokumentet innehåller information om nätverk funktionerna i Azure och information om hur kunder kan använda Azures inbyggda funktioner för att skydda sina informationstillgångar.

Avsedda målgrupper för den här rapporten innehåller:

- Tekniska chefer, administratörer och utvecklare som söker efter säkerhetslösningar tillgängliga och stöds i Azure.

-   Små och medelstora företag eller business process chefer som vill få en överblick på Azure nätverkstekniker och tjänster som är relevanta i kring nätverkssäkerhet i det offentliga Azure-molnet.

## <a name="azure-networking-big-picture"></a>Azure nätverk översikt
Microsoft Azure innehåller en stabil nätverksinfrastruktur för att stödja dina program och tjänsten anslutningskrav. Nätverksanslutningen är möjlig mellan resurser i Azure, mellan lokala och Azure värdbaserade resurser, och till och från Internet och Azure.

![Azure nätverk översikt](media/azure-network-security/azure-network-security-fig-1.png)

Den [Azure nätverksinfrastruktur](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) kan du ansluta Azure-resurser på ett säkert sätt till varandra med virtuella nätverk (Vnet). Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk isolering av nätverket Azure-molnet dedikerad till din prenumeration. Du kan ansluta Vnet till ditt lokala nätverk.

Har stöd för Azure dedikerad WAN-länken anslutning till lokalt nätverk och ett Azure Virtual Network med [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Länken mellan Azure och din plats använder en dedikerad anslutning som inte går via det offentliga Internet. Om din Azure-program körs i flera datacenter, kan du använda [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) på vägen begäranden från användare intelligent i instanser av programmet. Du kan också dirigera trafik till tjänster inte körs i Azure om de är tillgängliga från Internet.

## <a name="enterprise-view-of-azure-networking-components"></a>Enterprise-vyn Azure nätverkskomponenter
Azure har många komponenter som är relevanta för network security diskussioner. Vi beskriver dessa nätverkskomponenter och fokusera på säkerhetsproblem som rör dem.

> [!Note]
> Beskrivs inte alla aspekter av Azure-nätverk – diskuterar vi endast de som anses vara spela en central i planering och utformning av en säker nätverksinfrastruktur runt dina tjänster och program som du distribuerar i Azure.

I det här dokumentet blir omfattar följande Azure nätverk enterprise-funktioner:

-   Grundläggande nätverksanslutning

-   Hybridanslutning

-   Säkerhetsåtgärder

-   Nätverksverifieringen

### <a name="basic-network-connectivity"></a>Grundläggande nätverksanslutning

Den [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) kan du ansluta Azure-resurser på ett säkert sätt till varandra med virtuella nätverk (VNet). Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk isolering av Azure nätverkets infrastruktur som är dedikerad till din prenumeration. Du kan också ansluta Vnet till varandra och till ditt lokala nätverk med plats-till-plats VPN och dedikerade [WAN-länkar](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Grundläggande nätverksanslutning](media/azure-network-security/azure-network-security-fig-2.png)

Frågan är medveten om att du använder virtuella datorer till värdservrar i Azure, hur dessa virtuella datorer ansluta till ett nätverk. Svaret är att virtuella datorer ansluta till en [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Virtuella Azure-nätverk är som den virtuella nätverk du använder lokala med din egen plattform virtualiseringslösningar, till exempel Microsoft Hyper-V- eller VMware.

#### <a name="intra-vnet-connectivity"></a>Intra-VNet-anslutningar

Du kan ansluta Vnet till varandra, aktivera resurser som är anslutna till något virtuellt nätverk att kommunicera med varandra via Vnet. Du kan använda ett eller båda av följande alternativ för att ansluta Vnet till varandra:

- **Peering:** gör att resurser som anslutits till olika virtuella Azure-nätverk inom samma Azure-plats att kommunicera med varandra. Bandbredd och fördröjning mellan VNet är samma som resurserna som är anslutna till samma virtuella nätverk. Mer information om peering [virtuella nätverk peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Peering](media/azure-network-security/azure-network-security-fig-3.png)

- **VNet-till-VNet-anslutningen:** gör att resurser som anslutits till olika Azure-VNet i samma eller olika Azure platser. Till skillnad från peering, är bandbredd eftersom begränsade mellan Vnet trafik måste gå genom en Azure VPN-Gateway.

![VNet-till-VNet-anslutningen](media/azure-network-security/azure-network-security-fig-4.png)


Mer information om hur du ansluter Vnet med ett VNet-till-VNet-anslutningen i [konfigurera en anslutning VNet-till-VNet-artikel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Funktioner för virtuella Azure-nätverket:

Som du ser innehåller ett Azure Virtual Network virtuella datorer kan ansluta till nätverket så att de kan ansluta till andra nätverksresurser på ett säkert sätt. Grundläggande anslutningsmöjligheter är dock bara början. Följande funktioner i tjänsten Azure Virtual Network exponera säkerhetsegenskaper för det virtuella Azure-nätverket:

-   Isolering

-   Internetanslutning

-   Azure-resurs-anslutning

-   VNet-anslutningar

-   Lokala anslutningsmöjligheter

-   Trafikfiltrering

-   Routning

**Isolering**

Vnet är [isolerade](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) från varandra. Du kan skapa separata Vnet för utveckling, testning och produktion som använder samma [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) adressen block. Däremot kan du skapa flera virtuella nätverk som använder olika CIDR-Adressblock och ansluta nätverk tillsammans. Du kan dela ett VNet i flera undernät.

Azure erbjuder intern namnmatchning för virtuella datorer och [molntjänster](https://azure.microsoft.com/services/cloud-services/) rollinstanser som är ansluten till ett virtuellt nätverk. Du kan också konfigurera ett virtuellt nätverk för att använda dina egna DNS-servrar i stället för intern namnmatchning för Azure.

Du kan implementera flera Vnet i varje Azure [prenumeration](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) och Azure [region](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Varje virtuellt nätverk är isolerad från andra Vnet. För varje virtuellt nätverk kan du:

-   Ange en egen privata IP-adressutrymmet med hjälp av offentliga och privata (RFC 1918)-adresser. Azure tilldelar resurser anslutna till VNet en privat IP-adress från adressutrymmet kan du allokera.

-   Segmentera VNet i en eller flera undernät och tilldela en del av VNet-adressutrymmet för varje undernät.

-   Använd Azure-tillhandahållna namnmatchning eller ange egna DNS-server för användning av resurser som är anslutna till ett virtuellt nätverk. Mer information om namnmatchning i Vnet i [namnmatchning för virtuella datorer och molntjänster](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Internetanslutning**

Alla [Azure virtuella datorer (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) och molntjänster rollinstanser som är ansluten till ett virtuellt nätverk har åtkomst till Internet, som standard. Du kan också aktivera inkommande åtkomst till specifika resurser efter behov. (VM) och molntjänster rollinstanser som är ansluten till ett virtuellt nätverk har åtkomst till Internet, som standard. Du kan också aktivera inkommande åtkomst till specifika resurser efter behov.

Alla resurser som är ansluten till ett virtuellt nätverk har utgående anslutning till Internet som standard. Privata IP-adressen för resursen är källan nätverksadress översättas (SNAT) till en offentlig IP-adress av Azure-infrastrukturen. Du kan ändra standard anslutningen genom att implementera anpassad Routning och filtrera trafik. Om du vill veta mer om utgående Internetanslutning kan du läsa den [förstå utgående anslutningar i Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Att kommunicera inkommande Azure-resurser från Internet eller kommunicera utgående till Internet utan SNAT tilldelas en resurs en offentlig IP-adress. Mer information om den offentliga IP-adresser i [offentliga IP-adresser](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Azure-resurs-anslutning**

[Azure-resurser](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) som molntjänster och virtuella datorer kan anslutas till samma virtuella nätverk. Resurserna kan ansluta till varandra med privata IP-adresser, även om de finns i olika undernät. Azure tillhandahåller standardroutning mellan undernät, virtuella nätverk och lokala nätverk, så du behöver att konfigurera och hantera vägar.

Du kan ansluta flera Azure-resurser till ett virtuellt nätverk, till exempel virtuella datorer (VM), Cloud Services, Apptjänstmiljöer och Skalningsuppsättningar i virtuella datorer. Virtuella datorer ansluta till ett undernät i ett virtuellt nätverk via ett nätverksgränssnitt (NIC). Mer information om nätverkskort på [nätverksgränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**VNet-anslutningar**

[Vnet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) kan anslutas till varandra, aktivera resurser som är anslutna till alla virtuella nätverk för att kommunicera med alla resurser för andra virtuella nätverk.

Du kan ansluta Vnet till varandra, aktivera resurser som är anslutna till något virtuellt nätverk att kommunicera med varandra via Vnet. Du kan använda ett eller båda av följande alternativ för att ansluta Vnet till varandra:

- **Peering:** gör att resurser som anslutits till olika virtuella Azure-nätverk inom samma Azure-plats att kommunicera med varandra. Bandbredd och fördröjning mellan till Vnet är samma som om resurserna som är anslutna till samma VNet.To Lär dig mer om peering kan läsa den [virtuella nätverk peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **VNet-till-VNet-anslutningen:** gör att resurser som anslutits till olika Azure-VNet i samma eller olika Azure platser. Till skillnad från peering, är bandbredd eftersom begränsade mellan Vnet trafik måste gå genom en Azure VPN-Gateway. Mer information om hur du ansluter Vnet med en VNet-till-VNet-anslutning. Mer information finns i [konfigurera VNet-till-VNet-anslutningen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Lokala anslutningsmöjligheter**

Vnet kan anslutas till [lokalt](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) nätverk via privata nätverksanslutningar mellan ditt nätverk och Azure, eller en plats-till-plats VPN-anslutning via Internet.

Du kan ansluta dina lokala nätverk till ett virtuellt nätverk med hjälp av följande alternativ:

- **Punkt-till-plats virtuellt privat nätverk (VPN):** upprättas mellan en enda dator som är ansluten till nätverket och VNet. Den här anslutningen är bra om du precis har börjat med Azure, eller för utvecklare, eftersom det krävs lite eller ingen ändringar i ditt befintliga nätverk. Anslutningen använder SSTP-protokollet för att tillhandahålla krypterad kommunikation via Internet mellan datorn och VNet. Svarstiden för en punkt-till-plats-VPN är oförutsägbart eftersom trafiken färdas genom Internet.

- **Plats-till-plats-VPN:** mellan din VPN-enhet och en Azure VPN-Gateway. Den här anslutningstypen gör att alla lokala resurser som du har behörighet att komma åt ett VNet. Anslutningen är en IPsec/IKE-VPN som tillhandahåller krypterad kommunikation via Internet mellan din lokala enhet och Azure VPN-gatewayen. Svarstiden för en plats-till-plats-anslutning är oförutsägbart eftersom trafiken färdas genom Internet.

- **Azure ExpressRoute:** upprättas mellan ditt nätverk och Azure, via en ExpressRoute-partner. Den här anslutningen är privat. Trafik inte går över Internet. Svarstid för en ExpressRoute-anslutning är förutsägbar eftersom trafiken inte sker via Internet. Mer information om alla tidigare anslutningsalternativ den [anslutning Topologidiagram](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Trafikfiltrering**

Molntjänster och Virtuella rollinstanser [nätverkstrafik](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) kan filtreras inkommande och utgående efter källans IP-adress och port, mål-IP-adress och port och protokoll.

Du kan filtrera nätverkstrafiken mellan undernät med ett eller båda av följande alternativ:

- **Nätverkssäkerhetsgrupper (NSG):** varje NSG kan innehålla flera inkommande och utgående säkerhetsregler som gör det möjligt att filtrera trafik genom käll- och IP-adress, port och protokoll. Du kan använda en NSG för varje nätverkskort på en virtuell dator. Du kan också använda en NSG till undernätet för ett nätverkskort eller andra Azure-resurs är ansluten till. Mer information om NSG: er i [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Virtuella nätverksinstallationer:** en virtuell nätverksenhet är en virtuell dator kör programvara som utför en funktion i nätverket, till exempel en brandvägg. Visa en lista över tillgängliga NVAs i Azure Marketplace. NVAs är också tillgängliga som ger WAN-optimering och andra nätverksenheter trafik funktioner. NVAs används vanligtvis med användardefinierade eller BGP-vägar. Du kan också använda en NVA för att filtrera trafik mellan virtuella nätverk.

**Routning**

Alternativt kan du åsidosätta Azures standard routning genom att konfigurera egna vägar eller använda BGP-vägar via en nätverksgateway.

Azure skapar routningstabeller som gör att resurser som är anslutna till alla undernät i alla virtuella nätverk för att kommunicera med varandra som standard. Du kan implementera ett eller båda av följande alternativ för att åsidosätta standardvägar Azure skapar:

- **Användardefinierade vägar:** kan du skapa anpassade routningstabeller med vägar som styr där trafik dirigeras till för varje undernät. Mer information om användardefinierade vägar i [användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **BGP-vägar:** om du ansluter ditt VNet till ditt lokala nätverk med hjälp av en Azure VPN-Gateway eller ExpressRoute-anslutning du sprida BGP-vägar till din Vnet.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hybrid Internetanslutning: ansluta till ett lokalt nätverk
Du kan ansluta dina lokala nätverk till ett virtuellt nätverk med hjälp av följande alternativ:

-   Internetanslutning

-   Punkt-till-plats-VPN (P2S VPN)

-   VPN för plats-till-plats (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Internet-anslutning

Som namnet antyder gör Internetanslutning dina arbetsbelastningar tillgänglig från Internet, genom att låta du exponera olika offentliga slutpunkter för arbetsbelastningar som är live i det virtuella nätverket. Dessa arbetsbelastningar kan exponeras med hjälp av [Internetriktade belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) eller bara tilldela en offentlig IP-adress till den virtuella datorn. På så sätt kan blir det möjligt för vad som helst på Internet för att kunna nå den virtuella datorn som en värdbrandvägg [nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), och [användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) tillåter att inträffa.

I det här scenariot kan du exponera ett program som måste vara offentlig till Internet och att kunna ansluta till den från var som helst, eller från specifika platser, beroende på konfigurationen av din arbetsbelastning.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Punkt-till-plats VPN eller VPN för plats-till-plats
Dessa två hamnar i samma kategori. De båda behöver ditt VNet att ha en VPN-Gateway och du kan ansluta till den med hjälp av antingen en VPN-klient till din arbetsstation som en del av den [punkt-till-plats configuration](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) eller så kan du konfigurera dina lokala [VPN-enhet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)för att kunna avsluta en plats-till-plats-VPN. Det här sättet kan lokala enheter ansluta till resurser inom VNet.

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. P2S är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol).

![Punkt-till-plats VPN](media/azure-network-security/azure-network-security-fig-5.png)

Punkt-till-plats-anslutningar är användbara när du vill ansluta till ditt VNet från en fjärrdator som hemifrån eller ett konferensrum center eller när du har bara ett fåtal klienter som ska ansluta till ett virtuellt nätverk.

P2S-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. Du upprättar VPN-anslutningen från klientdatorn. Därför rekommenderas inte P2S sätt att ansluta till Azure om du behöver en beständig anslutning från många lokala enheter och datorer till Azure-nätverk.

![Plats-till-plats VPN](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Mer information om anslutningar för punkt-till-plats finns i [punkt-till-plats MI v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2).

Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Den här anslutningen sker via Internet och kan du ”tunnel” information i en krypterad anslutning mellan ditt nätverk och Azure. Plats-till-plats VPN är en säker, mogen teknik som har distribuerats av företag i alla storlekar för åren. Tunnel kryptering utförs med [IPSec-tunnelläge](https://technet.microsoft.com/library/cc786385.aspx).

Plats-till-plats VPN är en betrodd, tillförlitlig och etablerade teknik, trafik i tunneln sker via Internet. Dessutom begränsad bandbredd relativt till högst 200 Mbit/s.

Om du behöver en särskilda nivå av säkerhet och prestanda för anslutningar mellan platser, rekommenderar vi att du använder Azure ExpressRoute för dina korsanslutningar. ExpressRoute är en fast WAN länken mellan din lokala plats eller en värdleverantör för Exchange. Eftersom detta är en anger anslutning är data överföras inte via Internet och därför visas inte för de potentiella riskerna som Internet-kommunikation.

> [!Note]
> Mer information om VPN-gatewayer finns [VPN-gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Fast WAN-länk
Microsoft Azure ExpressRoute kan du utöka ditt lokala nätverk till Azure via en dedikerad privata anslutning underlättas av en provider för anslutningen.

ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.

![ Fast WAN-länk](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Mer information om hur du ansluter nätverket till Microsoft med hjälp av ExpressRoute finns [ExpressRoute anslutning modeller](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) och [teknisk översikt för ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Som med alternativen för plats-till-plats-VPN-kan ExpressRoute du också att ansluta till resurser som inte nödvändigtvis i endast ett virtuellt nätverk. I själva verket beroende på SKU, kan du ansluta till 10 virtuella nätverk. Om du har den [premium tillägg](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), anslutningar till upp till 100 Vnet är möjligt, beroende på bandbredd. Mer information om dessa typer av anslutningar Sök som [anslutning Topologidiagram](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Säkerhetsåtgärder
Ett Azure Virtual Network ger en säker, logiska nätverk som är isolerad från andra virtuella nätverk och stöder många säkerhetsåtgärder som du använder på ditt lokala nätverk. Kunderna skapa sina egna struktur med hjälp av: undernät – de använda sina egna privata IP-adressintervall, konfigurera vägtabeller, nätverkssäkerhetsgrupper, åtkomstkontrollistor (ACL), gateways och virtuella installationer att köra deras arbetsbelastningar i molnet.

Följande är säkerhetsåtgärder som du kan använda i dina virtuella Azure-nätverk:

-   Åtkomstkontroller för nätverk

-   Användardefinierade vägar

-   Säkerhet för nätverksenhet

-   Application Gateway

-   Brandvägg för Azure webbaserade program

-   Kontrollen av nätverkets tillgänglighet

#### <a name="network-access-controls"></a>Åtkomstkontroller för nätverk
Medan Azure Virtual Network (VNet) är en hörnsten i modellen för Azure nätverk och ger isolering och skydd av [Nätverkssäkerhetsgrupp grupper (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) är det viktigaste verktyget som du använder för att tillämpa och styra regler för nätverkstrafik på de nivå.

![ Åtkomstkontroller för nätverk](media/azure-network-security/azure-network-security-fig-8.png)


Du kan styra åtkomsten genom att tillåta eller neka kommunikation mellan arbetsbelastningar inom ett virtuellt nätverk från system i kundens nätverk via anslutning, eller dirigera Internet-kommunikation.

I diagrammet finns både Vnet och NSG: er i ett visst lager i Azure övergripande security-stacken, där NSG: er, UDR och virtuella nätverksenheter kan användas för att skapa säkerhetsgränser för att skydda application-distributioner i det skyddade nätverket.

NSG: er använder 5-tuppel för att utvärdera trafik (och används i de regler som du konfigurerar för NSG: N):

-   [Käll- och IP-adress](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Käll- och port](https://technet.microsoft.com/library/dd197515)

-   Protokoll: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) eller [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Det innebär att du kan styra åtkomsten mellan en enda virtuell dator och en grupp virtuella datorer eller en enskild virtuell dator till en annan enskild virtuell dator eller hela undernät. Igen, Tänk på att det är enkelt tillståndskänslig paket filtrering, inte fullständig paketinspektion. Det finns ingen protokollet verifiering eller nätverksnivån ID: N eller kapaciteten för IP-adresser i en Nätverkssäkerhetsgrupp.

En NSG innehåller vissa inbyggda regler som du bör vara medveten om. Dessa är:

-   **All trafik i ett specifikt virtuellt nätverk:** alla virtuella datorer på samma virtuella Azure-nätverket kan kommunicera med varandra.

-   **Tillåt Azure belastningsutjämning för inkommande:** regeln tillåter trafik från alla Källadress alla måladresser för Azure belastningsutjämnaren.

-   **Neka alla inkommande:** regeln blockerar all trafik som källa från Internet som du uttryckligen tillåts.

-   **Tillåt all trafik utgående till Internet:** den här regeln kan virtuella datorer initiera anslutningar till Internet. Om du inte vill att dessa anslutningar som initierats måste du skapa en regel för att blockera anslutningarna eller tvinga tvingad tunneling.

#### <a name="system-routes-and-user-defined-routes"></a>Systemvägar och användardefinierade vägar

När du lägger till virtuella datorer (VM) till ett virtuellt nätverk (VNet) i Azure kan se du att de virtuella datorerna ska kunna kommunicera med varandra över nätverket, automatiskt. Du behöver inte ange någon gateway, även om VM:arna befinner sig på olika undernät.

Samma sak gäller för kommunikation från VM:arna till det offentliga Internet samt till ditt lokala nätverk när det finns en hybridanslutning från Azure till ditt eget datacenter.

![Systemvägar](media/azure-network-security/azure-network-security-fig-9.png)

Det här flödet av kommunikation är möjligt eftersom Azure använder en uppsättning systemvägar för att definiera hur IP-trafiken flödar. Systemvägar kontrollerar kommunikationsflödet i följande scenarier:

-   Från inom samma undernät.

-   Från ett undernät till ett annat inom ett VNet.

-   Från VM:ar till Internet.

-   Från ett VNet till ett annat VNet via en VPN-gateway.

-   Från ett VNet till ett annat VNet via VNet-Peering ([Service länkning](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Från ett VNet till ditt lokala nätverk via en VPN-gateway.

Många företag har strikt säkerhet och krav på lokal kontroll av alla nätverkspaket tillämpa särskilda principer. Azure tillhandahåller en mekanism som kallas [Tvingad tunneltrafik](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) som dirigerar trafik från de virtuella datorerna till lokalt genom att skapa en anpassad väg eller genom att [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) annonser via ExpressRoute eller VPN.

Tvingad tunneling i Azure konfigureras via virtuella nätverk användardefinierade vägar (UDR). Omdirigera trafik till en lokal plats uttrycks som en standardväg till Azure VPN-gatewayen.

Följande avsnitt innehåller aktuella begränsningen på Routning och vägar för ett Azure Virtual Network:

-   Varje undernät för virtuellt nätverk har en inbyggd, system-routningstabell. Routningstabellen för systemet har följande tre grupper av vägar:

 -  **Lokal VNet vägar:** direkt till målet virtuella datorer i samma virtuella nätverk

 - **På lokala vägar:** till Azure VPN-gateway

 -  **Standardvägen:** direkt till Internet. Paket avsedda för de privata IP-adresser som inte omfattas av föregående två vägar tas bort.

-   Med lanseringen av användardefinierade vägar, kan du skapa en routningstabell för att lägga till en standardväg och sedan associerar routningstabellen VNet-undernät för att aktivera Tvingad tunneltrafik på dessa undernät.

-   Du måste ange en ”standardwebbplats” bland de lokala mellan lokala platserna anslutna till det virtuella nätverket.

-   Tvingad tunneling måste vara kopplad till ett virtuellt nätverk som har en dynamisk routning VPN-gateway (inte en statisk gateway).

- ExpressRoute Tvingad tunneltrafik konfigureras inte via den här mekanismen, men har aktiverats genom att annonsera en standardväg via ExpressRoute BGP-peeringsessioner i stället.

> [!Note]
> Mer information finns i [ExpressRoute dokumentation](https://azure.microsoft.com/documentation/services/expressroute/) för mer information.

#### <a name="network-security-appliances"></a>Nätverksinstallationer för säkerhet
Medan Nätverkssäkerhetsgrupper och användardefinierade vägar kan tillhandahålla ett viss mått av nätverkssäkerhet vid nätverks- och lager i den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), kommer det att finnas situationer där du vill eller måste du aktivera säkerhet på högre nivåer i nätverksstacken. I sådana situationer rekommenderar vi att du distribuerar virtuella nätverk säkerhetsenheter som tillhandahålls av Azure partners.

![Nätverksinstallationer för säkerhet](./media/azure-network-security/azure-network-security-fig-10.png)

Azure security nätverksinstallationer förbättra VNet säkerhets- och nätverksfunktioner och de är tillgängliga från flera leverantörer via den [Azure Marketplace](https://azuremarketplace.microsoft.com). Dessa virtuella säkerhetsenheter kan distribueras för att tillhandahålla:

-   Hög tillgänglighet brandväggar

-   Intrångsskydd

-   Intrångsidentifiering

-   Web application brandväggar (WAFs)

-   WAN-optimering

-   Routning

-   Belastningsutjämning

-   VPN

-   Certifikathantering

-   Active Directory

-   Multifaktorautentisering

#### <a name="application-gateway"></a>Programgateway

[Microsoft Azure Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) är en dedikerad virtuell installation som ger en programmet leverans styrenhet (ADC) som en tjänst.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Programgateway kan du optimera web servergruppen prestanda och tillgänglighet genom att avlasta CPU beräkningsintensiva SSL-avslutning av programgatewayen (SSL-avlastning). Det finns även andra layer 7 funktioner inklusive:

-   Resursallokering för inkommande trafik

-   Cookie-baserad session tillhörighet

-   URL-sökväg-baserad Routning

-   Möjligheten att vara värd för flera webbplatser bakom en enda Programgateway


En [Brandvägg för webbaserade program (Brandvägg)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) ges också som en del av programgatewayen. Detta ger skydd till webbprogram från vanliga web säkerhetsrisker och trojaner. Programgateway kan konfigureras som en Internetuppkopplad gateway, interna endast gateway eller en kombination av båda.

Programmet Gateway Brandvägg kan köras i läget identifiering eller förebyggande. Det är ett vanliga användningsfall för administratörer att identifiera läget Se trafik för skadliga mönster. När potentiella trojaner upptäcks till förebyggande läge stängs blockerar misstänkta inkommande trafik.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Dessutom kan programmet Gateway Brandvägg hjälper dig att övervaka webbprogram mot attacker med hjälp av en Brandvägg realtidsloggen som är integrerad med [Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) och [Azure Security Center](https://azure.microsoft.com/services/security-center/) att spåra Brandvägg aviseringar och enkelt kan övervaka trender.

Loggen för JSON-formaterad går direkt till kundens lagringskonto. Du har fullständig kontroll över dessa loggar och kan tillämpa en egen bevarandeprinciper.

Du kan också mata in dessa loggar i din egen analytics system med hjälp av [Azure Log-integrering](https://aka.ms/AzLog). Brandvägg loggar också är integrerade med [Operations Management Suite (OMS)](https://www.microsoft.com/cloud-platform/operations-management-suite) så att du kan använda OMS logganalys för att utföra avancerade detaljerade frågor.

#### <a name="azure-web-application-firewall-waf"></a>Azure web application firewall (Brandvägg)

Webbprogram allt är riktad mot angrepp som utnyttjar vanliga kända sårbarheter, till exempel SQL injection mellan platsen skriptmiljö attacker och andra attacker som visas i den [OWASP topp 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Förhindra sådan kryphål i programmet kräver rigorösa underhåll, uppdatering och övervakning av flera lager i Programtopologi.

 ![Azure Web Application Firewall (Brandvägg)](./media/azure-network-security/azure-network-security-fig-13.png)

En centraliserad [Brandvägg för webbaserade program (Brandvägg)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) kan skydda mot webbattacker och säkerhetshanteringen utan några ändringar i programmet.

En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

#### <a name="network-availability-controls"></a>Nätverkskontroller tillgänglighet

Det finns olika alternativ för att distribuera nätverkstrafik med Microsoft Azure. De här alternativen fungerar på olika sätt, har olika funktionsuppsättningar och stöder olika scenarier. De kan användas var för sig eller i kombination med varandra.

Följande är tillgänglighet nätverkskontroller:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Azure belastningsutjämnare**

Ger hög tillgänglighet och nätverkets prestanda för dina program. Det är en belastningsutjämnare för lager 4 (TCP, UDP) som distribuerar inkommande trafik mellan felfri instanser av tjänster som definierats i en belastningsutjämnad uppsättning.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Azure belastningsutjämnare kan konfigureras för att:

-   Läsa in Utjämna inkommande Internet-trafiken till virtuella datorer. Den här konfigurationen kallas [mot Internet för belastningsutjämning](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Läs in Utjämna trafiken mellan virtuella datorer i ett virtuellt nätverk, mellan virtuella datorer i molntjänster eller mellan lokala datorer och virtuella datorer i ett virtuellt nätverk mellan platser. Den här konfigurationen kallas [intern belastningsutjämning](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Vidarebefordra externa trafik till en specifik virtuell dator.

Alla resurser i molnet måste en offentlig IP-adress ska kunna nås från Internet. Moln-infrastruktur i Azure använder icke-dirigerbara IP-adresser för dess resurser. Azure använder network address translation (NAT) med offentliga IP-adresser för att kommunicera med Internet.

 **Programgateway**

 Programgateway fungerar på programnivå (Layer 7 i OSI-referens nätverksstacken). Den fungerar som en omvänd proxy-tjänst och avslutar klientanslutningen och vidarebefordrar begäranden till slutpunkter i serverdelen.

 **Traffic manager**

Microsoft Azure Traffic Manager kan du styra distributionen av användartrafik för slutpunkter i olika datacenter. Service-slutpunkter som stöds av Traffic Manager omfattar virtuella Azure-datorer Web Apps och molntjänster. Du kan även använda Traffic Manager med externa slutpunkter som inte tillhör Azure.

Traffic Manager använder Domain Name System (DNS) för att dirigera klientbegäranden till den lämpligaste slutpunkten baserat på en [routning av nätverkstrafik metoden](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) och hälsotillståndet för slutpunkter. Traffic Manager erbjuder en uppsättning routning av nätverkstrafik metoder efter programbehov för olika, endpoint hälsa [övervakning](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring), och automatisk redundans. Traffic Manager är känsligt för fel, inklusive fel på en hel Azure-region.

Azure Traffic Manager kan du styra distributionen av trafik över dina slutpunkter för programmet. En slutpunkt är alla mot Internet-tjänst som finns i eller utanför Azure.

Traffic Manager har två viktiga fördelar:

-   Fördelning av trafik enligt ett av flera [routning av nätverkstrafik metoder](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Kontinuerlig övervakning av hälsotillstånd för slutpunkten](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) och automatisk redundans när slutpunkter misslyckas.

När en klient försöker ansluta till en tjänst, måste det först matcha DNS-namn för tjänsten till en IP-adress. Klienten ansluter sedan till att IP-adress för att komma åt tjänsten. Traffic Manager använder DNS för att dirigera klienterna till specifika slutpunkter baserat på regler för routning av nätverkstrafik-metoden. Klienterna ansluter direkt till den valda slutpunkten. Traffic Manager är inte en proxy eller en gateway. Traffic Manager kan inte se trafik som skickas mellan klienten och tjänsten.

### <a name="azure-network-validation"></a>Validering av Azure-nätverk

Azure nätverksverifieringen är att säkerställa att Azure-nätverk fungerar eftersom den är konfigurerad och verifiering kan utföras med hjälp av tjänster och funktioner som är tillgängliga för att övervaka nätverket. Med Azure Nätverksbevakaren du har åtkomst till en mängd olika loggning och diagnostik funktioner som kan ge insikter att förstå nätverksprestanda- och hälsa. Dessa funktioner är tillgängliga via portalen, Power Shell, CLI, Rest-API och SDK.

Azure operativ säkerhet refererar till tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure. Azure operativ säkerhet bygger på ett ramverk som innehåller kunskap via en olika funktioner som är unika för Microsoft, inklusive den Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-program , och djup medvetenhet om cyber security hotbilden i grunden.

-   [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure nätverksbevakaren](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure resource manager

Personer och processer som fungerar Microsoft Azure är kanske de viktigaste säkerhetsfunktionen i plattformen. Det här avsnittet beskrivs funktionerna i Microsofts globala infrastruktur underlätta och upprätthålla säkerheten, verksamhetskontinuitet och sekretess.

Infrastrukturen för ditt program består normalt av många komponenter – kanske en virtuell dator, storage-konto och virtuella nätverk eller en webbapp, databas, databasserver och tjänster från tredje part. Du ser inte de här komponenterna som separata entiteter, utan som relaterade delar av samma enhet som är beroende av varandra. Du vill distribuera, hantera och övervaka dem som en grupp. Med Azure Resource Manager kan du arbeta med resurserna i en lösning som en grupp.

Du kan distribuera, uppdatera eller ta bort alla resurser i lösningen i en enda, samordnad åtgärd. Du använder en mall för distributionen. Mallen kan användas i olika miljöer, till exempel för testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper dig att hantera dina resurser efter distributionen.

**Fördelarna med att använda Resource Manager**

Resource Manager har flera fördelar:

-   Du kan distribuera, hantera och övervaka alla resurserna för din lösning som en grupp i stället för att hantera resurserna separat.

-   Du kan distribuera lösningen flera gånger genom utvecklingslivscykeln och vara säker på att dina resurser distribueras på ett enhetligt sätt.

-   Du kan hantera infrastrukturen med hjälp av deklarativa mallar i stället för skript.

-   Du kan definiera beroenden mellan resurser, så att de distribueras i rätt ordning.

-   Du kan använda åtkomstkontroll för alla tjänster i resursgruppen eftersom rollbaserad åtkomstkontroll (RBAC) är inbyggt i hanteringsplattformen.

-   Du kan lägga till taggar för resurser och organisera alla logiskt i din prenumeration.

-   Du kan tydliggöra fakturering för din organisation genom att visa kostnaderna för en grupp med resurser som delar taggen.

> [!Note]
> Resource Manager erbjuder ett nytt sätt att distribuera och hantera lösningar. Om du använde den tidigare distributionsmodellen och vill veta mer om ändringarna läser du [Förstå Resource Manager-distribution och klassisk distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Azure-nätverk loggning och övervakning

Azure erbjuder många verktyg för att övervaka, förebygga, upptäcka och åtgärda säkerhetshändelser i nätverket. Några av de viktigaste verktygen som är tillgängliga i det här området är:

-   Network Watcher

-   Resursen nivån nätverksövervakning

-   Log Analytics

### <a name="network-watcher"></a>Nätverksbevakaren

[Nätverk Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -scenariot-baserad övervakning tillhandahålls med funktionerna i Nätverksbevakaren. Den här tjänsten innehåller paketinsamling, nästa hopp, IP-flöde Kontrollera NSG flödet loggar i gruppvyn säkerhet. Scenariot nivån övervakning innehåller en heltäckande vy av nätverksresurser i stället för enskilda resurs nätverksövervakning.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Nätverksbevakaren är en regionala tjänst som gör att du kan övervaka och diagnostisera villkor på nätverket scenariot nivå, till och från Azure. Diagnostiska nätverks- och visualiseringsverktyg som finns tillgängliga med Nätverksbevakaren hjälpa dig att förstå, diagnostisera och få insyn i nätverket i Azure.

Nätverksbevakaren har för närvarande följande funktioner:

#### <a name="topology"></a>topologi

[Topologi](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) returnerar ett diagram över nätverksresurser i ett virtuellt nätverk. Diagrammet visar sambandet mellan resurser för att representera nätverksanslutning för slutpunkt till slutpunkt. I portalen returnerar topologi resursobjekt på enligt grunden för virtuella nätverk. Relationerna illustreras med linjer mellan resurser utanför Nätverksbevakaren region, även om resursen gruppen inte visas. Resurser som returneras i vyn portal är en delmängd av nätverkskomponenter som är visas i diagram registreringen. Om du vill se en fullständig lista över nätverksresurser, kan du använda [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) eller [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Som resurser returneras modelleras anslutningen mellan de under två relationer.

- **Inneslutning** -virtuellt nätverk innehåller ett undernät som innehåller ett nätverkskort.

- **Associerade** -A NIC som är kopplad till en virtuell dator.

#### <a name="variable-packet-capture"></a>Variabeln paketinsamling

Nätverk Watcher [variabeln paketinsamling](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) kan du skapa paket avbilda sessioner för att spåra trafik till och från en virtuell dator. Paketet avbilda bidrar till att diagnostisera nätverk avvikelser båda reaktivt och proactivity. Andra användningsområden omfattar att samla in nätverksstatistik får information om nätverket intrång felsöka klient-/ serverkommunikation och mycket mer.

Paketinsamling är ett tillägg för virtuell dator som startas från en fjärrdator via Nätverksbevakaren. Den här funktionen underlättar för att köra en paketinsamling manuellt på den önskade virtuella datorn, vilket sparar värdefull tid. Paketinsamling kan aktiveras via portalen, PowerShell, CLI eller REST API. Ett exempel på hur paketinsamling kan utlösas är med virtuella aviseringar.

#### <a name="ip-flow-verify"></a>Kontrollera IP-flöde

[Kontrollera IP-flöden](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) kontrollerar om ett paket tillåts eller nekas till eller från en virtuell dator baserat på 5-tuppel information. Den här informationen består av riktning, protokoll, lokal IP-, fjärr-IP, lokal port och Fjärrport. Om paketet nekas av en säkerhetsgrupp, returneras namnet på regeln som nekats paketet. När alla käll- eller IP-adresser kan väljas hjälper administratörer att snabbt diagnostisera problem med nätverksanslutningen från eller till internet och från eller till den lokala miljön i den här funktionen.

IP-flöden Kontrollera riktar sig till ett nätverksgränssnitt för en virtuell dator. Trafikflöde verifieras sedan baserat på de konfigurerade inställningarna till eller från nätverksgränssnittet. Den här funktionen är användbar för att bekräfta om en regel i en Nätverkssäkerhetsgrupp blockerar inkommande trafik eller utgående trafik till eller från en virtuell dator.

#### <a name="next-hop"></a>Nästa hopp

Anger den [nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) för paket som vidarebefordras i Azure nätverksinfrastruktur, vilket gör det lättare att diagnostisera eventuella felkonfigurerat användardefinierade vägar. Trafik från en virtuell dator som skickas till ett mål som är baserat på de effektiva vägar som är associerad med ett nätverkskort. Nästa hopp hämtar nästa hopptyp och IP-adressen för ett paket från en specifik virtuell dator och nätverkskort. Detta hjälper dig för att avgöra om paketet omdirigeras till målet eller är trafik som svart holed.

Nästa hopp returnerar också routningstabellen som är associerad med nästa hopp. När du frågar ett nexthop om vägen definieras som en användardefinierad väg, returneras den rutten. Annars returnerar nästa hopp ”Systemväg”.

#### <a name="security-group-view"></a>gruppvy för säkerhet

Hämtar de effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator. Nätverkssäkerhetsgrupper associeras på en undernätverksnivå eller på en NIC-nivå. Som är associerad till en undernätverksnivå, gäller alla VM-instanser i undernätet. Nätverket [säkerhetsgrupp visa](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) returnerar alla konfigurerade NSG: er och regler som är associerade på ett nätverkskort och undernät nivå för en virtuell dator som ger inblick i konfigurationen. Dessutom returneras effektiva säkerhetsregler för varje nätverkskort i en virtuell dator. Med hjälp av Nätverkssäkerhetsgruppen vyn du utvärdera en virtuell dator för säkerhetsrisker i nätverket, till exempel öppna portar. Du kan också verifiera om säkerhetsgrupp för nätverk fungerar som förväntat baserat på en [jämförelse mellan den konfigurerade och effektiva säkerhetsregler](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>NSG flöda loggning

 Loggarna flöde Nätverkssäkerhetsgrupper kan du samla in loggar som rör trafik som tillåts eller nekas av säkerhetsregler i gruppen. Flödet definieras av en 5-tuppel information – käll-IP, mål-IP, källport, målport och protokoll.

[Nätverkssäkerhetsgruppen flöde loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) är en funktion i Nätverksbevakaren där du kan visa information om ingående och utgående IP-trafik via en Nätverkssäkerhetsgrupp.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Virtuella nätverksgatewayen och anslutningen felsökning

Nätverksbevakaren innehåller många funktioner relateras till att förstå nätverksresurserna i Azure. En av dessa funktioner är resurs felsökning. [Felsökning av resursen](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) kan anropas av PowerShell, CLI eller REST API. När den anropas, Nätverksbevakaren kontrollerar hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar resultatet.

Det här avsnittet tar dig igenom de olika administrativa uppgifter som är tillgängliga för felsökning av resursen.

-   [Felsöka en virtuell nätverksgateway](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Felsöka en anslutning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Nätverket prenumerationsbegränsningar

[Nätverk prenumerationsbegränsningar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) ger dig information om användning av varje nätverksresurs i en prenumeration i en region mot det maximala antalet tillgängliga resurser.

#### <a name="configuring-diagnostics-log"></a>Konfigurera diagnostik logg

Nätverksbevakaren ger en [diagnostikloggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) vyn. Den här vyn innehåller alla nätverksresurser som stöder diagnostikloggning. I den här vyn kan du aktivera och inaktivera nätverksresurser lätt och snabbt.

### <a name="network-resource-level-monitoring"></a>Resursen nivån nätverksövervakning

Följande funktioner är tillgängliga för nivån Resursövervakning:

#### <a name="audit-log"></a>granskningslogg

Åtgärder som utförs som en del av konfigurationen av nätverk loggas. Dessa granskningsloggar är nödvändiga för att upprätta olika compliances. Dessa loggar kan visas i Azure-portalen eller hämtas med hjälp av Microsoft-verktyg, till exempel Power BI eller verktyg från tredje part. Granskningsloggar är tillgängliga via portalen, PowerShell, CLI och Rest-API.

> [!Note]
> Mer information om granskningsloggarna finns [granskningsåtgärder med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Granskningsloggar är tillgängliga för åtgärder som utförs på alla nätverksresurser.


#### <a name="metrics"></a>Mått

Mått är prestandamått och prestandaräknare som samlats in under en period. Mått är tillgängliga för Programgateway. Mått kan användas för att utlösa varningar baserat på tröskelvärdet. Azure Application Gateway som standard övervakar tillståndet för alla resurser i dess backend-pool och tar automatiskt bort alla resurser som bedöms som dåligt från poolen. Programgateway fortsätter att övervaka ohälsosamt instanser och lägger till dem tillbaka till Felfri backend-poolen när de blir tillgängliga och svara på hälsoavsökningar. Programgateway skickar hälsoavsökning med samma port som har definierats i backend-HTTP-inställningar. Den här konfigurationen garanterar att sonden provar samma port som kunder använder skulle ansluta till serverdelen.

> [!Note]
> Se [Gateway Programdiagnostik](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) att visa hur mått kan användas för att skapa aviseringar.

#### <a name="diagnostic-logs"></a>Diagnostikloggar

Periodiska och eget initiativ händelser skapas av nätverksresurser och inloggad storage-konton, skickas till en Händelsehubb eller logganalys. Dessa loggar ger insikter om hälsotillståndet för en resurs. Dessa loggar kan visas i verktyg som Power BI och logganalys. Ta reda på hur du visar diagnostikloggar [logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Diagnostikloggar är tillgängliga för [belastningsutjämnaren](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), vägar och [Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Nätverksbevakaren ger diagnostikloggar vyn. Den här vyn innehåller alla nätverksresurser som stöder diagnostikloggning. I den här vyn kan du aktivera och inaktivera nätverksresurser lätt och snabbt.

### <a name="log-analytics"></a>Log Analytics

[Logga Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) är en tjänst i [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) som övervakar molnet och lokala miljöer för att upprätthålla sin tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor.

Logganalys erbjuder följande lösningar för att övervaka dina nätverk:

-   Network Performance Monitor (NPM)

-   Azure Application Gateway analytics

-   Azure Network Security Group analytics

#### <a name="network-performance-monitor-npm"></a>Prestandaövervakaren för nätverk (NPM)
Den [Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) hanteringslösning är ett nätverk övervakningslösning som övervakar hälsa, tillgänglighet och tillgänglighet av nätverk.

Används för att övervaka anslutningen mellan:

-   offentliga molnet och lokalt

-   datacenter och användarplatser (avdelningskontor)

-   undernät som är värd för olika nivåer av ett program med flera nivåer.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Azure-program gateway analyser i logganalys

Följande loggar stöds för Programgatewayer:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Följande mått stöds för Programgatewayer:

-   5 minuter genomflöde

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Analyser av gruppen för Azure-nätverk i logganalys

Följande loggar stöds för [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** innehåller poster för vilka NSG-regler tillämpas på virtuella datorer och instansen roller baserat på MAC-adress. Status för de här reglerna samlas var 60: e sekund.

- **NetworkSecurityGroupRuleCounter:** innehåller poster för hur många gånger varje NSG-regel används för att neka eller Tillåt trafiken.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om säkerheten genom att läsa in några av våra djupgående säkerhetsfrågor:

-   [Log Analytics för Nätverkssäkerhetsgrupper (NSG: er)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Nätverk innovationer som enheter avbrott i molnet](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: Nätverket växla programvara som stänger Microsoft globala molnet](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Hur Microsoft bygger dess snabb och tillförlitlig globalt nätverk](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Ljus upp nätverk](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
