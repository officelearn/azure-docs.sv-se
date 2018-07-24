---
title: Översikt över Azure Standard Load Balancer | Microsoft Docs
description: Översikt över Azure Standard Load Balancer-funktioner
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
ms.date: 07/20/2018
ms.author: kumud
ms.openlocfilehash: 1a7f37d3f95701779a16cf5dc6844fb67ee7f956
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215109"
---
# <a name="azure-load-balancer-standard-overview"></a>Översikt över Azure Load Balancer Standard

Azure Load Balancer kan du skala dina program och skapa hög tillgänglighet för dina tjänster. Belastningsutjämnare kan användas för inkommande samt utgående scenarier och innehåller med låg fördröjning och högt dataflöde och kan skalas upp till miljontals flöden för alla TCP och UDP-tillämpningar. 

Den här artikeln fokuserar på Standard Load Balancer.  En mer allmän översikt för Azure Load Balancer, granska [översikt över Standardbelastningsutjämnare](load-balancer-overview.md) samt.

## <a name="what-is-standard-load-balancer"></a>Vad är Standard Load Balancer?

Standard Load Balancer är en ny produkt i belastningsutjämnaren för alla TCP och UDP-program med en utökad och mer detaljerade funktioner över belastningsutjämnare.  Det finns många likheter, är det viktigt att bekanta dig med skillnader som beskrivs i den här artikeln.

Du kan använda Standard Load Balancer som en offentlig eller intern belastningsutjämnare. Och en virtuell dator kan anslutas till en offentlig och en resurs för interna belastningsutjämnare.

Belastningsutjämnaren resursfunktioner uttrycks alltid som en klientdel, en regel, en hälsoavsökning och en definition för backend-poolen.  En resurs kan innehålla flera regler. Du kan placera virtuella datorer i serverdelspoolen genom att ange serverdelspoolen från den virtuella datorns nätverkskort resurs.  Den här parametern skickas via nätverksprofilen och expanderas när du använder VM-skalningsuppsättningar.

En viktig aspekt är omfånget för det virtuella nätverket för resursen.  Basic Load Balancer finns inom omfånget för en tillgänglighetsuppsättning, en Standard Load Balancer är helt integrerat med omfånget för ett virtuellt nätverk och alla virtual network-koncept gäller.

Resurser för belastningsutjämning är objekt som du kan uttrycka hur Azure bör programmet sin infrastruktur för flera klienter att uppnå det scenario som du vill skapa.  Det finns ingen direkt relation mellan belastningshanterare och faktiska infrastruktur. Skapa en belastningsutjämnare skapar inte en instans, kapacitet är alltid tillgänglig och det finns ingen start- eller skalning fördröjningar att tänka på. 

