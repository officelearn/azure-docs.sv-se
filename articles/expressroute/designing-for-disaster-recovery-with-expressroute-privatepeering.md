---
title: Utformning för katastrofåterställning med Azure ExpressRoute | Microsoft Docs
description: Den här sidan innehåller arkitektoniska rekommendationer för katastrofåterställning när du använder Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466075"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Utformning för katastrofåterställning med ExpressRoute privat peering

ExpressRoute är utformat för hög tillgänglighet för operatör i företagsklass privat nätverksanslutningar till Microsoft-resurser. Det finns alltså ingen enskild felpunkt i ExpressRoute-sökvägen i Microsoft-nätverket. Designöverväganden för att maximera tillgängligheten för en ExpressRoute-krets, se [utforma för hög tillgänglighet med ExpressRoute][HA].

Dock tar Murphys populära ordspråket--*om något går fel, kommer det att*--i beräkningen, i den här artikeln kan vi fokusera på lösningar som går bortom fel som kan åtgärdas med en enda ExpressRoute-krets. Med andra ord i den här artikeln Låt oss titta på Nätverksöverväganden arkitektur för att skapa robusta serverdel nätverksanslutningar för haveriberedskap med hjälp av geo-redundant ExpressRoute-kretsar.

## <a name="need-for-redundant-connectivity-solution"></a>Behovet av redundant anslutning lösning

Det finns möjligheter och instanser där hela regionala tjänsten (oavsett om det som Microsoft, network-leverantörer, kundens eller andra leverantörer av molntjänster) hämtar försämrad. Den grundläggande orsaken för sådana inverkan på regionala wide tjänsten omfattar naturlig calamity. Därför, för verksamhetskontinuitet och verksamhetskritiska verksamhetskritiska program är det viktigt att planera för katastrofåterställning.   

Oavsett om du kör dina verksamhetskritiska program i en Azure-region eller en lokal eller någon annanstans, kan du använda en annan Azure-region som plats för redundans. I följande artiklar adresser haveriberedskap från program och perspektiv för frontend-åtkomst:

- [Haveriberedskap i företagsskala][Enterprise DR]
- [SMB-haveriberedskap med Azure Site Recovery][SMB DR]

Om du förlitar dig på ExpressRoute-anslutningen mellan ditt lokala nätverk och Microsoft för verksamhetskritiska kritiska åtgärder, bör din haveriberedskapsplan också innehålla geo-redundant nätverksanslutning. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Utmaningar med att använda flera ExpressRoute-kretsar

När du sammankoppling samma uppsättning nätverk med hjälp av mer än en anslutning, introducerar parallella sökvägar mellan nätverk. Parallell sökvägar, när de inte korrekt skapats, kan leda till asymmetrisk routning. Om du har tillståndskänsliga enheter (till exempel NAT, brandvägg) i sökvägen blockerar asymmetrisk routning flödet i nätverkstrafiken.  Över ExpressRoute privat peering sökvägen stöta inte du normalt på tillståndskänsliga entiteter, till exempel NAT eller brandvägg. Därför blockerar asymmetrisk Routning över ExpressRoute privat peering inte nödvändigtvis flödet i nätverkstrafiken.
 
Om du läser in belastningsutjämna trafik mellan geo-redundant parallella sökvägar, oavsett om du har tillståndskänsliga entiteter eller inte, skulle du uppleva inkonsekvent nätverkets prestanda. I den här artikeln diskuterar vi att lösa dessa problem.

## <a name="small-to-medium-on-premises-network-considerations"></a>Små till medelstora lokala Nätverksöverväganden

Anta att du har det nätverket för exemplet som illustreras i följande diagram. I det här exemplet har geo-redundant ExpressRoute-anslutning upprättats mellan ett Contoso lokal plats och Contosos virtuellt nätverk i en Azure-region. I diagrammet, heldragen grön linje anger förstahandsvalet (via ExpressRoute-1) och den prickad som representerar vänteläge sökväg (via ExpressRoute 2).

[![1]][1]

När du utformar ExpressRoute-anslutningen för katastrofåterställning, måste du tänka på:

- med hjälp av geo-redundant ExpressRoute-kretsar
- med olika service provider nätverk för olika ExpressRoute-krets
- Designa var och en av ExpressRoute-kretsen för [hög tillgänglighet][HA]
- avslutande olika ExpressRoute-kretsen på annan plats på kundens nätverk

