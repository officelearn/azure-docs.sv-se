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
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: e7ddb548d8dad5bdcc3021941877903377af9318
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771486"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Standard Load Balancer och tillgänglighetszoner

Azure Standard Load Balancer stöder scenarier med [tillgänglighets zoner](../availability-zones/az-overview.md) . Du kan använda Standard Load Balancer för att optimera tillgängligheten i ditt slut punkt till slut punkt genom att justera resurser med zoner och distribuera dem i olika zoner.  Granska [tillgänglighets zoner](../availability-zones/az-overview.md) för vägledning om vilka tillgänglighets zoner är, vilka regioner som för närvarande stöder tillgänglighets zoner och andra relaterade begrepp och produkter. Tillgänglighets zoner i kombination med Standard Load Balancer är en mycket och flexibel funktions uppsättning som kan skapa många olika scenarier.  Granska det här dokumentet för att förstå dessa [begrepp](#concepts) och [design rikt linjer](#design)för grundläggande scenarier.

>[!IMPORTANT]
>Granska [Tillgänglighetszoner](../availability-zones/az-overview.md) för närliggande ämnen, inklusive information om regioner.

## <a name="concepts"></a>Tillgänglighetszoner begrepp som tillämpas på Load Balancer

Det finns ingen direkt relation mellan Load Balancer resurser och faktisk infrastruktur. När du skapar en Load Balancer skapas inte en instans. Load Balancer-resurser är objekt inom vilka du kan uttrycka hur Azure ska program mera fördefinierad infrastruktur för flera innehavare för att uppnå det scenario som du vill skapa.  Detta är viktigt i samband med tillgänglighets zoner eftersom en enda Load Balancer resurs kan styra programmering av infrastruktur i flera tillgänglighets zoner medan en zon redundant tjänst visas som en resurs från en kunds visnings punkt.  

En Load Balancer själva resursen är regional och aldrig zonindelade.  Och ett VNet och undernät är alltid regionala och aldrig zonindelade. Vilken kornig het du kan konfigurera begränsas av varje konfiguration av en klient dels-, regel-och Server dels uppsättnings definition.

I samband med tillgänglighets zoner beskrivs beteende och egenskaper för en Load Balancer regel som zoner-redundanta eller zonindelade.  Zon-redundant och zonindelade beskriver egenskapen zonality för en egenskap.  I kontexten för Load Balancer innebär zon-redundant alltid *flera zoner* och zonindelade innebär att tjänsten isoleras till en *enda zon*.

Både offentliga och interna Load Balancer stödja zoner – redundanta och zonindelade scenarier och båda kan dirigera trafik mellan zoner vid behov (*belastnings utjämning mellan*zoner). 

### <a name="frontend"></a>Klientdel

En Load Balancer-frontend är en IP-konfiguration för klient delen som hänvisar till antingen en offentlig IP-adressresurs eller en privat IP-adress inom under nätet för en virtuell nätverks resurs.  Den utgör den belastningsutjämnade slut punkten där tjänsten exponeras.

En Load Balancer resurs kan innehålla regler med zonindelade-och Zone-redundanta klient delar samtidigt. 

När en offentlig IP-resurs eller en privat IP-adress har garanterats till en zon, är zonality (eller avsaknad av sådan) inte föränderligt.  Om du vill ändra eller utelämna zonality för en offentlig IP-adress eller en privat IP-adresspool måste du återskapa den offentliga IP-adressen i lämplig zon.  Tillgänglighets zoner ändrar inte begränsningarna för flera klient delar, granskar [flera klient delar för Load Balancer](load-balancer-multivip-overview.md) för information om den här möjligheten.

#### <a name="zone-redundant-by-default"></a>Zonen är redundant som standard

I en region med tillgänglighets zoner är en Standard Load Balancer-frontend zon-redundant som standard.  Zon-redundant innebär att alla inkommande eller utgående flöden hanteras av flera tillgänglighets zoner i en region samtidigt med en enda IP-adress. DNS-redundanta scheman är inte obligatoriska. En enda IP-adress för klient delen kan överleva zon haveriet och kan användas för att få åtkomst till alla (inte påverkade) Server dels medlemmar oberoende av zonen. En eller flera tillgänglighets zoner kan inte köras och data Sök vägen finns kvar så länge en zon i regionen är felfri. Klient delens enda IP-adress behandlas samtidigt av flera oberoende infrastruktur distributioner i flera tillgänglighets zoner.  Detta innebär inte hitless data Sök väg, men eventuella återförsök eller återupprättade kommer att lyckas i andra zoner som inte påverkas av zon felet.   

Följande utdrag är en illustration för hur du definierar en offentlig IP-adress för en zon-redundant offentlig IP-adress som ska användas med din offentliga Standard Load Balancer. Om du använder befintliga Resource Manager-mallar i konfigurationen lägger du till avsnittet **SKU** i dessa mallar.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Följande utdrag är en illustration för hur du definierar en zon-redundant IP-adress för klient delen för din interna Standard Load Balancer. Om du använder befintliga Resource Manager-mallar i konfigurationen lägger du till avsnittet **SKU** i dessa mallar.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Föregående utdrag är inte fullständiga mallar, men tänk på att visa egenskaper för att uttrycka tillgänglighets zoner.  Du måste lägga till dessa instruktioner i dina mallar.

#### <a name="optional-zone-isolation"></a>Valfri zon isolering

Du kan välja att en klient del garanteras för en enda zon, som kallas en *zonindelade-frontend*.  Detta innebär att alla inkommande eller utgående flöden betjänas av en enskild zon i en region.  Din klient del delar i nedbrytningen med zonens hälsa.  Data Sök vägen påverkas inte av andra problem i andra zoner än de som har garanterats. Du kan använda zonindelade-frontend-klienter för att exponera en IP-adress per tillgänglighets zon.  

Dessutom kan du använda zonindelade-frontend-klienter direkt för belastningsutjämnade slut punkter i varje zon. Du kan också använda detta för att exponera belastnings Utjämnings slut punkter per zon för att övervaka varje zon individuellt.  Eller för offentliga slut punkter kan du integrera dem med en DNS-belastnings Utjämnings produkt som [Traffic Manager](../traffic-manager/traffic-manager-overview.md) och använda ett enda DNS-namn. Klienten kommer sedan att matcha detta DNS-namn till flera zonindelade-IP-adresser.  

Om du vill blanda dessa begrepp (zoner-redundanta och zonindelade för samma server del) granskar du [flera klient delar för Azure Load Balancer](load-balancer-multivip-overview.md).

För en offentlig Load Balancer-klient lägger du till en *zon* parameter till den offentliga IP-resurs som refereras av IP-konfigurationen för klient delen som används av respektive regel.

För en intern Load Balancer-frontend lägger du till en *zon* parameter till den interna Load Balancer IP-konfiguration för klient delen. Zonindelade-frontend gör att Load Balancer garantera en IP-adress i ett undernät till en speciell zon.

Följande utdrag är en illustration för hur du definierar en offentlig IP-adress för zonindelade-standard i tillgänglighets Zon 1. Om du använder befintliga Resource Manager-mallar i konfigurationen lägger du till avsnittet **SKU** i dessa mallar.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Följande utdrag är en illustration för hur du definierar en intern Standard Load Balancer klient del i tillgänglighets Zon 1. Om du använder befintliga Resource Manager-mallar i konfigurationen lägger du till avsnittet **SKU** i dessa mallar. Definiera också egenskapen **zoner** i klient DELENS IP-konfiguration för den underordnade resursen.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

De föregående utdragen är inte fullständiga mallar, men tänk på att visa egenskaper för att uttrycka tillgänglighets zoner.  Du måste lägga till dessa instruktioner i dina mallar.

### <a name="cross-zone-load-balancing"></a>Belastnings utjämning mellan zoner

Belastnings utjämning mellan zoner är möjligheten för Load Balancer att komma åt en backend-slutpunkt i en zon och är oberoende av klient dels-och zonality.  Alla belastnings Utjämnings regler kan rikta in backend-instansen i alla tillgänglighets zoner eller regionala instanser.

Du måste vara noga med att skapa scenariot på ett sätt som uttrycks av en tillgänglighets zon. Du behöver exempelvis garantera distributionen av virtuella datorer i en enskild zon eller flera zoner och justera zonindelade-frontend-och zonindelade-backend-resurser till samma zon.  Om du korsar tillgänglighets zoner med endast zonindelade-resurser, kommer scenariot att fungera, men det kanske inte har ett rensat felläge med avseende på tillgänglighets zoner. 

### <a name="backend"></a>Serverdel

Load Balancer fungerar med Virtual Machines-instanser.  Dessa kan vara fristående, tillgänglighets uppsättningar eller skalnings uppsättningar för virtuella datorer.  Alla instanser av virtuella datorer i ett enda virtuellt nätverk kan vara en del av backend-poolen oavsett om den har garanterats till en zon eller om den var garanterad för en zon.

Om du vill justera och garantera klient delen och Server delen med en enda zon kan du bara placera virtuella datorer i samma zon i respektive backend-pool.

Om du vill adressera virtuella datorer över flera zoner kan du helt enkelt placera virtuella datorer från flera zoner i samma backend-pool.  När du använder skalnings uppsättningar för virtuella datorer kan du placera en eller flera skalnings uppsättningar för virtuella datorer i samma backend-pool.  Och var och en av de virtuella datorernas skalnings uppsättningar kan finnas i en eller flera zoner.

### <a name="outbound-connections"></a>Utgående anslutningar

Samma zon-redundanta och zonindelade egenskaper gäller för [utgående anslutningar](load-balancer-outbound-connections.md).  En zon-redundant offentlig IP-adress som används för utgående anslutningar betjänas av alla zoner. En offentlig IP-adress för zonindelade hanteras endast av zonen som den garanterar i.  Utgående anslutning SNAT port tilldelningar överleva zon haverier och scenariot fortsätter att tillhandahålla utgående SNAT-anslutningar om de inte påverkas av zon haveriet.  Detta kan kräva överföring eller för att anslutningar ska kunna återupprättas för zoner – redundanta scenarier om ett flöde har betjänats av en berörd zon.  Flöden i andra zoner än de påverkade zonerna påverkas inte.

Algoritmen för Förallokering av SNAT-port är densamma i eller utan tillgänglighets zoner.

### <a name="health-probes"></a>Hälsotillståndsavsökningar

Dina befintliga definitioner av hälso avsökningen förblir som de är utan tillgänglighets zoner.  Vi har dock expanderat hälso modellen på en infrastruktur nivå. 

När du använder zoner-redundanta frontend-enheter, expanderar Load Balancer sin interna hälso modell för att oberoende avsökning av tillgängligheten för en virtuell dator från varje tillgänglighets zon och stänga av sökvägar över zoner som kan ha misslyckats utan att kunden behöver göra något.  Om en specifik sökväg inte är tillgänglig från Load Balancer infrastruktur för en zon till en virtuell dator i en annan zon kan Load Balancer identifiera och undvika det här felet. Andra zoner som kan komma åt den här virtuella datorn kan fortsätta att betjäna den virtuella datorn från sina respektive klient datorer.  Därför är det möjligt att varje zon vid fel händelser kan ha olika distributioner av nya flöden samtidigt som den övergripande hälsan för din slut punkt till slut punkt skyddas.

## <a name="design"></a>Design överväganden

Load Balancer är avsiktligt flexibelt i samband med tillgänglighets zoner. Du kan välja att justera till zoner eller så kan du välja att vara zon-redundant för varje regel.  Ökad tillgänglighet kan komma till priset för ökad komplexitet och du måste utforma för att få till gång till optimala prestanda.  Låt oss ta en titt på några viktiga design överväganden.

### <a name="automatic-zone-redundancy"></a>Automatisk zon – redundans

Load Balancer gör det enkelt att ha en enda IP-adress som en zon redundant klient del. En zon redundant IP-adress kan på ett säkert sätt betjäna en zonindelade-resurs i en zon och kan överleva en eller flera zon fel så länge en zon fortfarande är felfri i regionen. Däremot är en zonindelade-frontend en minskning av tjänsten till en enda zon och delas med respektive zon.

Zon-redundans innebär inte hitless Datapath eller kontroll plan.  Det är ett uttryckligt data plan. Zoner – redundanta flöden kan använda alla zoner och en kunds flöden kommer att använda alla felfria zoner i en region. I händelse av zon fel påverkas inte trafik flöden som använder felfria zoner vid denna tidpunkt.  Trafik flöden som använder en zon vid tidpunkten för zon fel kan påverkas, men program kan återställas. Dessa flöden kan fortsätta i de återstående friska zonerna inom regionen vid återöverföring eller återupprättande av Azure när Azure har konvergerat runt zon fel.

### <a name="xzonedesign"></a>Gränser för mellan zoner

Det är viktigt att förstå att varje gång en slutpunkt-till-slutpunkt-tjänst korsar zoner. du kan dela en omvandling med inte en zon men potentiellt flera zoner.  Till följd av detta kanske din slutpunkt-till-slutpunkt-tjänst inte har fått någon tillgänglighet över icke-zonindelade distributioner.

Undvik att införa oönskade kors zon beroenden, vilket kommer att upphäver tillgänglighets vinster när du använder tillgänglighets zoner.  När ditt program består av flera komponenter och du vill bli elastisk till zon haveri, måste du vara noga med att säkerställa att tillräckliga kritiska komponenter är överlevnads i händelse av en zon som inte kan utföras.  Till exempel kan en enskild kritisk komponent för ditt program påverka hela programmet om det bara finns i en annan zon än den som finns i zonen.  Dessutom kan du också överväga zon återställningen och hur programmet kommer att konvergera. Du måste förstå hur ditt program är på grund av fel i delar av det. Vi går igenom några viktiga punkter och använder dem som inspiration för frågor när du tycker att du tänker igenom ditt speciella scenario.

- Om ditt program har två komponenter som en IP-adress och en virtuell dator med hanterad disk, och de är garanterade i zon 1 och zon 2, när zon 1 Miss lyckas, kommer din slutpunkt-till-slutpunkt-tjänst inte att överleva när zon 1 Miss lyckas.  Du kan inte korsa zoner med zonindelade-scenarier om du inte fullt ut förstår att du skapar ett potentiellt farligt felläge.  Det här scenariot kan ge flexibilitet.

- Om ditt program har två komponenter som en IP-adress och en virtuell dator med hanterad disk, och de är garanterade att vara zoner-redundanta och zon 1, kommer din slutpunkt-till-slutpunkt-tjänst överleva zon fel i zon 2, zon 3 eller båda om inte zon 1 har misslyckats.  Men du förlorar viss möjlighet att tänka på hälso tillståndet för din tjänst om allt du finner är klient delens tillgänglighet.  Överväg att utveckla en mer omfattande hälso-och kapacitets modell.  Du kan använda zon-redundanta och zonindelade koncept tillsammans för att utöka insikter och hanterbarhet.

- Om ditt program har två komponenter som en zon redundant Load Balancer-frontend och en skalnings uppsättning för virtuella datorer mellan zoner i tre zoner, kommer dina resurser i zoner som inte påverkas av ett haveri att vara tillgängliga, men din tjänst kapacitet för slut punkt till slut punkt kan försämras vid zon haveri. Från ett infrastruktur perspektiv kan distributionen överleva ett eller flera zon haverier, och detta ger följande frågor:
  - Förstår du hur ditt program är på grund av sådana fel och försämrad kapacitet?
  - Behöver du ha skydd i tjänsten för att tvinga fram en redundansväxling till ett regions par om det behövs?
  - Hur ska du övervaka, identifiera och minimera ett sådant scenario? Du kanske kan använda Standard Load Balancer Diagnostics för att utöka övervakningen av dina prestanda för slut punkt till slut punkt. Överväg vad som är tillgängligt och vad som kan behöva utökningen för en fullständig bild.

- Zoner kan göra det enklare att förstå och innesluta zoner.  Zon Haverin skiljer sig dock från andra problem när den kommer till koncept som tids gränser, återförsök och backoff-algoritmer. Även om Azure Load Balancer tillhandahåller zoner som är redundanta och försöker återställa snabbt, kan återöverföringar eller återställningar ske när ett fel uppstår och det är viktigt att förstå hur ditt program går till fel. Schemat för belastnings utjämning kommer att överleva, men du måste planera för följande:
  - Om en zon Miss lyckas, är din slutpunkt-till-slutpunkt-tjänst att förstå detta och om status förloras, hur kommer du att återställa?
  - Kan programmet förstå hur man konvergerar på ett säkert sätt när en zon returnerar?

Gå igenom [design mönster för molnet i molnet](https://docs.microsoft.com/azure/architecture/patterns/) för att förbättra återhämtningen hos ditt program till haveri situationer.

### <a name="zonalityguidance"></a>Zone-redundant jämfört med zonindelade

Zon-redundant kan ge en enkelhet med ett oberoende alternativ och samtidigt elastiskt alternativ med en enda IP-adress för tjänsten.  Det kan minska komplexiteten i tur och ett.  Zon-redundant har även rörlighet mellan zoner och kan användas på ett säkert sätt på resurser i alla zoner.  Dessutom är det framtida korrektur i regioner utan tillgänglighets zoner som kan begränsa de ändringar som krävs när en region har tillgänglighets zoner.  Konfigurations syntaxen för en zon-redundant IP-adress eller klient del har slutförts i valfri region, inklusive de utan tillgänglighets zoner: en zon har inte angetts i resursens egenskaps zoner:.

Zonindelade kan ge en uttrycklig garanti till en zon, som uttryckligen delar in överlappande med hälso tillståndet för zonen. Att skapa en Load Balancer regel med en zonindelade IP-adress-frontend eller zonindelade intern Load Balancer-frontend kan vara ett bra tillägg, särskilt om din anslutna resurs är en zonindelade virtuell dator i samma zon.  Eller så kanske ditt program kräver uttrycklig kunskap om vilken zon en resurs finns i förväg och du vill veta mer om tillgänglighet i separata zoner.  Du kan välja att exponera flera zonindelade-frontend-enheter för en slutpunkt-till-slutpunkt-tjänst som distribueras mellan zoner (det vill säga per zon zonindelade-frontend för flera zonindelade Virtual Machine Scale set).  Och om dina zonindelade-frontend-klienter är offentliga IP-adresser kan du använda dessa flera zonindelade-frontend-klienter för att exponera tjänsten med [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Eller så kan du använda flera zonindelade-frontend-klienter för att få kunskap om hälso tillstånd och prestanda i olika zoner genom att övervaka lösningar från tredje part och exponera den övergripande tjänsten med en zon redundant klient del. Du bör bara hantera zonindelade-resurser med zonindelade-frontend-platser justerade till samma zon och undvika potentiellt skadliga kors zons scenarier för zonindelade-resurser.  Zonindelade-resurser finns bara i regioner där tillgänglighets zoner finns.

Det finns ingen allmän vägledning om att ett är ett bättre alternativ än den andra utan att känna till tjänst arkitekturen.  Gå igenom [design mönster för molnet i molnet](https://docs.microsoft.com/azure/architecture/patterns/) för att förbättra återhämtningen hos ditt program till haveri situationer.

## <a name="limitations"></a>Begränsningar

- Medan data planet är helt zoner – redundant (om inte zonindelade-garanti har angetts), är kontroll Plans åtgärder inte helt zoner-redundanta.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Tillgänglighetszoner](../availability-zones/az-overview.md)
- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md)
- Lär dig att [belastningsutjämna virtuella datorer inom en zon med hjälp av en standard Load Balancer med en zonindelade-frontend](load-balancer-standard-public-zonal-cli.md)
- Lär dig att [belastningsutjämna virtuella datorer över zoner med hjälp av en standard Load Balancer med en zon-redundant klient](load-balancer-standard-public-zone-redundant-cli.md) del
- Lär dig mer om [moln design mönster i Azure](https://docs.microsoft.com/azure/architecture/patterns/) för att förbättra programmets återhämtnings förmåga till haveri situationer.
