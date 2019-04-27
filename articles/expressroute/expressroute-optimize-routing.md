---
title: 'Optimera routning - ExpressRoute-kretsar: Azure | Microsoft Docs'
description: Den här sidan innehåller information om hur du optimerar routning när du har mer än en ExpressRoute-krets för att ansluta till Microsoft från ditt företagsnätverk.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 65c23b05cfcb623f8e2870df813f5516b3039d5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883575"
---
# <a name="optimize-expressroute-routing"></a>Optimera ExpressRoute-routning
När du har flera ExpressRoute-kretsar måste ha du mer än en sökväg för att ansluta till Microsoft. Därför kan en icke-optimal routning inträffa - vilket innebär att din trafik får en längre sökväg till Microsoft, och Microsoft till nätverket. Ju längre nätverkssökvägen är, desto längre svarstid. Svarstiden har direkt inverkan på programmens prestanda och användarupplevelse. Den här artikeln beskriver problemet och förklarar hur du optimerar routning med standardroutningstekniker.

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Icke-optimal routning från kund till Microsoft
Låt oss titta närmare på routningsproblemet med ett exempel. Anta att du har två kontor i USA, ett i Los Angeles och ett i New York. Ditt kontor ansluts i ett WAN (Wide Area Network), som kan vara antingen ditt eget stamnät eller leverantörens IP VPN. Du har två ExpressRoute-kretsar, en i USA, västra och en i USA, östra som även är anslutna i WAN-nätverket. Naturligtvis har du två sökvägar för att ansluta till Microsoft-nätverket. Anta nu att du har Azure-distribution (t.ex. Azure App Service) i både ”USA, västra” och ”USA, östra”. Din avsikt är att ansluta dina användare i Los Angeles till Azure i USA, västra och användarna i New York till Azure i USA, östra eftersom tjänstadministratören vill att varje kontorsanvändare ska ha åtkomst till närliggande Azure-tjänster för en optimal upplevelse. Planen fungerar bra för ostkustanvändarna, men inte för västkustanvändarna. Orsaken till problemet är följande. På varje ExpressRoute-krets annonserar vi både prefixet i Azure för USA, östra (23.100.0.0/16) och prefixet i Azure för USA, västra (13.100.0.0/16). Om du inte vet vilket prefix som är från vilken region, kan du inte behandla dem olika. WAN-nätverket kan tro att båda prefixen är närmare USA, östra än USA, västra och därför dirigera båda kontorens användare till ExpressRoute-kretsen i USA, östra. Till slut har du många missnöjda användare på Los Angeles-kontoret.

