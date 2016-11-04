---
title: Optimera ExpressRoute-routning | Microsoft Docs
description: Den här sidan innehåller information om hur du optimerar routning när en kund har mer än en ExpressRoute-krets som ansluter mellan Microsoft och kundens företagsnätverk.
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: ''

ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: charwen

---
# <a name="optimize-expressroute-routing"></a>Optimera ExpressRoute-routning
När du har flera ExpressRoute-kretsar måste ha du mer än en sökväg för att ansluta till Microsoft. Därför kan en icke-optimal routning inträffa - vilket innebär att din trafik får en längre sökväg till Microsoft, och Microsoft till nätverket. Ju längre nätverkssökvägen är, desto längre svarstid. Svarstiden har direkt inverkan på programmens prestanda och användarupplevelse. Den här artikeln beskriver problemet och förklarar hur du optimerar routning med standardroutningstekniker.

## <a name="suboptimal-routing-case-1"></a>Icke-optimal routning, fall 1
Låt oss titta närmare på routningsproblemet med ett exempel. Anta att du har två kontor i USA, ett i Los Angeles och ett i New York. Ditt kontor ansluts i ett WAN (Wide Area Network), som kan vara antingen ditt eget stamnät eller leverantörens IP VPN. Du har två ExpressRoute-kretsar, en i västra USA och en i östra USA, som även är anslutna i WAN-nätverket. Naturligtvis har du två sökvägar för att ansluta till Microsoft-nätverket. Anta nu att du har Azure-distribution (t.ex. Azure Apptjänst) i både västra och östra USA. Din avsikt är att ansluta dina användare i Los Angeles till Azure i västra USA och användarna i New York till Azure i östra USA, eftersom tjänstadministratören vill att varje kontorsanvändare ska ha åtkomst till närliggande Azure-tjänster för en optimal upplevelse. Planen fungerar bra för ostkustanvändarna, men inte för västkustanvändarna. Orsaken till problemet är följande. På varje ExpressRoute-krets annonserar vi både prefixet i Azure för östra USA (23.100.0.0/16) och prefixet i Azure för västra USA (13.100.0.0/16). Om du inte vet vilket prefix som är från vilken region, kan du inte behandla dem olika. WAN-nätverket kan tro att båda prefixen är närmare östra USA än västra USA och därför dirigera båda kontorens användare till ExpressRoute-kretsen i östra USA. Till slut har du många missnöjda användare på Los Angeles-kontoret.

![](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution:-use-bgp-communities"></a>Lösning: Använd BGP-communities
För att optimera routningen för båda kontoren måste du veta vilket prefix som är från Azure i västra USA och vilket som är från Azure i östra USA. Vi kodar informationen genom att använda [BGP Community-värden](expressroute-routing.md). Vi har tilldelat ett unikt BGP Community-värde för varje Azure-region, t.ex. ”12076:51004” för östra USA och ”12076:51006” för västra USA. Nu när du vet vilket prefix är från vilken Azure-region, kan du konfigurera de ExpressRoute-kretsar som ska användas. Eftersom vi använder BGP till att utbyta routningsinformation kan du använda BGP:s lokala inställningar för att påverka routningen. I vårt exempel kan du tilldela ett högre lokalt inställningsvärde för 13.100.0.0/16 i västra USA än i östra USA, och på samma sätt ett högre lokalt inställningsvärde för 23.100.0.0/16 i östra USA än i västra USA. Den här konfigurationen ser till att, när båda sökvägarna till Microsoft är tillgängliga, användarna i Los Angeles kan använda ExpressRoute-kretsen i västra USA för att ansluta till Azure där, medan dina användare i New York tar ExpressRoute i östra USA till Azure där. Routning är optimerad på båda sidorna. 

![](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

## <a name="suboptimal-routing-case-2"></a>Icke-optimal routning, fall 2
Här är ett annat exempel där anslutningar från Microsoft tar en längre sökväg till ditt nätverk. I detta fall måste du använda lokala Exchange-servrar och Exchange Online i en [hybridmiljö](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Dina kontor är anslutna till ett WAN-nätverk. Du annonserar prefixen för dina lokala servrar på båda kontoren till Microsoft via de två ExpressRoute-kretsarna. Exchange Online initierar anslutningar till lokala servrar vid exempelvis migrering av postlådor. Tyvärr dirigeras anslutningen till Los Angeles-kontoret till ExpressRoute-kretsen i östra USA, innan den färdas över hela kontinenten tillbaka till västkusten. Orsaken till problemet liknar det första fallet. Microsofts nätverk kan inte att avgöra vilket kundprefix som är nära östra USA och vilket som är nära västra USA. Det råkar välja fel sökväg till kontoret i Los Angeles.

![](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution:-use-as-path-prepending"></a>Lösning: Använd AS PATH
Det finns två lösningar på problemet. Den första är att du bara annonserar ditt lokala prefix för LA-kontoret, 177.2.0.0/31, på ExpressRoute-kretsen i västra USA och ditt lokala prefix för kontoret i New York, 177.2.0.2/31, på ExpressRoute-kretsen i östra USA. Det innebär att det bara finns en enda sökväg för Microsofts anslutning till dina olika kontor. Det finns inga tveksamheter och routningen optimeras. Du måste tänka på din redundansstrategi med den här designen. Du måste se till att Exchange Online fortfarande kan ansluta till dina lokala servrar i händelse av att sökvägen till Microsoft via ExpressRoute bryts. 

Den andra lösningen är att du fortsätter att annonsera båda prefixen för båda ExpressRoute-kretsarna, men dessutom ger du oss en ledtråd för vilka prefix som ligger nära dina kontor. Du kan konfigurera AS PATH för ditt prefix om du vill påverka routningen eftersom vi stöder BGP. I det här exemplet kan du förlänga AS PATH för 172.2.0.0/31 i östra USA så att vi prioriterar ExpressRoute-kretsen i västra USA för trafik till det prefixet (vårt nätverk kommer att tro att sökvägen till prefixet är kortare i väst). På samma sätt kan du förlänga AS PATH för 172.2.0.2/31 i västra USA så att vi prioriterar ExpressRoute-kretsen i östra USA. Routning är optimerat för båda kontoren. Med den här designen kan Exchange Online fortfarande nå dig via en annan ExpressRoute-krets och ditt WAN om en ExpressRoute-krets bryts. 

> [!IMPORTANT]
> Vi tar bort privata AS-nummer i AS PATH för de prefix som togs emot med Microsoft-peering. Du måste lägga till offentliga AS-nummer i AS PATH som påverkar routningen för Microsoft-peering.
> 
> 

![](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!IMPORTANT]
> Även om exemplen här är för Microsoft och offentliga peerings, stöder vi samma funktioner för privat peering. Dessutom fungerar AS Path-prepending inom en enda ExpressRoute-krets för att påverka valet av primära och sekundära sökvägar.
> 
> 

<!--HONumber=Oct16_HO3-->


