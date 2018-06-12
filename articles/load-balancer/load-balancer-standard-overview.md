---
title: Översikt över Azure Standard belastningsutjämnaren | Microsoft Docs
description: Översikt över Azure Standard belastningsutjämnaren funktioner
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2018
ms.author: kumud
ms.openlocfilehash: 20897137c617ddf9a33a8f4966bcd7e30ac7c60c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261941"
---
# <a name="azure-load-balancer-standard-overview"></a>Översikt över Azure Load Balancer Standard

Azure belastningsutjämnare kan du skala ditt program och skapa hög tillgänglighet för dina tjänster. Belastningsutjämnare kan användas för inkommande samt utgående scenarier och ger hög genomströmning, låg svarstid och skalas upp till miljontals flöden för alla TCP och UDP-program. 

Den här artikeln fokuserar på belastningsutjämnare som Standard.  En mer allmän översikt för Azure-belastningsutjämnaren, granska [översikt över belastningen belastningsutjämnare](load-balancer-overview.md) samt.

## <a name="what-is-standard-load-balancer"></a>Vad är Standard belastningsutjämnaren?

Standard belastningsutjämning är en ny produkt i belastningsutjämnaren för alla TCP och UDP-program med en utökad och mer detaljerade funktioner över grundläggande belastningsutjämnaren.  Det finns många likheter, är det viktigt att bekanta dig med skillnader som beskrivs i den här artikeln.

Du kan använda Standard belastningsutjämnare som en offentlig eller intern belastningsutjämnare. Och en virtuell dator kan vara ansluten till en offentlig och en intern belastningsutjämnare resurs.

Resursen belastningsutjämnaren funktioner uttrycks alltid som en klientdel, en regel, en hälsoavsökningen och en definition för backend-adresspool.  En resurs kan innehålla flera regler. Du kan placera virtuella datorer i serverdelspoolen genom att ange serverdelspoolen från den virtuella datorns nätverkskort resurs.  Den här parametern skickas via nätverksprofilen och expanderas när du använder skalningsuppsättningar i virtuella datorer.

En viktig del är omfånget för det virtuella nätverket för resursen.  Även om grundläggande belastningsutjämnaren finns inom omfånget för en tillgänglighetsuppsättning, en Standard belastningsutjämnare är helt integrerat med omfånget för ett virtuellt nätverk och alla virtuella nätverkskoncept gäller.

Läs in belastningsutjämnaren resurser är objekt inom vilken express hur Azure ska programmet sin infrastruktur för flera innehavare för att få det scenario som du vill skapa.  Det finns ingen direkt relation mellan belastningsutjämnaren resurser och faktiska infrastruktur. Skapa en belastningsutjämnare inte skapa en instans, kapacitet är alltid tillgängligt och det finns ingen start- eller skalning fördröjningar att tänka på. 

