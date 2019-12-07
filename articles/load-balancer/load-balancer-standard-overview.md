---
title: Vad är Azure Standard Load Balancer?
titleSuffix: Azure Load Balancer
description: Med den här utbildnings vägen kan du komma igång med en översikt över Azure Standard Load Balancer-funktioner.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: 3b6a16436b2719d1571f5d5a3c16711a9100b75d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894412"
---
# <a name="azure-standard-load-balancer-overview"></a>Översikt över Azure Standard Load Balancer

Med Azure Load Balancer kan du skala dina program och skapa hög tillgänglighet för dina tjänster. Load Balancer kan användas för inkommande och utgående scenarier och ger låg latens, högt data flöde och skalar upp till miljon tals flöden för alla TCP-och UDP-program. 

Den här artikeln fokuserar på Standard Load Balancer.  En mer allmän översikt över Azure Load Balancer finns i [Load Balancer översikt](load-balancer-overview.md) .

## <a name="what-is-standard-load-balancer"></a>Vad är Standard Load Balancer?

Standard Load Balancer är en ny Load Balancer-produkt för alla TCP-och UDP-program med en utökad och mer detaljerad funktions uppsättning med Basic-Load Balancer.  Även om det finns många likheter är det viktigt att bekanta dig med skillnaderna som beskrivs i den här artikeln.

Du kan använda Standard Load Balancer som en offentlig eller intern Load Balancer. Och en virtuell dator kan anslutas till en offentlig och en intern Load Balancer resurs.

Load Balancer resursens funktioner uttrycks alltid som en klient del, en regel, en hälso avsökning och en definition av en server dels pool.  En resurs kan innehålla flera regler. Du kan placera virtuella datorer i backend-poolen genom att ange backend-poolen från den virtuella datorns NIC-resurs.  Den här parametern skickas via nätverks profilen och expanderas när du använder skalnings uppsättningar för virtuella datorer.

En viktig aspekt är omfånget för resursens virtuella nätverk.  När Basic Load Balancer finns inom omfånget för en tillgänglighets uppsättning, är en Standard Load Balancer helt integrerad med omfånget för ett virtuellt nätverk och alla virtuella nätverks koncept gäller.

Load Balancer-resurser är objekt som du kan använda för att uttrycka hur Azure ska program mera infrastruktur för flera innehavare för att uppnå det scenario som du vill skapa.  Det finns ingen direkt relation mellan Load Balancer resurser och faktisk infrastruktur. När du skapar en Load Balancer skapas inte en instans, kapaciteten är alltid tillgänglig och det finns inga fördröjningar att starta eller skala. 

## <a name="why-use-standard-load-balancer"></a>Varför ska jag använda Standard Load Balancer?

Med Standard Load Balancer kan du skala dina program och skapa hög tillgänglighet för småskaliga distributioner till stora och komplexa arkitekturer i flera zoner.

I tabellen nedan hittar du en översikt över skillnaderna mellan Standard Load Balancer och grundläggande Load Balancer:

