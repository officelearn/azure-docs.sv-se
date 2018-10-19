---
title: Azure-nätverkssäkerhet | Microsoft Docs
description: Läs mer om molnbaserad databehandling tjänster som omfattar ett brett urval av beräkningsinstanser och tjänster som kan skalas upp och ned automatiskt för att uppfylla behoven i ditt program eller enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 1d94ac5f799fc4bad13ab6a5e97a225a7499380d
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405981"
---
# <a name="azure-network-security"></a>Azure-nätverkssäkerhet

Vi vet att säkerhet är ett jobb i molnet och hur viktigt det är att du hitta korrekt och snabb information om Azure-säkerhet. En av de bästa skälen att använda Azure för dina program och tjänster är att dra nytta av Azures mängd säkerhetsverktyg och-funktioner. Dessa verktyg och funktioner för att göra det möjligt att skapa säkra lösningar på Azure-plattformen.

Microsoft Azure tillhandahåller sekretess, integritet och tillgänglighet av kundinformation, samtidigt som transparent ansvarstagande. Som hjälper dig att bättre förstå insamling av nätverket säkerhetsåtgärder som införts i Microsoft Azure från kundens eget perspektiv, är den här artikeln, ”Azure Network Security” avsedd att tillhandahålla en omfattande titt på nätverket säkerhetskontroller tillgängligt med Microsoft Azure.

Det här dokumentet är avsedd att informera dig om det breda utbud av nätverkskontroller som du kan konfigurera för att förbättra säkerheten för de lösningar som du distribuerar i Azure. Om du vill veta vad Microsoft gör för att skydda nätverksresurserna på själva Azure-plattformen, se avsnittet Azure-säkerhet i den [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure).

## <a name="azure-platform"></a>Azure-plattformen

Azure är en offentlig molntjänstplattform som har stöd för ett brett urval av operativsystem, programmeringsspråk, ramverk, verktyg, databaser och enheter.  Det kan köra Linux-behållare med Docker-integrering skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; Bygg serverdelar för iOS, Android och Windows enheter. Azure-molntjänster stöd för samma teknik som miljontals utvecklare och IT-proffs redan använder och litar på.

När du bygger på, eller migrerar IT-tillgångar till en offentlig molntjänstleverantör du lita på den organisationens förmåga att skydda dina program och data med tjänsterna och de kontroller som de tillhandahåller för att hantera säkerheten för dina molnbaserade tillgångar.

Azures infrastruktur är utformad från anläggningen till program som värd för miljoner kunder samtidigt, och det ger en säker grund som företaget kan möta sina säkerhetskrav. Dessutom ger Azure dig en omfattande uppsättning konfigurerbara säkerhetsalternativ samt möjligheten att kontrollera dem så att du kan anpassa säkerheten för att uppfylla de specifika behoven för din organisations distributioner.

## <a name="abstract"></a>Abstrakt

Microsofts offentliga molntjänster leverera hyperskalade tjänster och infrastruktur, funktioner i företagsklass och många alternativ för hybridanslutning. Du kan välja att få åtkomst till dessa tjänster via Internet eller med Azure ExpressRoute, som tillhandahåller privata nätverksanslutningar. Microsoft Azure-plattformen kan du sömlöst kan utöka din infrastruktur till molnet och skapa flera nivåer arkitektur. Tredje part kan dessutom aktivera förbättrade funktioner genom att erbjuda tjänster och virtuella installationer.

Azures nätverkstjänster maximera flexibiliteten, tillgänglighet, återhämtning, säkerhet och integritet avsiktligt. Detta faktablad innehåller information om nätverket fungerar för Azure och information om hur kunder kan använda Azures interna säkerhetsfunktioner för att skydda sina informationstillgångar.

De avsedda målgrupperna för detta White Paper är:

- Tekniska chefer, administratörer och utvecklare som söker efter säkerhetslösningar tillgängliga och stöds i Azure.

-   Små och medelstora företag eller företag processen chefer som vill få en översikt på Azure nätverkstekniker och tjänster som är relevanta i runt nätverkssäkerhet i Azure offentligt moln.

## <a name="azure-networking-big-picture"></a>Azure networking helheten
Microsoft Azure innehåller en stabil nätverksinfrastruktur för att stödja din program- och anslutningskrav för tjänsten. Nätverksanslutningen är mellan resurser i Azure, mellan lokala och Azure-värdbaserade resurser, och till och från Internet och Azure.

![Azure Networking helheten](media/azure-network-security/azure-network-security-fig-1.png)

Den [Azure nätverksinfrastruktur](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) gör det möjligt att på ett säkert sätt ansluta Azure-resurser till varandra med virtuella nätverk (Vnet). Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk isolering för nätverket för Azure-molnet som är anpassad efter din prenumeration. Du kan ansluta virtuella nätverk till ditt lokala nätverk.

