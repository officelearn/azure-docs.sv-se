---
title: 'Azure ExpressRoute: Designa för haveriberedskap'
description: Den här sidan innehåller arkitekturrekommendationer för haveriberedskap när du använder Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076691"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Designa för haveriberedskap med ExpressRoute privat peering

ExpressRoute är utformat för hög tillgänglighet för att tillhandahålla privat nätverksanslutning till Microsoft-resurser i operatörsklass. Med andra ord finns det ingen enskild felpunkt i ExpressRoute-sökvägen i Microsoft-nätverket. För designöverväganden för att maximera tillgängligheten för en ExpressRoute-krets, se [Designa för hög tillgänglighet med ExpressRoute][HA].

Men med Murphys populära ordspråk -*om något kan gå fel, kommer det*- i beaktande, i den här artikeln låt oss fokusera på lösningar som går utöver misslyckanden som kan åtgärdas med hjälp av en enda ExpressRoute krets. Med andra ord, i den här artikeln låt oss titta på nätverksarkitektur överväganden för att bygga robust backend nätverksanslutning för katastrofåterställning med geo-redundanta ExpressRoute kretsar.

## <a name="need-for-redundant-connectivity-solution"></a>Behov av redundant anslutningslösning

Det finns möjligheter och fall där en hel regional tjänst (oavsett om microsoft, leverantörer av nätverkstjänster, kunder eller andra molntjänstleverantörer) försämras. Grundorsaken till en sådan regional omfattande servicepåverkan inkluderar naturlig katastrof. Därför, för affärskontinuitet och verksamhetskritiska program är det viktigt att planera för haveriberedskap.   

Oavsett om du kör dina verksamhetskritiska program i en Azure-region eller lokalt eller någon annanstans kan du använda en annan Azure-region som redundansplats. Följande artiklar behandlar katastrofåterställning från program och åtkomstperspektiv för frontend:

- [Haveriberedskap i företagsskala][Enterprise DR]
- [SMB-haveriberedskap med Azure Site Recovery][SMB DR]

Om du förlitar dig på ExpressRoute-anslutning mellan det lokala nätverket och Microsoft för verksamhetskritiska åtgärder bör katastrofåterställningsplanen även innehålla geoupptundsling av nätverksanslutningen. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Utmaningar med att använda flera ExpressRoute-kretsar

När du kopplar samman samma uppsättning nätverk med mer än en anslutning introducerar du parallella sökvägar mellan nätverken. Parallella vägar, när de inte är korrekt formade, kan leda till asymmetrisk routning. Om du har tillståndskänsliga entiteter (till exempel NAT, brandvägg) i sökvägen kan asymmetrisk routning blockera trafikflödet.  Vanligtvis över ExpressRoute privata peering sökväg du inte kommer att stöta på tillståndskänsliga enheter som NAT eller brandväggar. Därför blockerar asymmetrisk routning över ExpressRoute privat peering inte nödvändigtvis trafikflödet.
 
Men om du laddar fördelningstrafik över geo-redundanta parallella vägar, oavsett om du har tillståndskänsliga entiteter eller inte, skulle du uppleva inkonsekventa nätverksprestanda. I den här artikeln ska vi diskutera hur du ska hantera dessa utmaningar.

## <a name="small-to-medium-on-premises-network-considerations"></a>Små till medelstora lokala näthänsyn

Låt oss betrakta exempelnätverket som illustreras i följande diagram. I exemplet upprättas geo redundant ExpressRoute-anslutning mellan en Contosos lokala plats och Contosos virtuella nätverk i en Azure-region. I diagrammet anger den heldragna gröna linjen önskad bana (via ExpressRoute 1) och den prickade representerar stand-by-banan (via ExpressRoute 2).

[![1]][1]

När du utformar ExpressRoute-anslutning för haveriberedskap måste du tänka på följande:

- använda geo-redundanta ExpressRoute-kretsar
- använda olika tjänsteleverantörsnätverk för olika ExpressRoute-kretsar
- designa var och en av ExpressRoute-kretsen för [hög tillgänglighet][HA]
- avsluta den olika ExpressRoute-kretsen på olika platser i kundnätverket

Om du annonserar på vägar på samma sätt över alla ExpressRoute-sökvägar, kommer Azure som standard att belastningsutjämna lokal bunden trafik över alla ExpressRoute-sökvägar med hjälp av ECMP-routning (Equal-cost multi-path).

