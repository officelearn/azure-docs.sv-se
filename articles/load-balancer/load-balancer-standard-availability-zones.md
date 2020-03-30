---
title: Azure Standard belastningsutjämnings- och tillgänglighetszoner
titleSuffix: Azure Load Balancer
description: Med den här utbildningssökvägen kommer du igång med Azure Standard Load Balancer och Availability Zones.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197155"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Standard Load Balancer och tillgänglighetszoner

Azure Standard Load Balancer stöder scenarier [för tillgänglighetszoner.](../availability-zones/az-overview.md) Du kan använda Standard belastningsutjämnare för att optimera tillgängligheten i slut-till-slut-scenariot genom att justera resurser med zoner och distribuera dem mellan zoner.  Granska [tillgänglighetszoner](../availability-zones/az-overview.md) för vägledning om vilka tillgänglighetszoner som är, vilka regioner som för närvarande stöder tillgänglighetszoner och andra relaterade koncept och produkter. Tillgänglighetszoner i kombination med standardbelastningsutjämning är en expansiv och flexibel funktionsuppsättning som kan skapa många olika scenarier.  Granska det här dokumentet för att förstå dessa [begrepp](#concepts) och grundläggande [designvägledning](#design)för scenario .

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>Begreppen Tillgänglighetszoner som tillämpas på belastningsutjämnaren

En load balancer-resurs i sig är regional och aldrig zonindelning. Granulariteten för vad du kan konfigurera begränsas av varje konfiguration av klientdel, regel och serverdelspooldefinition.
I samband med tillgänglighetszoner beskrivs beteendet och egenskaperna för en belastningsutjämnad regel som zonundant eller zonindelning.  Zonundant och zonindelning beskriver zonaliteten för en egenskap.  I samband med belastningsutjämnare innebär zonuppsagda alltid *flera zoner* och zonindelning innebär att tjänsten isoleras till en *enda zon*.
Både offentliga och interna belastningsutjämnare stöder zonuppsagade och zonscenarier och båda kan dirigera trafik över zoner efter behov *(belastningsutjämning över zoner).* 

### <a name="frontend"></a>Klientdel

En belastningsutjämnad frontend är en klient-IP-konfiguration som refererar till antingen en offentlig IP-adressresurs eller en privat IP-adress i undernätet för en virtuell nätverksresurs.  Den utgör den belastningsbalanserade slutpunkten där tjänsten exponeras.
En belastningsutjämnadsresurs kan innehålla regler med zon- och zonupptrakta frontends samtidigt. När en offentlig IP-resurs eller en privat IP-adress har garanterats till en zon, är zonaliteten (eller bristen på sådan) inte föränderlig.  Om du vill ändra eller utelämna zonaliteten för en offentlig IP- eller privat IP-adress frontend måste du återskapa den offentliga IP-adressen i lämplig zon.  Tillgänglighetszoner ändrar inte begränsningarna för flera frontend, granska [flera frontends för belastningsutjämnare](load-balancer-multivip-overview.md) för mer information om den här möjligheten.

#### <a name="zone-redundant"></a>Zon redundant 

I en region med tillgänglighetszoner kan en standardbelastutjämningsklämningsklämd vara zonundant.  Zonundant innebär att alla inkommande eller utgående flöden betjänas av flera tillgänglighetszoner i en region samtidigt med en enda IP-adress. DNS-redundansscheman krävs inte. En enda klientdel IP-adress kan överleva zonfel och kan användas för att nå alla (icke-påverkade) backend poolmedlemmar oavsett zon. En eller flera tillgänglighetszoner kan misslyckas och datasökvägen överlever så länge en zon i regionen förblir felfri. Frontends enda IP-adress hanteras samtidigt av flera oberoende infrastrukturdistributioner i flera tillgänglighetszoner.  Detta betyder inte hitless datasökväg, men alla försök eller återupprättande kommer att lyckas i andra zoner som inte påverkas av zonen misslyckande.   

#### <a name="optional-zone-isolation"></a>Zonisolering som tillval

Du kan välja att ha en frontend garanterad till en enda zon, som kallas en *zonindelning frontend*.  Det innebär att alla inkommande eller utgående flöde betjänas av en enda zon i en region.  Din frontend delar ödet med zonens hälsa.  Datasökvägen påverkas inte av fel i andra zoner än där den garanterades. Du kan använda zonbaserade frontends för att exponera en IP-adress per tillgänglighetszon.  

Dessutom kan du använda zonbaserade frontends direkt för belastningsbalanserade slutpunkter inom varje zon. Du kan också använda detta för att exponera belastningsbalanserade slutpunkter per zon för att övervaka varje zon individuellt.  Eller för offentliga slutpunkter kan du integrera dem med en DNS-belastningsutjämningsprodukt som [Traffic Manager](../traffic-manager/traffic-manager-overview.md) och använda ett enda DNS-namn. Klienten kommer sedan att matcha till detta DNS-namn till flera zonbaserade IP-adresser.  

Om du vill blanda dessa begrepp (zonupptrakta och zonindelning för samma backend) granskar du [flera frontends för Azure Load Balancer](load-balancer-multivip-overview.md).

För en offentlig belastningsutjämnads frontend lägger du till en *zonparameter* i den offentliga IP-resurs som refereras av ip-konfigurationen för klientdel som används av respektive regel.

För en intern frontlinje för belastningsutjämnare lägger du till en *zonparameter* i den interna IP-konfigurationen för belastningsutjämning. Zonindelningsfrontend gör att belastningsutjämnaren garanterar en IP-adress i ett undernät till en viss zon.

### <a name="cross-zone-load-balancing"></a>Belastningsutjämning mellan zoner

Belastningsutjämning över flera zoner är belastningsutjämnarens förmåga att nå en slutpunkt för bakåtsidan i alla zoner och är oberoende av frontend och dess zonalitet.  Alla belastningsutjämningsregel kan rikta backend-instans i alla tillgänglighetszoner eller regionala instanser.

Du måste vara noga med att konstruera ditt scenario på ett sätt som uttryckte en tillgänglighet zoner begrepp. Du behöver till exempel garantera distributionen av den virtuella datorn inom en enda zon eller flera zoner och justera zoninsided- och zonbaserade serverdelsresurser till samma zon.  Om du korsar tillgänglighetszoner med endast zonresurser fungerar scenariot men kanske inte har ett tydligt felläge med avseende på tillgänglighetszoner. 

### <a name="backend"></a>Backend

Belastningsutjämnaren fungerar med instanser av virtuella datorer.  Dessa kan vara fristående, tillgänglighetsuppsättningar eller skaluppsättningar för virtuella datorer.  Alla instanser av virtuella datorer i ett enda virtuellt nätverk kan ingå i serverdelspoolen oavsett om den garanterades till en zon eller vilken zon som garanterades.

Om du vill justera och garantera frontend och serverdel med en enda zon, placera endast virtuella datorer inom samma zon i respektive serverdelspool.

Om du vill adressera virtuella datorer i flera zoner placerar du bara virtuella datorer från flera zoner i samma serverdpool.  När du använder skaluppsättningar för virtuella datorer kan du placera en eller flera skaluppsättningar för virtuella datorer i samma servergruppspool.  Och var och en av dessa virtuella dator skaluppsättningar kan vara i en enda eller flera zoner.

### <a name="outbound-connections"></a>Utgående anslutningar

Samma zonundant och zonegenskaper gäller för [utgående anslutningar](load-balancer-outbound-connections.md).  En zonundant offentlig IP-adress som används för utgående anslutningar betjänas av alla zoner. En offentlig IP-adress i zonområdet betjänas endast av den zon som den garanteras i.  Utgående anslutning SNAT-portallokerier överlever zonfel och ditt scenario fortsätter att tillhandahålla utgående SNAT-anslutning om den inte påverkas av zonfel.  Detta kan kräva att överföringar eller anslutningar återupprättas för zonundanta scenarier om ett flöde betjänades av en påverkad zon.  Flöden i andra zoner än de påverkade zonerna påverkas inte.

SNAT-portförallokeringsalgoritmen är densamma med eller utan tillgänglighetszoner.

### <a name="health-probes"></a>Hälsotillståndsavsökningar

Dina befintliga hälsoavsökningsdefinitioner förblir som de är utan tillgänglighetszoner.  Vi har dock utökat hälsomodellen på infrastrukturnivå. 

När du använder zonupptrakta frontends utökar Load Balancer sin interna hälsomodell för att självständigt söka igenom räckvidden för en virtuell dator från varje tillgänglighetszon och stänga av sökvägar över zoner som kan ha misslyckats utan kundintervention.  Om en viss sökväg inte är tillgänglig från belastningsutjämnarens infrastruktur för en zon till en virtuell dator i en annan zon, kan belastningsutjämning identifiera och undvika det här felet. Andra zoner som kan nå den här virtuella datorn kan fortsätta att betjäna den virtuella datorn från sina respektive frontends.  Därför är det möjligt att varje zon kan ha lite olika distributioner av nya flöden under felhändelser samtidigt som den skyddar den allmänna hälsan för din end-to-end-tjänst.

## <a name="design-considerations"></a><a name="design"></a>Design överväganden

Belastningsutjämnaren är avsiktligt flexibel i samband med tillgänglighetszoner. Du kan välja att justera mot zoner eller så kan du välja att vara zonundant för varje regel.  Ökad tillgänglighet kan komma till priset av ökad komplexitet och du måste designa för tillgänglighet för optimal prestanda.  Låt oss ta en titt på några viktiga design överväganden.

### <a name="automatic-zone-redundancy"></a>Automatisk zonredundans

Belastningsutjämnare gör det enkelt att ha en enda IP som en zon-redundant frontend. En zonuppsagd IP-adress kan på ett säkert sätt betjäna en zonresurs i valfri zon och kan överleva ett eller flera zonfel så länge en zon förblir felfri inom regionen. Omvänt är en zonindelning frontend en minskning av tjänsten till en enda zon och delar öde med respektive zon.

Zonredundans innebär inte att datasöken eller kontrollplanet är stötande.  det är uttryckligen dataplan. Zonundanterade flöden kan använda alla zoner och en kunds flöden använder alla felfria zoner i en region. I händelse av zonfel påverkas inte trafikflöden som använder felfria zoner vid den tidpunkten.  Trafikflöden med hjälp av en zon vid tidpunkten för zonfel kan påverkas, men program kan återställas. Dessa flöden kan fortsätta i de återstående felfria zonerna inom regionen vid återsändning eller återupprättande, när Azure har konvergerat runt zonfelet.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>Gränser för tvärzon

Det är viktigt att förstå att varje gång en end-to-end-tjänst korsar zoner, delar du ödet med inte en zon utan potentiellt flera zoner.  Därför kanske din heltäckande tjänst inte har fått någon tillgänglighet över distributioner som inte är zonbaserade.

Undvik att införa oavsiktliga beroenden mellan zoner, vilket upphäver tillgänglighetsvinster när du använder tillgänglighetszoner.  När ditt program består av flera komponenter och du vill vara motståndskraftig mot zonfel, måste du se till att tillräckligt kritiska komponenter överlever i händelse av att en zon misslyckas.  En enda kritisk komponent för ditt program kan till exempel påverka hela programmet om det bara finns i en annan zon än de efterlevande zonerna.  Dessutom bör du också överväga zonåterställning och hur ditt program kommer att konvergera. Du måste förstå hur din ansökan skäl med avseende på fel av delar av den. Låt oss granska några viktiga punkter och använda dem som inspiration för frågor som du tänker igenom ditt specifika scenario.

- Om ditt program har två komponenter som en IP-adress och en virtuell dator med hanterad disk, och de är garanterade i zon 1 och zon 2, när zon 1 misslyckas kommer din end-to-end-tjänst inte att överleva när zon 1 misslyckas.  Korsa inte zoner med zonscenarier om du inte har full förståelse för att du skapar ett potentiellt farligt felläge.  Det här scenariot kan ge flexibilitet.

- Om ditt program har två komponenter som en IP-adress och en virtuell dator med hanterad disk, och de är garanterade att zon-redundant och zon 1 respektive, kommer din end-to-end-tjänst att överleva zonfel i zon 2, zon 3 eller båda om inte zon 1 har misslyckats.  Men du förlorar en viss förmåga att resonera om hälsan hos din tjänst om allt du observerar är räckvidden för frontend.  Överväg att utveckla en mer omfattande hälso- och kapacitetsmodell.  Du kan använda zonuppsagda och zonspecifika begrepp tillsammans för att utöka insikt och hanterbarhet.

- Om ditt program har två komponenter som en zon redundant Load Balancer-frontend och en skala från flera zoner virtuell dator som har angetts i tre zoner, kommer dina resurser i zoner som inte påverkas av fel att vara tillgängliga, men din servicekapacitet från slutpunkt till slutpunkt kan försämras under zonfel. Ur ett infrastrukturperspektiv kan distributionen överleva ett eller flera zonfel och detta väcker följande frågor:
  - Förstår du hur din ansökan resonerar om sådana fel och försämrad kapacitet?
  - Behöver du ha skydd i din tjänst för att tvinga en redundans till en region par om det behövs?
  - Hur kommer du att övervaka, upptäcka och mildra ett sådant scenario? Du kanske kan använda standardbelastningsutjämningsdiagnostik för att öka övervakningen av tjänstens prestanda från slut-till-slut. Fundera över vad som är tillgängligt och vad som kan behöva förstärkas för en fullständig bild.

- Zoner kan göra fel lättare att förstå och inneslutna.  Zonfel skiljer sig dock inte från andra fel när det gäller begrepp som timeout, återförsök och bakåtkomstalgoritmer. Även om Azure Load Balancer tillhandahåller zonupptrakta sökvägar och försöker återställa snabbt kan det uppstå omfördelningar eller ometableringar under ett feluppkomst och det är viktigt att förstå hur ditt program klarar av att Misslyckanden. Ditt belastningsutjämningssystem kommer att överleva, men du måste planera för följande:
  - När en zon misslyckas, förstår din end-to-end-tjänst detta och om tillståndet går förlorat, hur kommer du att återhämta dig?
  - När en zon returneras, förstår ditt program hur du konvergerar på ett säkert sätt?

Granska [Azure-molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns/) för att förbättra återhämtningen för ditt program till misslyckade scenarier.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [tillgänglighetszoner](../availability-zones/az-overview.md)
- Läs mer om [standardbelastningsjämningsapparat](load-balancer-standard-overview.md)
- Lär dig hur du [laddar virtuella datorer inom en zon med hjälp av en standardbelastningsutjämnare med en zoninriktad frontend](load-balancer-standard-public-zonal-cli.md)
- Lär dig hur du [laddar fördelnings virtuella datorer över zoner med hjälp av en standardbelastningsutjämnare med en zonundant frontend](load-balancer-standard-public-zone-redundant-cli.md)
- Lär dig mer om [Azure-molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns/) för att förbättra återhämtningen för ditt program till felscenarier.