Azure har stöd för dedikerade WAN-länken anslutning till ditt lokala nätverk och Azure Virtual Network med [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Länken mellan Azure och din webbplats använder en dedikerad anslutning som inte går via det offentliga Internet. Om ditt Azure-program körs i flera datacenter, kan du använda [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) för begäranden från användare smart i instanser av programmet. Du kan också dirigera trafik till tjänster som inte körs i Azure om de är tillgänglig från Internet.

## <a name="enterprise-view-of-azure-networking-components"></a>Enterprise-vy över Azure nätverkskomponenter
Azure har många nätverkskomponenter som är relevanta för network security diskussioner. Vi beskriver dessa nätverkskomponenter och fokusera på säkerhetsproblem som rör dem.

> [!Note]
> Beskrivs inte alla aspekter av Azure-nätverk – diskuterar vi endast de som anses vara pivotal i planerar och utformar en säker nätverksinfrastruktur runt dina tjänster och program som du distribuerar i Azure.

I det här dokumentet blir omfattar följande Azure networking enterprise-funktioner:

-   Grundläggande nätverksanslutning

-   Hybridanslutning

-   Säkerhetskontroller

-   Verifiera nätverket

### <a name="basic-network-connectivity"></a>Grundläggande nätverksanslutning

Den [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) tjänsten kan du ansluta Azure-resurser på ett säkert sätt till varandra med virtuella nätverk (VNet). Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk avgränsning av Azure nätverksinfrastrukturen för din prenumeration. Du kan också ansluta virtuella nätverk till varandra och till dina lokala nätverk med hjälp av plats-till-plats-VPN: er och dedikerade [WAN-länkar](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Grundläggande nätverksanslutning](media/azure-network-security/azure-network-security-fig-2.png)

Frågan är medveten om att du använder virtuella datorer till värdservrar i Azure, hur dessa virtuella datorer ansluter till ett nätverk. Svaret är att virtuella datorer ansluta till en [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Azure-nätverk är som om den virtuella nätverk du använder lokalt med dina egna lösningar för virtualisering plattform, till exempel Microsoft Hyper-V eller VMware.

#### <a name="intra-vnet-connectivity"></a>Intra-VNet-anslutning

Du kan ansluta virtuella nätverk till varandra, så att resurser som är anslutna till något virtuellt nätverk kan kommunicera med varandra över virtuella nätverk. Du kan använda ett eller båda av följande alternativ för att ansluta virtuella nätverk till varandra:

- **Peering:** kan resurser som är anslutna till olika virtuella Azure-nätverk inom samma Azure-plats att kommunicera med varandra. Nätverksbandbredd och svarstid i det virtuella nätverket är samma som om resurserna som var anslutna till samma virtuella nätverk. Mer information om peering [peerkoppling av virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Peering](media/azure-network-security/azure-network-security-fig-3.png)

- **VNet-till-VNet-anslutning:** kan resurser som är anslutna till olika Azure virtuellt nätverk inom samma eller olika Azure platser. Bandbredden är begränsad mellan virtuella nätverk till skillnad från peering, eftersom trafiken måste flöda via Azure VPN Gateway.

![VNet-till-VNet-anslutning](media/azure-network-security/azure-network-security-fig-4.png)


Mer information om hur du ansluter virtuella nätverk med en VNet-till-VNet-anslutning i [konfigurera en VNet-till-VNet-anslutning artikel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Funktioner för Azure-nätverk:

Som du kan se ger Azure Virtual Network virtuella datorer kan ansluta till nätverket så att de kan ansluta till andra nätverksresurser på ett säkert sätt. Grundläggande anslutning är dock bara början. Följande funktioner i tjänsten Azure Virtual Network exponera säkerhetsegenskaper för Azure Virtual Network:

-   Isolering

-   Internetanslutning

-   Azure-resurs-anslutning

-   VNet-anslutning

-   Lokala anslutningsmöjligheter

-   Trafikfiltrering

-   Routning

**Isolering**

De virtuella nätverken ingår [isolerade](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) från varandra. Du kan skapa separata virtuella nätverk för utveckling, testning och produktion som använder samma [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) åtgärda block. Däremot kan du skapa flera virtuella nätverk som använder olika CIDR-Adressblock och ansluta nätverk tillsammans. Du kan ändra ett virtuellt nätverk i flera undernät.

Azure erbjuder intern namnmatchning för virtuella datorer och [molntjänster](https://azure.microsoft.com/services/cloud-services/) rollinstanser som är anslutna till ett virtuellt nätverk. Du kan också konfigurera ett virtuellt nätverk om du vill använda din egen DNS-servrar, istället för att använda intern namnmatchning för Azure.

Du kan implementera flera virtuella nätverk i varje Azure [prenumeration](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) och Azure [region](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Varje virtuellt nätverk är isolerat från andra virtuella nätverk. För varje virtuellt nätverk kan du:

-   Ange ett anpassat privat IP-adressutrymme med offentliga och privata adresser (RFC 1918). Azure tilldelar resurser anslutna till det virtuella nätverket en privat IP-adress från adressutrymmet, du tilldelar.

-   Segmentera det virtuella nätverket i en eller flera undernät och allokera en del av virtuella nätverkets adressutrymme till varje undernät.

-   Använd Azure-tillhandahållen namnmatchning eller ange din egen DNS-server för användning av resurser som är anslutna till ett virtuellt nätverk. Mer information om namnmatchning i virtuella nätverk i [namnmatchning för virtuella datorer och molntjänster](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Internetanslutning**

Alla [Azure-datorer (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) och Cloud Services-rollinstanser som är anslutna till ett virtuellt nätverk har åtkomst till Internet, som standard. Du kan också aktivera ingående åtkomst till specifika resurser efter behov. (VM) och Cloud Services-rollinstanser som är anslutna till ett virtuellt nätverk har åtkomst till Internet, som standard. Du kan också aktivera ingående åtkomst till specifika resurser efter behov.

Alla resurser som är anslutna till ett virtuellt nätverk har utgående anslutning till Internet som standard. Privata IP-adressen för resursen är adress källnätverket översättas (SNAT) till en offentlig IP-adress via Azure-infrastrukturen. Du kan ändra standard-anslutningen genom att implementera anpassad Routning och trafikfiltrering. Mer information om utgående Internet-anslutning i [förstå utgående anslutningar i Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Att kommunicera inkommande till Azure-resurser från Internet eller för att kommunicera utgående till Internet utan SNAT, måste en resurs tilldelas en offentlig IP-adress. Mer information om offentliga IP-adresser i [offentliga IP-adresser](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Azure-resurs-anslutning**

[Azure-resurser](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) som molntjänster och virtuella datorer kan anslutas till samma virtuella nätverk. Resurserna som kan ansluta till varandra med privata IP-adresser, även om de finns i olika undernät. Azure tillhandahåller standardroutning mellan undernät, virtuella nätverk och lokala nätverk, så att du inte behöver att konfigurera och hantera vägar.

Du kan ansluta flera Azure-resurser till ett virtuellt nätverk, till exempel virtuella datorer (VM), Cloud Services, App Service-miljöer och Virtual Machine Scale Sets. Virtuella datorer ansluta till ett undernät i ett virtuellt nätverk via ett nätverksgränssnitt (NIC). Mer information om nätverkskort på [nätverksgränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**VNet-anslutning**

[Virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) kan anslutas till varandra, så att resurser som är anslutna till alla VNet kan kommunicera med resurser för andra virtuella nätverk.

Du kan ansluta virtuella nätverk till varandra, så att resurser som är anslutna till något virtuellt nätverk kan kommunicera med varandra över virtuella nätverk. Du kan använda ett eller båda av följande alternativ för att ansluta virtuella nätverk till varandra:

- **Peering:** kan resurser som är anslutna till olika virtuella Azure-nätverk inom samma Azure-plats att kommunicera med varandra. Nätverksbandbredd och svarstid för de virtuella nätverken är samma som om resurserna som var anslutna till samma VNet.To mer information om peering, Läs den [peerkoppling av virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **VNet-till-VNet-anslutning:** kan resurser som är anslutna till olika Azure virtuellt nätverk inom samma eller olika Azure platser. Bandbredden är begränsad mellan virtuella nätverk till skillnad från peering, eftersom trafiken måste flöda via Azure VPN Gateway. Mer information om hur du ansluter virtuella nätverk med en VNet-till-VNet-anslutning. Mer information finns i [konfigurera en VNet-till-VNet-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Lokala anslutningsmöjligheter**

Virtuella nätverk kan anslutas till [lokala](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) nätverk via privata nätverksanslutningar mellan ditt nätverk och Azure eller via en plats-till-plats VPN-anslutning via Internet.

Du kan ansluta ditt lokala nätverk till ett virtuellt nätverk med hjälp av följande alternativ:

- **Punkt-till-plats virtuellt privat nätverk (VPN):** upprättas mellan en enda dator som är ansluten till nätverket och det virtuella nätverket. Den här anslutningstypen är bra om du precis har börjat med Azure, eller för utvecklare, eftersom det krävs få eller inga ändringar i ditt befintliga nätverk. Anslutningen använder SSTP-protokollet för att tillhandahålla krypterad kommunikation via Internet mellan datorn och det virtuella nätverket. Svarstiden för en punkt-till-plats-VPN är oförutsägbar eftersom trafiken passerar via Internet.

- **Plats-till-plats-VPN:** upprättas mellan VPN-enheten och Azure VPN Gateway. Den här anslutningstypen kan alla lokala resurser som tillåter du att få åtkomst till ett virtuellt nätverk. Anslutningen är en IPsec/IKE VPN som tillhandahåller krypterad kommunikation via Internet mellan din lokala enhet och Azure VPN-gatewayen. Svarstiden för en plats-till-plats-anslutning är oförutsägbar eftersom trafiken passerar via Internet.

- **Azure ExpressRoute:** Upprättas mellan ditt nätverk och Azure, via en ExpressRoute-partner. Den här anslutningen är privat. Trafiken sker inte via Internet. Svarstiden för en ExpressRoute-anslutning är förutsägbara eftersom trafiken inte via Internet. Mer information om alla tidigare anslutningsalternativ den [anslutningstopologi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Trafikfiltrering**

Virtuell dator och Cloud Services-rollinstanser [nätverkstrafik](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) kan filtreras inkommande och utgående efter källans IP-adress och port, målets IP-adress och port och protokoll.

Du kan filtrera nätverkstrafik mellan undernät med ett eller båda av följande alternativ:

- **Nätverkssäkerhetsgrupper (NSG):** varje NSG kan innehålla flera inkommande och utgående säkerhetsregler som gör det möjligt att filtrera trafik genom att källans och målets IP-adress, port och protokoll. Du kan använda en NSG för varje nätverkskort på en virtuell dator. Du kan också använda en NSG till undernätet för ett nätverkskort eller andra Azure-resursen är ansluten till. Mer information om Nätverkssäkerhetsgrupper i [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Virtuella nätverksinstallationer:** en virtuell nätverksinstallation är en virtuell dator som kör program som utför en nätverksfunktion, till exempel en brandvägg. Visa en lista med tillgängliga nva: er i Azure Marketplace. Nva: er är också tillgängliga som tillhandahåller WAN-optimering och andra funktioner för trafik. Nva används vanligtvis med en användardefinierad eller BGP-vägar. Du kan också använda en NVA för att filtrera trafik mellan virtuella nätverk.

**Routning**

Du kan också åsidosätta Azures standard routning genom att konfigurera dina egna vägar eller använda BGP-vägar via en nätverks-gateway.

Azure skapar routningstabeller som gör att resurser som är anslutna till alla undernät i alla VNet kan kommunicera med varandra, som standard. Du kan implementera ett eller båda av följande alternativ för att åsidosätta de standardvägar som Azure skapar:

- **Användardefinierade vägar:** du kan skapa anpassade routningstabeller med vägar som styr vart trafik dirigeras till för varje undernät. Mer information om användardefinierade vägar finns i [användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **BGP-vägar:** om du ansluter ditt virtuella nätverk till ditt lokala nätverk med hjälp av en Azure VPN Gateway eller ExpressRoute-anslutning kan du sprida BGP-vägar till dina virtuella nätverk.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hybrid Internetanslutning: ansluta till ett lokalt nätverk
Du kan ansluta ditt lokala nätverk till ett virtuellt nätverk med hjälp av följande alternativ:

-   Internetanslutning

-   VPN för punkt-till-plats (P2S VPN)

-   VPN för plats-till-plats (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Internetanslutning

Som namnet antyder, gör Internet-anslutning dina arbetsbelastningar kan nås från Internet, genom att låta du exponera olika offentliga slutpunkter för arbetsbelastningar som bor i virtuella nätverk. De här arbetsbelastningarna kan exponeras med hjälp av [internetuppkopplade belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) eller bara tilldela en offentlig IP-adress till den virtuella datorn. På så sätt kan det blir möjligt för vad som helst på Internet för att kunna ansluta till den virtuella datorn, tillhandahålls en värdbrandvägg [nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), och [användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) tillåta att inträffa.

I det här scenariot kan du lägga upp ett program som måste vara offentliga till Internet och att kunna ansluta till den från var som helst, eller från specifika platser, beroende på konfigurationen för arbetsbelastningarna.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Punkt-till-plats-VPN eller VPN för plats-till-plats
Dessa två tillhör samma kategori. De båda behöver ditt VNet till en VPN-gateway och du kan ansluta till den med hjälp av antingen en VPN-klient för din arbetsstation som en del av den [punkt-till-plats-konfiguration](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) eller så kan du konfigurera din lokala [VPN-enheten](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)för att kunna avsluta en plats-till-plats-VPN. På så sätt kan kan lokala enheter ansluta till resurser i det virtuella nätverket.

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. P2S är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol).

![Punkt-till-plats-VPN](media/azure-network-security/azure-network-security-fig-5.png)

Punkt-till-plats-anslutningar är användbara när du vill ansluta till ditt VNet från en annan plats, exempelvis från hemmet eller en konferens center, eller när du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk.

P2S-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. Du upprättar VPN-anslutningen från klientdatorn. Därför rekommenderas inte P2S sätt att ansluta till Azure om du behöver en beständig anslutning från många lokala enheter och datorer till din Azure-nätverk.

![Plats-till-plats-VPN](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Mer information om punkt-till-plats-anslutningar finns i den [punkt-till-plats FA v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2).

Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Den här anslutningen sker via Internet och kan du ”tunnel” information i en krypterad anslutning mellan ditt nätverk och Azure. Plats-till-plats-VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar för flera decennier. Tunnel kryptering utförs med hjälp av [IPSec-tunnelläge](https://technet.microsoft.com/library/cc786385.aspx).

Plats-till-plats-VPN är en betrodda och tillförlitliga etablerad teknik, trafiken i tunneln via Internet. Dessutom begränsad bandbredd relativt till högst 200 Mbit/s.

Om du behöver en enastående nivå av säkerhet eller prestanda för dina anslutningar mellan flera platser rekommenderar vi att du använder Azure ExpressRoute för dina korsanslutningar. ExpressRoute är en dedikerad WAN länken mellan din lokala plats eller en värdbaserade Exchange-provider. Eftersom detta är en telco-anslutning kan överföras inte via Internet i dina data och därför visas inte för potentiella risker i Internet-kommunikation.

> [!Note]
> Mer information om VPN-gatewayer finns [VPN-gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Fast WAN-länk
Microsoft Azure ExpressRoute kan du utöka ditt lokala nätverk till Azure via en dedikerad privat anslutning från en anslutningsleverantör.

ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.

![ Fast WAN-länk](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Information om hur du ansluter nätverket till Microsoft med ExpressRoute finns i [ExpressRoute-anslutningsmodeller](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) och [teknisk översikt över ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Som med alternativ för plats-till-plats VPN kan ExpressRoute du också att ansluta till resurser som inte nödvändigtvis i endast ett virtuellt nätverk. Beroende på vilken SKU ansluta du i själva verket kan till 10 virtuella nätverk. Om du har den [premiumtillägg](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), anslutningar till upp till 100 virtuella nätverk är möjligt, beroende på bandbredd. Mer information om dessa typer av anslutningar Sök som [anslutningstopologi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Säkerhetskontroller
Azure Virtual Network ger en säker, logiska nätverk som är isolerad från andra virtuella nätverk och har stöd för många säkerhetskontroller som du använder på din lokala nätverk. Kunderna skapa sina egna strukturen med hjälp av: undernät – de använder sina egna privata IP-adressintervall, konfigurera routningstabeller, nätverkssäkerhetsgrupper, åtkomstkontrollistor (ACL), gatewayer och virtuella enheter för att köra sina arbetsbelastningar i molnet.

Följande är de säkerhetskontroller som du kan använda i dina virtuella Azure-nätverk:

-   Åtkomstkontroll för nätverk

-   Användardefinierade vägar

-   Säkerhetsfunktion

-   Application Gateway

-   Azure Web Application Firewall

-   Nätverkskontroll för tillgänglighet

#### <a name="network-access-controls"></a>Åtkomstkontroll för nätverk
Även om Azure Virtual Network (VNet) är en hörnsten i Azure Nätverksmodellen och ger isolering och skydd, den [Nätverkssäkerhetsgrupper (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) är det viktigaste verktyget när du använder för att tillämpa och styra regler för nätverkstrafik på de nivå.

![ Åtkomstkontroll för nätverk](media/azure-network-security/azure-network-security-fig-8.png)


Du kan styra åtkomsten genom att tillåta eller neka kommunikationen mellan arbetsbelastningar inom ett virtuellt nätverk, från system i kundens virtuella nätverk via flera platsanslutningar, eller direkt Internet-kommunikation.

I diagrammet finns både virtuella nätverk och NSG: er i ett visst lager i Azure övergripande security-stacken, där NSG: er, UDR och virtuella nätverksenheter kan användas för att skapa säkerhetsgränser för att skydda programdistributioner i skyddat nätverk.

NSG: er använder en 5-tuppel för att utvärdera trafik (och som används i de regler som du konfigurerar för NSG: N):

-   [Källans och målets IP-adress](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Käll-och mål](https://technet.microsoft.com/library/dd197515)

-   Protokoll: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) eller [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Det innebär att du kan styra åtkomsten mellan en enskild virtuell dator och en grupp med virtuella datorer eller en enskild virtuell dator till en annan enskild virtuell dator, eller mellan hela undernät. Igen, Tänk på att det är enkelt tillståndskänsliga paket som filtrering, inte fullständiga paketinspektion. Det finns ingen protokollverifiering eller nätverksnivån ID: N eller IP-funktionen i en Nätverkssäkerhetsgrupp.

En NSG innehåller vissa inbyggda regler som du bör känna till. Dessa är:

-   **Tillåt all trafik i ett specifikt virtuellt nätverk:** alla virtuella datorer på samma Azure Virtual Network kan kommunicera med varandra.

-   **Tillåt Azure belastningsutjämning för inkommande:** den här regeln tillåter trafik från alla källadress till alla måladresser för Azure load balancer.

-   **Neka alla inkommande:** den här regeln blockerar all trafik som källa från Internet som du uttryckligen tillåts.

-   **Tillåt all trafik utgående till Internet:** den här regeln kan virtuella datorer initiera anslutningar till Internet. Om du inte vill att dessa anslutningar som initieras, måste du skapa en regel för att blockera anslutningarna eller framtvinga Tvingad tunneltrafik.

#### <a name="system-routes-and-user-defined-routes"></a>Systemvägar och användardefinierade vägar

När du lägger till virtuella datorer (VM) till ett virtuellt nätverk (VNet) i Azure, märker du att de virtuella datorerna ska kunna kommunicera med varandra över nätverket, automatiskt. Du behöver inte ange någon gateway, även om VM:arna befinner sig på olika undernät.

Samma sak gäller för kommunikation från VM:arna till det offentliga Internet samt till ditt lokala nätverk när det finns en hybridanslutning från Azure till ditt eget datacenter.

![Systemvägar](media/azure-network-security/azure-network-security-fig-9.png)

Det här flödet av kommunikation är möjligt eftersom Azure använder en uppsättning systemvägar för att definiera hur IP-trafiken flödar. Systemvägar kontrollerar kommunikationsflödet i följande scenarier:

-   Från inom samma undernät.

-   Från ett undernät till ett annat inom ett VNet.

-   Från VM:ar till Internet.

-   Från ett VNet till ett annat VNet via en VPN-gateway.

-   Från ett virtuellt nätverk till ett annat VNet via VNet-Peering ([Tjänstlänkning](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Från ett VNet till ditt lokala nätverk via en VPN-gateway.

Många företag har stränga och krav på efterlevnad som kräver en lokal granskning av alla nätverkspaket tillämpa specifika principerna. Azure tillhandahåller en mekanism som heter [Tvingad tunneltrafik](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) som dirigerar trafik från de virtuella datorerna till den lokala genom att skapa en anpassad väg eller genom att [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) annonser via ExpressRoute eller VPN.

Tvingad tunneltrafik i Azure konfigureras via virtuella nätverk användardefinierade vägar (UDR). Omdirigerar trafik till en lokal plats uttrycks som en standardväg till Azure VPN-gatewayen.

I följande avsnitt visas den aktuella begränsningen Routning och vägar för ett virtuellt Azure-nätverk:

-   Varje virtuellt nätverksundernät har en inbyggd, system-routningstabell. Routningstabellen system har följande tre grupper av vägar:

 -  **Lokala virtuella nätverket vägar:** direkt till målets virtuella datorer i samma virtuella nätverk

 - **På lokala vägar:** till Azure VPN-gateway

 -  **Standardvägen:** direkt till Internet. Ignoreras paket som är avsedd för de privata IP-adresser som inte omfattas av de föregående två vägarna.

-   Med lanseringen av användardefinierade vägar kan du skapa en routningstabell för att lägga till en standardväg och sedan associera routningstabellen till ditt VNet-undernät för att aktivera Tvingad tunneltrafik på dessa undernät.

-   Du måste ange en ”standardwebbplats” mellan de över flera lokala platserna anslutna till det virtuella nätverket.

-   Tvingad tunneltrafik måste vara associerad med ett virtuellt nätverk som har en VPN-gateway för dynamisk routning (inte en statisk gateway).

- ExpressRoute Tvingad tunneltrafik är inte konfigurerad via den här mekanismen, men i stället aktiveras genom att annonsera en standardväg via ExpressRoute BGP-peeringsessioner.

> [!Note]
> Mer information finns i den [dokumentation om ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) för mer information.

#### <a name="network-security-appliances"></a>Funktioner för nätverkssäkerhet
Nätverkssäkerhetsgrupper och användardefinierade vägar kan användas för en viss grad av nätverkssäkerhet på Nätverks- och plattformsnivå av den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), kommer det att finnas situationer där du vill eller måste du aktivera säkerhet på högre nivåer av nätverksstacken. I så fall rekommenderar vi att du distribuerar virtuella nätverket säkerhetsenheter som tillhandahålls av Azure-partner.

![Funktioner för nätverkssäkerhet](./media/azure-network-security/azure-network-security-fig-10.png)

Azure funktioner för nätverkssäkerhet förbättra VNet säkerhets- och nätverksfunktioner och de är tillgängliga från ett stort antal leverantörer via den [Azure Marketplace](https://azuremarketplace.microsoft.com). Dessa virtuella säkerhetsenheter kan distribueras för att tillhandahålla:

-   Med hög tillgänglighet brandväggar

-   Intrångsskydd

-   Intrångsidentifiering

-   Brandväggar för webbprogram (Waf)

-   WAN-optimering

-   Routning

-   Belastningsutjämning

-   VPN

-   Certifikathantering

-   Active Directory

-   Multifaktorautentisering

#### <a name="application-gateway"></a>Programgateway

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) är en dedikerad virtuell installation som tillhandahåller en application Deliver controller (ADC) som en tjänst.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Application Gateway kan du optimera web servergruppen prestanda och tillgänglighet genom att avlasta CPU-intensiv SSL avslutning till application gateway (SSL-avlastning). Det finns även andra layer 7-routningsfunktioner inklusive:

-   Resursallokeringsdistribution av inkommande trafik

-   Cookie-baserad sessionstillhörighet

-   URL-sökvägsbaserad Routning

-   Möjligheten att vara värd för flera webbplatser bakom en enda Application Gateway


En [Brandvägg för webbaserade program (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) ingår också som en del av application gateway. Detta skyddar webbprogram mot vanliga säkerhetsproblem och hot på Internet. Application Gateway kan konfigureras som en Internetuppkopplad gateway, endast intern gateway eller en kombination av båda.

Application Gateway WAF kan köras i läget för identifiering och skydd. Det är ett vanligt användningsfall för administratörer att köra i identifieringsläge att Observera trafik för skadliga mönster. När potentiella hot upptäckts, byter till förhindringsläge blockerar misstänkt inkommande trafik.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Dessutom kan Application Gateway WAF hjälper dig att övervaka webbprogram mot attacker med hjälp av en WAF-loggen i realtid som är integrerad med [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) och [Azure Security Center](https://azure.microsoft.com/services/security-center/) att spåra WAF-aviseringar och enkelt övervaka trender.

Loggen för JSON-formaterade går direkt till kundens lagringskonto. Du har fullständig kontroll över de här loggarna och kan använda din egen bevarandeprinciper.

Du kan också mata in de här loggarna i dina egna system med [Azure Log Integration](https://aka.ms/AzLog). WAF-loggar är också integrerat med [Log Analytics](../log-analytics/log-analytics-overview.md) så att du kan använda Log Analytics för att köra avancerade detaljerade frågor.

#### <a name="azure-web-application-firewall-waf"></a>Azure web application firewall (WAF)

Webbprogram är allt för attacker som utnyttjar kända svagheter, till exempel SQL-inmatning, mellan site skriptattacker och andra attacker som visas i den [OWASP topp 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Förhindrar sådana sårbarheter i programmet kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin.

 ![Azure Web Application Firewall (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

En centraliserad [Brandvägg för webbaserade program (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) kan skydda mot attacker och förenklar hanteringen av informationssäkerhet utan några ändringar i programmet.

En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

#### <a name="network-availability-controls"></a>Nätverkskontroller för tillgänglighet

Det finns olika alternativ för att distribuera nätverkstrafik med Microsoft Azure. De här alternativen fungerar på olika sätt, har olika funktionsuppsättningar och stöder olika scenarier. De kan användas var för sig eller i kombination med varandra.

Nedan följer tillgänglighet nätverkskontroller:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Azure belastningsutjämnare**

Levererar hög tillgänglighet och nätverksprestanda till dina program. Det är en belastningsutjämnare för Layer-4 (TCP, UDP) som distribuerar inkommande trafik mellan felfria instanser av tjänster som definierats i en belastningsutjämnad uppsättning.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Azure Load Balancer kan konfigureras för att:

-   Läsa in belastningsutjämna inkommande Internettrafik till virtuella datorer. Den här konfigurationen kallas [belastningsutjämning för Internet-riktade](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Belastningsutjämna trafik mellan virtuella datorer i ett virtuellt nätverk, mellan virtuella datorer i molntjänster eller mellan lokala datorer och virtuella datorer i ett virtuellt nätverk med flera platser. Den här konfigurationen kallas [intern belastningsutjämning](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Vidarebefordra extern trafik till en specifik virtuell dator.

Alla resurser i molnet måste en offentlig IP-adress ska nås från Internet. Moln-infrastruktur i Azure använder icke-dirigerbara IP-adresser för dess resurser. Azure använder network adress translation (NAT) med offentliga IP-adresser för att kommunicera med Internet.

 **Programgateway**

 Application Gateway fungerar på programlagret (Layer 7 i OSI-nätverksreferensstacken). Den fungerar som en omvänd proxy-tjänst och avslutar klientanslutningen och vidarebefordrar begäranden till slutpunkter i serverdelen.

 **Traffic manager**

Microsoft Azure Traffic Manager kan du styra distributionen av användartrafik för tjänstslutpunkter i olika datacenter. Tjänstslutpunkter som stöds av Traffic Manager innefattar virtuella Azure-datorer, Web Apps och molntjänster. Du kan även använda Traffic Manager med externa slutpunkter som inte tillhör Azure.

Traffic Manager använder Domain Name System (DNS) för att dirigera klientbegäranden till den lämpligaste slutpunkten baserat på en [trafikdirigeringsmetoden](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) och hälsotillståndet för slutpunkterna. Traffic Manager erbjuder en uppsättning trafikroutningsmetoder efter programbehov för olika, slutpunktshälsa [övervakning](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring), och automatisk redundans. Traffic Manager har bra återhämtningsförmåga i händelse av fel, inklusive fel som påverkar en hel Azure-region.

Med Azure Traffic Manager kan du styra distributionen av trafiken mellan slutpunkterna för din. En slutpunkt är en Internetansluten tjänst i eller utanför Azure.

Traffic Manager har två viktiga fördelar:

-   Fördelning av trafik enligt ett av flera [trafikroutningsmetoder](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Kontinuerlig övervakning av slutpunktshälsa](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) och automatisk redundans när slutpunkter misslyckas.

När en klient försöker ansluta till en tjänst, måste den först matcha DNS-namnet på tjänsten till en IP-adress. Klienten ansluter sedan till IP-adressen åtkomst till tjänsten. Traffic Manager använder DNS för att dirigera klienterna till specifika tjänstslutpunkter utifrån reglerna för routning av nätverkstrafik-metoden. Klienterna ansluter direkt till den valda slutpunkten. Traffic Manager är inte en proxy eller en gateway. Traffic Manager kan inte se den trafik som passerar mellan klienten och tjänsten.

### <a name="azure-network-validation"></a>Verifiera Azure nätverket

Verifiera Azure nätverket är att säkerställa att Azure-nätverket fungerar som den är konfigurerad och verifiering kan göras med hjälp av tjänster och funktioner som är tillgängliga för att övervaka nätverket. Med Azure Network Watcher kan du komma åt en mängd olika loggning och diagnostiska funktioner som hjälper dig med insikter för att förstå nätverkets prestanda och hälsa. Dessa funktioner är tillgängliga via portalen, PowerShell, CLI, Rest API och SDK.

Azure driftsäkerhet refererar till de tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure. Azure driftsäkerhet bygger på ett ramverk som innehåller den kunskap som en olika funktioner som är unika för Microsoft, inklusive på Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-program , och djup medvetenhet om hotlandskapet cyberhot säkerhet.

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure resource manager

Människor och processer som fungerar Microsoft Azure är kanske den viktigaste säkerhetsfunktionen för plattformen. Det här avsnittet beskriver funktionerna i Microsofts globala datacenterinfrastruktur som hjälper dig att förbättra och upprätthålla säkerhet, verksamhetskontinuitet och sekretess.

Infrastrukturen för ditt program består normalt av många komponenter – kanske en virtuell dator, ett lagringskonto och ett virtuellt nätverk eller en webbapp, en databas, en databasserver och tjänster från tredje part. Du ser inte de här komponenterna som separata entiteter, utan som relaterade delar av samma enhet som är beroende av varandra. Du vill distribuera, hantera och övervaka dem som en grupp. Med Azure Resource Manager kan du arbeta med resurserna i en lösning som en grupp.

Du kan distribuera, uppdatera eller ta bort alla resurser i lösningen i en enda, samordnad åtgärd. Du använder en mall för distributionen. Mallen kan användas i olika miljöer, till exempel för testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper dig att hantera dina resurser efter distributionen.

**Fördelarna med att använda Resource Manager**

Resource Manager har flera fördelar:

-   Du kan distribuera, hantera och övervaka alla resurserna för din lösning som en grupp i stället för att hantera resurserna separat.

-   Du kan distribuera lösningen flera gånger genom utvecklingslivscykeln och vara säker på att dina resurser distribueras på ett enhetligt sätt.

-   Du kan hantera infrastrukturen med hjälp av deklarativa mallar i stället för skript.

-   Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.

-   Du kan använda åtkomstkontroll för alla tjänster i resursgruppen eftersom rollbaserad åtkomstkontroll (RBAC) är inbyggt i hanteringsplattformen.

-   Du kan lägga till taggar för resurser och organisera alla logiskt i din prenumeration.

-   Du kan tydliggöra din organisations fakturering genom att visa kostnaderna för en grupp med resurser som delar tagg.

> [!Note]
> Resource Manager erbjuder ett nytt sätt att distribuera och hantera lösningar. Om du använde den tidigare distributionsmodellen och vill veta mer om ändringarna läser du [Förstå Resource Manager-distribution och klassisk distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Azure-nätverk loggning och övervakning

Azure erbjuder många verktyg för att övervaka, förhindra, upptäcka och svara på nätverket säkerhetshändelser. Några av de viktigaste verktygen som är tillgängliga i det här området är:

-   Network Watcher

-   Resursen på nätverksövervakning

-   Log Analytics

### <a name="network-watcher"></a>Nätverksbevakare

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -Scenario-baserad övervakning tillhandahålls med funktionerna i Network Watcher. Den här tjänsten innefattar paketinsamling, nästa hopp, IP-flöde verifiera säkerhetsgruppvy, NSG-flödesloggar. Scenariot på övervakning ger en heltäckande vy av nätverksresurser, till skillnad från enskilda resource nätverksövervakning.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Network Watcher är en regional tjänst som hjälper dig att övervaka och diagnostisera villkor på nätverksnivå, till och från Azure. Nätverksdiagnostik- och visualiseringsverktyg för Network Watcher hjälper dig att förstå, diagnostisera och få information om ditt nätverk i Azure.

Network Watcher har för närvarande följande funktioner:

#### <a name="topology"></a>Topologi

[Topologi](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) returnerar ett diagram över nätverksresurser i ett virtuellt nätverk. Diagrammet visar gränssnittet mellan resurser för att representera nätverksanslutning för slutpunkt till slutpunkt. I portalen returnerar topologi resursobjekt på enligt bas för virtuellt nätverk. Relationerna illustreras med linjer mellan resurser utanför regionen Network Watcher, även om i resursen i gruppen inte visas. Resurser som returneras i portal-vy är en delmängd av nätverkskomponenter som är visas i diagram registreringen. Om du vill se en fullständig lista över nätverksresurser, kan du använda [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) eller [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Eftersom resurser returneras modelleras anslutningen mellan de under två relationer.

- **Inneslutning** -virtuellt nätverk innehåller ett undernät som innehåller ett nätverkskort.

- **Associerade** -A NIC som är associerad med en virtuell dator.

#### <a name="variable-packet-capture"></a>Variabla infångade paket

Network Watcher [variabla infångade paket](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) kan du skapa packet capture sessioner för att spåra trafik till och från en virtuell dator. Packet capture bidrar till att diagnostisera nätverk avvikelser både reaktivt och proactivity. Andra användningsområden är att samla in nätverksstatistik, få information om nätverk intrång, felsöka klient-/ serverkommunikation och mycket mer.

Paketfångsten är ett tillägg för virtuell dator som startas via en fjärranslutning via Network Watcher. Den här funktionen förenklar arbetet med att köra ett infångat manuellt på den önskade virtuella datorn, vilket sparar värdefull tid. Paketfångsten kan aktiveras via portal, PowerShell, CLI eller REST API. Ett exempel på hur du kan aktivera infångade paket är med VM-aviseringar.

#### <a name="ip-flow-verify"></a>Kontrollera IP-flöde

[Verifiera IP-flöden](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) kontrollerar om ett paket tillåts eller nekas till eller från en virtuell dator utifrån 5-tuppel-informationen. Den här informationen består av riktning, protokoll, lokal IP-, fjärr-IP, lokal port och portar. Om paketet nekas av en säkerhetsgrupp, returneras namnet på regeln som nekade paketet. När alla käll- eller målservrar IP-adresser kan väljas, hjälper den här funktionen administratörer att snabbt diagnostisera anslutningsproblem från eller till internet och från eller till en lokal miljö.

IP-flöden verifiera riktar sig mot ett nätverksgränssnitt för en virtuell dator. Flödet i nätverkstrafiken verifieras sedan baserat på de konfigurerade inställningarna till eller från nätverksgränssnittet. Den här funktionen är användbar för att bekräfta om en regel i en Nätverkssäkerhetsgrupp blockerar inkommande eller utgående trafik till eller från en virtuell dator.

#### <a name="next-hop"></a>Nästa hopp

Anger den [nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) för paket som vidarebefordras i Azure Network Fabric, så att du kan diagnostisera eventuella felkonfigurerad användardefinierade vägar. Skickas trafik från en virtuell dator till ett mål baserat på de effektiva vägarna som är associerade med ett nätverkskort. Nästa hopp hämtar nästa hopptyp och IP-adress för ett paket från en specifik virtuell dator och nätverkskort. Detta hjälper dig för att avgöra om paketet dirigeras till målet eller är trafiken som svart holed.

Nästa hopp returnerar också routningstabellen som är associerad med nästa hopp. När du frågar efter en nästa hopp om vägen definieras som en användardefinierad väg, returneras det flödet. Annars returneras nästa hopp ”Systemväg”.

#### <a name="security-group-view"></a>säkerhetsgruppvy

Hämtar de effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator. Nätverkssäkerhetsgrupper är associerade på en undernätverksnivå eller på en NIC-nivå. När associerade på en undernätverksnivå måste tillämpas på alla VM-instanser i undernätet. Nätverk [säkerhetsgrupp visa](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) returnerar alla konfigurerade NSG: er och regler som är associerade på Nätverkskorts- och undernätsnivå nivå för en virtuell dator som ger inblick i konfigurationen. Dessutom returneras gällande säkerhetsregler för varje nätverkskort på en virtuell dator. Med hjälp av nätverkssäkerhetsgruppvy, kan du utvärdera en virtuell dator för nätverk, till exempel öppna portar. Du kan också bekräfta om Nätverkssäkerhetsgruppen fungerar som förväntat baserat på en [jämförelse mellan den konfigurerade och gällande säkerhetsregler](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>NSG-Flödesloggar loggning

 Flödesloggar för Nätverkssäkerhetsgrupper kan du samla in loggar som rör trafik som tillåts eller nekas av säkerhetsregler i gruppen. Flödet definieras av en 5-tuppel-information – käll-IP, mål-IP, källport, målport och protokoll.

[Flödesloggar för Nätverkssäkerhetsgruppen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) är en funktion i Network Watcher där du kan visa information om ingående och utgående IP-trafik via en Nätverkssäkerhetsgrupp.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Virtuella nätverksgateway och felsökning av anslutning

Network Watcher innehåller många funktioner som den relaterar till att förstå nätverksresurserna i Azure. En av dessa funktioner är resurs felsökning. [Felsökning av resursen](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) kan anropas av PowerShell, CLI eller REST API. När den anropas, Network Watcher kontrollerar hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar resultatet.

Det här avsnittet tar dig igenom de olika administrativa uppgifter som är tillgängliga för felsökning av resursen.

-   [Felsöka en virtuell nätverksgateway](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Felsöka en anslutning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Nätverk-prenumerationsbegränsningar

[Nätverks-prenumerationsbegränsningar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) ger dig information om användning av varje nätverksresurs i en prenumeration i en region mot det maximala antalet resurser som är tillgängliga.

#### <a name="configuring-diagnostics-log"></a>Konfigurera Log-diagnostik

Network Watcher får en [diagnostikloggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) vy. Den här vyn innehåller alla nätverksresurser som har stöd för Diagnostisk loggning. Du kan aktivera och inaktivera nätverksresurser smidigt och snabbt från den här vyn.

### <a name="network-resource-level-monitoring"></a>Resursen på nätverksövervakning

Följande funktioner är tillgängliga för nivån Resursövervakning:

#### <a name="audit-log"></a>Granskningslogg

Åtgärder som utförs som en del av konfigurationen av nätverk loggas. Dessa granskningsloggar är avgörande för att upprätta olika efterlevnadskraven. Dessa loggar kan ses i Azure-portalen eller hämtas med hjälp av Microsoft-verktyg, till exempel Power BI eller verktyg från tredje part. Granskningsloggarna är tillgängliga via portalen, PowerShell, CLI och Rest API.

> [!Note]
> Mer information om granskningsloggarna finns i [granskningsåtgärder med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Granskningsloggarna är tillgängliga för åtgärder på alla nätverksresurser.


#### <a name="metrics"></a>Mått

Mått är prestandamått och prestandaräknare som samlas in under en period. Mått är tillgängliga för Application Gateway. Mått kan användas till att utlösa aviseringar baserat på tröskelvärdet. Azure Application Gateway som standard övervakar hälsotillståndet för alla resurser i dess backend-poolen och att ta bort automatiskt alla resurser som anses vara felaktigt från poolen. Application Gateway fortsätter att övervaka de felaktiga instanserna och lägger till dem till Felfri backend-poolen när de blir tillgängliga och svara på hälsokontroller av slutpunkter. Programgateway skickar hälsoavsökningar med samma port som definierats i backend-HTTP-inställningarna. Den här konfigurationen garanterar att avsökningen provar på samma port som kunder skulle använda för att ansluta till serverdelen.

> [!Note]
> Se [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) att visa hur mått kan användas för att skapa aviseringar.

#### <a name="diagnostic-logs"></a>Diagnostikloggar

Periodiska och spontant händelser skapas av nätverksresurser och loggat in storage-konton kan skickas till en Event Hub, eller Log Analytics. Dessa loggar ge insikter om hälsotillståndet för en resurs. Dessa loggar kan ses i verktyg som Power BI och Log Analytics. Du kan få veta hur du visar diagnostikloggar [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Diagnostiska loggar är tillgängliga för [belastningsutjämnaren](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), vägar, och [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher innehåller en vy för diagnostikloggar. Den här vyn innehåller alla nätverksresurser som har stöd för Diagnostisk loggning. Du kan aktivera och inaktivera nätverksresurser smidigt och snabbt från den här vyn.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) är en tjänst i Azure som övervakar dina molnbaserade och lokala miljöer för att bibehålla tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor.

Log Analytics erbjuder följande lösningar för att övervaka dina nätverk:

-   Övervakare av nätverksprestanda (NPM)

-   Azure Application Gateway analytics

-   Azure Network Security Group analytics

#### <a name="network-performance-monitor-npm"></a>Övervakare av nätverksprestanda (NPM)
Den [Övervakare av nätverksprestanda](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) lösning är en lösning som övervakar hälsa, tillgänglighet och Åtkomstmöjligheten i nätverk för nätverksövervakning.

Används för att övervaka anslutningen mellan:

-   Offentliga molnet och lokalt

-   Datacenter och platser för användare (avdelningskontor)

-   Undernät som är värd för olika nivåer av ett program med flera nivåer.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Azure application gateway analytics i log analytics

Följande loggar kan användas för Application Gateway:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Följande mått har stöd för Application Gateway:

-   5 minuter dataflöde

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Azure network security group analytics i log analytics

Följande loggar stöds för [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** innehåller poster för vilka NSG-regler tillämpas på virtuella datorer och instans regler som baseras på MAC-adress. Status för de här reglerna som samlas in var 60: e sekund.

- **NetworkSecurityGroupRuleCounter:** innehåller poster för hur många gånger varje NSG-regel används för att neka eller tillåta trafik.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om säkerhet genom att läsa några av våra djupgående säkerhetsfrågor:

-   [Log Analytics för Nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Nätverk innovationer som driver kan avbrott i molnet](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: Nätverket växla programvara som driver det globala Microsoft-molnet](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Hur Microsoft skapar dess snabb och tillförlitlig globalt nätverk](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Drar igång nätverk innovation](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
