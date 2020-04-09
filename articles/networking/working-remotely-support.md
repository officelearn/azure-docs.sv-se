---
title: Arbeta på distans med Azure-nätverkstjänster
description: På den här sidan beskrivs hur du kan använda Azure-nätverkstjänster som är tillgängliga för att möjliggöra fjärrbearbetning och hur du kan minska trafikproblem till följd av ett ökat antal personer som arbetar på distans.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982862"
---
# <a name="working-remotely-using-azure-networking-services"></a>Arbeta på distans med Azure-nätverkstjänster

>[!NOTE]
> I den här artikeln beskrivs hur du kan utnyttja Azure-nätverkstjänster, Microsoft-nätverk och Azure-partnerekosystemet för att arbeta på distans och minska nätverksproblem som du kan ställas inför på grund av COVID-19-krisen.

I den här artikeln beskrivs de alternativ som är tillgängliga för organisationer för att konfigurera fjärråtkomst för sina användare eller för att komplettera sina befintliga lösningar med ytterligare kapacitet under perioder med maximal användning. Nätverksarkitekter står inför följande utmaningar:

- Åtgärda en ökning av nätverksanvändningen.
- Tillhandahålla tillförlitlig anslutning till fler anställda i företaget och kunderna.
- Tillhandahålla anslutning till avlägsna platser över hela världen.

Alla nätverk (till exempel privata WAN- och företagskärnnätverk) upplever inte överbelastning från maximal fjärrarbetsbelastning. Flaskhalsarna rapporteras vanligen endast i hembredbandsnät och VPN-gateways av lokala nätverk av företag.

Nätverksplanerare kan hjälpa till att lindra flaskhalsarna och minska nätverksöverbelastningen genom att tänka på att olika trafiktyper behöver olika prioriteringar för nätverksbehandling och genom viss smart omdirigering/distribution av belastning. Till exempel, realtid tele-medecine trafik av läkare-patient interaktion är av stor betydelse och fördröjning / nervositet känslig. Replikering av samma trafik mellan lagringar är inte fördröjningskänslig. Den tidigare trafiken måste dirigeras via den mest optimala nätvägen med högre servicekvalitet. Det är godtagbart att dirigera den senare trafiken via suboptimal väg.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Dela våra bästa metoder – Azure-nätverket är utformat för elasticitet och hög tillgänglighet

Azure är utformat för att tåla plötsliga förändringar i användningen av resurser och kan i hög grad hjälpa till under perioder av maximal användning. Dessutom underhåller och driver Microsoft ett av världens största nätverk. Microsofts nätverk har utformats för hög tillgänglighet som tål olika typer av fel: från ett enda nätverkselement som inte fungerar i en hel region.

Microsoft-nätverket är utformat för att uppfylla kraven och ger optimal prestanda för olika typer av nätverkstrafik, inklusive fördröjning av känslig multimedietrafik för Skype och Teams, CDN, stordataanalys i realtid, Azure-lagring, Bing och Xbox. För att ge optimal prestanda för olika typer av trafik drar Microsoft-nätverket till sig all trafik som är avsedd att- eller vill transitera genom- sina resurser så nära trafikens ursprung som möjligt.

>[!NOTE] 
>Genom att använda azure-nätverksfunktionerna som beskrivs nedan utnyttjas trafikattraktionsbeteendet i Microsofts globala nätverk för att ge en bättre kundnätverksupplevelse. Beteendet för trafikattraktion i Microsoft-nätverket hjälper till att avlasta trafik så snart som möjligt från de första/sista milens nätverk som kan uppleva överbelastning under perioder av topputnyttjande.
>

## <a name="enable-employees-to-work-remotely"></a>Gör det möjligt för anställda att arbeta på distans

Azure VPN-gateway stöder både P2S-anslutningar (Point-to-Site) och S2S-anslutningar (Site-to-Site). Med Hjälp av Azure VPN-gatewayen kan du skala dina anställdas anslutningar för att på ett säkert sätt komma åt både dina Azure-distribuerade resurser och dina lokala resurser. Mer information finns i [Så här aktiverar du användare att arbeta på distans](../vpn-gateway/work-remotely-support.md). 