>[!NOTE]
> Azure tillhandahåller en uppsättning helt hanterad lösningar för dina scenarier för belastningsutjämning.  Om du behöver för TLS-avslutning (”SSL avlastning”) eller per HTTP/HTTPS-begäran application layer bearbetning, granska [Programgateway](../application-gateway/application-gateway-introduction.md).  Om du behöver för globala DNS belastningsutjämning, granska [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Slutpunkt till slutpunkt-scenarier kan dra nytta av att kombinera dessa lösningar efter behov.

## <a name="why-use-standard-load-balancer"></a>Varför använda Standard belastningsutjämnaren?

Med Standard Load Balancer kan du skala dina program och skapa hög tillgänglighet för småskaliga distributioner till stora och komplexa arkitekturer i flera zoner.

Granska tabellen nedan ger en översikt över skillnaderna mellan Standard belastningsutjämnare och grundläggande belastningsutjämnare:

>[!NOTE]
> Nya Designer bör använda Standard belastningsutjämnaren. 

| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| Storlek för backend-pool | upp till 1 000 instanser | upp till 100 instanser |
| Backend-pool-slutpunkter | En virtuell dator i ett enda virtuellt nätverk, inklusive blandning av virtuella datorer, tillgänglighetsuppsättningar virtuella datorn anger. | virtuella datorer i en enda tillgänglighet eller en virtuell dator skala |
| Tillgänglighetszoner | Zonredundant och zonal frontends för inkommande och utgående, utgående flöden mappningar klara zonen fel, cross-zon belastningsutjämning | / |
| Diagnostik | Övervakare för Azure flerdimensionella mått, t.ex. byte och paket räknare, hälsa avsökning status, anslutningsförsök (TCP SYN), utgående anslutningshälsa (SNAT lyckade och misslyckade flöden), aktiva data plan mått | Azure Log Analytics för offentliga belastningsutjämnare, SNAT uttömning avisering, backend poolen hälsa antal |
| Hög tillgänglighet portar | Interna belastningsutjämnare | / |
| Som standard | standard stängd för offentliga IP- och belastningsutjämnare slutpunkter och en nätverkssäkerhetsgrupp måste användas för att explicit godkända för trafiken flöda | standard öppen nätverkssäkerhetsgruppen valfria |
| Utgående anslutningar | Flera frontends med per regel CEIP. Ett scenario för utgående _måste_ skapas explicit för den virtuella datorn för att kunna använda utgående anslutning.  [VNet Tjänsteslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) kan nås utan utgående anslutning och räknas inte mot data som bearbetas.  Alla offentliga IP-adresser, inklusive Azure PaaS tjänster inte är tillgängliga som VNet Tjänsteslutpunkter måste uppnås via utgående anslutning och antal mot data som bearbetas. När bara en intern belastningsutjämnare används av en virtuell dator, är utgående anslutningar via standard SNAT inte tillgängliga. Utgående SNAT programmering är transportprotokollet specifika baserat på protokollet för inkommande regel för belastningsutjämning. | Enskild klientdel slumpmässigt valda när det finns flera frontends.  När endast interna belastningsutjämnare används av en virtuell dator, används standardvärdet SNAT. |
| Flera klienter | Inkommande och utgående | Endast inkommande |
| Hanteringsåtgärder | De flesta åtgärder < 30 sekunder | 60-90 sekunder vanliga |
| SLA | 99,99% för datasökväg med två felfri virtuella datorer | Implicit i VM SLA | 
| Prissättning | Debiteras baserat på antalet regler bearbetade data inkommande eller utgående som hör till resursen  | Utan kostnad |

Granska [tjänsten gränser för belastningsutjämnaren](https://aka.ms/lblimits), samt [priser](https://aka.ms/lbpricing), och [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Serverdelspool

Standard belastningsutjämnaren serverdelspooler utökas till virtuella resurser i ett virtuellt nätverk.  Den kan innehålla upp till 1000 backend-instanser.  En backend-instansen är en IP-konfiguration, vilket är en egenskap för en NIC-resurs.

Serverdelspoolen kan innehålla tillgänglighetsuppsättningar, fristående virtuella datorer eller virtuella datorer.  Du kan också blanda resurser i serverdelspoolen. Du kan kombinera upp till 150 resurser i serverdelspoolen per resurs för belastningsutjämnaren.

När du överväger hur du utformar din serverdelspool, du kan utforma minst antal enskilda backend resurser för att ytterligare optimera varaktigheten för hanteringsåtgärder.  Det finns ingen skillnad i prestanda för data-plan eller skala.

## <a name="az"></a>Tillgänglighet zoner

Standard belastningsutjämnaren stöder ytterligare funktioner i regioner där tillgänglighet zoner är tillgängliga.  Dessa funktioner är inkrementell till alla belastningsutjämnare som Standard innehåller.  Tillgänglighet zoner konfigurationer är tillgängliga för offentliga och interna Standard belastningsutjämnaren.

Icke-zonal frontends bli zonredundant som standard när de distribueras i en region med tillgänglighet zoner.   Zonredundant klientdel FRISKRIVNINGEN zon fel och hanteras av dedikerad infrastruktur i alla zoner samtidigt. 

Dessutom kan du garantera en klientdel till en viss zon. En zonal klientdel delar omvandling med respektive zon och hanteras endast av dedikerad infrastruktur i en zon.

Globala zonen belastningsutjämning är tillgänglig för serverdelspoolen och virtuella resurser i ett virtuellt nätverk kan vara en del av en serverdelspool.

Granska [detaljerad beskrivning av tillgänglighet zoner relaterade förmågor](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> diagnostik

Standard belastningsutjämnare ger flerdimensionella mätvärden via Azure-Monitor.  De här måtten kan filtreras, grupperas och delats upp för en viss dimension.  De ger aktuella och historiska insikter om prestanda och hälsotillståndet för din tjänst.  Resource Health stöds också.  Nedan följer en kort översikt över stöds diagnostik:

| Mått | Beskrivning |
| --- | --- |
| VIP-tillgänglighet | Load Balancer Standard utför kontinuerligt datasökväg från inom en region till belastningsutjämnaren frontend ända till SDN stacken som har stöd för den virtuella datorn. Så länge felfri instanser förblir följer mätningen samma sökväg som ditt program belastningsutjämnad trafik. Datasökväg som används av dina kunder också valideras. Måttet är osynliga för ditt program och störa inte andra åtgärder.|
| DIP tillgänglighet | Läsa in Standard belastningsutjämnare använder en distribuerad hälsa avsökning av tjänst som övervakar dina program endpoint hälsa enligt inställningarna. Det här måttet ger en aggregering eller per slutpunkt filtrerad vy för varje enskild instans slutpunkt i belastningsutjämnaren poolen.  Du kan se hur belastningsutjämnaren visar hälsotillståndet för programmet som anges av din konfiguration för avsökning av hälsotillstånd.
| SYN-paket | Load Balancer Standard inte avsluta TCP-anslutningar och interagera med TCP eller UDP-paket-flöden. Flöden och deras handskakningar på är alltid mellan käll- och VM-instans. Om du vill felsöka bättre scenariet TCP-protokollet, kan du använda SYN paket räknare för att förstå hur många TCP-anslutning görs försök. Måttet rapporterar antalet TCP SYN-paket som tagits emot.|
| SNAT anslutningar | Load Balancer Standard rapporterar antalet utgående flöden masqueraded frontend offentliga IP-adress. SNAT portar är en icke förnybara resurs. Det här måttet kan ge en indikation på hur mycket tillämpningsprogrammet förlitar sig på SNAT för utgående flöden som har sitt ursprung.  Räknare för lyckade och misslyckade utgående SNAT flöden rapporteras och kan användas för att felsöka och förstå hälsotillståndet för din utgående flöden.|
| Räknare för byte | Load Balancer Standard rapporterar data bearbetas per frontend.|
| Paketet räknare | Load Balancer Standard rapporterar de paket som bearbetas per frontend.|

Granska [detaljerad beskrivning av Standard belastningen belastningsutjämnaren diagnostik](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Hög tillgänglighet portar

Standard belastningsutjämnaren stöder en ny typ av regel.  

Du kan konfigurera regler för att göra skala ditt program och mycket pålitlig för belastningsutjämning. När du använder en hög tillgänglighet portar belastningsutjämningsregeln, Standard belastningsutjämnare ger per flöde för belastningsutjämning på varje tillfälliga portar för en intern Standard Belastningsutjämnares klientdelens IP-adress.  Funktionen kan användas för andra scenarier där det är opraktiska eller önskvärt att ange enskilda portar.

En regel för hög tillgänglighet portar av belastningsutjämning kan du skapa aktivt-passivt eller aktivt-aktivt n + 1 scenarier för nätverk virtuella installationer och alla program som kräver stora intervall med ingående portar.  En hälsoavsökningen kan användas för att avgöra vilka serverdelar ska kunna ta emot nya flöden.  Du kan använda en Nätverkssäkerhetsgrupp för att emulera en port intervallet scenario.

>[!IMPORTANT]
> Om du planerar att använda en virtuell nätverksenhet, kontrollera med leverantören om hjälp om sina produkter har testats med hög tillgänglighet portar och följer deras specifika riktlinjer för implementering. 

Granska [detaljerad beskrivning av hög tillgänglighet portar](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Som standard

Standard belastningsutjämnare är helt publicerats så att det virtuella nätverket.  Det virtuella nätverket är ett privat, stängd nätverk.  Eftersom belastningsutjämnare som Standard och offentliga IP-adresser har utformats för att det här virtuella nätverket som kan nås från utanför det virtuella nätverket, resurserna nu som standard om du öppnar dem. Detta innebär Nätverkssäkerhetsgrupper (NSG: er) används nu för att tillåta explicit och godkända tillåts trafik.  Du kan skapa hela virtuella datacentret och bestäm via NSG vad och när den ska vara tillgänglig.  Om du inte har en NSG till ett undernät eller nätverkskort på den virtuella datorresursen tillåts trafiken inte att nå den här resursen.

Mer information om NSG: er och hur de ska användas för ditt scenario finns [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

### <a name="outbound"></a> Utgående anslutningar

Belastningsutjämnaren stöder inkommande och utgående scenarier.  Standard belastningsutjämnaren skiljer sig avsevärt för grundläggande belastningsutjämnaren med avseende på utgående anslutningar.

Källan Network Address Translation (SNAT) används för att mappa interna, privata IP-adresser på det virtuella nätverket till den offentliga IP-adresser på belastningsutjämnaren frontends.

Standard belastningsutjämnaren introducerar en ny algoritm för en [robust, skalbara och förutsägbar SNAT algoritmen](load-balancer-outbound-connections.md#snat) och möjliggör nya funktioner och tar bort tvetydighet framtvingar explicit konfigurationer i stället sida effekter. Dessa ändringar är nödvändigt att tillåta att nya funktioner för att se ett mönster. 

Dessa är viktiga tenets komma ihåg när du arbetar med Standard belastningsutjämnare:

- slutförandet av en regel styr resursen belastningsutjämnaren.  alla Azure-programmering härleds från dess konfiguration.
- När flera frontends, alla frontends används och varje klientdel multiplicerar antalet tillgängliga portar för SNAT
- Du kan välja och styra om du inte vill att för en viss klientdel som ska användas för utgående anslutningar.
- utgående scenarier är explicit och utgående anslutning finns inte förrän det har angetts.
- belastningsutjämningsregler härleda hur SNAT programmerad. Belastningsutjämningsregler är protokoll. SNAT är protokoll och konfiguration bör återspegla det i stället för att skapa en sidoeffekt.

#### <a name="multiple-frontends"></a>Flera klienter
Om du vill fler SNAT portar eftersom du förväntar dig eller inte redan har en hög belastning för utgående anslutningar, du kan också lägga till inkrementella SNAT port inventering genom att konfigurera ytterligare frontends, regler och serverdelspooler till samma virtuella dator resurser.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Kontrollera vilka frontend används för utgående
Om du vill begränsa utgående anslutningar till endast kommer från en specifik klientdelens IP-adress kan du om du vill inaktivera utgående SNAT på den regel som representerar utgående mappningen.

#### <a name="control-outbound-connectivity"></a>Kontrollen utgående anslutning
Standard belastningsutjämnaren finns inom kontexten för det virtuella nätverket.  Ett virtuellt nätverk är ett isolerat privat nätverk.  Om det inte finns en koppling med en offentlig IP-adress, är anslutning för offentliga inte tillåtet.  Du kan nå [VNet Tjänsteslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) eftersom de används för och lokala till det virtuella nätverket.  Om du vill upprätta en utgående anslutning till en plats utanför det virtuella nätverket har två alternativ:
- tilldela en offentlig IP-adress för Standard-SKU som en instansnivå offentliga IP-adress till den virtuella datorresursen eller
- Placera den virtuella datorresursen i serverdelspoolen av en offentlig Standard belastningsutjämnare.

Båda tillåter utgående anslutning från det virtuella nätverket till utanför det virtuella nätverket. 

Om du _endast_ har en intern Standard belastningsutjämnare som är kopplade till serverdelspoolen som din virtuella resursen finns, den virtuella datorn kan endast nå nätverksresurser på virtuella datorer och [VNet Service Slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md).  Du kan följa stegen som beskrivs i föregående stycke för att skapa utgående anslutning.

Utgående anslutning för en resurs för virtuell dator som inte är associerade med Standard-SKU: er förblir som innan.

Granska [detaljerad beskrivning av utgående anslutningar](load-balancer-outbound-connections.md).

### <a name="multife"></a>Flera frontends
Belastningsutjämnaren har stöd för flera regler med flera frontends.  Standard belastningsutjämnaren expanderar det utgående scenarier.  Utgående scenarier är i stort sett inversen av en inkommande regel för belastningsutjämning.  Den inkommande regel för belastningsutjämning även skapar en kollega för utgående anslutningar. Standard belastningsutjämnare använder alla frontends som är kopplad till en virtuell datorresurs via en regel för belastningsutjämning.  Dessutom en parameter på belastningsutjämning regel och gör att du kan ignorera en regel för en belastningsutjämning för utgående anslutningar som du kan välja specifika frontends inklusive ingen.

Grundläggande belastningsutjämnaren väljer en enda klientdel slumpmässigt för jämförelse, och det finns ingen möjlighet att styra vilken har valts.

Granska [detaljerad beskrivning av utgående anslutningar](load-balancer-outbound-connections.md).

### <a name="operations"></a> Hanteringsåtgärder

Standard belastningsutjämnaren resurserna finns på en helt ny infrastruktursplattform.  Detta möjliggör betydligt snabbare hanteringsåtgärder för Standard-SKU: er och färdiga är vanligtvis mindre än 30 sekunder per resurs för Standard-SKU.  Observera att när serverdelspooler ökar i storlek, varaktighet som krävs för serverdel poolen ändras även öka.

Du kan ändra Standard belastningsutjämnaren resurser och flytta Standard offentlig IP-adress från en virtuell dator till en annan snabbare.

## <a name="migration-between-skus"></a>Migrering mellan SKU: er

SKU: er är inte föränderliga. Följ stegen i det här avsnittet för att flytta från en resurs SKU till en annan.

>[!IMPORTANT]
>Granska det här dokumentet i sin helhet att förstå skillnaderna mellan SKU: er och noggrant har undersökt ditt scenario.  Du kan behöva göra fler ändringar kan anpassa ditt scenario.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrera från enkel till Standard-SKU

1. Skapa en ny Standard resurs (belastningsutjämning och offentliga IP-adresser, vid behov). Skapa dina regler och avsökning definitioner.

2. Skapa en ny eller uppdatera befintliga NSG på nätverkskortet eller undernät i listan över godkända belastningsutjämnade trafik, avsökningen samt all annan trafik som du vill tillåta.

3. Ta bort grundläggande SKU-resurser (belastningsutjämning och offentliga IP-adresser, i tillämpliga fall) från alla VM-instanser. Se till att även ta bort alla VM-instanser i tillgänglighetsuppsättningen.

4. Bifoga alla VM-instanser till de nya Standard-SKU-resurserna.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrera från Standard till grundläggande SKU

1. Skapa en ny grundläggande resurs (belastningsutjämning och offentliga IP-adresser, vid behov). Skapa dina regler och avsökning definitioner. 

2. Ta bort Standard-SKU-resurser (belastningsutjämning och offentliga IP-adresser, i tillämpliga fall) från alla VM-instanser. Se till att även ta bort alla VM-instanser i tillgänglighetsuppsättningen.

3. Bifoga alla VM-instanser till de nya grundläggande SKU-resurserna.

>[!IMPORTANT]
>
>Det finns begränsningar avseende användning av Basic och Standard-SKU: er.
>
>Hög tillgänglighet är portar och diagnostik för Standard-SKU bara tillgängliga i Standard-SKU. Du kan inte migrera från Standard-SKU till grundläggande SKU: N och behåller även dessa funktioner.
>
>Både Basic och Standard-SKU har många skillnader som beskrivs i den här artikeln.  Kontrollera att du förstår och förbereda dem.
>
>Matchar SKU: er måste användas för belastningsutjämnaren och offentliga IP-resurser. Du kan inte ha en blandning av grundläggande SKU-resurser och Standard-SKU-resurser. Du kan inte bifoga fristående virtuella datorer, virtuella datorer i en tillgänglighetsuppsättningsresurs eller en virtuell dators skalningsuppsättningsresurser till båda SKU:erna samtidigt.

## <a name="region-availability"></a>Regional tillgänglighet

Load Balancer Standard finns för närvarande i alla regioner för offentliga moln.

## <a name="sla"></a>SLA

Standard belastningsutjämnare är tillgängliga med en SLA med 99,99%.  Granska de [Standard belastningen belastningsutjämnaren SLA](https://aka.ms/lbsla) mer information.

## <a name="pricing"></a>Prissättning

Standard belastningsutjämnare är en produkt som debiteras baserat på antalet belastningsutjämningsregler konfigurerad och alla inkommande och utgående data bearbetas. Standard belastningsutjämnaren information om priser finns i [belastningen belastningsutjämnaren priser](https://aka.ms/lbpricing) sidan.

## <a name="limitations"></a>Begränsningar

- SKU: er är inte föränderliga. Du kan inte ändra SKU av en befintlig resurs.
- En fristående virtuell datorresurs, tillgänglighetsuppsättning resurs eller virtuell scale set datorresurs kan referera till en SKU aldrig båda.
- En regel för belastningsutjämnare får inte omfatta två virtuella nätverk.  Frontends och deras relaterade backend-instanser måste finnas i samma virtuella nätverk.  
- Load Balancer frontends är inte tillgängliga över peering globala virtuella nätverk.
- [Flytta prenumeration operations](../azure-resource-manager/resource-group-move-resources.md) stöds inte för Standard SKU LB och PIP resurser.
- Web arbetsroller utan ett VNet och andra Microsoft-tjänster för plattformen kan vara tillgänglig när bara en intern Standard belastningsutjämnare används på grund av en sidoeffekt från hur pre-VNet-tjänster och andra platform services-funktionen. Du måste inte förlita dig på den här som respektive tjänst sig själv eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid förutsätter att du behöver skapa [utgående anslutning](load-balancer-outbound-connections.md) uttryckligen om du vill när du använder en intern Standard belastningsutjämnare endast.
- Belastningsutjämning är en TCP- eller UDP-produkt för belastningsutjämning och vidarebefordrade portar för dessa specifika IP-protokoll.  Regler för belastningsutjämning och inkommande NAT-regler som stöds för TCP och UDP- och stöds inte för andra IP-protokoll inklusive ICMP. Belastningsutjämnaren inte avslutas, svara eller annars interagera med nyttolasten för ett UDP eller TCP-flöde. Det är inte en proxy. Lyckad validering av anslutning till en frontend måste vidta plats i band med samma protokoll som används i en belastningsutjämning eller ingående NAT-regel (TCP eller UDP) _och_ minst en av dina virtuella datorer måste generera ett svar för en klient för att se ett svar från en frontend.  Inte får en in-band-svar från belastningsutjämnaren frontend anger inga virtuella datorer kan svara.  Det går inte att interagera med en belastningsutjämnare frontend utan en virtuell dator kan svara.  Detta gäller även för utgående anslutningar där [port maskerade SNAT](load-balancer-outbound-connections.md#snat) är stöds endast för TCP och UDP, några andra inklusive ICMP IP-protokoll fungerar inte.  Tilldela en instansnivå offentliga IP-adress för att minimera.
- Till skillnad från offentliga belastningsutjämnare som innehåller [utgående anslutningar](load-balancer-outbound-connections.md) vid övergång från privata IP-adresser i det virtuella nätverket till den offentliga IP-adresser, interna belastningsutjämnare inte översätta utgående ursprung anslutningar till klientdelen av en intern belastningsutjämnare som båda finns i privata IP-adressutrymme.  På så sätt undviker du risken för SNAT uttömning i unika interna IP-adressutrymmet som där översättning inte krävs.  Bieffekten är att om ett utgående flöde från en virtuell dator i backend-poolen försöker ett flöde till frontend för intern belastningsutjämnare i vilka pool finns _och_ mappas till sig själv, både ben i flöde matchar inte och flödet misslyckas .  Om flödet inte mappade till samma virtuella dator i backend-poolen som flödar till frontend har skapat, lyckas flödet.   När flödet mappar till sig själv utgående flödet verkar kommer från den virtuella datorn till frontend och motsvarande inkommande flödet visas som kommer från den virtuella datorn till sig själv. Ur gäst-OS matchar inte de inkommande och utgående delarna i samma flöde inuti den virtuella datorn. TCP-stacken känner inte igen dessa halvorna i samma flöde som en del av samma flöde som källa och mål inte matchar.  När flödet mappar till till andra Virtuella i backend-poolen, hälften av flödet matchar och den virtuella datorn har kan svara på flödet.  Symtom för det här scenariot är tillfälligt timeout. Det finns flera vanliga lösningar för på ett tillförlitligt sätt att uppnå det här scenariot (ursprung flödar från en backend-pool till backend-adresspooler respektive interna belastningsutjämnare frontend) som innehåller antingen infogning av en tredjeparts-proxy bakom interna belastningen Belastningsutjämnare eller [med DSR formatmallsregler](load-balancer-multivip-overview.md).  När en offentlig belastningsutjämnare kan användas för att minska, det resulterande scenariot är enkelt att [SNAT resursuttömning](load-balancer-outbound-connections.md#snat) och bör inte användas om inte hanteras noggrant.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du använder [Standard belastningsutjämnare och tillgänglighet zoner](load-balancer-standard-availability-zones.md)
- Lär dig mer om [tillgänglighet zoner](../availability-zones/az-overview.md).
- Lär dig mer om [standardbelastningen belastningsutjämnaren diagnostik](load-balancer-standard-diagnostics.md).
- Lär dig mer om [stöd för flerdimensionella mått](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) för diagnostik i [Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Lär dig mer om hur du använder [belastningsutjämnaren för utgående anslutningar](load-balancer-outbound-connections.md)
- Lär dig mer om [Standard belastningsutjämnare med hög tillgänglighet portar regler för belastningsutjämning](load-balancer-ha-ports-overview.md)
- Lär dig mer om hur du använder [belastningsutjämnaren med flera Frontends](load-balancer-multivip-overview.md)
- Lär dig mer om [virtuella nätverk](../virtual-network/virtual-networks-overview.md).
- Lär dig mer om [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).
- Lär dig mer om [VNet-tjänstens slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md)
- Lär dig mer om den andra nyckeln [nätverk](../networking/networking-overview.md) i Azure.
- Lär dig mer om [belastningsutjämnaren](load-balancer-overview.md).