>[!NOTE]
> Nya designer bör införa Standard Load Balancer. 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Granska [tjänst begränsningar för Load Balancer](https://aka.ms/lblimits), samt [priser](https://aka.ms/lbpricing)och [service avtal](https://aka.ms/lbsla).


### <a name="backend"></a>Backend-pool

Standard Load Balancer backend-pooler expandera till alla virtuella dator resurser i ett virtuellt nätverk.  Den kan innehålla upp till 1000 Server dels instanser.  En server dels instans är en IP-konfiguration, som är en egenskap hos en NIC-resurs.

Backend-poolen kan innehålla fristående virtuella datorer, tillgänglighets uppsättningar eller skalnings uppsättningar för virtuella datorer.  Du kan också blanda resurser i backend-poolen. Du kan kombinera upp till 150 resurser i backend-poolen per Load Balancer resurs.

När du överväger att utforma en backend-pool kan du utforma för det minsta antalet enskilda resurser för Server dels poolen för att ytterligare optimera varaktigheten för hanterings åtgärder.  Det finns ingen skillnad i prestanda eller skalning för data planet.

### <a name="probes"></a>Hälso avsökningar
  
Standard Load Balancer lägger till stöd för [https Health PROBE](load-balancer-custom-probe-overview.md#httpprobe) (http-avsökning med Transport Layer Security (TLS)) för att övervaka dina https-program korrekt.  

Dessutom tillåter Standard Load Balancer att alla upprättade TCP-anslutningar fortsätter när hela Server delen [avavsöks](load-balancer-custom-probe-overview.md#probedown). (Grundläggande Load Balancer avslutar alla TCP-anslutningar till alla instanser).

Granska [Load Balancer hälso avsökningar](load-balancer-custom-probe-overview.md) för mer information.

### <a name="az"></a>Tillgänglighetszoner

>[!IMPORTANT]
>Granska [Tillgänglighetszoner](../availability-zones/az-overview.md) för närliggande ämnen, inklusive information om regioner.

Standard Load Balancer stöder ytterligare funktioner i regioner där Tillgänglighetszoner är tillgängliga.  Dessa funktioner är stegvisa för alla Standard Load Balancer tillhandahåller.  Tillgänglighetszoner konfigurationer är tillgängliga för offentliga och interna Standard Load Balancer.

Icke-zonindelade-frontend-klienter blir zoner-redundanta som standard när de distribueras i en region med Tillgänglighetszoner.   En zon-redundant klient del överleva zon haveri och betjänas av dedikerad infrastruktur i alla zoner samtidigt. 

Dessutom kan du garantera en klient del till en speciell zon. En zonindelade-frontend-resurs avbildar med respektive zon och hanteras endast av dedikerad infrastruktur i en enda zon.

Belastnings utjämning mellan zoner är tillgängligt för backend-poolen och alla virtuella dator resurser i ett VNet kan ingå i en backend-pool.

Granska [detaljerad diskussion om Tillgänglighetszoner relaterade förmågor](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a>Diagnostikprogrammet

Standard Load Balancer ger flerdimensionella mått via Azure Monitor.  Dessa mått kan filtreras, grupperas och delas upp för en specifik dimension.  De tillhandahåller aktuella och historiska insikter om prestanda och hälsa för din tjänst.  Resource Health stöds också.  Nedan följer en kort översikt över vilka diagnostiker som stöds:

| Mått | Beskrivning |
| --- | --- |
| VIP-tillgänglighet | Standard Load Balancer kontinuerligt informerar data Sök vägen från en region till Load Balancer front-end, hela vägen till SDN-stacken som stöder den virtuella datorn. Så länge som felfria instanser är kvar, följer måtten samma sökväg som programmets belastningsutjämnade trafik. Data Sök vägen som används av dina kunder verifieras också. Måttet är osynligt för ditt program och stör inte andra åtgärder.|
| DIP-tillgänglighet | Standard Load Balancer använder en distribuerad hälso deklarations tjänst som övervakar din program slut punkts hälsa enligt dina konfigurations inställningar. Det här måttet innehåller en mängd eller per slut punkt filtrerad – Visa varje enskild instans slut punkt i Load Balancer poolen.  Du kan se hur Load Balancer visar hälso tillståndet för programmet som det anges i konfigurationen för hälso avsökningen.
| SYN paket | Standard Load Balancer avslutar inte TCP-anslutningar eller interagerar med TCP-eller UDP-paketfilter. Flöden och deras hand skakningar är alltid mellan källan och den virtuella dator instansen. För att bättre felsöka dina scenarier med TCP-protokoll kan du använda räknare för SYN paket för att förstå hur många TCP-anslutningsfel som görs. Måttet rapporterar antalet TCP-SYN-paket som tagits emot.|
| SNAT-anslutningar | Standard Load Balancer rapporterar antalet utgående flöden som är maskerade till den offentliga IP-adressen. SNAT-portar är en exhaustible-resurs. Det här måttet kan ge en indikation på hur mycket ditt program förlitar sig på SNAT för utgående, ursprungliga flöden.  Räknare för lyckade och misslyckade utgående SNAT-flöden rapporteras och kan användas för att felsöka och förstå hälso tillståndet för dina utgående flöden.|
| Byte räknare | Standard Load Balancer rapporterar de data som bearbetas per front-end.|
| Paket räknare | Standard Load Balancer rapporterar de paket som bearbetas per klient del.|

Granska [detaljerad diskussion om standard Load Balancer diagnostik](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>HA-portar

Standard Load Balancer stöder en ny typ av regel.  

Du kan konfigurera regler för belastnings utjämning för att göra programmets skalning och vara mycket tillförlitlig. När du använder en belastnings Utjämnings regel för belastnings utjämning ger Standard Load Balancer per flödes belastnings utjämning för varje tillfällig port i en intern Standard Load Balancers IP-adress för klient delen.  Funktionen är användbar för andra scenarier där det är opraktiskt eller olämpligt att ange enskilda portar.

Med belastnings Utjämnings regeln för en belastnings utjämning kan du skapa aktiva, passiva eller aktiva-aktiva n + 1-scenarier för virtuella nätverks enheter och program, vilket kräver stora intervall av inkommande portar.  En hälso avsökning kan användas för att avgöra vilka Server delar som ska ta emot nya flöden.  Du kan använda en nätverks säkerhets grupp för att emulera ett scenario för port intervall.

>[!IMPORTANT]
> Om du planerar att använda en virtuell nätverks installation kontaktar du leverantören för att få vägledning om huruvida produkten har testats med HA-portar och följer de rikt linjer som gäller för implementeringen. 

Granska [detaljerad diskussion om ha-portar](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Skydda som standard

Standard Load Balancer har publicerats fullständigt till det virtuella nätverket.  Det virtuella nätverket är ett privat, stängt nätverk.  Eftersom standard belastnings utjämning och offentliga standard-IP-adresser är utformade för att tillåta att det här virtuella nätverket kan nås från utanför det virtuella nätverket, är dessa resurser som standard stängda om du inte öppnar dem. Det innebär att nätverks säkerhets grupper (NSG: er) nu används för att explicit tillåta och vitlista tillåten trafik.  Du kan skapa hela det virtuella data centret och välja mellan NSG vad och när det ska vara tillgängligt.  Om du inte har en NSG på ett undernät eller ett nätverkskort för den virtuella dator resursen, tillåts inte trafik att komma åt den här resursen.

Mer information om NSG: er och hur du tillämpar dem för ditt scenario finns i [nätverks säkerhets grupper](../virtual-network/security-overview.md).

### <a name="outbound"></a>Utgående anslutningar

Load Balancer stöder inkommande och utgående scenarier.  Standard Load Balancer är avsevärt annorlunda än grundläggande Load Balancer med avseende på utgående anslutningar.

Källan Network Address Translation (SNAT) används för att mappa interna, privata IP-adresser i ditt virtuella nätverk till offentliga IP-adresser på Load Balancer-frontend.

Standard Load Balancer introducerar en ny algoritm för en [mer robust, skalbar och förutsägbar SNAT-algoritm](load-balancer-outbound-connections.md#snat) och aktiverar nya funktioner, tar bort tvetydighet och tvingar fram explicita konfigurationer i stället för sido effekter. De här ändringarna krävs för att nya funktioner ska kunna visas. 

Det här är de viktiga Tenets som du behöver tänka på när du arbetar med Standard Load Balancer:

- När en regel slutförs, bevaras Load Balancer resursen.  all programmering av Azure härleds från konfigurationen.
- När flera klient delar är tillgängliga används alla-frontend och varje klient del multiplicerar antalet tillgängliga SNAT-portar
- Du kan välja och kontrol lera om du inte vill att en viss klient del ska användas för utgående anslutningar.
- utgående scenarier är explicita och utgående anslutning finns inte förrän det har angetts.
- regler för belastnings utjämning härleder hur SNAT programmeras. Belastnings Utjämnings regler är protokoll information. SNAT är protokoll information och konfigurationen bör avspegla detta i stället för att skapa en sido effekt.

#### <a name="multiple-frontends"></a>Flera klienter
Om du vill ha fler SNAT-portar eftersom du förväntar dig eller redan har en hög efter frågan för utgående anslutningar, kan du också lägga till en incrementy-port inventering genom att konfigurera ytterligare klient portar, regler och backend-pooler till samma virtuella dator resurser.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Styr vilken klient del som används för utgående
Om du vill begränsa utgående anslutningar till endast härstammar från en särskild IP-adress för klient delen kan du välja att inaktivera utgående SNAT för regeln som uttrycker utgående mappning.

#### <a name="control-outbound-connectivity"></a>Kontrol lera utgående anslutning
Standard Load Balancer finns inom kontexten för det virtuella nätverket.  Ett virtuellt nätverk är ett isolerat privat nätverk.  Om det inte finns någon Association med en offentlig IP-adress, tillåts inte offentlig anslutning.  Du kan komma åt [VNet-tjänstens slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) eftersom de finns inuti och lokalt i det virtuella nätverket.  Om du vill upprätta utgående anslutning till ett mål utanför det virtuella nätverket har du två alternativ:
- tilldela en offentlig IP-adress för standard-SKU som en offentlig IP-adress på instans nivå till den virtuella dator resursen eller
- Placera den virtuella dator resursen i backend-poolen för en offentlig Standard Load Balancer.

Båda kommer att tillåta utgående anslutningar från det virtuella nätverket till utanför det virtuella nätverket. 

Om du _bara_ har ett internt standard Load Balancer associerat med den backend-pool där din virtuella dator resurs finns, kan den virtuella datorn bara komma åt virtuella nätverks resurser och virtuella nätverks [slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md).  Du kan följa stegen som beskrivs i föregående stycke för att skapa utgående anslutning.

Den utgående anslutningen för en virtuell dator resurs som inte är associerad med standard-SKU: er finns kvar som tidigare.

Granska [detaljerad diskussion om utgående anslutningar](load-balancer-outbound-connections.md).

### <a name="multife"></a>Flera klient delar
Load Balancer stöder flera regler med flera klient delar.  Standard Load Balancer expanderar detta till utgående scenarier.  Utgående scenarier är i stort sett inversen till en inkommande regel för belastnings utjämning.  Den inkommande belastnings Utjämnings regeln skapar även en koppling för utgående anslutningar. Standard Load Balancer använder alla frontend-klienter som är kopplade till en virtuell dator resurs via en belastnings Utjämnings regel.  Dessutom kan du använda en parameter i belastnings Utjämnings regeln och låta dig utelämna en belastnings Utjämnings regel för utgående anslutningar, vilket gör att du kan välja vissa klient delar, inklusive ingen.

För jämförelsen väljer Basic Load Balancer en enskild klient del slumpmässigt och det finns ingen möjlighet att styra vilken som har valts.

Granska [detaljerad diskussion om utgående anslutningar](load-balancer-outbound-connections.md).

### <a name="operations"></a>Hanterings åtgärder

Standard Load Balancer resurser finns på en helt ny infrastruktur plattform.  Detta möjliggör snabbare hanterings åtgärder för standard-SKU: er och slut för ande tider är vanligt vis mindre än 30 sekunder per standard-SKU-resurs.  Eftersom backend-pooler ökar i storlek ökar även den tid som krävs för ändringar i backend-poolen.

Du kan ändra Standard Load Balancer resurser och flytta en offentlig standard-IP-adress från en virtuell dator till en annan mycket snabbare.

## <a name="migration-between-skus"></a>Migrering mellan SKU: er

SKU: er är inte föränderligt. Följ stegen i det här avsnittet om du vill flytta från en resurs-SKU till en annan.

>[!IMPORTANT]
>Granska det här dokumentet i sin helhet för att förstå skillnaderna mellan SKU: er och ha noggrant undersökt ditt scenario.  Du kan behöva göra ytterligare ändringar för att justera ditt scenario.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrera från Basic till standard-SKU

1. Skapa en ny standard resurs (Load Balancer och offentliga IP-adresser om det behövs). Återskapa reglerna och avsöknings definitionerna.  Om du tidigare använde en TCP-avsökning till 443/TCP bör du överväga att ändra detta avsöknings protokoll till en HTTPS-avsökning och lägga till en sökväg.

2. Skapa nya eller uppdatera befintliga NSG på NIC eller undernät för att vitlista belastnings bal anse rad trafik, avsökning, samt all annan trafik som du vill tillåta.

3. Ta bort de grundläggande SKU-resurserna (Load Balancer och offentliga IP-adresser, efter behov) från alla VM-instanser. Se även till att ta bort alla VM-instanser för en tillgänglighets uppsättning.

4. Koppla alla VM-instanser till de nya standard-SKU-resurserna.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrera från standard till Basic SKU

1. Skapa en ny grundläggande resurs (Load Balancer och offentliga IP-adresser om det behövs). Återskapa reglerna och avsöknings definitionerna.  Ändra en HTTPS-avsökning till en TCP-avsökning till 443/TCP. 

2. Ta bort standard-SKU-resurserna (Load Balancer och offentliga IP-adresser, efter behov) från alla VM-instanser. Se även till att ta bort alla VM-instanser för en tillgänglighets uppsättning.

3. Koppla alla VM-instanser till de nya grundläggande SKU-resurserna.

>[!IMPORTANT]
>
>Det finns begränsningar gällande användningen av Basic-och standard-SKU: er.
>
>HA-portar och diagnostik för standard-SKU: n är bara tillgängliga i standard-SKU: n. Du kan inte migrera från standard-SKU: n till den grundläggande SKU: n och även behålla dessa funktioner.
>
>Både Basic-och standard-SKU: n har ett antal skillnader som beskrivs i den här artikeln.  Se till att du förstår och förbereder dem.
>
>Matchande SKU: er måste användas för Load Balancer och offentliga IP-resurser. Du kan inte ha en blandning av grundläggande SKU-resurser och standard-SKU-resurser. Du kan inte bifoga fristående virtuella datorer, virtuella datorer i en tillgänglighetsuppsättningsresurs eller en virtuell dators skalningsuppsättningsresurser till båda SKU:erna samtidigt.

## <a name="region-availability"></a>Tillgänglighet för regioner

Standard Load Balancer är för närvarande tillgänglig i alla offentliga moln regioner.

## <a name="sla"></a>SLA

Standard belastnings utjämning är tillgängliga med ett service avtal på 99,99%.  Se [standard load BALANCER SLA](https://aka.ms/lbsla) för mer information.

## <a name="pricing"></a>Prissättning

Standard Load Balancer-användning debiteras.

- Antalet konfigurerade belastningsutjämningsregler och utgående regler (inkommande NAT-regler räknas inte till det totala antalet regler).
- Mängden inkommande och utgående data som bearbetas oavsett regel. 

Prisinformation om Standard Load Balancer finns på sidan med [Load Balancer-priser](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Begränsningar

- SKU: er är inte föränderligt. Du kan inte ändra SKU: n för en befintlig resurs.
- En fristående virtuell dator resurs, tillgänglighets uppsättnings resurs eller en resurs för skalnings uppsättning för virtuell dator kan referera till en SKU, aldrig båda.
- En Load Balancer regel kan inte omfatta två virtuella nätverk.  Frontend-enheter och deras relaterade Server dels instanser måste finnas i samma virtuella nätverk.  
- [Flytt av prenumerations åtgärder](../azure-resource-manager/resource-group-move-resources.md) stöds inte för standard-SKU: er och pip-resurser.
- Webb arbets roller utan ett virtuellt nätverk och andra Microsofts plattforms tjänster kan nås när bara en intern Standard Load Balancer används på grund av en sido effekt från hur för-VNet-tjänster och andra plattforms tjänster fungerar. Du måste inte förlita dig på detta eftersom själva själva tjänsten eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du behöver skapa [utgående anslutningar](load-balancer-outbound-connections.md) om du vill när du bara använder en intern standard Load Balancer.
- Load Balancer är en TCP- eller UDP-produkt för belastningsutjämning och portvidarebefordran för dessa specifika IP-protokoll.  Belastningsutjämningsregler och inkommande NAT-regler stöds för TCP och UDP och stöds inte för andra IP-protokoll, inklusive ICMP. Load Balancer avslutar inte, svarar inte på eller på annat sätt interagerar med nyttolasten för ett UDP- eller TCP-flöde. Det är inte en proxy. Lyckad verifiering av anslutningen till en klient del måste ske i band med samma protokoll som används i en belastnings utjämning eller en inkommande NAT-regel (TCP eller UDP) _och_ minst en av dina virtuella datorer måste generera ett svar för en klient för att se ett svar från en klient del.  Om du inte tar emot ett in-band-svar från Load Balancer klient delen indikerar det att inga virtuella datorer kunde svara.  Det går inte att interagera med en Load Balancer klient del utan att en virtuell dator kan svara.  Det här gäller även för utgående anslutningar där [portmaskerings-SNAT](load-balancer-outbound-connections.md#snat) bara stöds för TCP och UDP. Alla andra IP-protokoll, inklusive ICMP, kommer att misslyckas.  Tilldela en offentlig IP-adress på instansnivå som åtgärd.
- Till skillnad från offentliga belastningsutjämnare som tillhandahåller [utgående anslutningar](load-balancer-outbound-connections.md) vid över gång från privata IP-adresser i det virtuella nätverket till offentliga IP-adresser, översätter inte interna belastningsutjämnare inte utgående, utgående anslutningar till klient delen av en intern Load Balancer som båda finns i privata IP-adressutrymme.  Detta gör det möjligt att använda SNAT-tömning inuti unika interna IP-adressutrymme där ingen översättning krävs.  Sido effekt är att om ett utgående flöde från en virtuell dator i backend-poolen försöker köra ett flöde till klient delen av den interna Load Balancer i vilken pool den finns _och_ som mappas tillbaka till sig själv, kommer båda benen i flödet inte att matcha och flödet kommer att Miss lyckas.  Om flödet inte mappar tillbaka till samma virtuella dator i backend-poolen som skapade flödet till klient delen, lyckas flödet.   När flödet mappar tillbaka till sig själv visas det utgående flödet som härstammar från den virtuella datorn till klient delen och motsvarande inkommande flöde visas som härstammar från den virtuella datorn till sig själv. Från gästoperativsystemets perspektiv matchar inte de inkommande och utgående delarna av samma flöde i den virtuella datorn. TCP-stacken känner inte igen dessa halvor av samma flöde som en del av samma flöde som källan eftersom källan och målet inte matchar.  När flödet mappar till en annan virtuell dator i backend-poolen, matchar de hälften av flödet och den virtuella datorn kan svara på flödet.  Symptomet för det här scenariot är tillfälligt anslutnings-timeout. Det finns flera vanliga lösningar för att på ett tillförlitligt sätt uppnå det här scenariot (ursprungligt flöde från en backend-pool till backend-poolerna respektive interna Load Balancer klient delen) som innehåller antingen infogning av en tredjeparts-Proxy bakom den interna Load Balancer eller [med hjälp av DSR-stil regler](load-balancer-multivip-overview.md).  Du kan använda en offentlig lastbalanserare som åtgärd men det resulterande scenariot har en fallenhet för [SNAT-överbelastning](load-balancer-outbound-connections.md#snat) och bör undvikas om det inte hanteras noggrant.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att använda [standard Load Balancer och Tillgänglighetszoner](load-balancer-standard-availability-zones.md).
- Läs mer om [hälso avsökningar](load-balancer-custom-probe-overview.md).
- Läs mer om [Tillgänglighetszoner](../availability-zones/az-overview.md).
- Lär dig mer om [standard Load Balancer Diagnostics](load-balancer-standard-diagnostics.md).
- Lär dig mer om [flerdimensionella mått som stöds](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) för diagnostik i [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Läs om hur du använder [belastningsutjämnare för utgående anslutningar](load-balancer-outbound-connections.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md).
- Läs mer om [TCP-återställning vid inaktivitet](load-balancer-tcp-reset.md).
- Lär dig mer om att [standard Load Balancer med belastnings Utjämnings regler för belastnings utjämning](load-balancer-ha-ports-overview.md).
- Lär dig mer om att använda [Load Balancer med flera klient](load-balancer-multivip-overview.md)delar.
- Lär dig mer om [virtuella nätverk](../virtual-network/virtual-networks-overview.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
- Lär dig mer om [tjänst slut punkter för VNet](../virtual-network/virtual-network-service-endpoints-overview.md).
- Lär dig mer om några av de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
- Läs mer om [Load Balancer](load-balancer-overview.md).
