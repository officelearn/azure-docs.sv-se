---
title: Azure Standard Load Balancer och Tillgänglighetszoner
titlesuffix: Azure Load Balancer
description: Standard Load Balancer och tillgänglighetszoner
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/08/2018
ms.author: kumud
ms.openlocfilehash: d157c331b633617bacfb5cc7254d188516f10ad7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187066"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Standard Load Balancer och tillgänglighetszoner

Har stöd för Azure Load Balancer Standard-SKU [Tillgänglighetszoner](../availability-zones/az-overview.md) scenarier. Flera nya begrepp är tillgängliga med Standard Load Balancer, vilket gör att du kan optimera tillgänglighet i ditt scenario för slutpunkt till slutpunkt genom att justera resurser med zoner och fördela dem i olika zoner.  Granska [Tillgänglighetszoner](../availability-zones/az-overview.md) råd om Tillgänglighetszoner är vilka regioner stöder för närvarande Tillgänglighetszoner och andra relaterade begrepp och produkter. Tillgänglighetszoner i kombination med Standard Load Balancer är en omfattande och flexibel funktionsuppsättning som kan skapa många olika scenarier.  Granska det här dokumentet för att förstå dessa [begrepp](#concepts) och grundläggande scenario [designvägledning](#design).

>[!NOTE]
>Granska [Tillgänglighetszoner](https://aka.ms/availabilityzones) för andra relaterade ämnen. 

## <a name="concepts"></a> Tillgänglighetszoner begrepp som tillämpas på belastningsutjämnare

Det finns ingen direkt relation mellan belastningshanterare och faktiska infrastruktur. Skapa en belastningsutjämnare, skapar inte en instans. Resurser för belastningsutjämning är objekt som du kan uttrycka hur Azure bör programmet den fördefinierade flera innehavare-infrastrukturen för att uppnå det scenario som du vill skapa.  Det här är betydande i samband med Availability Zones eftersom en enskild resurs för belastningsutjämnaren kan styra programmering av infrastrukturen i flera Tillgänglighetszoner medan en zonredundant tjänst visas som en resurs från en kund synsätt.

Funktioner för en belastningsutjämnare resource uttrycks som en klientdel, en regel, en hälsoavsökning och en definition för backend-poolen.

I samband med Availability Zones beskrivs beteende och egenskaperna för en belastningsutjämnare-resurs som zonredundant eller zonindelade.  Zonredundant och zonindelade beskriver zonality för en egenskap.  I samband med belastningsutjämnaren, zonredundant alltid innebär *alla zoner* och zonindelade sätt, vilket ger tjänsten till en *samma zon*.

Stöd för både offentliga och interna belastningsutjämnare zonredundant och zonindelade och båda kan dirigera trafik mellan zoner efter behov (*belastningsutjämning mellan zoner*).

En belastningsutjämnare datorresursen är regionala och zonindelade aldrig.  Och ett virtuellt nätverk och undernät är alltid regionala och zonindelade aldrig.

### <a name="frontend"></a>Klientdel

En klientdel för belastningsutjämnare är en Frontend IP-konfiguration som refererar till en offentlig IP-adressresurs eller en privat IP-adress inom undernätet för en resurs för virtuella nätverk.  Den utgör den belastningsutjämnade slutpunkten där tjänsten exponeras.

En belastningsutjämnare resurs kan innehålla både zonindelade och zonredundanta klientdelar samtidigt. 

När en zon har garanterat en offentlig IP-resurs, inte zonality (eller avsaknad av) föränderliga.  Om du vill ändra eller utelämna zonality av en offentlig IP-klientdel, måste du återskapa den offentliga IP-Adressen i den aktuella zonen.  

Du kan ändra zonality av en klientdel för en intern belastningsutjämnare genom att ta bort och återskapa klientdelen, ändrar eller tar bort zonality.

När du använder flera klienter, granska [flera klienter för belastningsutjämnaren](load-balancer-multivip-overview.md) för mer information.

#### <a name="zone-redundant-by-default"></a>Zonredundant som standard

I en region med Azure Availability Zones är en Standard Load Balancer-klientdel zonredundant som standard.  En enda frontend IP-adress kan överleva zon fel och kan användas för att nå alla medlemmar i serverdelspool oavsett zonen. Detta innebär inte hitless datasökväg, men alla återförsök eller reestablishment lyckas. DNS-redundans scheman inte behövs. Den frontend IP-adress hanteras samtidigt av flera oberoende infrastruktur distributioner i flera Tillgänglighetszoner.  Zonredundant innebär att alla inkommande eller utgående flöden betjänas av flera Tillgänglighetszoner i en region samtidigt med hjälp av en IP-adress.

En eller flera Tillgänglighetszoner kan misslyckas och datasökvägen kvarstår så länge som en zon i regionen förblir felfritt. Zonredundant konfigurationen är standard och kräver inga ytterligare åtgärder.  När en region får möjlighet att stöd för Tillgänglighetszoner, blir en befintlig klientdel zonredundant automatiskt.

Använd följande skript för att skapa en zonredundant offentlig IP-adress för interna Standard Load Balancer. Om du använder befintliga Resource Manager-mallar i din konfiguration, lägger du till den **sku** avsnitt för att dessa mallar.

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

Använd följande skript för att skapa en zonredundant frontend IP-adress för din interna Standard Load Balancer. Om du använder befintliga Resource Manager-mallar i din konfiguration, lägger du till den **sku** avsnitt för att dessa mallar.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "[resourceGroup().location]",
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

#### <a name="optional-zone-guarantee"></a>Valfritt zon garanti

Du kan välja att ha en klientdel att garantera att en zon, vilket kallas en *zonindelad klientdel*.  Det innebär att alla inkommande eller utgående flöden hanteras av en enskild zon i en region.  Dina klientdelsservrar delar öde med hälsotillståndet för zonen.  Datasökvägen påverkas inte av fel i zoner än där det var säkert. Du kan använda zonindelad klienter för att exponera en IP-adress per Tillgänglighetszon.  Dessutom du kan använda zonindelad klienter direkt eller, när klientdelen består av offentliga IP-adresser, integrera dem med en DNS-belastningsutjämning produkt som [Traffic Manager](../traffic-manager/traffic-manager-overview.md) och använda en enda DNS-namn som matchar en klient med flera zonindelad IP-adresser.  Du kan också använda det för att exponera per zon belastningsutjämnade slutpunkter individuellt övervaka varje zon.  Om du vill att blanda dessa koncept (zonredundant och zonindelade för samma serverdel) kan du granska [flera klienter för Azure Load Balancer](load-balancer-multivip-overview.md).

En offentlig Load Balancer-klientdel, lägger du till en *zoner* parametern till den offentliga IP-Adressen som refereras av klientdelens IP-konfiguration.  

För en intern belastningsutjämnare klientdel, lägga till en *zoner* parametern till den interna belastningsutjämnaren klientdelens IP-konfigurationen. Zonindelade klientdelen gör att belastningsutjämnaren ska garanterar en IP-adress i ett undernät till en viss zon.

Använd följande skript för att skapa en zonindelad offentlig IP-adress i tillgänglighet zon 1. Om du använder befintliga Resource Manager-mallar i din konfiguration, lägger du till den **sku** avsnitt för att dessa mallar.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "[resourceGroup().location]",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Använd följande skript för att skapa en intern Standard Load Balancer-klientdel i tillgänglighet zon 1.

Om du använder befintliga Resource Manager-mallar i din konfiguration, lägger du till den **sku** avsnitt för att dessa mallar. Dessutom definiera den **zoner** -egenskapen i klientdelens IP-konfiguration för den underordnade resursen.

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

### <a name="cross-zone-load-balancing"></a>Mellan zoner belastningsutjämning

Mellan zoner belastningsutjämning är möjligheten för Load Balancer att nå en serverdelens slutpunkt i alla zoner och är oberoende av klient- och dess zonality.

Om du vill justera och garantera din distribution inom en enskild zon justera zonindelad klient- och zonindelade serverdelsresurser till samma zon. Ingen ytterligare åtgärd krävs.

### <a name="backend"></a>Serverdel

Belastningsutjämnaren fungerar med virtuella datorer.  Virtuella datorer i ett enskilt virtuellt nätverk kan inte ingå i serverdelspoolen oavsett om det var säkert till en zon eller vilken zon har garanteras.

Om du vill justera och garantera din klient- och serverdelsportarna med samma zon kan du bara placera virtuella datorer i samma zon i respektive serverdelspoolen.

Om du vill att adressen virtuella datorer över flera zoner kan du helt enkelt placera virtuella datorer från flera zoner i samma serverdelspool.  När du använder VM-skalningsuppsättningar, kan du placera en eller flera VM-skalningsuppsättningar i samma serverdelspool.  Och var och en av dessa VM-skalningsuppsättningar kan finnas i en eller flera zoner.

### <a name="outbound-connections"></a>Utgående anslutningar

[Utgående anslutningar](load-balancer-outbound-connections.md) betjänas av alla zoner och är i en region med Azure Availability Zones automatiskt zonredundant när en virtuell dator som är associerad med en offentlig belastningsutjämnare och en zonredundant klientdel.  Utgående anslutning SNAT port allokeringar tåla zon haverier.  

Om den virtuella datorn är associerad med en offentlig belastningsutjämnare och en zonindelad klientdel, garanterat utgående anslutningar i sin tur att hanteras av en enskild zon.  Utgående anslutningar dela öde med respektive zonens hälsa.

SNAT port Förallokering och algoritmen är samma med eller utan zoner.

### <a name="health-probes"></a>Hälsotillståndsavsökningar

Befintliga hälsotillstånd avsökningen definitionerna förblir som de är utan Tillgänglighetszoner.  Men vi har utökat hälsomodellen på infrastrukturnivå. 

När du använder zonredundant utökar klientdelar, belastningsutjämnare sin interna hälsomodellen för att oberoende avsökning Åtkomstmöjligheten i en virtuell dator från varje Tillgänglighetszon och stänga av sökvägar mellan zoner som kan ha misslyckats utan inblandning av kunden.  Om en viss sökväg inte är tillgänglig från belastningsutjämnaren infrastrukturen i en zon till en virtuell dator i en annan zon kan belastningsutjämnare identifiera och undvika det här felet. Andra zoner som kan nå den här virtuella datorn kan fortsätta att hantera den virtuella datorn från deras respektive klienter.  Det är därför under felhändelser, varje zon kan ha något annat flöde distributioner samtidigt som du skyddar den övergripande hälsan för din slutpunkt till slutpunkt-tjänst.

## <a name="design"></a> Designöverväganden

Belastningsutjämnare är ändamålsenligt flexibla i samband med Tillgänglighetszoner. Du kan välja att justera zoner eller du kan välja att vara zonredundant.  Ökad tillgänglighet kan komma till samma pris ökad komplexitet och du måste skapa för tillgänglighet för optimala prestanda.  Låt oss ta en titt på några viktiga designrelaterade aspekter.

### <a name="automatic-zone-redundancy"></a>Automatisk zonredundans

Belastningsutjämnaren gör det enkelt att ha en enda IP-adress som en zonredundant klientdel. En zonredundant IP-adress på ett säkert sätt kan fungera en zonindelade resursen i alla zoner och kan överleva en eller flera zonen fel så länge en zon är felfri för regionen. En zonindelad klientdel är däremot en minskning av tjänsten till en enskild zon och resurser öde med respektive zonen.

Redundans innebär inte hitless datapath eller kontrollplanet;  Det är uttryckligen dataplanet. Zonredundant flöden kan använda alla zoner och flöden för en kund använder alla felfria zoner i en region. Om zonen kraschar påverkas inte trafikflöden med felfri zoner i det här läget i tid.  Trafikflöden med hjälp av en zon vid tidpunkten för felet zon kan påverkas men program kan återställas och dessa flöden kan fortsätta i återstående felfria zonerna i regionen vid återöverföring eller reestablishment när Azure har konvergerats runt zon-fel.

### <a name="xzonedesign"></a> Mellan zon gränser

Det är viktigt att förstå att när som helst tjänstens slutpunkt till slutpunkt korsar zoner kan du dela öde med inte en zon men potentiellt flera zoner.  Därför kanske slutpunkt till slutpunkt-tjänsten inte fått några tillgänglighet över icke-zonindelad distributioner.

Undvika att introducera oönskade mellan zoner beroenden, som kommer upphäver tillgänglighet får när du använder Tillgänglighetszoner.  När ditt program består av flera komponenter och ska vara motståndskraftig mot zon fel, måste du noga till att de räcker viktiga komponenter i händelse av en zon misslyckas.  En enda kritisk komponent för ditt program kan påverka hela programmet om det finns bara i en zon än kvarvarande zonerna.  Dessutom kan också beakta zon återställningen och hur programmet kommer att Konvergera. Nu ska vi gå igenom några viktiga saker och använda dem som inspiration för frågor som du tänka igenom din situation.

- Om ditt program består av två komponenter som en IP-adress och en virtuell dator med hanterade diskar och är garanterade i zon 1 och zon 2, när zon 1 inte din slutpunkt till slutpunkt-tjänst överlever inte när zon 1 misslyckas.  Inte mellan zoner, såvida inte du förstår att du skapar ett potentiellt farliga fel-läge.

- Om ditt program består av två komponenter som en IP-adress och en virtuell dator med hanterad disk, och de garanterat zonredundant och respektive zon 1, din slutpunkt till slutpunkt-tjänst överlever zon fel i zon 2, zon 3, eller båda, såvida inte zon 1 har misslyckats.  Du förlorar emellertid vissa möjligheten att avgöra om hälsotillståndet för din tjänst om du får Åtkomstmöjligheten i klientdelen.  Överväg att utveckla en mer omfattande modell för hälso- och kapacitet.  Du kan använda zonredundant och zonindelade begrepp tillsammans för att expandera insikt och hanterbarhet.

- Om ditt program består av två komponenter som en zonredundant Load Balancer-klientdel och en mellan zoner VM-skalningsuppsättning i tre zoner, dina resurser i zoner som inte berörs av felet kommer att vara tillgängliga men din tjänstkapacitet för slutpunkt till slutpunkt-kan försämras under zon fel. Distributionen kan överleva en eller flera zonen fel från en infrastruktur-perspektiv och detta genererar följande frågor:
  - Har du förstå hur ditt program orsakerna till att om sådana fel och försämrad kapacitet?
  - Behöver du ha skydd i tjänsten framtvingar en redundansväxling till en regionparet vid behov?
  - Hur ska du övervaka, identifiera och minimera sådant scenario? Du kan använda Standard Load Balancer diagnostik för att utöka övervakning av tjänstens slutpunkt till slutpunkt prestanda. Fundera över vad som är tillgängligt och kanske behöver tokenomvandling för en bild.

- Zoner kan göra fel lättare att förstå och som finns.  Zon fel är dock inte skiljer sig från andra fel när det gäller begrepp som tidsgränser, omförsök och backoff algoritmer. Även om Azure Load Balancer ger zonredundant sökvägar och försöker att återställa snabbt, på en paketnivå i realtid, begäranden som skickats eller reestablishments kan uppstå under utbrott av ett fel och det är viktigt att förstå hur ditt program copes med fel. Ett schema för Utjämning av nätverksbelastning överlever, men du måste planera för följande:
  - När en zon inte din slutpunkt till slutpunkt-tjänst förstår detta och om tillståndet är borttappad, hur ska du återställa?
  - När en zon returnerar programmets förstår hur att Konvergera på ett säkert sätt?

### <a name="zonalityguidance"></a> Zonredundant jämfört med zonindelad

Zonredundant kan ange en zon-oberoende och vid samma tid elastiska alternativet med en enda IP-adress för tjänsten.  Det kan minska komplexiteten i sin tur.  Zonredundant också har mobilitetstjänsten i flera zoner och kan användas på ett säkert sätt på resurser i alla zoner.  Det är även framtidssäkrat i regioner utan Tillgänglighetszoner, där du kan begränsa ändringar som krävs när en region få Tillgänglighetszoner.  Konfigurationen syntaxen för en zonredundant IP-adress eller en klientdel lyckas i alla regioner, inklusive de som saknar Tillgänglighetszoner.

Zonindelade kan ge en explicit garanti till en zon dela öde med hälsotillståndet för zonen. Associera en zonindelad IP kan-adress eller zonindelad klientdel för belastningsutjämnare vara attributet önskvärt eller rimlig särskilt om en ansluten resurs är en zonindelad virtuell dator i samma zon.  Eller kanske programmet kräver explicit kunskap om vilken zon finns en resurs i och du vill inte att uttryckligen anledning om tillgänglighet i separata zoner.  Du kan välja att exponera flera zonindelad klienter för en slutpunkt till slutpunkt-tjänst som distribueras i flera zoner (det vill säga per zon zonindelad klientdelar för flera zonindelad virtuell datorskalning anger).  Och om din zonindelad klienter är offentliga IP-adresser, du kan använda dessa flera zonindelad klienter för att visa dina tjänster med [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Du kan också använda flera zonindelad klienter och få per zon hälsotillstånd och prestanda insikter via tredje part övervakningslösningar och exponera den övergripande tjänsten med en zonredundant klientdel. Du bör bara ge zonindelade resurser med zonindelad klienter justeras till samma zon och undvika potentiellt skadliga mellan zoner scenarier för zonindelade resurser.  Zonindelade resurser finns bara i regioner där Availability Zones finns.

Det finns inga allmänna riktlinjer som en är bättre än andra utan att känna till service-arkitektur.

## <a name="limitations"></a>Begränsningar

- När data plan är helt zonredundant (om inte zonindelad garanti har angetts), kontrollplanåtgärder inte fullständigt zonredundant.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Tillgänglighetszoner](../availability-zones/az-overview.md)
- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md)
- Lär dig hur du [belastningsutjämna virtuella datorer inom en zon med hjälp av en Standardbelastningsutjämnare med en zonindelad klientdel](load-balancer-standard-public-zonal-cli.md)
- Lär dig hur du [belastningsutjämna virtuella datorer i flera zoner med hjälp av en Standardbelastningsutjämnare med en zonredundant klientdel](load-balancer-standard-public-zone-redundant-cli.md)