Men med de geo-redundanta ExpressRoute-kretsarna måste vi ta hänsyn till olika nätverksprestanda med olika nätverksvägar (särskilt för nätverksfördröjning). Om du vill få mer konsekvent nätverksprestanda under normal drift kanske du vill föredra ExpressRoute-kretsen som erbjuder den minimala svarstiden.

Du kan påverka Azure att föredra en ExpressRoute-krets framför en annan med hjälp av någon av följande tekniker (listad i effektivitetsordning):

- reklam mer specifik väg över önskad ExpressRoute krets jämfört med andra ExpressRoute kretsar (s)
- konfigurera högre anslutningsvikt på anslutningen som länkar det virtuella nätverket till önskad ExpressRoute-krets
- annonsera rutterna över mindre föredragna ExpressRoute krets med längre AS Path (AS Path prepend)

### <a name="more-specific-route"></a>Mer specifik rutt

Följande diagram illustrerar hur ExpressRoute-sökvägen påverkar valet av mer specifik ruttannons. I det illustrerade exemplet annonseras Contoso lokalt /24 IP-intervall som två /25-adressintervall via önskad sökväg (ExpressRoute 1) och som /24 via stand-by-banan (ExpressRoute 2).

[![2]][2]

Eftersom /25 är mer specifik, jämfört med /24, skulle Azure skicka den trafik som är avsedd till 10.1.11.0/24 via ExpressRoute 1 i normalt tillstånd. Om båda anslutningarna av ExpressRoute 1 går ner, då VNet skulle se 10.1.11.0/24 rutt annons endast via ExpressRoute 2; och därför används standby-kretsen i detta feltillstånd.

### <a name="connection-weight"></a>Anslutningsvikt

Följande skärmbild illustrerar att konfigurera vikten för en ExpressRoute-anslutning via Azure-portalen.

[![3]][3]

Följande diagram illustrerar påverkan på ExpressRoute-sökvägsval med hjälp av anslutningsvikt. Standardanslutningsvikten är 0. I exemplet nedan konfigureras anslutningens vikt för ExpressRoute 1 som 100. När ett virtuella nätverk tar emot ett vägprefix som annonseras via mer än en ExpressRoute-krets, föredrar det virtuella nätverket anslutningen med den högsta vikten.

[![4]][4]

Om båda anslutningarna av ExpressRoute 1 går ner, då VNet skulle se 10.1.11.0/24 rutt annons endast via ExpressRoute 2; och därför används standby-kretsen i detta feltillstånd.

### <a name="as-path-prepend"></a>AS-sökväg prepend

Följande diagram illustrerar påverkan ExpressRoute sökväg val med AS path prepend. I diagrammet anger ruttannonsen över ExpressRoute 1 standardbeteendet för eBGP. På ruttannonsen över ExpressRoute 2 förbereds det lokala nätverkets ASN dessutom på ruttens AS-sökväg. När samma väg tas emot via flera ExpressRoute-kretsar, enligt eBGP-ruttvalsprocessen, skulle VNet föredra rutten med den kortaste AS-sökvägen. 

[![5]][5]

Om båda anslutningarna av ExpressRoute 1 går ner, då VNet skulle se 10.1.11.0/24 rutt annons endast via ExpressRoute 2. Följaktligen skulle den längre AS-vägen bli irrelevant. Därför skulle standby-kretsen användas i det här feltillståndet.

Om du påverkar Azure för att föredra en av dina ExpressRoute framför andra måste du också se till att det lokala nätverket också föredrar samma ExpressRoute-sökväg för Azure-bunden trafik för att undvika asymmetriska flöden. Vanligtvis används lokal preferensvärde för att påverka lokala nätverk för att föredra en ExpressRoute-krets framför andra. Lokal invald är ett internt BGP-mått (iBGP). Bgp-vägen med det högsta lokala preferensvärdet är att föredra.

> [!IMPORTANT]
> När du använder vissa ExpressRoute-kretsar som stand-by måste du aktivt hantera dem och regelbundet testa redundansdrift. 
> 

## <a name="large-distributed-enterprise-network"></a>Stort distribuerat företagsnätverk

