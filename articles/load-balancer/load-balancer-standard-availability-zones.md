---
title: Standard Azure belastningsutjämnare och tillgänglighet zoner | Microsoft Docs
description: Standard belastningsutjämnare och tillgänglighet zoner
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: kumud
ms.openlocfilehash: f5d46fda6bdb32c1a5000883c6aedb2da15e796a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="standard-load-balancer-and-availability-zones"></a>Standard belastningsutjämnare och tillgänglighet zoner

Har stöd för Azure belastningsutjämnare Standard SKU [tillgänglighet zoner](../availability-zones/az-overview.md) scenarier. Flera nya begrepp är tillgängliga med Standard belastningsutjämnare, vilket gör att du kan optimera tillgänglighet i slutpunkt till slutpunkt-scenario genom att justera resurser med zoner samt distribuera dem i zoner.  Granska [tillgänglighet zoner](../availability-zones/az-overview.md) för anvisningar om tillgänglighet zoner är, vilka regioner som för närvarande stöder tillgänglighet zoner och andra relaterade begrepp och produkter. Tillgänglighet zoner i kombination med Standard belastningsutjämnare är en omfattande och flexibel funktionsuppsättning som kan skapa många olika scenarier.  Granska det här dokumentet för att förstå dessa [begrepp](#concepts) och grundläggande scenario [utforma vägledning](#design).

>[!NOTE]
>Granska [tillgänglighet zoner](https://aka.ms/availabilityzones) för andra relaterade ämnen. 

## <a name="concepts"></a> Tillgänglighet zoner begrepp som används för belastningsutjämnare

Det finns ingen direkt relation mellan belastningsutjämnaren resurser och faktiska infrastruktur. Skapa en belastningsutjämnare skapa inte en instans. Belastningsutjämnaren resurser är objekt inom vilken express hur Azure ska programmet färdiga flera innehavare infrastrukturen för att få det scenario som du vill skapa.  Detta är betydande i samband med tillgänglighet zoner eftersom en enskild resurs för belastningsutjämnaren kan styra programmering av infrastrukturen i flera zoner som tillgänglighet medan en zonredundant tjänsten visas som en resurs från en kund synvinkel.

En belastningsutjämnare resurs funktioner uttrycks som en klientdel, en regel, en hälsoavsökningen och en definition för backend-adresspool.

I samband med tillgänglighet zoner beskrivs beteende och egenskaper för en belastningsutjämnare resurs som zonredundant eller zonal.  Zonredundant och zonal beskriver zonality för en egenskap.  I samband med belastningsutjämnaren zonredundant alltid innebär *alla zoner* och zonal tjänsten för att garantera en *zon*.

Stöd för både offentliga och interna belastningsutjämnare zonredundant och zonal scenarier och båda kan dirigera trafik över zoner vid behov (*mellan zon belastningsutjämning*).

En belastningsutjämnare-resurs är regionala och aldrig zonal.  Och ett VNet och undernät är alltid regionala och aldrig zonal.

### <a name="frontend"></a>Klientdel

En belastningsutjämnare klientdel är en Frontend-IP-konfiguration refererar till en offentlig IP-adressresurs eller en privat IP-adress i undernät för virtuellt nätverk.  Utgör den belastningsutjämnade slutpunkten där tjänsten exponeras.

En belastningsutjämnare resurs kan innehålla både zonal och zonredundant frontends samtidigt. 

När en zon har garanterat en offentlig IP-resurs, är det inte föränderliga med zonality (eller avsaknad av).  Om du vill ändra eller utelämna zonality av en offentlig IP-klientdel, måste du återskapa offentliga IP-Adressen i den aktuella zonen.  

Du kan ändra zonality av en klientdel för en intern belastningsutjämnare genom att ta bort och återskapa klientdelen, ändrar eller tar bort zonality.

När du använder flera frontends, granska [flera frontends för belastningsutjämnaren](load-balancer-multivip-overview.md) för mer information.

#### <a name="zone-redundant-by-default"></a>Zonredundant som standard

I en region med tillgänglighet zoner är en Standard belastningsutjämnaren klientdel zonredundant som standard.  En enda klientdelens IP-adress kan överleva zonen fel och kan användas för att nå alla medlemmar i serverdelen oavsett zonen. Detta innebär inte hitless datasökväg, men alla försök eller reestablishment lyckas. DNS-redundans scheman krävs inte. Den klientdelens IP-adress är samtidigt som betjänar oberoende distributioner i varje zon tillgänglighet.  Zonredundant innebär att alla inkommande eller utgående flöden behandlas alla zoner för tillgänglighet i en region samtidigt med en enda IP-adress.

En eller flera tillgänglighet zoner kan misslyckas och datasökvägen kvarstår så länge som en zon i regionen är felfri. Zonredundant konfigurationen är standard och kräver inga ytterligare åtgärder.  När en region får möjlighet att stödja tillgänglighet zoner, blir en befintlig klientdel zonredundant automatiskt.

Använd följande skript för att skapa en zonredundant offentliga IP-adress för din interna Standard belastningsutjämnaren. Om du använder befintliga Resource Manager-mallar i konfigurationen, lägger du till den **sku** avsnitt till dessa mallar.

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

Använd följande skript för att skapa en zonredundant klientdelens IP-adress för din interna Standard belastningsutjämnaren. Om du använder befintliga Resource Manager-mallar i konfigurationen, lägger du till den **sku** avsnitt till dessa mallar.

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

#### <a name="optional-zone-guarantee"></a>Valfria zonen garanti

Du har en klientdel som garanterar att en zon, som kallas för en *zonal klientdel*.  Det innebär att inkommande eller utgående flöde hanteras av en zon i en region.  Din klientdel delar omvandling med hälsotillståndet för zonen.  Datasökvägen påverkas inte av fel i zoner än där den var garanterad. Du kan använda zonal frontends för att exponera en IP-adress per tillgänglighet zon.  Dessutom du kan använda zonal frontends direkt eller, när klientdelen består av offentliga IP-adresser, integrera dem med en produkt som för DNS-av belastningsutjämning [Traffic Manager](../traffic-manager/traffic-manager-overview.md) och använda en enda DNS-namn som en klient ska matcha flera zonal IP-adresser.  Du kan också använda detta för att exponera per zon belastningsutjämnade slutpunkter individuellt övervaka varje zon.  Om du vill blanda dessa koncept (zonredundant och zonal för samma serverdel) granska [flera frontends för Azure-belastningsutjämnaren](/load-balancer-multivip-overview.md).

För en offentlig belastningsutjämnare klientdel du lägga till en *zoner* parametern till den offentliga IP-Adressen refererar till klientdelens IP-konfiguration.  

För en intern belastningsutjämnare klientdel lägga till en *zoner* parametern till den interna belastningsutjämnaren klientdelens IP-konfigurationen. Zonal klientdel gör belastningsutjämnare att garantera en IP-adress i ett undernät till en viss zon.

Använd följande skript för att skapa en zonal Standard offentlig IP-adress i tillgänglighet zon 1. Om du använder befintliga Resource Manager-mallar i konfigurationen, lägger du till den **sku** avsnitt till dessa mallar.

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

Använd följande skript för att skapa en intern Standard belastningsutjämnaren klientdelen i tillgänglighet zon 1.

Om du använder befintliga Resource Manager-mallar i konfigurationen, lägger du till den **sku** avsnitt till dessa mallar. Dessutom definiera den **zoner** egenskap i klientdelens IP-konfiguration för den underordnade resursen.

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

### <a name="cross-zone-load-balancing"></a>Belastningsutjämning mellan zon

Globala zonen nätverksbelastning är belastningsutjämnare förmåga att nå en backend-slutpunkt i alla zoner och är oberoende av klientdelen och dess zonality.

Om du vill justera och garantera din distribution inom en zon justera zonal klientdel och zonal serverdelsresurser till samma zon. Ingen ytterligare åtgärd krävs.

### <a name="backend"></a>Serverdel

Belastningsutjämnaren fungerar med virtuella datorer.  Någon virtuell dator i en enda virtuella nätverk kan vara en del av serverdelspoolen oavsett om den var garanterad till en zon eller vilken zon har garanteras.

Om du vill justera och garantera din klient- och servergränssnitten med en zon kan du bara placera virtuella datorer i samma zon i respektive serverdelspoolen.

Om du vill adress virtuella datorer över flera zoner du helt enkelt placera virtuella datorer från flera zoner i samma serverdelspoolen.  När du använder virtuella datorn anger placera du en eller flera skalningsuppsättningar i virtuella datorer i samma serverdelspoolen.  Och var och en av dessa skalningsuppsättningar i virtuella datorer kan vara i en enda eller flera zoner.

### <a name="outbound-connections"></a>Utgående anslutningar

[Utgående anslutningar](load-balancer-outbound-connections.md) behandlas alla zoner och är i en region med tillgänglighet zoner automatiskt zonredundant när en virtuell dator som är associerad med en offentlig belastningsutjämnare och en zonredundant klientdel.  Utgående anslutning SNAT port allokeringar klarar zonen fel.  

I sin tur, om den virtuella datorn är associerad med en offentlig belastningsutjämnare och en zonal klientdel är utgående anslutningar garanterat betjänas av en zon.  Utgående anslutningar dela omvandling med respektive zonens hälsa.

SNAT port Förallokering och algoritmen är lika med eller utan zoner.

### <a name="health-probes"></a>Hälsotillståndsavsökningar

Din befintliga hälsa avsökningen definitionerna förblir som de är utan tillgänglighet zoner.  Men vi har utökat hälsomodell på en infrastrukturnivå. 

När du använder zonredundant expanderar frontends belastningsutjämnaren dess interna hälsomodell för att oberoende avsökning av tillgänglighet för en virtuell dator från varje zon för tillgänglighet och stänga av sökvägar i zoner som kan ha misslyckats utan åtgärder från kunden.  Om en angiven sökväg inte är tillgänglig från infrastrukturen för belastningsutjämning för en zon till en virtuell dator i en annan zon belastningsutjämnaren identifiera och lösa det här felet. Andra zoner som kan nå den här virtuella datorn kan fortsätta att hantera den virtuella datorn från deras respektive frontends.  Det är därför under felhändelser, varje zon kan ha lite olika flödet distributioner samtidigt skydda den övergripande hälsan för slutpunkt till slutpunkt-tjänsten.

## <a name="design"></a> Överväganden vid utformning

Belastningsutjämning är ändamålsenligt flexibla i samband med tillgänglighet zoner. Du kan välja att justera till zoner eller kan du vara zonredundant.  Ökad tillgänglighet kan komma till samma pris ökad komplexitet och du måste skapa för tillgänglighet för optimala prestanda.  Låt oss ta en titt på några viktiga överväganden vid utformning av.

### <a name="automatic-zone-redundancy"></a>Automatisk-redundans

Belastningsutjämnaren gör det enkelt att ha en enda IP-adress som en zonredundant klientdel. En zonredundant IP-adress på ett säkert sätt kan hantera en zonal resurs i alla zoner och kan överleva en eller flera fel i zonen så länge en zon är felfri för regionen. Däremot är en zonal klientdel en minskning av tjänsten till en enda zon och resurser omvandling med respektive zon.

Redundans innebär inte hitless datapath eller kontrollplan;  Det är uttryckligen dataplan. Zonredundant flöden kan använda alla zoner och en kund flöden använder alla felfri zoner i en region. Om zonen kraschar påverkas inte trafikflöden använder felfri zoner då i tid.  Trafikflöden med en zon vid tiden för felet zon kan påverkas men program kan återställa och flödena kan fortsätta i de återstående felfria zonerna för regionen återöverföring eller reestablishment när Azure har konvergerats runt zonen felet.

### <a name="xzonedesign"></a> Mellan zon gränser

Det är viktigt att förstå att varje gång en slutpunkt till slutpunkt-tjänst korsar zoner kan du dela omvandling med inte en zon men eventuellt flera zoner.  Därför kan slutpunkt till slutpunkt-tjänsten inte har fått någon tillgänglighet över icke-zonal distributioner.

Undvika att oavsiktligt mellan zonen beroenden som kommer upphäver tillgänglighet får när du använder tillgänglighet zoner.  När ditt program består av flera komponenter och du vill bli motståndskraftiga mot zon fel, måste du vara försiktig så överlevnad tillräckligt viktiga komponenter vid en zon misslyckas.  En enda kritisk komponent för ditt program kan påverka hela programmet om det finns bara i en zon än kvarvarande zonerna.  Dessutom kan också överväga återställningen zon och hur programmet konvergerar. Nu ska vi gå igenom några huvudpunkter och använda dem som inspiration för frågor som du tror via din situation.

- Om ditt program består av två komponenter som en IP-adress och en virtuell dator med hanterad disk, och de är garanterat i zonen 1 och zonen 2 när zonen 1 misslyckas slutpunkt till slutpunkt-tjänsten kommer inte klarar när zonen 1 misslyckas.  Zoner inte går över om du vet att du skapar ett potentiellt farliga fel läge.

- Om ditt program består av två komponenter som en IP-adress och en virtuell dator med hanterad disk, och de är garanterat zonredundant och zon 1 respektive, slutpunkt-till-slutpunkt-tjänsten överlever zonen fel i zonen 2, zonen 3 eller båda om zonen 1 misslyckades.  Men förlorar du vissa möjlighet till orsak om hälsotillståndet för din tjänst om du observerar tillgänglighet för frontend.  Överväg att utveckla en mer omfattande hälsa och kapacitet modell.  Du kan använda koncept zonredundant och zonal tillsammans för att expandera insikt och hanterbarhet.

- Om ditt program består av två komponenter som en zonredundant belastningsutjämnaren klientdel och en zon mellan virtuella datorns skaluppsättning i tre zoner, resurserna i zoner som inte påverkas av fel är tillgänglig men slutpunkt till slutpunkt-tjänsten kan försämras i kapacitet under zonen fel. Distributionen kan överleva fel minst en zon från en infrastruktur-perspektiv och detta genererar följande frågor:
  - Har du förstå hur programmet orsakerna till om dessa fel och försämrad kapacitet?
  - Du behöver skydd i din tjänst att tvinga växla över till en region paret vid behov?
  - Hur ska du övervaka, identifiera och minska sådant scenario? Du kan använda Standard belastningsutjämnaren diagnostik för att utöka övervakning av prestanda för din slutpunkt till slutpunkt-tjänsten. Fundera över vad som är tillgängligt och vad kan behöva förstärkning för en komplett bild.

- Zoner kan göra fel blir det lättare att förstå och finns.  Zonen fel är dock inte annorlunda än andra fel när det gäller begrepp som tidsgränser, återförsök och backoff algoritmer. Även om Azure belastningsutjämnare ger zonen redundanta sökvägar och försöker att återställa snabbt på en paketnivå i realtid, nya sändningar eller reestablishments kan uppstå under utbrott av ett fel och det är viktigt att förstå hur programmet copes med fel. Schemat för belastningsutjämning överlever, men du måste planera för följande:
  - När en zon inte slutpunkt till slutpunkt-tjänsten förstår detta och om tillståndet är förlorade, hur ska du återställa?
  - När en zon återkommer programmet förstår hur att Konvergera på ett säkert sätt?

### <a name="zonalityguidance"></a> Zonredundant jämfört med zonal

Zonredundant kan tillhandahålla en zon-oberoende och på samma tid flexibla alternativ för med en enda IP-adressen för tjänsten.  Det kan minska komplexiteten i sin tur.  Zonredundant också har mobilitetstjänsten över zoner och kan användas på ett säkert sätt på resurser i en zon.  Det är också framtida bevis i regioner utan tillgänglighet zoner, vilket kan begränsa ändringar krävs när en region få tillgänglighet zoner.  Konfigurationen syntaxen för en zonredundant IP-adress eller klientdel lyckas i en region, inklusive de som saknar tillgänglighet zoner.

Zonal kan ge en explicit garanti till en zon dela omvandling med hälsotillståndet för zonen. Associera en zonal IP kan-adress eller zonal belastningsutjämnaren klientdel vara attributet önskvärt eller rimliga särskilt om dina anslutna resursen är en zonal virtuell dator i samma zon.  Eller kanske explicit kunskap om vilken zon finns en resurs i krävs för ditt program och du vill skäl om tillgänglighet i separata zoner explicit.  Du kan välja att exponera flera zonal frontends för en slutpunkt till slutpunkt-tjänst som är fördelade på zoner (det vill säga per zon zonal frontends för flera zonal virtuella skala anger).  Och om din zonal frontends offentliga IP-adresser, kan du använda dessa flera zonal frontends för att visa din tjänst med [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Eller du kan använda flera zonal frontends för att få per zon insikter om hälsa och prestanda via tredje part övervakningslösningar och exponera övergripande tjänsten med zonredundant klientdel. Du bör bara hantera zonal resurser med zonal frontends justeras till samma zon och undvika potentiellt skadligt mellan zon scenarier för zonal resurser.  Det finns endast zonal resurser i regioner där tillgänglighet zoner finns.

Det finns inga allmänna riktlinjer som en är ett bättre alternativ än andra utan att känna till service-arkitektur.

## <a name="limitations"></a>Begränsningar

- När data plan är fullständigt zonredundant (såvida inte zonal garanti har angetts), kontroll plan åtgärder är inte fullständigt zonredundant.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [tillgänglighet zoner](../availability-zones/az-overview.md)
- Lär dig mer om [Standard belastningsutjämnare](load-balancer-standard-overview.md)
- Lär dig hur du [belastningsutjämna virtuella datorer i en zon med en zonal klientdel Standard belastningsutjämnare](load-balancer-standard-public-zonal-cli.md)
- Lär dig hur du [belastningsutjämna virtuella datorer i zoner som Standard belastningsutjämnare med zonredundant klientdel](load-balancer-standard-public-zone-redundant-cli.md)
