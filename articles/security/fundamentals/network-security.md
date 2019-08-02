---
title: Säkerhet för Azure-nätverk | Microsoft Docs
description: Lär dig mer om molnbaserad data behandlings tjänster som innehåller ett brett urval av beräknings instanser & tjänster som kan skala upp och ned automatiskt för att möta behoven hos ditt program eller företag.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: db3f5aca0240c19f67d5d0775148d5eec76daa03
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726992"
---
# <a name="azure-network-security"></a>Azure-nätverkssäkerhet

Vi vet att säkerheten är ett jobb i molnet och hur viktigt det är att du hittar korrekt och tidsödande information om Azure-säkerhet. Ett av de bästa orsakerna till att använda Azure för dina program och tjänster är att dra nytta av Azures breda utbud av säkerhets verktyg och-funktioner. Med dessa verktyg och funktioner kan du skapa säkra lösningar på Azure-plattformen.

Microsoft Azure ger konfidentialitet, integritet och tillgänglighet för kund information, samtidigt som du aktiverar transparent ansvars rätt. För att hjälpa dig att bättre förstå samlingen av nätverks säkerhets kontroller som implementeras i Microsoft Azure från kundens perspektiv, är den här artikeln "Azure Network Security" skriven för att ge en omfattande översikt över nätverks säkerhets kontrollerna tillgängligt med Microsoft Azure.

Det här dokumentet är avsett att informera dig om de olika nätverks kontroller som du kan konfigurera för att förbättra säkerheten för de lösningar som du distribuerar i Azure. Om du är intresse rad av vad Microsoft skyddar nätverks infrastruktur resursen för själva Azure-plattformen kan du läsa avsnittet Azure-säkerhet i [Microsoft säkerhets Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure).

## <a name="azure-platform"></a>Azure-plattformen

Azure är en offentlig moln tjänst plattform som har stöd för ett brett urval av operativ system, programmeringsspråk, ramverk, verktyg, databaser och enheter.  Den kan köra Linux-behållare med Docker-integrering, Bygg appar med Java Script, python, .NET, PHP, Java och Node. js. Bygg Server delar för iOS-, Android-och Windows-enheter. Azure Cloud Services har stöd för samma teknik miljon tals utvecklare och IT-proffs är redan förlitande på och litar på.

När du bygger på, eller migrerar IT-tillgångar till, en offentlig moln tjänst leverantör, förlitar du dig på den organisationens möjligheter att skydda dina program och data med tjänsterna och de kontroller som de tillhandahåller för att hantera säkerheten för dina molnbaserade till gångar.

Azures infrastruktur är utformad från anläggningen till program för att vara värd för miljon tals kunder samtidigt, och det ger en säker grund där företag kan uppfylla sina säkerhets krav. Dessutom ger Azure dig en omfattande samling konfigurerbara säkerhets alternativ och möjligheten att kontrol lera dem så att du kan anpassa säkerheten för att uppfylla de unika kraven i din organisations distributioner.

## <a name="abstract"></a>Abstrakt

Microsofts offentliga moln tjänster levererar storskaliga tjänster och infrastruktur, funktioner i företags klass och många alternativ för Hybrid anslutning. Du kan välja att få åtkomst till dessa tjänster antingen via Internet eller med Azure ExpressRoute, som tillhandahåller privat nätverks anslutning. Microsoft Azures plattformen gör att du sömlöst kan utöka din infrastruktur till molnet och bygga flera nivå arkitekturer. Dessutom kan tredje part Aktivera förbättrade funktioner genom att erbjuda säkerhets tjänster och virtuella enheter.

Azures nätverks tjänster maximerar flexibilitet, tillgänglighet, återhämtning, säkerhet och integritet efter design. Den här white paper innehåller information om nätverksfunktioner i Azure och information om hur kunder kan använda Azures inbyggda säkerhetsfunktioner för att skydda sina informations till gångar.

De avsedda mål grupperna för detta whitepaper är:

- Tekniska chefer, nätverks administratörer och utvecklare som söker efter säkerhetslösningar som är tillgängliga och stöds i Azure.

-   Små och medel stora företag eller affärs processer som vill ha en översikt över Azures nätverks tekniker och tjänster som är relevanta för diskussioner kring nätverks säkerheten i det offentliga Azure-molnet.

## <a name="azure-networking-big-picture"></a>Stor bild av Azure-nätverk
Microsoft Azure innehåller en robust nätverks infrastruktur som stöder dina anslutnings krav för program och tjänster. Nätverks anslutningen är möjlig mellan resurser som finns i Azure, mellan lokala och Azure-värdbaserade resurser och till och från Internet och Azure.

![Stor bild av Azure-nätverk](./media/network-security/azure-network-security-fig-1.png)

Med [Azures nätverks infrastruktur](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) kan du på ett säkert sätt ansluta Azure-resurser till varandra med virtuella nätverk (virtuella nätverk). Ett VNet är en representation av ditt eget nätverk i molnet. Ett VNet är en logisk isolering av Azure Cloud Network dedikerad till din prenumeration. Du kan ansluta virtuella nätverk till dina lokala nätverk.