När du har ett stort distribuerat företagsnätverk har du troligen flera ExpressRoute-kretsar. I det här avsnittet ska vi se hur du utformar haveriberedskap med hjälp av aktiva ExpressRoute-kretsar, utan att behöva ytterligare stand-by-kretsar. 

Låt oss betrakta exemplet som illustreras i följande diagram. I exemplet har Contoso två lokala platser anslutna till två Contoso IaaS-distribution i två olika Azure-regioner via ExpressRoute-kretsar på två olika peering-platser. 

[![6]][6]

Hur vi arkitekt katastrofåterställningen har en inverkan på hur kors regional till kors plats (region1/region2 till plats2/location1) trafik dirigeras. Låt oss betrakta två olika katastrofarkitekturer som dirigerar trafik över regionplatser på olika sätt.

### <a name="scenario-1"></a>Scenario 1

I det första scenariot ska vi utforma haveriberedskap så att all trafik mellan en Azure-region och lokalt nätverk flödar via den lokala ExpressRoute-kretsen i stabilt tillstånd. Om den lokala ExpressRoute-kretsen misslyckas används fjärrkretsen ExpressRoute för alla trafikflöden mellan Azure och det lokala nätverket.

Scenario 1 illustreras i följande diagram. I diagrammet anger gröna linjer sökvägar för trafikflödet mellan VNet1 och lokala nätverk. De blå linjerna anger sökvägar för trafikflödet mellan VNet2 och lokala nätverk. Heldragna linjer anger önskad bana i stabilt tillstånd och de streckade linjerna anger trafiksökväg i fel på motsvarande ExpressRoute-krets som transporterar trafikflöde i stabilt tillstånd. 

[![7]][7]

Du kan skapa scenariot med anslutningsvikt för att påverka virtuella nätverk för att föredra anslutning till den lokala peering-platsen ExpressRoute för lokal nätverksbunden trafik. För att slutföra lösningen måste du säkerställa symmetrisk omvänd trafikflöde. Du kan använda lokala inställningar på iBGP-sessionen mellan dina BGP-routrar (där ExpressRoute-kretsar avslutas på lokal sida) för att föredra en ExpressRoute-krets. Lösningen illustreras i följande diagram. 

[![8]][8]

### <a name="scenario-2"></a>Scenario 2

Scenario 2 illustreras i följande diagram. I diagrammet anger gröna linjer sökvägar för trafikflödet mellan VNet1 och lokala nätverk. De blå linjerna anger sökvägar för trafikflödet mellan VNet2 och lokala nätverk. I det stabila tillståndet (heldragna linjer i diagrammet) flödar all trafik mellan virtuella nätverk och lokala platser via Microsofts stamnät för det mesta, och flödar genom sammankopplingen mellan lokala platser endast i feltillståndet (prickade linjer i diagrammet) av en ExpressRoute.

[![9]][9]

Lösningen illustreras i följande diagram. Som illustreras kan du skapa scenariot antingen med mer specifik väg (alternativ 1) eller AS-path prepend (alternativ 2) för att påverka VNet-sökvägsvalet. Om du vill påverka val av lokala nätverksvägar för Azure-begränsad trafik måste du konfigurera sammankopplingen mellan den lokala platsen som mindre att föredra. Hur du konfigurerar samtrafiklänken som att föredra beror på vilket routningsprotokoll som används i det lokala nätverket. Du kan använda lokala inställningar med iBGP eller mått med IGP (OSPF eller IS-IS).

[![10]][10]


## <a name="next-steps"></a>Nästa steg

I den här artikeln diskuterade vi hur du utformar för haveriberedskap av en ExpressRoute-krets privat peering-anslutning. Följande artiklar behandlar katastrofåterställning från program och åtkomstperspektiv för frontend:

- [Haveriberedskap i företagsskala][Enterprise DR]
- [SMB-haveriberedskap med Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "små till medelstora lokala näthänsyn"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "påverka val av bana med mer specifika rutter"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "konfigurera anslutningsvikt via Azure-portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "påverka val av bana med hjälp av anslutningsvikt"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "påverka banmarkeringen med HJÄLP AV AS-bana prepend"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "stora distribuerade lokala nätöverväganden"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenario 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "aktiva ExpressRoute-kretsar lösning 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "aktiva ExpressRoute-kretsar lösning 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