Om du annonsera vägar identiskt över alla ExpressRoute-sökvägar som standard att belastningsutjämna lokala bunden trafik över alla ExpressRoute-sökvägar med hjälp av Equal-cost Multipath (ECMP) routning Azure.

Med geo-redundant ExpressRoute-kretsar måste vi dock ta med i beräkningen annat nätverk prestanda med olika nätverksvägar (särskilt för svarstid för nätverk). För att få mer konsekvent nätverkets prestanda under normal drift kan du prioriterar ExpressRoute-kretsen som ger minimal fördröjning.

Du kan påverka Azure du föredrar en ExpressRoute-krets framför en ny med någon av följande metoder (som anges i den ordning som effektivitet):

- annonserar mer specifik väg via önskade ExpressRoute-kretsen jämfört med andra ExpressRoute-kretsar
- Konfigurera högre Anslutningsvikten på den anslutning som länkar det virtuella nätverket till den önskade ExpressRoute-kretsen
- annonsera vägar via mindre önskade ExpressRoute-krets med längre AS Path (som sökväg åtkomstgruppen)

### <a name="more-specific-route"></a>Mer specifik väg

Följande diagram illustrerar påverkande ExpressRoute bana markering med mer specifik väg annons. I exemplet illustrerade att Contoso lokal/24 IP-adressintervall annonseras som två /25 adressintervall via den primära sökvägen (ExpressRoute-1) och som /24 via vänteläge-sökväg (ExpressRoute-2).

[![2]][2]

Eftersom /25 är mer specifik, skickar jämfört med /24, Azure trafik till 10.1.11.0/24 via ExpressRoute 1 i det normala tillståndet. Om båda anslutningarna ExpressRoute 1 kraschar sedan visas det virtuella nätverket 10.1.11.0/24 vägannonsen endast via ExpressRoute 2. och därför standby-krets används i den här feltillstånd.

### <a name="connection-weight"></a>Anslutningsvikten

Följande skärmbild illustrerar konfigurera vikten för en ExpressRoute-anslutning via Azure-portalen.

[![3]][3]

Följande diagram illustrerar påverkande ExpressRoute bana markering med anslutningsvikten. Standard anslutningsvikten är 0. I exemplet nedan konfigureras vikten för anslutningen för ExpressRoute 1 som 100. När ett virtuellt nätverk får en väg-prefix som annonseras via mer än en ExpressRoute-krets, föredrar anslutningen med högsta vikt att det virtuella nätverket.

[![4]][4]

Om båda anslutningarna ExpressRoute 1 kraschar sedan visas det virtuella nätverket 10.1.11.0/24 vägannonsen endast via ExpressRoute 2. och därför standby-krets används i den här feltillstånd.

### <a name="as-path-prepend"></a>EFTERSOM sökvägen åtkomstgruppen

Följande diagram illustrerar påverkande ExpressRoute bana markering med som åtkomstgruppen för sökvägen. I diagrammet anger vägen annonsen via ExpressRoute 1 standardbeteendet för eBGP. På vägen annons över ExpressRoute 2, är ASN-NUMRET för det lokala nätverket tillagt dessutom på flödets som sökväg. När samma väg som tas emot via flera ExpressRoute-kretsar, per urvalsprocessen eBGP väg föredrar VNet vägen med den kortaste som sökväg. 

[![5]][5]

Om båda anslutningarna ExpressRoute 1 kraschar sedan visas det virtuella nätverket 10.1.11.0/24 vägannonsen endast via ExpressRoute 2. Det gör, skulle desto längre tid eftersom sökvägen bli irrelevanta. Därför används standby-krets i den här feltillstånd.

Använder någon av teknikerna du påverka Azure att föredra en av din ExpressRoute framför andra, du måste också se till att det lokala nätverket också föredrar samma ExpressRoute-sökväg för Azure-bunden trafik att undvika asymmetrisk flöden. Normalt används lokalt inställningsvärde för att påverka lokala nätverk till föredrar en ExpressRoute-krets framför andra. Lokala inställningar är en intern BGP (iBGP)-mått. BGP-väg med det högsta värdet för lokala inställningar är att föredra.

> [!IMPORTANT]
> När du använder vissa ExpressRoute-kretsar som vänteläge, måste du aktivt hantera dem och regelbundet testa redundans. 
> 

## <a name="large-distributed-enterprise-network"></a>Stora distribuerade företagsnätverk