>[!NOTE]
> Azure tillhandahåller en uppsättning fullständigt hanterade lösningar för dina scenarier för belastningsutjämning.  Om du behöver för TLS-avslutning (”SSL-avlastning”) eller per HTTP/HTTPS-begäran application layer bearbetning, granska [Application Gateway](../application-gateway/application-gateway-introduction.md).  Om du behöver för globala DNS belastningsutjämning, granska [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Slutpunkt till slutpunkt-scenarier kan dra nytta av att kombinera dessa lösningar efter behov.

## <a name="why-use-standard-load-balancer"></a>Varför använda Standard Load Balancer?

Med Standard Load Balancer kan du skala dina program och skapa hög tillgänglighet för småskaliga distributioner till stora och komplexa arkitekturer i flera zoner.

Granska i tabellen nedan för en översikt över skillnaderna mellan Standard Load Balancer och grundläggande belastningsutjämnare:

>[!NOTE]
> Ny utformning intar Standard Load Balancer. 

| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| Backend-pool-storlek | upp till 1000 instanser | upp till 100 instanser |
| Slutpunkter för backend-pool | alla virtuella datorer i ett enda virtuellt nätverk, inklusive blandning av virtuella datorer, tillgänglighetsuppsättningar, VM-skalningsuppsättningar. | virtuella datorer i en enda tillgänglighet eller en virtuell dator skala |
| Tillgänglighetszoner | zonredundant och zonindelade klienter för inkommande och utgående, utgående flöden mappningar överleva zon fel, belastningsutjämning mellan zoner | / |
| Diagnostik | Azure Monitor flerdimensionella mått, till exempel byte och paket räknare, hälsa avsökning status, anslutningsförsök (TCP SYN), utgående anslutning health (SNAT lyckade och misslyckade flöden), aktiva data plan mätning av faktisk användning | Azure Log Analytics för offentlig belastningsutjämnare, SNAT överbelastning avisering, backend-pool hälsotillstånd antal |
| Hög tillgänglighet portar | den interna belastningsutjämnaren | / |
| Säker som standard | standard stängd för offentliga IP-adress och belastningsutjämnare slutpunkter och en nätverkssäkerhetsgrupp måste användas för att uttryckligen lista över tillåtna för trafik kan flöda | standard öppen nätverkssäkerhetsgrupp valfritt |
| [Utgående anslutningar](load-balancer-outbound-connections.md) | Flera klienter med per avanmälan av regeln för belastningsutjämning. Ett scenario för utgående _måste_ uttryckligen skapas för den virtuella datorn för att kunna använda utgående anslutning.  [VNet-tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) kan nås utan utgående anslutning och räknas inte mot data som bearbetas.  Alla offentliga IP-adresser, inklusive Azure PaaS-tjänster som är inte tillgängligt som VNet-tjänstslutpunkter, måste uppnås via utgående anslutning och antal för data som bearbetas. Utgående anslutningar via standard SNAT är inte tillgängliga när bara en intern belastningsutjämnare fungerar som värd för en virtuell dator. Utgående SNAT-programmering är transportprotokollet specifika baserat på protokollet av inkommande regel för belastningsutjämning. | Enkel klientdel, slumpmässigt valda när det finns flera klienter.  När endast den interna belastningsutjämnaren fungerar som värd för en virtuell dator, används standardvärdet SNAT. |
| [Flera klienter](load-balancer-multivip-overview.md) | Inkommande och [utgående](load-balancer-outbound-connections.md) | Endast inkommande |
| [Hälsoavsökning ned beteende](load-balancer-custom-probe-overview.md) | TCP-anslutningarna förblir aktiva på instansen avsökning av __och__ på alla avsökningar av | TCP-anslutningarna förblir aktiva på instansen avsökningen ned. Alla TCP-anslutningar avslutas på alla avsökningar av |
| Hanteringsåtgärder | De flesta åtgärder < 30 sekunder | 60 – 90 sekunder vanliga |
| SLA | 99,99% för sökvägen med två felfria virtuella datorer | Implicit i VM-serviceavtal | 
| Prissättning | Debiteras baserat på antalet regler kan data som bearbetas för inkommande eller utgående som är associerad med resurs  | utan kostnad |

Granska [tjänstbegränsningar för Load Balancer](https://aka.ms/lblimits), samt [priser](https://aka.ms/lbpricing), och [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Serverdelspool

Standard Load Balancer serverdelspooler expanderas till resursen för alla virtuella datorer i ett virtuellt nätverk.  Det kan innehålla upp till 1000 serverdelsinstanser.  En backend-instans är en IP-konfiguration, vilket är en egenskap för en NIC-resurs.

Serverdelspoolen kan innehålla fristående virtuella datorer, tillgänglighetsuppsättningar eller VM-skalningsuppsättningar.  Du kan även blanda resurser i serverdelspoolen. Du kan kombinera upp till 150 resurser i serverdelspoolen per resurs för belastningsutjämnaren.

När du överväger hur du utformar backend-pool, kan du utforma för minsta möjliga antal enskilda backend-poolresurser för att ytterligare optimera varaktigheten för hanteringsåtgärder.  Det finns ingen skillnad i data plan prestanda eller skala.

## <a name="az"></a>Tillgänglighetszoner

Standard Load Balancer stöder ytterligare funktioner i regioner där Availability Zones är tillgängligt.  Dessa funktioner är inkrementell säkerhetskopiering mot alla Standard Load Balancer tillhandahåller.  Tillgänglighetszoner konfigurationer är tillgängliga för offentliga och interna Standard Load Balancer.

Icke-zonindelad klientdelar bli zonredundant som standard när de distribueras i en region med Tillgänglighetszoner.   En zonredundant klientdel överlever nedgångar zon fel och hanteras av dedikerad infrastruktur i alla zoner samtidigt. 

Dessutom kan du garantera en klientdel till en viss zon. En zonindelad klientdel delar öde med respektive zonen och hanteras endast av dedikerad infrastruktur i samma zon.

Belastningsutjämning mellan zoner är tillgänglig för serverdelspoolen och virtuella resurser i ett virtuellt nätverk kan ingå i en serverdelspool.

Granska [detaljerad beskrivning av Tillgänglighetszoner relaterade funktioner](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> Diagnostik

Standard Load Balancer ger flerdimensionella mätvärden via Azure Monitor.  De här måtten kan filtreras, grupperas och specificeras för en viss dimension.  Tillhandahåller de aktuella och historiska insikter om prestanda och hälsa för din tjänst.  Resource Health stöds också.  Nedan följer en kort översikt över stöds diagnostik:

| Mått | Beskrivning |
| --- | --- |
| VIP-tillgänglighet | Load Balancer Standard utför kontinuerligt datasökväg från inom en region till Belastningsutjämnarens klientdel hela vägen till SDN-stacken som har stöd för den virtuella datorn. Så länge felfria instanser förblir följer mätningen samma sökväg som ditt programs belastningsutjämnad trafik. Datasökväg som används av kunderna har även verifierats. Måttet är osynliga för ditt program och störa inte andra åtgärder.|
| Tillgängligheten för DIP | Load Balancer Standard använder en distribuerad hälsotillstånd avsökning tjänst som övervakar din programslutpunkt hälsa enligt dina inställningar. Det här måttet tillhandahåller en aggregering eller per slutpunkt filtrerade-vy av varje enskild instans-slutpunkt i belastningsutjämnaren lagringspoolen.  Du kan se hur belastningsutjämnaren visar hälsotillståndet för programmet som anges av din konfiguration för avsökning av hälsotillstånd.
| SYN-paket | Load Balancer Standard inte avsluta TCP-anslutningar och interagera med flöden för TCP eller UDP-paket. Flöden och deras handskakningar är alltid mellan käll- och VM-instansen. Om du vill felsöka bättre scenariet TCP-protokollet, kan du använda SYN paket räknare att förstå hur många TCP-anslutning försök görs. Måttet rapporterar antalet TCP SYN-paket som tagits emot.|
| SNAT-anslutningar | Load Balancer Standard rapporterar antalet utgående flöden som masqueraded till offentliga IP-adressen frontend. Det finns en icke förnybara resurs SNAT portar. Det här måttet kan ge en indikation på hur mycket programmet förlitar sig på SNAT för utgående trafikflöden.  Räknare för lyckade och misslyckade utgående SNAT flöden rapporteras och kan användas för att felsöka och förstå hälsotillståndet för dina utgående flöden.|
| Räknare för byte | Load Balancer Standard rapporterar data som bearbetas per klient.|
| Paket-räknare | Load Balancer Standard rapporterar de paket som bearbetas per klient.|

Granska [detaljerad beskrivning av diagnostik för Standard Load Balancer](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Hög tillgänglighet portar

Standard Load Balancer har stöd för en ny typ av regel.  

Du kan konfigurera belastningsutjämningsregler för att göra dina program skalas och vara mycket pålitlig. När du använder en HA Ports belastningsutjämningsregel: Standard Load Balancer ger per flöde belastningsutjämning på varje tillfällig port för en intern Standard Belastningsutjämnarens klientdel IP-adress.  Funktionen är användbar för andra scenarier där det är opraktiskt eller oönskade att ange enskilda portar.

En belastningsutjämningsregel för hög tillgänglighet portar kan du skapa aktivt-passivt eller aktivt-aktivt n + 1 scenarier för virtuella nätverksenheter och alla program som kräver stora intervall med ingående portar.  En hälsoavsökning kan användas för att avgöra vilka serverdelar ska ta emot nya flöden.  Du kan använda en Nätverkssäkerhetsgrupp för att emulera en port intervallet scenario.

>[!IMPORTANT]
> Om du planerar att använda en virtuell nätverksenhet, kontrollera med leverantören för information om huruvida sin produkt har testats med hög tillgänglighet portar och följa sina specifika riktlinjer för implementering. 

Granska [detaljerad beskrivning av HA Ports](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Säker som standard

Standard Load Balancer är fullständigt integrerat till det virtuella nätverket.  Det virtuella nätverket är ett privat, stängda nätverk.  Eftersom Standard belastningsutjämnare och offentliga IP-adresser har utformats för att det här virtuella nätverket kan nås från utanför det virtuella nätverket, dessa resurser nu som standard om du öppnar dem. Det innebär att Nätverkssäkerhetsgrupper (NSG) används nu för att uttryckligen tillåta och listan över godkända tillåten trafik.  Du kan skapa hela virtuella datacenter och bestämma via NSG vad och när bör den vara tillgänglig.  Om du inte har en NSG på ett undernät eller NIC för den virtuella datorresursen tillåts inte trafik att nå den här resursen.

Läs mer om NSG: er och hur du använder dem för ditt scenario i [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

### <a name="outbound"></a> Utgående anslutningar

Belastningsutjämnare har stöd för inkommande och utgående scenarier.  Standard Load Balancer skiljer sig avsevärt för belastningsutjämnare med avseende på utgående anslutningar.

Källa Network adress Translation (SNAT) används för att mappa interna, privata IP-adresser i det virtuella nätverket till den offentliga IP-adresser på belastningsutjämnaren klienter.

Standard Load Balancer introducerar en ny algoritm för en [robust, skalbar och förutsägbara SNAT algoritmen](load-balancer-outbound-connections.md#snat) och aktiverar nya funktioner, tar bort tvetydighet och framtvingar explicit konfigurationer i stället sida effekter. Dessa ändringar är nödvändigt att tillåta att nya funktioner som dyker upp. 

Dessa är viktiga tenets att komma ihåg när du arbetar med Standard Load Balancer:

- slutförandet av en regel styr Load Balancer-resursen.  alla Azure-programmering härleds från konfigurationen.
- När det finns flera klienter, alla klienter som används och varje klientdel multiplicerar antalet tillgängliga SNAT-portar
- Du kan välja och kontrollera om du inte vill för en viss klientdel som ska användas för utgående anslutningar.
- utgående scenarier är explicit och utgående anslutning finns inte förrän det har angetts.
- belastningsutjämningsregler härleda hur SNAT är programmerade. Belastningsutjämningsregler är protokoll. SNAT är protokoll som är specifika och konfigurationen bör återspegla detta i stället för att skapa en sidoeffekt.

#### <a name="multiple-frontends"></a>Flera klienter
Om du vill mer SNAT portar eftersom du förväntar dig eller inte redan har en hög efterfrågan för utgående anslutningar, du kan också lägga till inkrementella SNAT port inventering genom att konfigurera ytterligare klienter, regler och serverdelspooler till samma virtuella dator resurser.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Kontrollera vilka frontend används för utgående
Om du vill begränsa utgående anslutningar till endast kommer från en specifik frontend IP-adress kan du också inaktivera utgående SNAT på den regel som uttrycker utgående mappningen.

#### <a name="control-outbound-connectivity"></a>Kontrollera utgående anslutningar
Standard Load Balancer finns i kontexten för det virtuella nätverket.  Ett virtuellt nätverk är en isolerad, privat nätverk.  Om det inte finns en koppling med en offentlig IP-adress, tillåts inte offentlig anslutning.  Du kan nå [VNet-tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) eftersom de är för och lokala till det virtuella nätverket.  Om du vill upprätta en utgående anslutning till ett mål utanför ditt virtuella nätverk har två alternativ:
- tilldela en offentlig IP-adress för Standard-SKU som en instansnivå offentliga IP-adressen till den virtuella datorresursen eller
- Placera den virtuella datorresursen i serverdelspoolen för en offentlig Standard Load Balancer.

Båda kan utgående trafik från det virtuella nätverket till utanför det virtuella nätverket. 

Om du _endast_ har en intern Standard Load Balancer som hör till backend-poolen där den virtuella datorresursen finns, den virtuella datorn kan endast nå virtuella nätverksresurser och [VNet Service Slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md).  Du kan följa stegen som beskrivs i föregående stycke att skapa utgående anslutning.

Utgående anslutning för en VM-resurs som inte är associerade med Standard-SKU: er finns kvar som innan.

Granska [detaljerad diskussion om utgående anslutningar](load-balancer-outbound-connections.md).

### <a name="multife"></a>Flera klienter
Belastningsutjämnare har stöd för flera regler med flera klienter.  Standard Load Balancer expanderar det utgående scenarier.  Utgående scenarier är i stort sett inversen till en inkommande regel för belastningsutjämning.  Inkommande belastningsutjämningsregeln även skapar en kollega för utgående anslutningar. Standard Load Balancer använder alla klienter som är associerade med en virtuell datorresurs via en belastningsutjämningsregel.  Dessutom en parameter på Utjämning av nätverksbelastning regel och gör att du vill ignorera en belastningsutjämningsregel för utgående anslutning som du kan välja specifika klienter inklusive none.

Jämförelse, belastningsutjämnare väljer en enda klientdel slumpmässigt och det finns ingen möjlighet att styra vilken som har valts.

Granska [detaljerad diskussion om utgående anslutningar](load-balancer-outbound-connections.md).

### <a name="operations"></a> Hanteringsåtgärder

Standard Load Balancer-resurser finns på en helt ny infrastruktursplattform för.  På så sätt kan betydligt snabbare hanteringsåtgärder för Standard-SKU: er och färdiga är vanligtvis mindre än 30 sekunder per resurs för Standard-SKU.  Observera att när serverdelspooler ökar i storlek, varaktighet som krävs för serverdelen poolen ändrar också ökning.

Du kan ändra Standard Load Balancer resurser och flytta en Standard offentliga IP-adress från en virtuell dator till en annan mycket snabbare.

## <a name="migration-between-skus"></a>Migrering mellan SKU: er

SKU: er är inte föränderliga. Följ stegen i det här avsnittet för att flytta från en resurs SKU till en annan.

>[!IMPORTANT]
>Granska det här dokumentet i sin helhet att förstå skillnaderna mellan SKU: er och noggrant har undersökt ditt scenario.  Du kan behöva göra ytterligare ändringar att justera ditt scenario.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrera från Basic till Standard-SKU

1. Skapa en ny Standard resurs (belastningsutjämnare och offentliga IP-adresser, vid behov). Återskapa dina regler och avsökning definitioner.

2. Skapa en ny eller uppdatera befintliga NSG på nätverkskortet eller undernätet till listan över godkända belastningsutjämnade trafik, avsökning, samt annan trafik som du vill tillåta.

3. Ta bort de grundläggande SKU-resurserna (belastningsutjämnare och offentliga IP-adresser, så är tillämpligt) från alla VM-instanser. Måste du också ta bort alla VM-instanser i en tillgänglighetsuppsättning.

4. Bifoga alla VM-instanser till de nya Standard-SKU-resurserna.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrera från Standard till grundläggande SKU

1. Skapa en ny grundläggande resurs (belastningsutjämnare och offentliga IP-adresser, vid behov). Återskapa dina regler och avsökning definitioner. 

2. Ta bort resurserna som Standard-SKU (belastningsutjämnare och offentliga IP-adresser, så är tillämpligt) från alla VM-instanser. Måste du också ta bort alla VM-instanser i en tillgänglighetsuppsättning.

3. Bifoga alla VM-instanser till de nya grundläggande SKU-resurserna.

>[!IMPORTANT]
>
>Det finns begränsningar om användande av grundläggande och standardmässig.
>
>Hög tillgänglighet är portar och diagnostik för Standard-SKU endast tillgängliga i Standard-SKU. Du kan inte migrera från Standard-SKU till en grundläggande SKU och behåller även de här funktionerna.
>
>Både Basic och Standard-SKU har många skillnader som beskrivs i den här artikeln.  Kontrollera att du förstå och förbereda för dessa.
>
>Matchande SKU: er måste användas för belastningsutjämnaren och offentlig IP-resurser. Du kan inte ha en blandning av grundläggande SKU-resurser och resurser som Standard-SKU. Du kan inte bifoga fristående virtuella datorer, virtuella datorer i en tillgänglighetsuppsättningsresurs eller en virtuell dators skalningsuppsättningsresurser till båda SKU:erna samtidigt.

## <a name="region-availability"></a>Regional tillgänglighet

Load Balancer Standard finns för närvarande i alla regioner för offentliga moln.

## <a name="sla"></a>SLA

Standard belastningsutjämnare är tillgängliga med ett serviceavtal på 99,99%.  Granska den [Standard Load Balancer SLA](https://aka.ms/lbsla) mer information.

## <a name="pricing"></a>Prissättning

Standard Load Balancer är en produkt som debiteras baserat på antalet konfigurerade regler och all inkommande och utgående data som bearbetas för belastningsutjämning. Standard Load Balancer information om priser finns i [Load Balancer priser](https://aka.ms/lbpricing) sidan.

## <a name="limitations"></a>Begränsningar

- SKU: er är inte föränderliga. Du kan inte ändra SKU: N för en befintlig resurs.
- En fristående virtuell datorresurs, resurs för tillgänglighetsuppsättning eller VM scale set resurs kan referera till en SKU aldrig båda.
- En belastningsutjämningsregel kan inte finnas två virtuella nätverk.  Klienter och deras relaterade serverdelsinstanser måste finnas i samma virtuella nätverk.  
- Load Balancer klienter är inte tillgängliga för global vnet-peering.
- [Flytta Prenumerationsåtgärder](../azure-resource-manager/resource-group-move-resources.md) stöds inte för Standard-SKU-Belastningsutjämnare och PIP-resurser.
- Webbarbetsroller utan ett virtuellt nätverk och andra Microsoft-plattformstjänster kan vara tillgängliga när bara en intern Standardbelastningsutjämnare används på grund av en sidoeffekt från hur pre-VNet-tjänster och andra plattformar tjänster funktion. Du måste inte förlita dig på detta när de hanterar själva eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du behöver skapa [utgående anslutning](load-balancer-outbound-connections.md) uttryckligen om du vill när du använder en intern Standard Load Balancer endast.
- Belastningsutjämnare är en TCP eller UDP-produkt för belastningsutjämning och portvidarebefordran för dessa specifika IP-protokoll.  Regler för belastningsutjämning och inkommande NAT-regler stöds för TCP och UDP och stöds inte för andra IP-protokoll, inklusive ICMP. Belastningsutjämnaren inte avslutar, svara eller annars interagera med nyttolasten för ett flöde för UDP eller TCP. Det är inte en proxy. Lyckad validering av anslutning till en klientdel måste vidta plats i band med samma protokoll som används i en belastning belastningsutjämning eller inkommande NAT-regel (TCP eller UDP) _och_ minst en av dina virtuella datorer måste generera ett svar för en klient för att se ett svar från en klientdel.  Inte får en in-band-svar från Belastningsutjämnarens klientdel anger att inga virtuella datorer kunde svara.  Det går inte att interagera med en belastningsutjämnare som är klientdelen utan en virtuell dator kan svara.  Detta gäller även för utgående anslutningar där [port maskerade SNAT](load-balancer-outbound-connections.md#snat) är stöds endast för TCP och UDP; alla andra IP-protokoll, inklusive ICMP också misslyckas.  Tilldela en offentlig IP-adress för på instansnivå att minska.
- Till skillnad från offentliga belastningsutjämnare som ger [utgående anslutningar](load-balancer-outbound-connections.md) när övergången från privata IP-adresser i virtuella nätverk till offentliga IP-adresser, interna belastningsutjämnare inte översätta utgående har sitt ursprung anslutningar till klientdelen av en intern belastningsutjämnare som båda är i privat IP-adressutrymme.  På så sätt undviker du risken för SNAT överbelastning i unika interna IP-adressutrymmet som där translation inte krävs.  Effekten på klientsidan är att om ett utgående flöde från en virtuell dator i backend poolen försöker ett flöde till klientdelen av den interna belastningsutjämnaren vilken pool finns _och_ mappas till sig själv, både ben av flödet inte matchar och flödet kommer att misslyckas .  Om flödet inte mappade till samma virtuella dator i backend-poolen som skapade flödet till klient, lyckas flödet.   När flödet mappar tillbaka till själva utgående flödet verkar kommer från den virtuella datorn till klient och motsvarande inkommande flödet visas som kommer från den virtuella datorn till sig själv. Från gäst-OS synsätt matchar inte de inkommande och utgående delarna av samma flöde inuti den virtuella datorn. TCP-stacken känner inte igen de här delarna av samma flöde som en del av samma flöde som källa och mål inte matchar.  När flödet mappar till andra virtuella datorer i backend-poolen, delarna av flödet kommer att matcha och den virtuella datorn har kan svara på flödet.  Symtom för det här scenariot är tillfälliga timeout. Det finns flera vanliga lösningar för på ett tillförlitligt sätt att uppnå det här scenariot (med ursprung flöden från en backend-pool till backend-adresspooler respektive den interna belastningsutjämnaren frontend) som innehåller antingen inmatningen av en tredjeparts-proxy bakom den interna belastningen Belastningsutjämnare eller [med DSR formatreglerna](load-balancer-multivip-overview.md).  Medan du kan använda en offentlig belastningsutjämnare för att minska, det resulterande scenariot är lätt att göra [SNAT resursuttömning](load-balancer-outbound-connections.md#snat) och bör inte användas om inte noggrant hanteras.

## <a name="next-steps"></a>Nästa steg

- Läs om hur du använder [Standard Load Balancer och Tillgänglighetszoner](load-balancer-standard-availability-zones.md)
- Läs mer om [Tillgänglighetszoner](../availability-zones/az-overview.md).
- Lär dig mer om [diagnostik för Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Lär dig mer om [stöd för flerdimensionella mått](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) för diagnostik i [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Läs om hur du använder [belastningsutjämnare för utgående anslutningar](load-balancer-outbound-connections.md)
- Lär dig mer om [Standard Load Balancer med HA Ports regler för belastningsutjämning](load-balancer-ha-ports-overview.md)
- Läs om hur du använder [belastningsutjämnaren med flera klienter](load-balancer-multivip-overview.md)
- Lär dig mer om [virtuella nätverk](../virtual-network/virtual-networks-overview.md).
- Läs mer om [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).
- Lär dig mer om [tjänstslutpunkter för virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md)
- Lär dig mer om den andra nyckeln [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
- Läs mer om [belastningsutjämnaren](load-balancer-overview.md).