Om du använder SSTP (Secure Sockets Tunneling Protocol) är antalet samtidiga anslutningar begränsat till 128. För att få ett högre antal anslutningar föreslår vi en övergång till OpenVPN eller IKEv2. Mer information finns i [Övergång till OpenVPN-protokoll eller IKEv2 från SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

För att komma åt dina resurser som distribueras i Azure kan fjärrutvecklare använda Azure Bastion-lösning, i stället för VPN-anslutning för att få säker shell-åtkomst (RDP eller SSH) utan att kräva att offentliga IPs på de virtuella datorerna används. Mer information finns i [Arbeta på distans med Azure Bastion](../bastion/work-remotely-support.md).

För att samla storskalig VPN-anslutning, för att stödja alla anslutningar mellan resurser på olika lokala globala platser, i olika regionala nav och ekrade virtuella nätverk, och för att optimera utnyttjandet av flera hem bredbandsnätverk kan du använda Azure Virtual WAN. För mer information, se [Kämpar för att tillgodose arbetet hemifrån behov? Här kan Azure Virtual WAN hjälpa .](../virtual-wan/work-remotely-support.md)

Ett annat sätt att stödja en fjärrarbetskraft är att distribuera en VDI-infrastruktur (Virtual Desktop Infrastructure) som finns i ditt virtuella Azure-nätverk, skyddad med en Azure-brandvägg. Windows Virtual Desktop (WVD) är till exempel en skrivbords- och appvirtualiseringstjänst som körs i Azure. Med Windows Virtual Desktop kan du konfigurera en skalbar och flexibel miljö i din Azure-prenumeration utan att behöva köra ytterligare gatewayservrar. Du är bara ansvarig för virtuella WVD-datorer i det virtuella nätverket. Mer information finns i [Azure Firewall fjärrarbetsstöd](../firewall/remote-work-support.md). 

Azure har också en rik uppsättning eco-systempartner. Våra partners Virtuella nätverksinstallationer på Azure kan också hjälpa till att skala VPN-anslutningen. Mer information finns i [NVA-överväganden (Network Virtual Appliance) för fjärrarbete](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Utöka medarbetares anslutning för att komma åt globalt distribuerade resurser

Följande Azure-tjänster kan hjälpa anställda att komma åt dina globalt distribuerade resurser. Dina resurser kan finnas i någon av Azure-regioner, lokala nätverk eller till och med i andra offentliga eller privata moln. 

- **Azure virtuell nätverks peering:** Om du distribuerar dina resurser i mer än en Azure-regioner och /eller om du aggregera anslutningen av fjärrarbetare med hjälp av flera virtuella nätverk, kan du upprätta anslutning mellan flera Virtuella Azure-nätverk med hjälp av virtuell nätverks peering. Mer information finns i [Virtual Network peering][VNet-peer].

- **Azure VPN-baserad lösning:** För dina fjärranställda som är anslutna till Azure via P2S eller S2S VPN kan du aktivera åtkomst till lokala nätverk genom att konfigurera S2S VPN mellan dina lokala nätverk och Azure VPN-gateway. Mer information finns i [Skapa en anslutning från plats till plats][S2S].

- **ExpressRoute**: Med Hjälp av ExpressRoute privat peering kan du aktivera privat anslutning mellan dina Azure-distributioner och lokal infrastruktur eller din infrastruktur i en samlokaliseringsfunktion. ExpressRoute, via Microsoft peering, tillåter också åtkomst till offentliga slutpunkter i Microsoft från ditt lokala nätverk. ExpressRoute-anslutningar går inte via offentligt Internet. De erbjuder säker anslutning, tillförlitlighet, högre dataflöde, med lägre och konsekventa svarstider än typiska anslutningar via Internet. Mer information finns i [Översikt över ExpressRoute][ExR]. Genom att utnyttja din befintliga nätverksleverantör som redan ingår i vårt [ExpressRoute-partnerekosystem][ExR-eco] kan du minska tiden för att få stora bandbreddsanslutningar till Microsoft.  Med [ExpressRoute Direct][ExR-D] kan du ansluta ditt lokala nätverk direkt till Microsofts stamnät. ExpressRoute Direct erbjuder två olika linjeprisalternativ med dubbla 10 Gbit/s eller 100 Gbit/s. 

- **Azure Virtual WAN**: Azure Virtual WAN möjliggör sömlös interoperabilitet mellan dina VPN-anslutningar och ExpressRoute-kretsar. Som tidigare nämnts stöder Azure Virtual WAN även alla anslutningar mellan resurser på olika globala platser på prem, i olika regionala nav och eker virtuella nätverk

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Skala kundanslutningen till frontend-resurser

Under tider när fler människor går online, många företagswebbplatser upplever ökad kundtrafik. Azure Application Gateway kan hjälpa till att hantera den ökade frontend-arbetsbelastningen. Mer information finns i [Stöd för programgateway med hög trafik](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft-stöd för trafik med flera moln

För dina distributioner i andra offentliga moln kan Microsoft tillhandahålla global anslutning. Azure Virtual WAN, VPN eller ExpressRoute kan hjälpa till i detta avseende. Om du vill utöka anslutningen från Azure till andra moln kan du konfigurera S2S VPN mellan de två molnen. Du kan också upprätta anslutning från Azure till andra offentliga moln med ExpressRoute. Oracle-molnet är en del av ExpressRoutes partnerekosystem. Du kan [ställa in en direkt sammankoppling mellan Azure och Oracles molninfrastruktur][Az-OCI]. De flesta tjänsteleverantörer som ingår i ExpressRoutes partnerekosystem erbjuder också privat anslutning till andra offentliga moln. Genom att utnyttja dessa tjänsteleverantörer kan du upprätta privat anslutning mellan dina distributioner i Azure och andra moln via ExpressRoute.

## <a name="next-steps"></a>Nästa steg

I följande artiklar beskrivs hur olika Azure-nätverksfunktioner kan användas för att skala användare så att de arbetar på distans:

| **Artikel** | **Beskrivning** |
| --- | --- |
| [Så här gör du det möjligt för användare att arbeta på distans](../vpn-gateway/work-remotely-support.md) | Granska tillgängliga alternativ för att konfigurera fjärråtkomst för användare eller för att komplettera deras befintliga lösningar med ytterligare kapacitet för din organisation.|
| [Kämpar för att tillgodose arbetet hemifrån behov? Här kan Azure Virtual WAN hjälpa](../virtual-wan/work-remotely-support.md) | Använd Azure Virtual WAN för att åtgärda organisationens fjärranslutningsbehov.|
| [Stöd för höga trafikvolymer i Application Gateway](../application-gateway/high-traffic-support.md) | Använd Application Gateway with Web Application Firewall (WAF) för ett skalbart och säkert sätt att hantera trafik till dina webbprogram. |
| [Nva-överväganden (Network Virtual Appliance) för fjärrarbete](../vpn-gateway/nva-work-remotely-support.md)|Granska vägledning om hur du använder NVA:er i Azure för att tillhandahålla fjärråtkomstlösningar. |
| [Övergång till OpenVPN-protokoll eller IKEv2 från SSTP](https://go.microsoft.com/fwlink/?linkid=2124112) | Övervinner anslutningsgränsen på 128 samtidiga anslutningar för SSTP genom att övergå till OpenVPN-protokollet eller IKEv2.|
| [Arbeta på distans med Azure Bastion](../bastion/work-remotely-support.md) | Tillhandahålla säker och sömlös RDP/SSH-anslutning till virtuella datorer i det virtuella Azure-nätverket, direkt i Azure-portalen, utan användning av en offentlig IP-adress. |
| [Använda Azure ExpressRoute för att skapa hybridanslutning för att stödja fjärranvändare](../expressroute/work-remotely-support.md) | Använd ExpressRoute för hybridanslutning för att göra det möjligt för användare i organisationen att arbeta på distans.|
| [Support för Fjärrarbete i Azure Firewall](../firewall/remote-work-support.md)|Skydda dina virtuella Azure-nätverksresurser med Azure-brandväggen. |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