![ExpressRoute fall 1 – Problem: Icke-optimal routning från kund till Microsoft](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Lösning: Använd BGP-communities
För att optimera routningen för båda kontoren måste du veta vilket prefix som är från Azure i USA, västra och vilket som är från Azure i USA, östra. Vi kodar informationen genom att använda [BGP Community-värden](expressroute-routing.md). Vi har tilldelat ett unikt BGP Community-värde för varje Azure-region, t.ex. ”12076:51004” för USA, östra och ”12076:51006” för USA, västra. Nu när du vet vilket prefix är från vilken Azure-region, kan du konfigurera de ExpressRoute-kretsar som ska användas. Eftersom vi använder BGP till att utbyta routningsinformation kan du påverka routningen med hjälp av BGP:s lokala inställningar. I vårt exempel kan du tilldela ett högre lokalt inställningsvärde för 13.100.0.0/16 i USA, västra än i USA, östra och på samma sätt ett högre lokalt inställningsvärde för 23.100.0.0/16 i USA, östra än i USA, västra. Den här konfigurationen ser till att, när båda sökvägarna till Microsoft är tillgängliga, användarna i Los Angeles kan använda ExpressRoute-kretsen i USA, västra för att ansluta till Azure där, medan dina användare i New York tar ExpressRoute i USA, östra till Azure där. Routning är optimerad på båda sidorna. 

![ExpressRoute fall 1 – Lösning: Använd BGP-communities](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> Samma teknik (med lokal prioritetsordning) kan tillämpas för routning från kund till det virtuella Azure-nätverket. Vi taggar inte värdet för BGP-community till de prefix som annonseras från Azure till ditt nätverk. Eftersom du vet vilka distributioner för virtuellt nätverk som är nära vilka kontor, kan du konfigurera dina routrar därefter och prioritera en ExpressRoute-krets framför en annan.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Icke-optimal routning från Microsoft till kund
Här är ett annat exempel där anslutningar från Microsoft tar en längre sökväg till ditt nätverk. I detta fall måste du använda lokala Exchange-servrar och Exchange Online i en [hybridmiljö](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Dina kontor är anslutna till ett WAN-nätverk. Du annonserar prefixen för dina lokala servrar på båda kontoren till Microsoft via de två ExpressRoute-kretsarna. Exchange Online initierar anslutningar till lokala servrar vid exempelvis migrering av postlådor. Tyvärr dirigeras anslutningen till Los Angeles-kontoret till ExpressRoute-kretsen i USA, östra, innan den färdas över hela kontinenten tillbaka till västkusten. Orsaken till problemet liknar det första fallet. Microsofts nätverk kan inte att avgöra vilket kundprefix som är nära USA, östra och vilket som är nära USA, västra. Det råkar välja fel sökväg till kontoret i Los Angeles.

![ExpressRoute fall 2 – Problem: Icke-optimal routning från Microsoft till kund](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Lösning: Använd AS PATH
Det finns två lösningar på problemet. Den första är att du bara annonserar ditt lokala prefix för LA-kontoret, 177.2.0.0/31, på ExpressRoute-kretsen i USA, västra och ditt lokala prefix för kontoret i New York, 177.2.0.2/31, på ExpressRoute-kretsen i USA, östra. Det innebär att det bara finns en enda sökväg för Microsofts anslutning till dina olika kontor. Det finns inga tveksamheter och routningen optimeras. Du måste tänka på din redundansstrategi med den här designen. Du måste se till att Exchange Online fortfarande kan ansluta till dina lokala servrar i händelse av att sökvägen till Microsoft via ExpressRoute bryts. 

Den andra lösningen är att du fortsätter att annonsera båda prefixen för båda ExpressRoute-kretsarna, men dessutom ger du oss en ledtråd för vilka prefix som ligger nära dina kontor. Du kan konfigurera AS PATH för ditt prefix om du vill påverka routningen eftersom vi stöder BGP. I det här exemplet kan du förlänga AS PATH för 172.2.0.0/31 i USA, östra så att vi prioriterar ExpressRoute-kretsen i USA, västra för trafik till det prefixet (vårt nätverk kommer att tro att sökvägen till prefixet är kortare i väst). På samma sätt kan du förlänga AS PATH för 172.2.0.2/31 i USA, västra så att vi prioriterar ExpressRoute-kretsen i USA, östra. Routning är optimerat för båda kontoren. Med den här designen kan Exchange Online fortfarande nå dig via en annan ExpressRoute-krets och ditt WAN om en ExpressRoute-krets bryts. 

> [!IMPORTANT]
> Vi tar bort privata AS-nummer i AS PATH för de prefix som togs emot med Microsoft-peering. Du måste lägga till offentliga AS-nummer i AS PATH som påverkar routningen för Microsoft-peering.
> 
> 

![ExpressRoute fall 2 – Lösning: Använd AS PATH-prepending](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Även om exemplen här är för Microsoft och offentliga peerings, stöder vi samma funktioner för privat peering. Dessutom fungerar AS Path-prepending inom en enda ExpressRoute-krets för att påverka valet av primära och sekundära sökvägar.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Icke-optimal routning mellan virtuella nätverk
Med ExpressRoute kan du använda ”virtuellt nätverk till virtuellt nätverk”-kommunikation (vilket även kallas ”VNet”) genom att länka dem till en ExpressRoute-krets. När du länkar de virtuella nätverken till flera ExpressRoute-kretsar kan icke-optimal routning uppstå mellan dem. Vi tar ett exempel. Anta att du har två ExpressRoute-kretsar, en i ”USA, västra” och en i ”USA, östra”. Du har två virtuella nätverk i varje region. Webbservrar distribueras i det ena virtuella nätverket och programservrar i det andra. För redundans länkar du de två virtuella nätverken i varje region till både den lokala ExpressRoute-kretsen och ExpressRoute-fjärrkretsen. Från varje virtuella nätverk finns två vägar till det andra virtuella nätverket (se nedan). De virtuella nätverken vet inte vilken ExpressRoute-krets som är den lokala kretsen och vilken som är fjärrkretsen. Eftersom de använder ECMP-routning (Equal-Cost-Multi-Path) för att belastningsutjämna trafik mellan virtuella nätverk kan vissa trafikflöden ta den längre vägen och dirigeras vid ExpressRoute-fjärrkretsen.

![ExpressRoute fall 3 – Icke-optimal routning mellan virtuella nätverk](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Lösning: Tilldela en hög vikt till den lokala anslutningen
Lösningen är enkel. Eftersom du vet var de virtuella nätverken och kretsarna är kan du berätta för oss vilken väg som varje virtuella nätverk ska prioritera. För just det här fallet tilldelar du en högre vikt till den lokala anslutningen än till fjärranslutningen (du hittar konfigurationsexemplet [här](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection)). När ett virtuellt nätverk mottar prefixet för det andra virtuella nätverket och det finns flera anslutningar, prioriterar nätverket anslutningen med den högsta vikten när trafik som är avsett för det prefixet skickas.

![ExpressRoute fall 3 – Lösning: Tilldela en hög vikt till den lokala anslutningen](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> Du kan också påverka routningen från det virtuella nätverket till ditt lokala nätverk om du har flera ExpressRoute-kretsar. Det gör du genom att konfigurera vikten för en anslutning i stället för att använda S PATH-prepending (en teknik som beskrivs i det andra scenariet ovan). För varje prefix tittar vi alltid på anslutningsvikten före AS Path-längden när vi avgör hur trafiken ska skickas.
>
>
