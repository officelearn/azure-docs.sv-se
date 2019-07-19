---
title: Azure Standard Load Balancer och Tillgänglighetszoner
titlesuffix: Azure Load Balancer
description: Standard Load Balancer och tillgänglighetszoner
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: allensu
ms.openlocfilehash: 5ef7de148d5ef4727602b8287164f2aff9ccf822
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274505"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Standard Load Balancer och tillgänglighetszoner

Azure Load Balancer standard-SKU: n stöder [Tillgänglighetszoner](../availability-zones/az-overview.md) scenarier. Flera nya begrepp är tillgängliga med Standard Load Balancer, vilket gör att du kan optimera tillgängligheten i ditt slut punkt till slut punkt genom att justera resurser med zoner och distribuera dem i olika zoner.  Läs [Tillgänglighetszoner](../availability-zones/az-overview.md) för vägledning om vad Tillgänglighetszoner är, vilka regioner som för närvarande stöder Tillgänglighetszoner och andra relaterade begrepp och produkter. Tillgänglighetszoner i kombination med Standard Load Balancer är en mycket och flexibel funktions uppsättning som kan skapa många olika scenarier.  Granska det här dokumentet för att förstå dessa [begrepp](#concepts) och [design rikt linjer](#design)för grundläggande scenarier.

>[!IMPORTANT]
>Granska [Tillgänglighetszoner](../availability-zones/az-overview.md) för närliggande ämnen, inklusive information om regioner.

## <a name="concepts"></a>Tillgänglighetszoner begrepp som tillämpas på Load Balancer

Det finns ingen direkt relation mellan Load Balancer resurser och faktisk infrastruktur. När du skapar en Load Balancer skapas inte en instans. Load Balancer-resurser är objekt inom vilka du kan uttrycka hur Azure ska program mera fördefinierad infrastruktur för flera innehavare för att uppnå det scenario som du vill skapa.  Detta är betydelsefullt i samband med Tillgänglighetszoner eftersom en enda Load Balancer resurs kan styra programmering av infrastruktur i flera Tillgänglighetszoner medan en zon redundant tjänst visas som en resurs från en kunds visnings plats.

En Load Balancer resurs funktioner uttrycks som en klient del, en regel, en hälso avsökning och en definition av en backend-pool.

I samband med Tillgänglighetszoner betecknas beteendet och egenskaperna för en Load Balancer resurs som zoner-redundanta eller zonindelade.  Zon-redundant och zonindelade beskriver egenskapen zonality för en egenskap.  I kontexten för Load Balancer innebär zon-redundant alltid *flera zoner* och zonindelade innebär att tjänsten isoleras till en *enda zon*.

Både offentliga och interna Load Balancer stödja zoner – redundanta och zonindelade scenarier och båda kan dirigera trafik mellan zoner vid behov (*belastnings utjämning mellan*zoner).

En Load Balancer själva resursen är regional och aldrig zonindelade.  Och ett VNet och undernät är alltid regionala och aldrig zonindelade.

### <a name="frontend"></a>Delen

En Load Balancer-frontend är en IP-konfiguration för klient delen som hänvisar till antingen en offentlig IP-adressresurs eller en privat IP-adress inom under nätet för en virtuell nätverks resurs.  Den utgör den belastningsutjämnade slut punkten där tjänsten exponeras.

En Load Balancer resurs kan innehålla både zonindelade-och Zone-redundanta klient delar samtidigt. 

När en offentlig IP-resurs har garanterats till en zon, är zonality (eller avsaknad av sådan) inte föränderligt.  Om du vill ändra eller utelämna zonality för en offentlig IP-klient måste du återskapa den offentliga IP-adressen i lämplig zon.  

Du kan ändra zonality för en klient del av en intern Load Balancer genom att ta bort och återskapa klient delen, ändra eller utesluta zonality.

När du använder flera klient delar granskar du [flera klient delar för Load Balancer](load-balancer-multivip-overview.md) viktiga överväganden.

#### <a name="zone-redundant-by-default"></a>Zonen är redundant som standard

>[!IMPORTANT]
>Granska [Tillgänglighetszoner](../availability-zones/az-overview.md) för närliggande ämnen, inklusive information om regioner.

I en region med Tillgänglighetszoner är en Standard Load Balancer-frontend zon-redundant som standard.  En enda IP-adress för klient delen kan överleva zon haveriet och kan användas för att få åtkomst till alla medlemmar i Server delen oberoende av zonen. Detta innebär inte hitless data Sök väg, men alla återförsök eller återupprättade data kommer att lyckas. DNS-redundanta scheman är inte obligatoriska. Klient delens enda IP-adress behandlas samtidigt av flera oberoende infrastruktur distributioner i flera Tillgänglighetszoner.  Zon-redundant innebär att alla inkommande eller utgående flöden betjänas av flera Tillgänglighetszoner i en region samtidigt med en enda IP-adress.

En eller flera Tillgänglighetszoner kan inte fungera och data Sök vägen finns kvar så länge en zon i regionen är felfri. Zon-redundant konfiguration är standard och kräver inga ytterligare åtgärder.  

Använd följande skript för att skapa en zon-redundant offentlig IP-adress för din interna Standard Load Balancer. Om du använder befintliga Resource Manager-mallar i konfigurationen lägger du till avsnittet **SKU** i dessa mallar.

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

Använd följande skript för att skapa en zon-redundant IP-adress för klient delen för din interna Standard Load Balancer. Om du använder befintliga Resource Manager-mallar i konfigurationen lägger du till avsnittet **SKU** i dessa mallar.

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

#### <a name="optional-zone-isolation"></a>Valfri zon isolering

Du kan välja att en klient del garanteras för en enda zon, som kallas en *zonindelade-frontend*.  Detta innebär att alla inkommande eller utgående flöden betjänas av en enskild zon i en region.  Din klient del delar i nedbrytningen med zonens hälsa.  Data Sök vägen påverkas inte av andra problem i andra zoner än de som har garanterats. Du kan använda zonindelade-frontend-klienter för att exponera en IP-adress per tillgänglighets zon.  Du kan också använda zonindelade-frontend direkt eller, när klient delen består av offentliga IP-adresser, integrera dem med en DNS-belastnings Utjämnings produkt som [Traffic Manager](../traffic-manager/traffic-manager-overview.md) och använda ett enda DNS-namn, som en klient kommer att matcha till flera zonindelade IP-adresser .  Du kan också använda detta för att exponera belastnings Utjämnings slut punkter per zon för att övervaka varje zon individuellt.  Om du vill blanda dessa begrepp (zoner-redundanta och zonindelade för samma server del) granskar du [flera klient delar för Azure Load Balancer](load-balancer-multivip-overview.md).

För en offentlig Load Balancer-frontend lägger du till en *zon* parameter till den offentliga IP-adressen som klient delens IP-konfiguration refererar till.  

För en intern Load Balancer-frontend lägger du till en *zon* parameter till den interna Load Balancer IP-konfiguration för klient delen. Zonindelade-frontend gör att Load Balancer garantera en IP-adress i ett undernät till en speciell zon.

Använd följande skript för att skapa en offentlig IP-adress för zonindelade-standard i tillgänglighets Zon 1. Om du använder befintliga Resource Manager-mallar i konfigurationen lägger du till avsnittet **SKU** i dessa mallar.

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

Använd följande skript för att skapa ett internt Standard Load Balancer klient delen i tillgänglighets Zon 1.

Om du använder befintliga Resource Manager-mallar i konfigurationen lägger du till avsnittet **SKU** i dessa mallar. Definiera också egenskapen **zoner** i klient DELENS IP-konfiguration för den underordnade resursen.

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

### <a name="cross-zone-load-balancing"></a>Belastnings utjämning mellan zoner

Belastnings utjämning mellan zoner är möjligheten för Load Balancer att komma åt en backend-slutpunkt i en zon och är oberoende av klient dels-och zonality.

Om du vill justera och garantera distributionen i en enda zon justerar du zonindelade-frontend-och zonindelade-backend-resurser till samma zon. Ingen ytterligare åtgärd krävs.

### <a name="backend"></a>Backend

Load Balancer fungerar med Virtual Machines.  Alla virtuella datorer i ett enda VNet kan vara en del av backend-poolen oavsett om den har garanterats till en zon eller om den var garanterad för en zon.

Om du vill justera och garantera klient delen och Server delen med en enda zon placerar du bara virtuella datorer inom samma zon i respektive backend-pool.

Om du vill adressera virtuella datorer över flera zoner placerar du bara virtuella datorer från flera zoner i samma backend-pool.  När du använder skalnings uppsättningar för virtuella datorer kan du placera en eller flera skalnings uppsättningar för virtuella datorer i samma backend-pool.  Och var och en av de virtuella datorernas skalnings uppsättningar kan finnas i en eller flera zoner.

### <a name="outbound-connections"></a>Utgående anslutningar

[Utgående anslutningar](load-balancer-outbound-connections.md) hanteras av alla zoner och är automatiskt zoner-redundanta i en region med Tillgänglighetszoner när en virtuell dator är associerad med en offentlig Load Balancer och en zon redundant klient del.  Utgående anslutning SNAT port tilldelningar överleva zon haverier.  

Om den virtuella datorn är associerad med en offentlig Load Balancer och en zonindelade-frontend, är det i sin tur garanterat att utgående anslutningar kan hanteras av en enda zon.  Utgående anslutningar delar omvandling med respektive zon hälsa.

SNAT-portens Förallokering och algoritm är detsamma i eller utan zoner.

### <a name="health-probes"></a>Hälsotillståndsavsökningar

Dina befintliga definitioner för hälso avsökningen är oförändrade utan Tillgänglighetszoner.  Men vi har expanderat hälso modellen på en infrastruktur nivå. 

När du använder Zondas-redundanta frontend-filer, expanderar Load Balancer sin interna hälso modell för att oberoende avsökning av tillgängligheten för en virtuell dator från varje tillgänglighets zon och stänga av sökvägar över zoner som kan ha misslyckats utan att kunden behöver göra något.  Om en specifik sökväg inte är tillgänglig från Load Balancer infrastruktur för en zon till en virtuell dator i en annan zon kan Load Balancer identifiera och undvika det här felet. Andra zoner som kan komma åt den här virtuella datorn kan fortsätta att betjäna den virtuella datorn från sina respektive klient datorer.  Därför är det möjligt att varje zon under fel händelser kan ha olika flödes fördelningar samtidigt som den övergripande hälso statusen för din slutpunkt-till-slutpunkt-tjänst skyddas.

## <a name="design"></a>Design överväganden

Load Balancer är avsiktligt flexibelt i samband med Tillgänglighetszoner. Du kan välja att justera till zoner eller så kan du välja att vara zon-redundant.  Ökad tillgänglighet kan komma till priset för ökad komplexitet och du måste utforma för att få till gång till optimala prestanda.  Låt oss ta en titt på några viktiga design överväganden.

### <a name="automatic-zone-redundancy"></a>Automatisk zon – redundans

Load Balancer gör det enkelt att ha en enda IP-adress som en zon redundant klient del. En zon redundant IP-adress kan på ett säkert sätt betjäna en zonindelade-resurs i en zon och kan överleva en eller flera zon fel så länge en zon fortfarande är felfri i regionen. Däremot är en zonindelade-frontend en minskning av tjänsten till en enda zon och delas med respektive zon.

Zon-redundans innebär inte hitless Datapath eller kontroll plan.  Det är ett uttryckligt data plan. Zoner – redundanta flöden kan använda alla zoner och en kunds flöden kommer att använda alla felfria zoner i en region. I händelse av zon fel påverkas inte trafik flöden som använder felfria zoner vid denna tidpunkt.  Trafik flöden som använder en zon vid tidpunkten för zon fel kan påverkas, men program kan återställas. Dessa flöden kan fortsätta i de återstående friska zonerna inom regionen vid återöverföring eller återupprättande av Azure när Azure har konvergerat runt zon fel.

### <a name="xzonedesign"></a>Gränser för mellan zoner

Det är viktigt att förstå att varje gång en slutpunkt-till-slutpunkt-tjänst korsar zoner. du kan dela en omvandling med inte en zon men potentiellt flera zoner.  Till följd av detta kanske din slutpunkt-till-slutpunkt-tjänst inte har fått någon tillgänglighet över icke-zonindelade distributioner.

Undvik att införa oönskade kors zon beroenden, vilket kommer att upphäver tillgänglighets vinster när du använder Tillgänglighetszoner.  När ditt program består av flera komponenter och du vill bli elastisk till zon haveri, måste du vara noga med att säkerställa att tillräckliga kritiska komponenter är överlevnads i händelse av en zon som inte kan utföras.  Till exempel kan en enskild kritisk komponent för ditt program påverka hela programmet om det bara finns i en annan zon än den som finns i zonen.  Dessutom kan du också överväga zon återställningen och hur programmet kommer att konvergera. Vi går igenom några viktiga punkter och använder dem som inspiration för frågor när du tycker att du tänker igenom ditt speciella scenario.

- Om ditt program har två komponenter som en IP-adress och en virtuell dator med hanterad disk, och de är garanterade i zon 1 och zon 2, när zon 1 Miss lyckas, kommer din slutpunkt-till-slutpunkt-tjänst inte att överleva när zon 1 Miss lyckas.  Du behöver inte korsa zoner om du inte fullt ut förstår att du skapar ett potentiellt farligt felläge.

- Om ditt program har två komponenter som en IP-adress och en virtuell dator med hanterade diskar och de garanterat är zoner-redundanta och zon 1, kommer din slutpunkt-till-slutpunkt-tjänst att överleva zon fel i zon 2, zon 3 eller både om zon 1 inte har misslyckats.  Men du förlorar viss möjlighet att tänka på hälso tillståndet för din tjänst om allt du finner är klient delens tillgänglighet.  Överväg att utveckla en mer omfattande hälso-och kapacitets modell.  Du kan använda zon-redundanta och zonindelade koncept tillsammans för att utöka insikter och hanterbarhet.

- Om ditt program har två komponenter som en zon redundant Load Balancer-frontend och en skalnings uppsättning för virtuella datorer mellan zoner i tre zoner, kommer dina resurser i zoner som inte påverkas av ett haveri att vara tillgängliga, men din tjänst kapacitet för slut punkt till slut punkt kan försämras vid zon haveri. Från ett infrastruktur perspektiv kan distributionen överleva ett eller flera zon haverier, och detta ger följande frågor:
  - Förstår du hur ditt program är på grund av sådana fel och försämrad kapacitet?
  - Behöver du ha skydd i tjänsten för att tvinga fram en redundansväxling till ett regions par om det behövs?
  - Hur ska du övervaka, identifiera och minimera ett sådant scenario? Du kanske kan använda Standard Load Balancer Diagnostics för att utöka övervakningen av dina prestanda för slut punkt till slut punkt. Överväg vad som är tillgängligt och vad som kan behöva utökningen för en fullständig bild.

- Zoner kan göra det enklare att förstå och innesluta zoner.  Zon Haverin skiljer sig dock från andra problem när den kommer till koncept som tids gränser, återförsök och backoff-algoritmer. Även om Azure Load Balancer tillhandahåller zoner som är redundanta och försöker återställa snabbt, kan återöverföringar eller återställningar ske när ett fel uppstår och det är viktigt att förstå hur ditt program går till fel. Schemat för belastnings utjämning kommer att överleva, men du måste planera för följande:
  - Om en zon Miss lyckas, är din slutpunkt-till-slutpunkt-tjänst att förstå detta och om status förloras, hur kommer du att återställa?
  - Kan programmet förstå hur man konvergerar på ett säkert sätt när en zon returnerar?

### <a name="zonalityguidance"></a>Zone-redundant jämfört med zonindelade

>[!IMPORTANT]
>Granska [Tillgänglighetszoner](../availability-zones/az-overview.md) för närliggande ämnen, inklusive information om regioner.

Zon-redundant kan tillhandahålla en zon-oberoende och samtidigt elastiskt alternativ med en enda IP-adress för tjänsten.  Det kan minska komplexiteten i tur och ett.  Zon-redundant har även rörlighet mellan zoner och kan användas på ett säkert sätt på resurser i alla zoner.  Dessutom är det framtida korrektur i regioner utan Tillgänglighetszoner, vilket kan begränsa ändringar som krävs när en region får Tillgänglighetszoner.  Konfigurations syntaxen för en zon-redundant IP-adress eller klient del har slutförts i valfri region, inklusive de utan Tillgänglighetszoner.

Zonindelade kan ge en uttrycklig garanti till en zon som delar överlappande med zonens hälsa. Att associera en zonindelade-IP-adress eller zonindelade Load Balancer-frontend kan vara ett önskvärdt eller rimligt attribut, särskilt om din anslutna resurs är en zonindelade VM i samma zon.  Eller så kanske ditt program kräver uttrycklig kunskap om vilken zon en resurs finns i och du vill veta mer om tillgänglighet i separata zoner.  Du kan välja att exponera flera zonindelade-frontend-enheter för en slutpunkt-till-slutpunkt-tjänst som distribueras mellan zoner (det vill säga per zon zonindelade-frontend för flera zonindelade Virtual Machine Scale set).  Och om dina zonindelade-frontend-klienter är offentliga IP-adresser kan du använda dessa flera zonindelade-frontend-klienter för att exponera tjänsten med [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Eller så kan du använda flera zonindelade-frontend-klienter för att få kunskap om hälso tillstånd och prestanda i olika zoner genom att övervaka lösningar från tredje part och exponera den övergripande tjänsten med en zon redundant klient del. Du bör bara hantera zonindelade-resurser med zonindelade-frontend-platser justerade till samma zon och undvika potentiellt skadliga kors zons scenarier för zonindelade-resurser.  Zonindelade-resurser finns bara i regioner där Tillgänglighetszoner finns.

Det finns ingen allmän vägledning om att ett är ett bättre alternativ än den andra utan att känna till tjänst arkitekturen.

## <a name="limitations"></a>Begränsningar

- Medan data planet är helt zoner – redundant (om inte zonindelade-garanti har angetts), är kontroll Plans åtgärder inte helt zoner-redundanta.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Tillgänglighetszoner](../availability-zones/az-overview.md)
- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md)
- Lär dig att [belastningsutjämna virtuella datorer inom en zon med hjälp av en standard Load Balancer med en zonindelade-frontend](load-balancer-standard-public-zonal-cli.md)
- Lär dig att [belastningsutjämna virtuella datorer över zoner med hjälp av en standard Load Balancer med en zon-redundant klient](load-balancer-standard-public-zone-redundant-cli.md) del