När du har ett stort distribuerat företagsnätverk kan förmodligen du att har flera ExpressRoute-kretsar. I det här avsnittet ska vi se hur du utformar haveriberedskap med hjälp av ExpressRoute-kretsar aktiv-aktiv utan att behöva ytterligare vänteläge kretsar. 

Anta att du exemplet som illustreras i följande diagram. I det här exemplet Contoso har två lokala platser som är ansluten till två Contoso IaaS-distribution i två olika Azure-regioner via ExpressRoute-kretsar i två olika peeringplatser. 

[![6]][6]

Hur vi skapa haveriberedskap påverkar på hur mellan nationella inställningar till mellan plats (område 1/Område2 till location2/location1)-trafiken dirigeras. Anta att du har två olika katastrof arkitekturer som dirigerar trafik mellan regioner-plats på olika sätt.

### <a name="scenario-1"></a>Scenario 1

I det första scenariot ska vi utforma haveriberedskap, så att all trafik mellan ett Azure-region och lokala nätverk som flödar genom lokala ExpressRoute-kretsen i stabilt tillstånd. Om den lokala ExpressRoute-kretsen misslyckas sedan ExpressRoute-fjärrkretsen används för alla trafiken flödar mellan Azure och lokalt nätverk.

Scenario 1 illustreras i följande diagram. I diagrammet visar gröna linjer sökvägar för trafikflödet mellan VNet1 och lokala nätverk. Blå raderna motsvarar sökvägar för trafikflödet mellan VNet2 och lokala nätverk. Heldragna linjer anger önskade sökvägen i stabilt tillstånd och de streckade linjerna visar trafik sökväg i fel på den motsvarande ExpressRoute-krets som överför stabil trafikflödet. 

[![7]][7]

Du kan skapa scenario med anslutning vikt för att påverka virtuella nätverk att föredra anslutning till lokala peeringplats ExpressRoute för lokalt nätverk-bunden trafik. För att slutföra lösningen måste du se till att symmetrisk omvänd trafikflödet. Du kan använda lokala inställningar för iBGP-sessionen mellan din BGP-routrar (där ExpressRoute-kretsar avslutas på den lokala sida) att föredra en ExpressRoute-krets. Lösningen illustreras i följande diagram. 

[![8]][8]

### <a name="scenario-2"></a>Scenario 2

Scenario 2 illustreras i följande diagram. I diagrammet visar gröna linjer sökvägar för trafikflödet mellan VNet1 och lokala nätverk. Blå raderna motsvarar sökvägar för trafikflödet mellan VNet2 och lokala nätverk. I stabiliteten (solid linjer i diagrammet), alla trafiken mellan virtuella nätverk och lokala platser flödar via inom Microsofts stamnätverk oftast och flödar genom gränssnittet mellan lokala platser endast i feltillstånd (prickade linjer i diagram) för en ExpressRoute.

[![9]][9]

Lösningen illustreras i följande diagram. Enligt, kan du skapa scenariot antingen med hjälp av mer specifik väg (alternativ 1) eller AS path åtkomstgruppen (alternativ 2) om du vill påverka val av VNet-sökväg. Om du vill påverka lokal väg val av nätverk för Azure-bunden trafik, måste du konfigurera gränssnittet mellan en lokal plats som mindre bättre. HOWE som du konfigurerar sammanlänkning länken som bättre beror på det routingprotokollet som används i det lokala nätverket. Du kan använda lokala inställningar med iBGP eller mått med IGP (OSPF eller IS IS).

[![10]][10]


## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs vi hur du utformar för haveriberedskap för en ExpressRoute-krets privata peering-anslutningen. I följande artiklar adresser haveriberedskap från program och perspektiv för frontend-åtkomst:

- [Haveriberedskap i företagsskala][Enterprise DR]
- [SMB-haveriberedskap med Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "små till medelstora lokala Nätverksöverväganden"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "spionprogrammet val av sökväg med hjälp av mer specifika vägar"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "konfigurera anslutningsvikten via Azure-portalen"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "spionprogrammet val av sökväg med hjälp av anslutningsvikten"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "åtkomstgruppen för spionprogrammet bana markering som sökväg"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "stora distribuerade på lokalt nätverk överväganden"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenario 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "aktiv-aktiv ExpressRoute circuits lösning 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "aktiv-aktiv ExpressRoute circuits lösning 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