Azure har stöd för dedikerad WAN-anslutning till ditt lokala nätverk och en Azure-Virtual Network med [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Länken mellan Azure och platsen använder en dedikerad anslutning som inte går via det offentliga Internet. Om ditt Azure-program körs i flera data Center kan du använda [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) för att dirigera begär Anden från användare intelligent över instanser av programmet. Du kan också dirigera trafik till tjänster som inte körs i Azure om de är tillgängliga från Internet.

## <a name="enterprise-view-of-azure-networking-components"></a>Enterprise-vy över Azure Networking-komponenter
Azure har många nätverks komponenter som är relevanta för nätverks säkerhets diskussioner. Vi beskriver dessa nätverks komponenter och fokuserar på de säkerhets problem som är relaterade till dem.

> [!Note]
> Alla aspekter av Azure-nätverk beskrivs inte – vi diskuterar bara de som anses vara pivoterade vid planering och design av en säker nätverks infrastruktur runt dina tjänster och program som du distribuerar i Azure.

I det här dokumentet kommer att avse följande företags funktioner i Azure:

-   Grundläggande nätverks anslutning

-   Hybrid anslutning

-   Säkerhetskontroller

-   Nätverks verifiering

### <a name="basic-network-connectivity"></a>Grundläggande nätverks anslutning

Med [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) -tjänsten kan du på ett säkert sätt ansluta Azure-resurser till varandra med virtuella nätverk (VNet). Ett VNet är en representation av ditt eget nätverk i molnet. Ett VNet är en logisk isolering av den Azure-nätverks infrastruktur som är dedikerad för din prenumeration. Du kan också ansluta virtuella nätverk till varandra och till dina lokala nätverk med plats-till-plats-VPN och dedikerade [WAN-länkar](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Grundläggande nätverks anslutning](./media/network-security/azure-network-security-fig-2.png)

I samband med att du använder virtuella datorer som värd för servrar i Azure, är frågan hur de virtuella datorerna ansluter till ett nätverk. Svaret är att virtuella datorer ansluter till ett [Azure-Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Virtuella Azure-nätverk är som de virtuella nätverk som du använder lokalt med dina egna virtualiseringslösningar, till exempel Microsoft Hyper-V eller VMware.

#### <a name="intra-vnet-connectivity"></a>Anslutning mellan virtuella nätverk

Du kan ansluta virtuella nätverk till varandra, vilket gör att resurser som är anslutna till det virtuella nätverket kan kommunicera med varandra över virtuella nätverk. Du kan använda något av eller båda av följande alternativ för att ansluta virtuella nätverk till varandra:

- **Peering** Aktiverar resurser som är anslutna till olika Azure-virtuella nätverk inom samma Azure-plats för att kommunicera med varandra. Bandbredden och fördröjningen i VNet är desamma som om resurserna var anslutna till samma VNet. Läs mer om peering i det [virtuella nätverkets peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

  ![Peering](./media/network-security/azure-network-security-fig-3.png)

- **VNet-till-VNet-anslutning:** Aktiverar resurser som är anslutna till olika Azure VNet på samma eller olika Azure-platser. Till skillnad från peering begränsas bandbredden mellan virtuella nätverk eftersom trafiken måste flöda genom en Azure-VPN Gateway.

![VNet-till-VNet-anslutning](./media/network-security/azure-network-security-fig-4.png)


Mer information om hur du ansluter virtuella nätverk med en VNet-till-VNet-anslutning finns i [artikeln Konfigurera en VNet-till-VNET-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Funktioner i Azure Virtual Network:

Som du kan se tillhandahåller en Azure-Virtual Network virtuella datorer för att ansluta till nätverket så att de kan ansluta till andra nätverks resurser på ett säkert sätt. Basic-anslutningen är dock bara början. Följande funktioner i Azure Virtual Network-tjänsten exponerar säkerhets egenskaperna för Azure-Virtual Network:

-   Isolering

-   Internetanslutning

-   Anslutning till Azure-resurs

-   VNet-anslutning

-   Lokala anslutningsmöjligheter

-   Trafikfiltrering

-   Routning

**Gruppera**

Virtuella nätverk är [isolerade](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) från varandra. Du kan skapa separata virtuella nätverk för utveckling, testning och produktion som använder samma [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) -Adressblock. Omvänt kan du skapa flera virtuella nätverk som använder olika CIDR-adressblock och ansluter nätverk till varandra. Du kan segmentera ett VNet i flera undernät.

Azure tillhandahåller intern namn matchning för virtuella datorer och [Cloud Services](https://azure.microsoft.com/services/cloud-services/) roll instanser som är anslutna till ett VNet. Du kan också konfigurera ett virtuellt nätverk för att använda dina egna DNS-servrar i stället för att använda intern namn matchning i Azure.

Du kan implementera flera virtuella nätverk i varje Azure- [prenumeration](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) och Azure- [region](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Varje virtuellt nätverk är isolerat från andra virtuella nätverk. För varje VNet kan du:

-   Ange ett anpassat privat IP-adressutrymme med offentliga och privata adresser (RFC 1918). Azure tilldelar resurser som är anslutna till VNet en privat IP-adress från adress utrymmet som du tilldelar.

-   Segmentera VNet till ett eller flera undernät och allokera en del av VNet-adressutrymmet till varje undernät.

-   Använd Azure-tillhandahållen namn matchning eller ange en egen DNS-server som ska användas av resurser som är anslutna till ett virtuellt nätverk. Läs mer om namn matchning i virtuella nätverk i [namn matchning för virtuella datorer och Cloud Services](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Internetanslutning**

Alla [Azure Virtual Machines (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) och Cloud Services roll instanser som är anslutna till ett VNet har åtkomst till Internet som standard. Du kan också aktivera inkommande åtkomst till vissa resurser efter behov. (VM) och Cloud Services roll instanser som är anslutna till ett VNet har åtkomst till Internet som standard. Du kan också aktivera inkommande åtkomst till vissa resurser efter behov.

Alla resurser som är anslutna till ett VNet har utgående anslutning till Internet som standard. Resursens privata IP-adress är käll nätverks adressen översatt (SNAT) till en offentlig IP-adress av Azure-infrastrukturen. Du kan ändra standard anslutningen genom att implementera anpassad Routning och trafik filtrering. Om du vill veta mer om utgående Internet anslutning läser du [förstå utgående anslutningar i Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

För att kunna kommunicera inkommande till Azure-resurser från Internet, eller för att kommunicera utgående till Internet utan SNAT, måste en resurs tilldelas en offentlig IP-adress. Läs mer om offentliga IP-adresser genom att läsa de [offentliga IP-adresserna](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Anslutning till Azure-resurs**

[Azure-resurser](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) som Cloud Services och virtuella datorer kan anslutas till samma VNet. Resurserna kan ansluta till varandra med hjälp av privata IP-adresser, även om de finns i olika undernät. Azure tillhandahåller standardroutning mellan undernät, virtuella nätverk och lokala nätverk, så du behöver inte konfigurera och hantera vägar.

Du kan ansluta flera Azure-resurser till ett VNet, till exempel Virtual Machines (VM), Cloud Services, App Service miljöer och Virtual Machine Scale Sets. Virtuella datorer ansluter till ett undernät i ett VNet via ett nätverks gränssnitt (NIC). Läs mer om nätverkskorten i [nätverks gränssnitten](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**VNet-anslutning**

[Virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) kan anslutas till varandra, vilket gör att resurser som är anslutna till ett VNet kan kommunicera med alla resurser i ett annat VNet.

Du kan ansluta virtuella nätverk till varandra, vilket gör att resurser som är anslutna till det virtuella nätverket kan kommunicera med varandra över virtuella nätverk. Du kan använda något av eller båda av följande alternativ för att ansluta virtuella nätverk till varandra:

- **Peering** Aktiverar resurser som är anslutna till olika Azure-virtuella nätverk inom samma Azure-plats för att kommunicera med varandra. Bandbredden och fördröjningen i virtuella nätverk är samma som om resurserna var anslutna till samma VNet.To. Läs mer om peering i det [virtuella nätverkets peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **VNet-till-VNet-anslutning:** Aktiverar resurser som är anslutna till olika Azure VNet på samma eller olika Azure-platser. Till skillnad från peering begränsas bandbredden mellan virtuella nätverk eftersom trafiken måste flöda genom en Azure-VPN Gateway. Lär dig mer om att ansluta virtuella nätverk med en VNet-till-VNet-anslutning. Läs mer i [Konfigurera en VNet-till-VNET-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Lokala anslutningsmöjligheter**

Virtuella nätverk kan anslutas till [lokala](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) nätverk via privata nätverks anslutningar mellan ditt nätverk och Azure, eller via en VPN-anslutning från plats till plats via Internet.

Du kan ansluta ditt lokala nätverk till ett VNet med valfri kombination av följande alternativ:

- **Punkt-till-plats virtuellt privat nätverk (VPN):** Upprättas mellan en enda dator som är ansluten till nätverket och VNet. Den här anslutningstypen är bra om du precis har börjat med Azure, eller för utvecklare, eftersom det krävs få eller inga ändringar i ditt befintliga nätverk. Anslutningen använder SSTP-protokollet för att tillhandahålla krypterad kommunikation via Internet mellan datorn och VNet. Svars tiden för en punkt-till-plats-VPN är oförutsägbara eftersom trafiken passerar Internet.

- **Plats-till-plats-VPN:** Upprättas mellan VPN-enheten och en Azure-VPN Gateway. Den här anslutnings typen aktiverar alla lokala resurser som du ger åtkomst till ett VNet. Anslutningen är en IPsec/IKE VPN-anslutning som ger krypterad kommunikation via Internet mellan den lokala enheten och Azure VPN-gatewayen. Svars tiden för en plats-till-plats-anslutning är oförutsägbara eftersom trafiken passerar Internet.

- **Azure ExpressRoute:** Upprättas mellan ditt nätverk och Azure, via en ExpressRoute-partner. Den här anslutningen är privat. Trafiken går inte igenom Internet. Svars tiden för en ExpressRoute-anslutning är förutsägbar eftersom trafiken inte färdas över Internet. Om du vill veta mer om alla tidigare anslutnings alternativ kan du läsa [diagrammen för anslutnings miljön](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Trafikfiltrering**

VM-och Cloud Services roll instanser [nätverks trafiken](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) kan filtreras inkommande och utgående av Källans IP-adress och port, MÅLETs IP-adress och port och protokoll.

Du kan filtrera nätverkstrafik mellan undernät med ett eller båda av följande alternativ:

- **Nätverks säkerhets grupper (NSG):** Varje NSG kan innehålla flera inkommande och utgående säkerhets regler som gör att du kan filtrera trafik efter källa och mål-IP-adress, port och protokoll. Du kan använda en NSG för varje nätverkskort i en virtuell dator. Du kan också använda en NSG till under nätet som ett nätverkskort eller någon annan Azure-resurs, är ansluten till. Läs mer om NSG: er i [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Virtual Network-anordningar:** En virtuell nätverks installation är en virtuell dator som kör program vara som utför en nätverks funktion, till exempel en brand vägg. Visa en lista över tillgängliga NVA i Azure Marketplace. NVA är också tillgängliga som tillhandahåller WAN-optimering och andra funktioner i nätverks trafiken. NVA används vanligt vis med användardefinierade eller BGP-vägar. Du kan också använda en NVA för att filtrera trafik mellan virtuella nätverk.

**Routning**

Du kan välja att åsidosätta Azures standardroutning genom att konfigurera egna vägar eller använda BGP-vägar via en nätverksgateway.

Azure skapar routningstabeller som gör det möjligt för resurser som är anslutna till ett undernät i ett VNet att kommunicera med varandra, som standard. Du kan implementera ett eller båda av följande alternativ för att åsidosätta de standardvägar som Azure skapar:

- **Användardefinierade vägar:** Du kan skapa anpassade routningstabeller med vägar som styr vart trafik dirigeras för varje undernät. Mer information om användardefinierade vägar finns i [användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **BGP-vägar:** Om du ansluter ditt VNet till ditt lokala nätverk med hjälp av en Azure VPN Gateway-eller ExpressRoute-anslutning kan du sprida BGP-vägar till din virtuella nätverk.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hybrid Internet anslutning: Ansluta till ett lokalt nätverk
Du kan ansluta ditt lokala nätverk till ett VNet med valfri kombination av följande alternativ:

-   Internetanslutning

-   Punkt-till-plats-VPN (P2S VPN)

-   VPN för plats-till-plats (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Internet anslutning

Som namnet antyder gör Internet anslutningen att dina arbets belastningar kan nås från Internet, genom att du exponerar olika offentliga slut punkter för arbets belastningar som finns i det virtuella nätverket. Dessa arbets belastningar kan exponeras med hjälp av [Internet Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) eller genom att enkelt tilldela en offentlig IP-adress till den virtuella datorn. På så sätt blir det möjligt för allt på Internet att kunna komma åt den virtuella datorn, förutsatt att en värd brand vägg, [nätverks säkerhets grupper (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)och [användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) tillåter att det sker.

I det här scenariot kan du exponera ett program som måste vara offentligt för Internet och kunna ansluta till det var som helst eller från specifika platser beroende på konfigurationen av dina arbets belastningar.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Punkt-till-plats-VPN eller plats-till-plats-VPN
Dessa två hamnar i samma kategori. De behöver ett virtuellt nätverk för att ha en VPN Gateway och du kan ansluta till den med hjälp av antingen en VPN-klient för din arbets station som en del av en plats [-till-plats-konfiguration](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) eller så kan du konfigurera den lokala [VPN-enheten](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) så att den kan avsluta en plats-till-plats Konfigurera. På så sätt kan lokala enheter ansluta till resurser i det virtuella nätverket.

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning från en enskild klientdator till ett virtuellt nätverk. P2S är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol).

![Punkt-till-plats-VPN](./media/network-security/azure-network-security-fig-5.png)

Punkt-till-plats-anslutningar är användbara när du vill ansluta till ditt VNet från en annan plats, t. ex. hemifrån eller från ett konferens Center, eller när du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk.

P2S-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. Du upprättar VPN-anslutningen från klientdatorn. Därför är P2S inte ett rekommenderat sätt att ansluta till Azure om du behöver en permanent anslutning från många lokala enheter och datorer till ditt Azure-nätverk.

![Plats-till-plats-VPN](./media/network-security/azure-network-security-fig-6.png)

> [!Note]
> Mer information om punkt-till-plats-anslutningar finns i [punkt-till-plats FA v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2).

Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Den här anslutningen sker via Internet och gör att du kan ange "tunnel"-information i en krypterad länk mellan ditt nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. Tunnel kryptering utförs med [IPSec-tunnelläge](https://technet.microsoft.com/library/cc786385.aspx).

Även om plats-till-plats-VPN är en betrodd, tillförlitlig och etablerad teknik, färdas trafiken i tunneln av Internet. Dessutom är bandbredden relativt begränsad till högst cirka 200 Mbit/s.

Om du behöver en exceptionell säkerhets nivå eller prestanda för dina anslutningar mellan olika platser rekommenderar vi att du använder Azure-ExpressRoute för din anslutning mellan olika platser. ExpressRoute är en särskild WAN-länk mellan din lokala plats eller en Exchange-värd leverantör. Eftersom det här är en Telco-anslutning överförs inte dina data via Internet och kan därför inte exponeras för potentiella risker i Internet kommunikation.

> [!Note]
> Mer information om VPN-gatewayer finns i om [VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Dedikerad WAN-länk
Med Microsoft Azure ExpressRoute kan du utöka dina lokala nätverk till Azure via en dedikerad privat anslutning som under lättas av en anslutnings leverantör.

ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.

![ Dedikerad WAN-länk](./media/network-security/azure-network-security-fig-7.png)

> [!Note]
> Information om hur du ansluter nätverket till Microsoft med ExpressRoute finns i [ExpressRoute Connectivity Models](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) and [ExpressRoute Technical Overview](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Precis som med alternativen för plats-till-plats-VPN kan ExpressRoute också ansluta till resurser som inte nödvändigt vis finns i ett VNet. Beroende på SKU: n, kan du i själva verket ansluta till 10 virtuella nätverk. Om du har [Premium-tillägget](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)är det möjligt att ansluta till upp till 100 virtuella nätverk, beroende på bandbredd. Om du vill veta mer om vilka typer av anslutningar som ser ut kan du läsa diagram för att läsa [anslutnings miljön](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Säkerhets kontroller
En Azure-Virtual Network tillhandahåller ett säkert, logiskt nätverk som är isolerat från andra virtuella nätverk och har stöd för många säkerhets kontroller som du använder i dina lokala nätverk. Kunderna skapar sin egen struktur genom att använda: undernät – de använder sina egna privata IP-adressintervall, konfigurerar routningstabeller, nätverks säkerhets grupper, åtkomst kontrol listor (ACL: er), gatewayer och virtuella enheter för att köra sina arbets belastningar i molnet.

Följande är säkerhets kontroller som du kan använda i dina virtuella Azure-nätverk:

-   Nätverks åtkomst kontroller

-   Användardefinierade vägar

-   Nätverks säkerhets installation

-   Application Gateway

-   Brand vägg för Azure-webbprogram

-   Nätverks tillgänglighets kontroll

#### <a name="network-access-controls"></a>Nätverks åtkomst kontroller
Även om Azure-Virtual Network (VNet) är hörnen av Azures nätverks modell och ger isolering och skydd, är [nätverks säkerhets grupper (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) det huvud verktyg som du använder för att genomdriva och kontrol lera nätverks trafik regler på nätverks nivå.

![ Nätverks åtkomst kontroller](./media/network-security/azure-network-security-fig-8.png)


Du kan kontrol lera åtkomst genom att tillåta eller neka kommunikation mellan arbets belastningarna i ett virtuellt nätverk, från system på kundens nätverk via anslutningar mellan olika platser eller direkt kommunikation via Internet.

I diagrammet finns både virtuella nätverk och NSG: er i ett särskilt lager i den övergripande säkerhets stacken för Azure, där NSG: er, UDR och virtuella nätverks enheter kan användas för att skapa säkerhets gränser för att skydda program distributionerna i det skyddade nätverket.

NSG: er använder en 5-tupel för att utvärdera trafik (och används i de regler som du konfigurerar för NSG):

-   [Käll-och mål-IP-adress](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Käll-och mål Port](https://technet.microsoft.com/library/dd197515)

-   Protokoll: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) eller [UDP (User Datagram Protocol)](https://technet.microsoft.com/library/cc940034.aspx)

Det innebär att du kan styra åtkomsten mellan en enskild virtuell dator och en grupp med virtuella datorer, eller en enskild virtuell dator till en annan virtuell dator, eller mellan hela undernät. Kom återigen ihåg att det här är en enkel tillstånds känslig paket filtrering, inte fullständig paket kontroll. Det finns ingen funktion för protokoll validering eller nätverks nivå-ID eller IP-adresser i en nätverks säkerhets grupp.

En NSG levereras med några inbyggda regler som du bör vara medveten om. Dessa är:

-   **Tillåt all trafik i ett enskilt virtuellt nätverk:** Alla virtuella datorer på samma Azure-Virtual Network kan kommunicera med varandra.

-   **Tillåt Azure belastnings utjämning till inkommande:**  den här regeln tillåter trafik från alla käll adresser till alla mål adresser för Azure Load Balancer.

-   **Neka alla inkommande:**  den här regeln blockerar all trafik källa från Internet som du uttryckligen har tillåtit.

-   **Tillåt all utgående trafik till Internet:** Med den här regeln kan virtuella datorer initiera anslutningar till Internet. Om du inte vill att dessa anslutningar ska initieras måste du skapa en regel för att blockera anslutningarna eller tvinga tvingad tunnel trafik.

#### <a name="system-routes-and-user-defined-routes"></a>System vägar och användardefinierade vägar

När du lägger till virtuella datorer i ett virtuellt nätverk (VNet) i Azure, ser du att de virtuella datorerna kan kommunicera med varandra över nätverket, automatiskt. Du behöver inte ange någon gateway, även om VM:arna befinner sig på olika undernät.

Samma sak gäller för kommunikation från VM:arna till det offentliga Internet samt till ditt lokala nätverk när det finns en hybridanslutning från Azure till ditt eget datacenter.

![Systemvägar](./media/network-security/azure-network-security-fig-9.png)

Det här flödet av kommunikation är möjligt eftersom Azure använder en uppsättning systemvägar för att definiera hur IP-trafiken flödar. Systemvägar kontrollerar kommunikationsflödet i följande scenarier:

-   Från inom samma undernät.

-   Från ett undernät till ett annat inom ett VNet.

-   Från VM:ar till Internet.

-   Från ett VNet till ett annat VNet via en VPN-gateway.

-   Från ett VNet till ett annat VNet via VNet-peering ([tjänst länkning](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Från ett VNet till ditt lokala nätverk via en VPN-gateway.

Många företag har strikta krav på säkerhet och efterlevnad som kräver lokal inspektion av alla nätverks paket för att genomdriva särskilda principer. Azure tillhandahåller en mekanism som kallas [Tvingad tunnel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) trafik som dirigerar trafik från de virtuella datorerna till lokala enheter genom att skapa en anpassad väg eller av en [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) -annonser via ExpressRoute eller VPN.

Tvingad tunneltrafik i Azure konfigureras via virtuella nätverk användardefinierade vägar (UDR). Omdirigerar trafik till en lokal plats uttrycks som en standardväg till Azure VPN-gatewayen.

I följande avsnitt visas den aktuella begränsningen Routning och vägar för ett virtuellt Azure-nätverk:

- Varje virtuellt nätverksundernät har en inbyggd, system-routningstabell. Routningstabellen system har följande tre grupper av vägar:

  -  **Lokala VNet-vägar:** Direkt till de virtuella mål datorerna i samma virtuella nätverk

  - **Lokala vägar:** Till Azure VPN-gatewayen

  -  **Standard väg:** Direkt till Internet. Paket som är avsedda för privata IP-adresser som inte omfattas av de föregående två vägarna ignoreras.

- Med den här versionen av användardefinierade vägar kan du skapa en routningstabell för att lägga till en standard väg och sedan koppla routningstabellen till VNet-undernätet för att aktivera Tvingad tunnel trafik på dessa undernät.

- Du måste ange en ”standardwebbplats” mellan de över flera lokala platserna anslutna till det virtuella nätverket.

- Tvingad tunneltrafik måste vara associerad med ett virtuellt nätverk som har en VPN-gateway för dynamisk routning (inte en statisk gateway).

- ExpressRoute Tvingad tunneltrafik är inte konfigurerad via den här mekanismen, men i stället aktiveras genom att annonsera en standardväg via ExpressRoute BGP-peeringsessioner.

> [!Note]
> Mer information finns i ExpressRoute- [dokumentationen](https://azure.microsoft.com/documentation/services/expressroute/) .

#### <a name="network-security-appliances"></a>Nätverks säkerhets enheter
Även om nätverks säkerhets grupper och användardefinierade vägar kan ge ett visst mått på nätverks säkerhet på Nätverks-och transport lager i OSI- [modellen](https://en.wikipedia.org/wiki/OSI_model), kommer det att finnas situationer där du vill eller behöver aktivera säkerhet på högre nivåer av nätverks stack. I sådana situationer rekommenderar vi att du distribuerar virtuella nätverks säkerhets enheter från Azure-partner.

![Nätverks säkerhets enheter](./media/network-security/azure-network-security-fig-10.png)

Azure Network Security-enheter förbättrar VNet-säkerhet och nätverks funktioner, och de är tillgängliga från flera leverantörer via [Azure Marketplace](https://azuremarketplace.microsoft.com). Dessa virtuella säkerhetsfunktioner kan distribueras för att tillhandahålla:

-   Brand väggar med hög tillgänglighet

-   Skydd mot intrång

-   Identifiering av intrång

-   Brand väggar för webbaserade program (WAF)

-   WAN-optimering

-   Routning

-   Belastningsutjämning

-   VPN

-   Certifikathantering

-   Active Directory

-   Multifaktorautentisering

#### <a name="application-gateway"></a>Programgateway

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) är en dedikerad virtuell installation som tillhandahåller en program leverans kontroll (ADC) som en tjänst.

 ![Application Gateway](./media/network-security/azure-network-security-fig-11.png)

Med Application Gateway kan du optimera webb server gruppens prestanda och tillgänglighet genom att avlasta processor intensiva SSL-avslutning till Application Gateway (SSL-avlastning). Den innehåller också andra funktioner för Layer 7-routning, inklusive:

-   Resursallokering (Round-Robin) distribution av inkommande trafik

-   Cookie-baserad session tillhörighet

-   URL-sökväg baserad på Routning

-   Möjlighet att vara värd för flera webbplatser bakom en enda Application Gateway


En [brand vägg för webbaserade program (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) ingår också som en del av programgatewayen. Detta ger skydd för webb program från vanliga webb sårbarheter och sårbarheter. Application Gateway kan konfigureras som en gateway på Internet, en intern gateway eller en kombination av båda.

Application Gateway WAF kan köras i identifierings-eller skydds läge. Ett vanligt användnings fall är att administratörer kan köras i identifierings läge för att observera trafik för skadliga mönster. När potentiella sårbarheter upptäcks, kan du aktivera skydds läget och förhindra misstänkt inkommande trafik.

 ![Application Gateway](./media/network-security/azure-network-security-fig-12.png)

Dessutom hjälper Application Gateway WAF dig att övervaka webb program mot attacker med en WAF-logg i real tid som är integrerad med [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) och [Azure Security Center](https://azure.microsoft.com/services/security-center/) för att spåra WAF aviseringar och enkelt övervaka trender.

Den JSON-formaterade loggen går direkt till kundens lagrings konto. Du har fullständig kontroll över dessa loggar och kan tillämpa dina egna bevarande principer.

Du kan också mata in dessa loggar i ditt eget analys system med hjälp av [Azure log integration](https://aka.ms/AzLog). WAF-loggar är också integrerade med [Azure Monitor loggar](/azure/log-analytics/log-analytics-overview) så att du kan använda Azure Monitors loggar för att köra sofistikerade detaljerade frågor.

#### <a name="azure-web-application-firewall-waf"></a>Brand vägg för Azure-webbprogram (WAF)

Webb program är allt större av skadliga attacker som utnyttjar vanliga kända sårbarheter, till exempel SQL-inmatning, kors webbplats skript attacker och andra attacker som visas i [OWASP 10 högsta](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Att förhindra sådan användning i programmet kräver rigoröst underhåll, uppdatering och övervakning på flera lager i programtopologin.

 ![Brand vägg för Azure-webbprogram (WAF)](./media/network-security/azure-network-security-fig-13.png)

En centraliserad [brand vägg för webbaserade program (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) kan skydda mot webb attacker och förenkla säkerhets hanteringen utan att några program ändringar krävs.

En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

#### <a name="network-availability-controls"></a>Nätverks tillgänglighets kontroller

Det finns olika alternativ för att distribuera nätverkstrafik med Microsoft Azure. De här alternativen fungerar på olika sätt, har olika funktionsuppsättningar och stöder olika scenarier. De kan användas var för sig eller i kombination med varandra.

Nedan visas nätverks tillgänglighets kontrollerna:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Azure Load Balancer**

Ger hög tillgänglighet och nätverks prestanda till dina program. Det är en belastningsutjämnare för Layer 4 (TCP, UDP) som distribuerar inkommande trafik mellan felfria instanser av tjänster som definierats i en belastningsutjämnad uppsättning.

 ![Azure Load Balancer](./media/network-security/azure-network-security-fig-14.png)


Azure Load Balancer kan konfigureras för att:

-   Belastningsutjämna inkommande Internet trafik till virtuella datorer. Den här konfigurationen kallas för [belastnings utjämning mot Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Belastnings Utjämnings trafik mellan virtuella datorer i ett virtuellt nätverk, mellan virtuella datorer i moln tjänster eller mellan lokala datorer och virtuella datorer i ett virtuellt nätverk mellan olika platser. Den här konfigurationen kallas [intern belastnings utjämning](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Vidarebefordra extern trafik till en enskild virtuell dator.

Alla resurser i molnet måste ha en offentlig IP-adress för att kunna kommas åt från Internet. Moln infrastrukturen i Azure använder icke-flyttbara IP-adresser för resurserna. Azure använder Network Address Translation (NAT) med offentliga IP-adresser för att kommunicera med Internet.

 **Application Gateway**

 Application Gateway fungerar på program lagret (skikt 7 i OSI-nätverkets referens stack). Den fungerar som en omvänd proxy-tjänst och avslutar klientanslutningen och vidarebefordrar begäranden till slutpunkter i serverdelen.

 **Traffic Manager**

Microsoft Azure Traffic Manager gör att du kan styra distributionen av användar trafik för tjänst slut punkter i olika data Center. Tjänst slut punkter som stöds av Traffic Manager inkluderar virtuella Azure-datorer, Web Apps och moln tjänster. Du kan även använda Traffic Manager med externa slutpunkter som inte tillhör Azure.

Traffic Manager använder Domain Name System (DNS) för att dirigera klient begär anden till den lämpligaste slut punkten baserat på en [Traffic-routningsmetod](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) och tillståndet för slut punkterna. Traffic Manager tillhandahåller en mängd metoder för trafik cirkulation som passar olika program behov, [övervakning](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)av slut punkts hälsa och automatisk redundans. Traffic Manager har bra återhämtningsförmåga i händelse av fel, inklusive fel som påverkar en hel Azure-region.

Med Azure Traffic Manager kan du styra trafiken mellan dina program slut punkter. En slutpunkt är en Internetansluten tjänst i eller utanför Azure.

Traffic Manager ger två viktiga fördelar:

-   Trafik spridning enligt en av flera metoder för [trafik cirkulation](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Kontinuerlig övervakning av slut punkts hälsa](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) och automatisk redundans när slut punkter inte fungerar.

När en klient försöker ansluta till en tjänst måste den först matcha DNS-namnet för tjänsten med en IP-adress. Klienten ansluter sedan till den IP-adressen för att få åtkomst till tjänsten. Traffic Manager använder DNS för att dirigera klienter till vissa tjänst slut punkter baserat på reglerna för metoden för trafik dirigering. Klienterna ansluter till den valda slut punkten direkt. Traffic Manager är inte en proxy eller gateway. Traffic Manager ser inte trafiken som passerar mellan klienten och tjänsten.

### <a name="azure-network-validation"></a>Verifiering av Azure-nätverk

Azure nätverks verifiering är att säkerställa att Azure-nätverket fungerar som det har kon figurer ATS och verifiering kan göras med hjälp av de tjänster och funktioner som finns tillgängliga för att övervaka nätverket. Med Azure Network Watcher kan du komma åt en mängd olika med loggnings-och diagnostiska funktioner som ger dig insikter om hur du kan förstå dina nätverks prestanda och din hälsa. Dessa funktioner är tillgängliga via portalen, Power Shell, CLI, REST API och SDK.

Azures drift säkerhet syftar på tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda data, program och andra till gångar i Microsoft Azure. Azures drift säkerhet bygger på ett ramverk som införlivar den kunskap som du har fått via en mängd olika funktioner som är unika för Microsoft, inklusive Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Centre-programmet och djupgående medvetenhet om cyberhot-säkerhetshotets landskap.

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage analys](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure Resource Manager

De personer och processer som använder Microsoft Azure är kanske de viktigaste säkerhetsfunktionerna i plattformen. I det här avsnittet beskrivs funktionerna i Microsofts globala data Center infrastruktur som hjälper till att förbättra och upprätthålla säkerhet, kontinuitet och sekretess.

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

-   Du kan klargöra din organisations fakturering genom att Visa kostnader för en grupp med resurser som delnings tag gen.

> [!Note]
> Resource Manager erbjuder ett nytt sätt att distribuera och hantera lösningar. Om du använde den tidigare distributionsmodellen och vill veta mer om ändringarna läser du [Förstå Resource Manager-distribution och klassisk distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Azure-nätverks loggning och övervakning

Azure erbjuder många verktyg för att övervaka, förhindra, identifiera och reagera på nätverks säkerhets händelser. Några av de mest kraftfulla verktygen som är tillgängliga i det här avsnittet är:

-   Network Watcher

-   Övervakning av nätverks resurs nivå

-   Azure Monitor-loggar

### <a name="network-watcher"></a>Nätverksbevakare

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -scenario-baserad övervakning tillhandahålls med funktionerna i Network Watcher. Den här tjänsten omfattar paket insamling, nästa hopp, kontrol lera IP-flöde, vyn säkerhets grupp, NSG Flow-loggar. Övervakning av scenario nivå ger en heltäckande vy över nätverks resurser i motsats till övervakning av enskilda nätverks resurser.

 ![Network Watcher](./media/network-security/azure-network-security-fig-15.png)

Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks scenario nivå i, till och från Azure. Diagnostikverktyg för nätverk och visualiserings verktyg som är tillgängliga med Network Watcher hjälpa dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure.

Network Watcher har för närvarande följande funktioner:

#### <a name="topology"></a>Topologi

[Topologi](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) returnerar ett diagram över nätverks resurser i ett virtuellt nätverk. Diagrammet visar sammanlänkningen mellan resurserna som motsvarar slut punkt till slut punkt för nätverks anslutningen. I portalen returnerar topologi resurs objekt på samma sätt som per virtuellt nätverk. Relationerna illustreras med rader mellan resurserna utanför den Network Watcher regionen, även om de inte visas i resurs gruppen. Resurserna som returneras i vyn Portal är en del av de nätverks komponenter som visas i diagrammet. Om du vill se en fullständig lista över nätverks resurser kan du använda [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) eller [rest](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

När resurserna returneras är anslutningen mellan de modelleras under två relationer.

- **Inne slutning** -Virtual Network innehåller ett undernät, som innehåller ett nätverkskort.

- **Associerad** – ett nätverkskort är associerat med en virtuell dator.

#### <a name="variable-packet-capture"></a>Variabla infångade paket

Med Network Watcher [variabel paket fångst](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) kan du skapa sessioner för att skapa paket och spåra trafik till och från en virtuell dator. Med paket fångst kan du diagnostisera nätverks avvikelser både återaktivt och proaktivt. Andra användnings områden innefattar insamling av nätverks statistik, få information om nätverks intrång, för att felsöka klient-server-kommunikation och mycket mer.

Paket fångst är ett tillägg för virtuell dator som har startats via en fjärr anslutning via Network Watcher. Den här funktionen fören klar belastningen på att köra en paket registrering manuellt på önskad virtuell dator, vilket sparar värdefull tid. Paket fångst kan utlösas via portalen, PowerShell, CLI eller REST API. Ett exempel på hur paket fångst kan utlösas är med aviseringar för virtuella datorer.

#### <a name="ip-flow-verify"></a>Kontrollera IP-flöde

[IP-flöden verifierar](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) kontroller om ett paket tillåts eller nekas till eller från en virtuell dator baserat på 5 tuple-information. Den här informationen består av riktning, protokoll, lokal IP-adress, fjärr-IP, lokal port och fjärran sluten port. Om paketet nekas av en säkerhets grupp returneras namnet på den regel som nekade paketet. När alla käll-eller mål-IP-adresser kan väljas hjälper den här funktionen administratörer att snabbt diagnostisera anslutnings problem från eller till Internet och från eller till den lokala miljön.

IP-flöden verifierar mål ett nätverks gränssnitt för en virtuell dator. Trafikflödet verifieras sedan baserat på de konfigurerade inställningarna till eller från det nätverks gränssnittet. Den här funktionen är användbar vid bekräftelse av om en regel i en nätverks säkerhets grupp blockerar ingångs-eller utgående trafik till eller från en virtuell dator.

#### <a name="next-hop"></a>Nästa hopp

Anger [nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) för paket som dirigeras i Azures nätverks infrastruktur resurs, så att du kan diagnostisera eventuella felkonfigurerade användardefinierade vägar. Trafik från en virtuell dator skickas till ett mål baserat på de effektiva vägar som är kopplade till ett nätverkskort. Nästa hopp hämtar nästa hopp typ och IP-adress för ett paket från en speciell virtuell dator och NIC. Detta hjälper dig att avgöra om paketet dirigeras till målet eller om trafiken blir svart.

Nästa hopp returnerar också den routningstabell som är associerad med nästa hopp. När du frågar efter nästa hopp om vägen definieras som en användardefinierad väg returneras den vägen. Annars returnerar nästa hopp "system Route".

#### <a name="security-group-view"></a>Säkerhetsgruppvy

Hämtar de effektiva och tillämpade säkerhets reglerna som tillämpas på en virtuell dator. Nätverks säkerhets grupper är kopplade till en under näts nivå eller på en NÄTVERKSKORTs nivå. När den är kopplad till en under näts nivå tillämpas den på alla virtuella dator instanser i under nätet. [Vyn nätverks säkerhets grupp](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) returnerar alla konfigurerade NSG: er och regler som är kopplade till ett nätverkskort och en under näts nivå för en virtuell dator som ger inblick i konfigurationen. Dessutom returneras de effektiva säkerhets reglerna för varje nätverkskort i en virtuell dator. Med vyn nätverks säkerhets grupp kan du utvärdera en virtuell dator för nätverks sårbarheter, till exempel öppna portar. Du kan också kontrol lera om nätverks säkerhets gruppen fungerar som förväntat baserat på en [jämförelse mellan de konfigurerade och gällande säkerhets reglerna](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>NSG flödes loggning

 Flödes loggar för nätverks säkerhets grupper gör att du kan avbilda loggar som är relaterade till trafik som tillåts eller nekas av säkerhets reglerna i gruppen. Flödet definieras av en 5-tupel-information – käll-IP, mål-IP, källport, mål Port och protokoll.

[Flödes loggar för nätverks säkerhets grupper](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) är en funktion i Network Watcher som gör att du kan visa information om inkommande och utgående IP-trafik via en nätverks säkerhets grupp.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Virtuell nätverksgateway och anslutnings fel sökning

Network Watcher innehåller många funktioner som är relaterade till förståelse av dina nätverks resurser i Azure. En av dessa funktioner är resurs fel sökning. [Resurs fel sökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) kan anropas av POWERSHELL, CLI eller REST API. När den anropas kontrollerar Network Watcher hälsan för en Virtual Network gateway eller en anslutning och returnerar resultatet.

Det här avsnittet tar dig igenom de olika hanterings aktiviteter som för närvarande är tillgängliga för resurs fel sökning.

-   [Felsöka en Virtual Network Gateway](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Felsöka en anslutning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Begränsningar för nätverks prenumeration

[Begränsningar för nätverks prenumeration](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) ger dig information om användningen av varje nätverks resurs i en prenumeration i en region mot det maximala antalet resurser som är tillgängliga.

#### <a name="configuring-diagnostics-log"></a>Konfigurera diagnostikloggar

Network Watcher innehåller en vy för [diagnostiska loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) . Den här vyn innehåller alla nätverks resurser som stöder diagnostisk loggning. I den här vyn kan du aktivera och inaktivera nätverks resurser enkelt och snabbt.

### <a name="network-resource-level-monitoring"></a>Övervakning av nätverks resurs nivå

Följande funktioner är tillgängliga för övervakning på resurs nivå:

#### <a name="audit-log"></a>Granskningslogg

Åtgärder som utförs som en del av konfigurationen av nätverk loggas. Dessa gransknings loggar är nödvändiga för att upprätta olika efterlevnadar. Dessa loggar kan visas i Azure Portal eller hämtas med hjälp av Microsoft-verktyg som Power BI eller verktyg från tredje part. Gransknings loggar är tillgängliga via portalen, PowerShell, CLI och REST API.

> [!Note]
> Mer information om gransknings loggar finns i [gransknings åtgärder med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Gransknings loggar är tillgängliga för åtgärder som utförs på alla nätverks resurser.


#### <a name="metrics"></a>Mått

Mått är prestanda mått och räknare som samlas in under en period. Mått är för närvarande tillgängliga för Application Gateway. Mått kan användas för att utlösa aviseringar baserat på tröskel. Azure Application Gateway övervakar som standard hälsan för alla resurser i sin backend-pool och tar automatiskt bort alla resurser som betraktas som felaktiga från poolen. Application Gateway fortsätter att övervaka skadade instanser och lägger tillbaka dem till den felfria backend-poolen när de blir tillgängliga och svarar på hälso avsökningar. Application Gateway skickar hälso avsökningar med samma port som definieras i Server delens HTTP-inställningar. Den här konfigurationen säkerställer att avsökningen testar samma port som kunderna skulle använda för att ansluta till Server delen.

> [!Note]
> Se [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) för att se hur mått kan användas för att skapa aviseringar.

#### <a name="diagnostic-logs"></a>Diagnostikloggar

Periodiska och spontana händelser skapas av nätverks resurser och loggas i lagrings konton, som skickas till en Händelsehubben eller Azure Monitor loggar. Dessa loggar ger insikter om hälso tillståndet för en resurs. Dessa loggar kan visas i verktyg som Power BI och Azure Monitor loggar. Information om hur du visar diagnostikloggar finns [Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Diagnostikloggar är tillgängliga för [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), vägar och [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher innehåller en vy för diagnostiska loggar. Den här vyn innehåller alla nätverks resurser som stöder diagnostisk loggning. I den här vyn kan du aktivera och inaktivera nätverks resurser enkelt och snabbt.

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) är en tjänst i Azure som övervakar molnet och lokala miljöer för att bibehålla deras tillgänglighet och prestanda. Den samlar in data som genereras av resurser i dina miljöer i molnet och lokalt och från andra övervakningsverktyg för att tillhandahålla analyser över flera källor.

Azure Monitor loggar innehåller följande lösningar för att övervaka dina nätverk:

-   Övervakare av nätverksprestanda (NPM)

-   Azure Application Gateway-analys

-   Analys av Azure nätverks säkerhets grupp

#### <a name="network-performance-monitor-npm"></a>Övervakaren nätverks prestanda (NPM)
Den [övervakare av nätverksprestanda](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) hanterings lösningen är en lösning för nätverks övervakning som övervakar hälso tillstånd, tillgänglighet och nåbarhet för nätverk.

Den används för att övervaka anslutningen mellan:

-   offentligt moln och lokalt

-   Data Center och användar platser (avdelnings kontor)

-   undernät är värdar för olika nivåer av ett program med flera nivåer.


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Azure Application Gateway Analytics i Azure Monitor loggar

Följande loggar stöds för Application Gateway:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Följande mått stöds för Application Gateway:

-   5 minuters data flöde

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Analys av Azure nätverks säkerhets grupper i Azure Monitor loggar

Följande loggar stöds för [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** Innehåller poster för vilka NSG-regler tillämpas på virtuella datorer och instans roller baserade på MAC-adress. Status för dessa regler samlas in var 60: e sekund.

- **NetworkSecurityGroupRuleCounter:** Innehåller poster för hur många gånger varje NSG-regel används för att neka eller tillåta trafik.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om säkerhet genom att läsa några av våra djupgående säkerhets ämnen:

-   [Azure Monitor loggar för nätverks säkerhets grupper (NSG: er)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Nätverks innovationer som driver moln störningar](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [Ljud Nätverks växelns program vara som har behörighet för Microsoft Global Cloud](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Hur Microsoft bygger sitt snabba och pålitliga globala nätverk](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Belysning av nätverks innovation](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
