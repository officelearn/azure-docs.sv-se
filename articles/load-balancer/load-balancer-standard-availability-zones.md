---
title: Azure Standard Load Balancer och Tillgänglighetszoner
titleSuffix: Azure Load Balancer
description: Med den här utbildnings vägen kommer du igång med Azure Standard Load Balancer och Tillgänglighetszoner.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: fca6b40f4bd871041d14d119eb44e8366d8562ee
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700468"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Standard Load Balancer och tillgänglighetszoner

Azure Standard Load Balancer stöder scenarier med tillgänglighets zoner. Du kan använda standard Load Balancer för att öka tillgängligheten i hela scenariot genom att justera resurser med och distribution mellan zoner. Tillgänglighets zoner i kombination med standard Load Balancer är en mycket och flexibel funktions uppsättning som kan skapa många olika scenarier.  Granska det här dokumentet för att förstå dessa [begrepp](#concepts) och [design rikt linjer](#design)för grundläggande scenarier.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Tillgänglighetszoner begrepp som tillämpas på Load Balancer

En belastningsutjämnare ärver zon konfigurationen från dess komponenter: 

* Klientdel
* Regler
* Definition av backend-pool

I samband med tillgänglighets zoner beskrivs beteende och egenskaper för en belastnings Utjämnings regel som Zone-redundant eller zonindelade.  I den här belastnings utjämningens kontext innebär zon-redundant alltid **flera zoner** och zonindelade innebär att tjänsten isoleras till en **enda zon**. En Azure Load Balancer har två typer, offentliga och interna. Båda typerna av redundans och zonindelade distribution av belastningsutjämnare.  Både belastnings Utjämnings typer kan dirigera trafik mellan zoner vid behov.

## <a name="frontend"></a>Klientdel

En klient del för belastningsutjämnare är en IP-konfiguration för klient delen som refererar antingen till en offentlig IP-adress eller en privat IP-adress inom under nätet för ett virtuellt nätverk. Den utgör den belastningsutjämnade slut punkten där tjänsten exponeras.

En belastnings Utjämnings resurs kan innehålla regler med zonindelade-och Zone-redundanta klient delar samtidigt. När en offentlig eller privat IP-adress garanteras till en zon kan zonality (eller avsaknad av den) inte ändras. Om du vill ändra eller utelämna zonality för en offentlig eller privat IP-adresspool skapar du om IP-adressen i lämplig zon. 

Tillgänglighets zoner ändrar inte begränsningar för flera klient delar. Granska [flera klient delar för Load Balancer](load-balancer-multivip-overview.md) för information om den här möjligheten.

### <a name="zone-redundant"></a>Zonen är redundant 

I en region med tillgänglighets zoner kan en standardfrontend-modul för belastningsutjämnare vara zon-redundant. Flera zoner kan betjäna inkommande eller utgående i en region. Den här trafiken hanteras av en enda IP-adress. DNS-redundanta scheman är inte obligatoriska. 

En IP-adress för en klient del kommer att överleva zon haveriet. Klient delens IP-adress kan användas för att få åtkomst till alla (icke-förbrukade) Server dels medlemmar oavsett zon. En eller flera tillgänglighets zoner kan inte köras och data Sök vägen finns kvar så länge en zon i regionen är felfri. 

Klient delens IP-adress hanteras samtidigt av flera oberoende infrastruktur distributioner i flera tillgänglighets zoner. Eventuella återförsök eller återupprättade kommer att lyckas i andra zoner som inte påverkas av zon felen. 

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*Bild: zon redundant belastningsutjämnare*

### <a name="zonal"></a>Zonindelade

Du kan välja att en klient del garanteras för en enda zon, som kallas en *zonindelade-frontend*.  Det här scenariot innebär att alla inkommande eller utgående flöden betjänas av en enda zon i en region.  Din klient del delar i nedbrytningen med zonens hälsa.  Data Sök vägen påverkas inte av andra problem i andra zoner än de som har garanterats. Du kan använda zonindelade-frontend-klienter för att exponera en IP-adress per tillgänglighets zon.  

Dessutom stöds användningen av zonindelade-frontend direkt för belastningsutjämnade slut punkter i varje zon. Du kan använda den här konfigurationen för att exponera belastnings Utjämnings slut punkter per zon för att övervaka varje zon individuellt. För offentliga slut punkter kan du integrera dem med en DNS-belastnings Utjämnings produkt som [Traffic Manager](../traffic-manager/traffic-manager-overview.md) och använda ett enda DNS-namn.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*Bild: zonindelade Load Balancer*

Om du vill blanda dessa begrepp (zoner-redundanta och zonindelade för samma server del) granskar du [flera klient delar för Azure Load Balancer](load-balancer-multivip-overview.md).

För en offentlig belastningsutjämnare-frontend lägger du till en **zon** parameter till den offentliga IP-adressen. Den här offentliga IP-adressen refereras till av klient delens IP-konfiguration som används av respektive regel.

För en intern belastningsutjämnare-frontend lägger du till en **zon** parameter till den interna belastnings Utjämnings-IP-konfigurationen. En zonindelade-frontend garanterar en IP-adress i ett undernät till en speciell zon.

## <a name="backend"></a>Serverdel

Azure Load Balancer fungerar med virtuella dator instanser. Dessa instanser kan vara fristående, tillgänglighets uppsättningar eller skalnings uppsättningar för virtuella datorer. Alla virtuella datorer i ett enda virtuellt nätverk kan vara en del av backend-poolen, vilken zon den virtuella datorn är garanterad till.

Om du vill justera och garantera klient delen och Server delen med en enda zon kan du bara placera virtuella datorer inom samma zon i motsvarande backend-pool.

Placera virtuella datorer från flera zoner i samma backend-pool för att adressera virtuella datorer över flera zoner. 

När du använder skalnings uppsättningar för virtuella datorer placerar du en eller flera skalnings uppsättningar för virtuella datorer i samma backend-pool. Skalnings uppsättningar kan vara i en eller flera zoner.

## <a name="outbound-connections"></a>Utgående anslutningar

Zon-redundant och zonindelade gäller för [utgående anslutningar](load-balancer-outbound-connections.md). En zon-redundant offentlig IP-adress som används för utgående anslutningar betjänas av alla zoner. En offentlig IP-adress för zonindelade hanteras endast av zonen som den garanterar i. 

Utgående anslutning SNAT port tilldelningar överleva zon haverier och scenariot fortsätter att tillhandahålla utgående SNAT-anslutningar om de inte påverkas av zon haveriet. Zon haverier kan kräva att anslutningar återupprättas för zoner – redundanta scenarier om trafiken betjänas av en berörd zon. Flöden i andra zoner än de berörda zonerna påverkas inte.

Algoritmen för Förallokering av SNAT-port är densamma i eller utan tillgänglighets zoner.

## <a name="health-probes"></a>Hälsoavsökningar

Dina befintliga definitioner av hälso avsökningen förblir som de är utan tillgänglighets zoner. Vi har dock expanderat hälso modellen på en infrastruktur nivå. 

När du använder zoner – redundanta klient delar, expanderar belastningsutjämnaren sin interna hälso kontroll. Belastningsutjämnaren avsöker oberoende av tillgängligheten för en virtuell dator från varje zon och stänger av sökvägar över zoner som har misslyckats utan åtgärd.  

Andra zoner som kan komma åt den här virtuella datorn kan fortsätta att betjäna den virtuella datorn från sina respektive klient datorer. Under fel händelser kan varje zon ha olika distributioner av nya flöden samtidigt som du skyddar tjänstens övergripande hälsa.

## <a name="design-considerations"></a><a name="design"></a> Design överväganden

Belastnings utjämning är flexibel i samband med tillgänglighets zoner. Du kan välja att justera zoner eller vara zoner-redundanta för varje regel. Ökad tillgänglighet kan komma till priset för ökad komplexitet. Design för tillgänglighet för optimala prestanda.

### <a name="zone-redundancy"></a>Zon-redundans

Load Balancer gör det enkelt att ha en enda IP-adress som en zon redundant klient del. En zon-redundant IP-adress kan hantera en zonindelade-resurs i alla zoner.  IP-adressen kan överleva en eller flera zon fel så länge en zon är felfri i regionen.  I stället är en zonindelade-frontend en minskning av tjänsten till en enda zon och delas med respektive zon.

Zon-redundans innebär inte hitless Datapath eller kontroll plan. Det är data planet. Zoner – redundanta flöden kan använda alla zoner och en kunds flöden kommer att använda alla felfria zoner i en region. I ett zon fel påverkas inte trafik flöden som använder felfria zoner. 

Trafik flöden som använder en zon vid tidpunkten för zon fel kan påverkas, men program kan återställas. Trafiken fortsätter i de friska zonerna i regionen när den återsänds när Azure har konvergerat runt zon felet.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Gränser för mellan zoner

Det är viktigt att förstå att när en tjänst korsar zoner kan du dela en omvandling med inte en zon, men kan ha flera zoner. Därför kanske tjänsten inte har fått någon tillgänglighet över icke-zonindelade distributioner.

Undvik att införa oönskade kors zon beroenden, vilket kommer att upphäver tillgänglighets vinster när du använder tillgänglighets zoner. Om ditt program innehåller flera kritiska komponenter bör du se till att överlevnaden om en zon Miss lyckas.

En enda viktig komponent för ditt program kan påverka hela programmet om det bara finns i en annan zon än den som finns i en annan zon. Överväg zon återställningen och hur programmet kommer att konvergeras. Förstå hur ditt program svarar på fel i delar av det. Granska viktiga punkter och Använd dem för frågor som du tycker om ditt speciella scenario.

- Om ditt program har två komponenter:

    * IP-adress
    * Virtuell dator med hanterad disk

De är konfigurerade i zon 1 och zon 2. När zon 1 Miss lyckas kommer tjänsten inte att överleva. Du kan inte korsa zoner med zonindelade-scenarier om du inte är helt medveten om att du skapar ett potentiellt farligt felläge. Det här scenariot kan ge flexibilitet.

- Om ditt program har två komponenter:

    * IP-adress
    * Virtuell dator med hanterad disk

De är konfigurerade för att vara zoner-redundanta och zon 1. Tjänsten kommer att överleva zon fel i zon 2, zon 3 eller både om zon 1 inte har misslyckats. Du kan dock förlora viss möjlighet att känna till hälso tillståndet för din tjänst om du inte har till gång till klient delens tillgänglighet.  Överväg att utveckla en mer omfattande hälso-och kapacitets modell.  Du kan använda zon-redundanta och zonindelade koncept tillsammans för att utöka insikter och hanterbarhet.

- Om ditt program har två komponenter:

    * Zon-redundant belastningsutjämnare för belastnings utjämning
    * Skalnings uppsättning för virtuella datorer i olika zoner i tre zoner

Dina resurser i zoner som inte påverkas av ett haveri är tillgängliga. Din tjänst kapacitet kan försämras under det här problemet. Från ett infrastruktur perspektiv kan distributionen överleva en eller flera zon haverier. Det här scenariot aktiverar följande frågor:

  - Förstår du hur ditt program svarar på fel och försämrad kapacitet?
  - Behöver du ha skydd i tjänsten för att tvinga fram en redundansväxling till ett regions par om det behövs?
  - Hur ska du övervaka, identifiera och minimera ett sådant scenario? Du kan använda standarddiagnostik för belastningsutjämnare för att utöka övervakningen av tjänstens prestanda. Överväg vad som är tillgängligt och vad som kan behöva utökningen.

- Zoner kan göra det enklare att förstå och innesluta zoner. Zon felen skiljer sig från andra problem när det gäller tids gränser, nya försök och backoff-algoritmer. Azure Load Balancer tillhandahåller zoner – redundanta sökvägar. Belastnings utjämningen försöker återställa snabbt, på paket nivå i real tid. Återöverföringar eller återupprättade försök kan uppstå när ett fel uppstår. Det är viktigt att förstå hur ditt program kan hantera fel. Schemat för belastnings utjämning kommer att överleva, men du måste planera för följande frågor:

  - När en zon Miss lyckas, kan din tjänst förstå felet och om status förloras, hur kommer du att återställa?
  - Kan programmet förstå hur man konvergerar på ett säkert sätt när en zon returnerar?

Gå igenom [design mönster för molnet i molnet](/azure/architecture/patterns/) för att förbättra återhämtningen hos ditt program till haveri situationer.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Tillgänglighetszoner](../availability-zones/az-overview.md)
- Läs mer om [standard Load Balancer](./load-balancer-overview.md)
- Lär dig att [belastningsutjämna virtuella datorer inom en zon med hjälp av en standard Load Balancer med en zonindelade-frontend](./quickstart-load-balancer-standard-public-cli.md)
- Lär dig att [belastningsutjämna virtuella datorer över zoner med hjälp av en standard Load Balancer med en zon-redundant klient](./quickstart-load-balancer-standard-public-cli.md) del
- Lär dig mer om [moln design mönster i Azure](/azure/architecture/patterns/) för att förbättra programmets återhämtnings förmåga till haveri situationer.