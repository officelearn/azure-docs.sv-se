---
title: Utgående anslutningar i Azure
titleSuffix: Azure Load Balancer
description: Den här artikeln förklarar hur Azure gör det möjligt för virtuella datorer att kommunicera med offentliga Internet tjänster.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: a2292dc789938b8bde709728f5bbffe661529cc2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072635"
---
# <a name="outbound-connections-in-azure"></a>Utgående anslutningar i Azure

Azure Load Balancer tillhandahåller utgående anslutning via olika mekanismer. I den här artikeln beskrivs scenarier och hur du hanterar dem. Om du har problem med utgående anslutning via en Azure Load Balancer kan du läsa [fel söknings guiden för utgående anslutningar](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE]
>Den här artikeln beskriver Resource Manager-distributioner. Microsoft rekommenderar Resource Manager för produktions arbets belastningar.

## <a name="terminology"></a>Terminologi

| Term | Tillämpligt protokoll | Information|
| ---------|---------| -------|
| Käll Network Address Translation (SNAT) | TCP, UDP | En distribution i Azure kan kommunicera med slut punkter utanför Azure i det offentliga IP-adressutrymmet. När en instans initierar ett utgående flöde till ett mål i det offentliga IP-adressutrymmet, mappar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När den här mappningen har skapats kan returnera trafik för det här utgående flödet även komma åt den privata IP-adressen som flödet kommer från. Azure använder **käll Network Address Translation (SNAT)** för att utföra den här funktionen.|
| Port maskerad SNAT (PAT)| TCP, UDP |  När flera privata IP-adresser är maskerade bakom en enskild offentlig IP-adress använder Azure **port adress översättning (Pat)** för att maskera/dölja privata IP-adresser. Tillfälliga portar används för PAT och är [förallokerade](#preallocatedports) baserat på Poolens storlek. När en offentlig Load Balancer-resurs är kopplad till virtuella dator instanser, som inte har dedikerade offentliga IP-adresser, skrivs varje utgående anslutnings källa om. Källan skrivs från det virtuella nätverkets privata IP-adressutrymme till belastningsutjämnarens offentliga IP-adress för klient delen. I det offentliga IP-adressutrymmet måste en 5-tupel i flödet (Källans IP-adress, källport, IP-transportläge, målets IP-adress, målport) vara unik. Port maskerade SNAT kan användas med TCP-eller UDP IP-protokoll. Tillfälliga portar (SNAT-portar) används för att uppnå detta när den privata Källans IP-adress har skrivits om, eftersom flera flöden härstammar från en enda offentlig IP-adress. Porten som maskerade SNAT-algoritmen allokerar SNAT-portar på olika sätt för UDP jämfört med TCP.|
| SNAT-portar| TCP | SNAT-portar är tillfälliga portar som är tillgängliga för en viss offentlig IP-datakälla. En SNAT-port konsumeras per flöde till en IP-adress för ett enda mål, port. För flera TCP-flöden till samma mål-IP-adress, port och protokoll förbrukar varje TCP-flöde en enskild SNAT-port. Detta säkerställer att flödena är unika när de härstammar från samma offentliga IP-adress och går till samma mål-IP-adress, port och protokoll. Flera flöden, var och en till en annan mål-IP-adress, port och protokoll, delar en enda SNAT-port. Målets IP-adress, port och protokoll gör flöden unika utan behovet av ytterligare käll portar för att särskilja flöden i det offentliga IP-adressutrymmet.|
|SNAT-portar | UDP | UDP SNAT-portar hanteras av en annan algoritm än TCP SNAT-portar.  Load Balancer använder en algoritm som kallas "Port-restricted kon NAT" för UDP.  En SNAT-port förbrukas för varje flöde, oavsett mål-IP-adress, port.|
| Uttömning | - | När SNAT-port resurserna är slut så att utgående flöden inte fungerar förrän befintliga flöden frigör SNAT-portar. Load Balancer frigör SNAT-portar när flödet stängs och använder en [tids gräns på 4 minuter](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) för att frigöra SNAT-portar från inaktiva flöden. UDP SNAT-portar är ofta mycket snabbare än TCP SNAT-portar på grund av skillnaden i algoritmen som används. Du måste utforma och skala test med den här skillnaden i åtanke.|
| Uppdaterings beteende för SNAT-porten | TCP | Om antingen servern/klienten skickar FINACK, frigörs SNAT-porten efter 240 sekunder. Om en min visas kommer SNAT-porten att lanseras efter 15 sekunder. Om tids gränsen för inaktivitet har nåtts släpps porten.|
| Uppdaterings beteende för SNAT-porten | UDP |Om tids gränsen för inaktivitet har nåtts släpps porten.|
| Åter användning av SNAT-portar | TCP, UDP | När en port har frigjorts är porten tillgänglig för åter användning vid behov.  Du kan tänka på SNAT-portar som en sekvens från lägsta till högsta tillgängliga för ett specifikt scenario och den första tillgängliga SNAT-porten används för nya anslutningar.|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algoritm för port tilldelning

Azure använder en algoritm för att fastställa antalet förallokerade SNAT-portar som är tillgängliga utifrån storleken på backend-poolen när du använder PAT. För varje offentlig IP-adress som är associerad med en belastningsutjämnare finns 64 000-portar som är tillgängliga som SNAT-portar för varje IP-protokoll för transport. Samma antal SNAT-portar är förallokerade för UDP respektive TCP och konsumeras oberoende av varje IP-protokoll för transport.  Men användning av SNAT-porten skiljer sig åt beroende på om flödet är UDP eller TCP. När utgående flöden skapas förbrukas dessa portar dynamiskt (upp till den förallokerade gränsen) och släpps när flödet stängs eller när [tids gränsen för inaktivitet](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) inträffar. Portar används bara om det är nödvändigt att göra flöden unika.

#### <a name="default-snat-ports-allocated"></a><a name="snatporttable"></a>Fördelade standard-SNAT-portar

Följande tabell visar förallokeringar av SNAT-portar för nivåer av backend-poolens storlekar:

| Poolstorlek (VM-instanser) | Förallokerade SNAT-portar per IP-konfiguration |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 |

Att ändra storleken på din backend-pool kan påverka vissa av dina upprättade flöden:

- Om storleken på backend-poolen ökar och övergår till nästa nivå, frigörs hälften av de förallokerade SNAT-portarna under över gången till nästa större nivå i Server delen. Flöden som är associerade med en representerad SNAT-port blir timeout och måste återupprättas. Om ett nytt flöde görs kommer flödet att lyckas omedelbart så länge som förallokerade portar är tillgängliga.
- Om storleken på backend-poolen sänks och övergår till en lägre nivå, ökar antalet tillgängliga SNAT-portar. I det här fallet påverkas inte befintliga allokerade SNAT-portar och deras respektive flöden.

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>Översikt över scenarier för utgående anslutningar

| Scenario | Metod | IP-protokoll | Beskrivning |
|  --- | --- | --- | --- |
|  1. virtuell dator med en offentlig IP-adress (med eller utan Azure Load Balancer | SNAT, Port maskerad, används inte | TCP, UDP, ICMP, ESP | Azure använder den offentliga IP-adress som tilldelats IP-konfigurationen av instansens nätverkskort för alla utgående flöden. Instansen har alla tillfälliga portar tillgängliga. Det spelar ingen roll om den virtuella datorn är belastningsutjämnad eller inte. Det här scenariot prioriteras framför de andra. En offentlig IP-adress som tilldelas till en virtuell dator är en 1:1-relation (i stället för 1: många) och implementeras som en tillstånds Lös 1:1 NAT. |
| 2. offentlig Load Balancer associerad med en virtuell dator (ingen offentlig IP-adress på den virtuella datorn/instansen) | SNAT med port maskerad (PAT) med hjälp av Load Balancer-frontend | TCP, UDP | I det här scenariot måste Load Balancer-resursen konfigureras med en belastnings Utjämnings regel för att skapa en länk mellan den offentliga IP-frontend-poolen med backend-poolen. Om du inte slutför den här regel konfigurationen är beteendet som beskrivs i scenario 3. Det är inte nödvändigt att regeln har en fungerande lyssnare i backend-poolen för att hälso avsökningen ska lyckas. När en virtuell dator skapar ett utgående flöde översätter Azure den privata käll-IP-adressen för det utgående flödet till den offentliga IP-adressen för den offentliga Load Balancer-frontend-tjänsten via SNAT. Tillfälliga portar för belastningsutjämnarens offentliga IP-adress för klient delen används för att särskilja enskilda flöden från den virtuella datorn. SNAT använder dynamiskt [förallokerade portar](#preallocatedports) när utgående flöden skapas. I det här sammanhanget kallas de tillfälliga portarna som används för SNAT som SNAT-portar. SNAT-portar är förallokerade enligt beskrivningen i [standardvärdet för den allokerade SNAT-porten](#snatporttable). |
| 3. virtuell dator (inga Load Balancer, ingen offentlig IP-adress) eller virtuell dator som är associerad med Basic Internal Load Balancer | SNAT med port maskerad (PAT) | TCP, UDP | När den virtuella datorn skapar ett utgående flöde översätter Azure den privata käll-IP-adressen för det utgående flödet till en offentlig käll-IP-adress. Den här offentliga IP-adressen kan **inte konfigureras**, kan inte reserveras och räknas inte mot prenumerationens offentliga IP-adressresurs. Om du distribuerar om den virtuella datorn eller tillgänglighets uppsättningen eller den virtuella datorns skalnings uppsättning frigörs den här offentliga IP-adressen och en ny offentlig IP-adress begärs. Använd inte det här scenariot för vit listning IP-adresser. Använd i stället scenario 1 eller 2 där du explicit deklarerar utgående beteende. SNAT-portar är fördelade enligt beskrivningen i [standardvärdet för den allokerade SNAT-porten](#snatporttable).

## <a name="outbound-rules"></a><a name="outboundrules"></a>Regler för utgående trafik

 Utgående regler gör det enkelt att konfigurera offentliga [standard load BALANCERS](load-balancer-standard-overview.md)utgående Network Address Translation.  Du har fullständig deklarativ kontroll över utgående anslutning för att skala och finjustera den här funktionen för dina behov. I det här avsnittet expanderar du scenario 2 (B) enligt beskrivningen ovan.

![Load Balancer utgående regler](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Med utgående regler kan du använda Load Balancer för att definiera utgående NAT från grunden. Du kan också skala och finjustera beteendet för befintlig utgående NAT.

Med utgående regler kan du styra:

- vilka virtuella datorer som ska översättas till vilka offentliga IP-adresser.
- Hur utgående SNAT-portar ska allokeras.
- vilka protokoll som ska tillhandahålla utgående översättning för.
- vilken varaktighet som ska användas för utgående timeout för utgående anslutning (4-120 minuter).
- Om du vill skicka en TCP-återställning vid inaktivitet
- både TCP-och UDP-transport protokoll med en enda regel

### <a name="outbound-rule-definition"></a>Regel definition för utgående trafik

Precis som alla Load Balancer regler, följer utgående regler samma välbekanta syntax som belastnings utjämning och inkommande NAT- **regler:**  +  Server dels**parametrar**i  +  **backend-poolen**. En utgående regel konfigurerar utgående NAT för _alla virtuella datorer som identifieras av backend-poolen_ som ska översättas till _klient delen_.  _Parametrarna_ ger ytterligare detaljerad kontroll över den utgående NAT-algoritmen.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Skala utgående NAT med flera IP-adresser

Varje ytterligare IP-adress som tillhandahålls av en klient del ger ytterligare 64 000 tillfälliga portar för Load Balancer att använda som SNAT-portar. Du kan använda flera IP-adresser för att planera för storskaliga scenarier och du kan använda utgående regler för att minska mönstren för [SNAT-överbelastningar](troubleshoot-outbound-connection.md#snatexhaust) .  

Du kan också använda ett [offentligt IP-prefix](https://aka.ms/lbpublicipprefix) direkt med en utgående regel.  Med det offentliga IP-prefixet får du enklare skalning och förenklad, vit lista över flöden från din Azure-distribution. Du kan konfigurera en IP-konfiguration för klient delen i Load Balancer resurs för att referera till ett offentligt IP-adressprefix direkt.  Detta gör att Load Balancer exklusiv kontroll över det offentliga IP-prefixet och regeln för utgående trafik automatiskt använder alla offentliga IP-adresser som finns i det offentliga IP-prefixet för utgående anslutningar.  Var och en av IP-adresserna inom det offentliga IP-prefixet ger ytterligare 64 000 tillfälliga portar per IP-adress för Load Balancer som ska användas som SNAT-portar.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a>Utgående tids gräns för flödes inaktivitet och TCP-återställning

Utgående regler tillhandahåller en konfigurations parameter för att kontrol lera utgående tids gräns för utgående trafik och matcha den mot ditt programs behov. Timeout för utgående inaktivitet är som standard 4 minuter. Du kan lära dig att [Konfigurera tids gränser för inaktivitet](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). Standard beteendet för Load Balancer är att släppa flödet tyst när tids gränsen för utgående inaktivitet har nåtts.  Med `enableTCPReset` -parametern kan du aktivera ett mer förutsägbart program beteende och kontrol lera om du vill skicka dubbelriktad TCP-återställning (TCP per-) vid tids gränsen för utgående inaktivitet. Granska [TCP-återställning vid inaktivitet](https://aka.ms/lbtcpreset) för mer information, inklusive region tillgänglighet.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Förhindra utgående anslutning

Belastnings Utjämnings regler ger automatisk programmering av utgående NAT. Vissa scenarier är dock förmånen eller kräver att du inaktiverar automatisk programmering av utgående NAT med belastnings Utjämnings regeln så att du kan styra eller förfina beteendet.  
Du kan använda den här parametern på två sätt:

1. Valfri Undertryckning av Använd den inkommande IP-adressen för utgående SNAT genom att inaktivera utgående SNAT för en belastnings Utjämnings regel
  
2. Justera de utgående SNAT-parametrarna för en IP-adress som används för inkommande och utgående samtidigt.  Automatisk utgående NAT-programmering måste inaktive ras för att tillåta en utgående regel att ta kontroll.  För att till exempel ändra SNAT-port tilldelningen för en adress som också används för inkommande, `disableOutboundSnat` måste parametern vara inställd på True.  Om du försöker använda en utgående regel för att omdefiniera parametrarna för en IP-adress som också används för inkommande och inte har publicerat ett utgående NAT-program för belastnings Utjämnings regeln, kommer åtgärden att konfigurera en utgående regel att Miss lyckas.

>[!IMPORTANT]
> Den virtuella datorn har inte någon utgående anslutning om du anger den här parametern till true och inte har någon utgående regel för att definiera utgående anslutning.  Vissa åtgärder för din virtuella dator eller ditt program kan bero på att det finns en utgående anslutning tillgänglig. Se till att du förstår beroendena för ditt scenario och att du anser att du har gjort den här ändringen.

Ibland är det olämpligt för en virtuell dator att tillåtas att skapa ett utgående flöde. Eller så kan det finnas ett krav för att hantera vilka mål som kan nås med utgående flöden eller vilka destinationer som kan starta inkommande flöden. I det här fallet kan du använda [nätverks säkerhets grupper](../virtual-network/security-overview.md) för att hantera de destinationer som den virtuella datorn kan komma åt. Du kan också använda NSG: er för att hantera vilka offentliga destinationer som kan initiera inkommande flöden.

När du tillämpar en NSG på en belastningsutjämnad virtuell dator, bör du tänka på [tjänst taggarna](../virtual-network/security-overview.md#service-tags) och [Standard säkerhets reglerna](../virtual-network/security-overview.md#default-security-rules). Du måste se till att den virtuella datorn kan ta emot begär Anden om hälso avsökning från Azure Load Balancer.

Om en NSG blockerar hälso avsöknings begär Anden från AZURE_LOADBALANCER standard tag gen inte din VM-hälsoavsökning och den virtuella datorn är markerad. Load Balancer slutar skicka nya flöden till den virtuella datorn.

## <a name="scenarios-with-outbound-rules"></a>Scenarier med utgående regler

| # | Scenario| Information|
|---|---|---|
| I | Rensa utgående anslutningar till en speciell uppsättning offentliga IP-adresser| Du kan använda en regel för utgående trafik för att rensa utgående anslutningar så att de kommer från en speciell uppsättning offentliga IP-adresser för att under lätta vit listning-scenarier.  Den här offentliga IP-adressen kan vara samma som används av en belastnings Utjämnings regel eller en annan uppsättning offentliga IP-adresser än vad som används av en belastnings Utjämnings regel.  1. Skapa [offentliga IP-prefix](https://aka.ms/lbpublicipprefix) (eller offentliga IP-adresser från offentliga IP-prefix) 2. Skapa en offentlig Standard Load Balancer 3. Skapa frontend-klienter som refererar till det offentliga IP-prefix (eller offentliga IP-adresser) som du vill använda 4. Återanvänd en backend-pool eller skapa en backend-pool och placera de virtuella datorerna i en backend-pool för den offentliga Load Balancer 5. Konfigurera en regel för utgående trafik på den offentliga Load Balancer till program utgående NAT för de här virtuella datorerna med hjälp av klient datorerna. Om du inte vill att belastnings Utjämnings regeln ska användas för utgående trafik måste du inaktivera utgående SNAT i belastnings Utjämnings regeln.
| II | Ändra SNAT-port tilldelning| Du kan använda utgående regler för att finjustera den [automatiska SNAT-port tilldelningen baserat på storleken på backend-poolen](load-balancer-outbound-connections.md#preallocatedports). Om du till exempel har två virtuella datorer som delar en enda offentlig IP-adress för utgående NAT, kanske du vill öka antalet SNAT-portar som har allokerats från standard-1024-portarna om du upplever SNAT-belastning. Varje offentlig IP-adress kan bidra upp till 64 000 tillfälliga portar.  Om du konfigurerar en utgående regel med en enda offentlig IP-adresspool kan du distribuera totalt 64 000 SNAT-portar till virtuella datorer i backend-poolen.  För två virtuella datorer kan högst 32 000 SNAT-portar tilldelas med en utgående regel (2x 32 000 = 64 000). Du kan använda utgående regler för att finjustera de SNAT-portar som tilldelas som standard. Du tilldelar mer eller mindre än standardvärdet för SNAT-port tilldelning. Varje offentlig IP-adress från alla klient delar av en utgående regel bidrar upp till 64 000 tillfälliga portar för användning som SNAT-portar.  Load Balancer allokerar SNAT-portar i multipler av 8. Om du anger ett värde som inte är delbart med 8 avvisas konfigurations åtgärden.  Om du försöker allokera fler SNAT-portar än vad som är tillgängligt baserat på antalet offentliga IP-adresser avvisas konfigurations åtgärden.  Om du till exempel allokerar 10 000-portar per virtuell dator och 7 virtuella datorer i en backend-pool delar en enskild offentlig IP-adress, så avvisas konfigurationen (7 x 10 000/SNAT-portar > 64 000/SNAT-portar).  Du kan lägga till fler offentliga IP-adresser till klient delen för den utgående regeln för att aktivera scenariot. Du kan återgå till [standard SNAT-port tilldelningen baserat på storlek på backend-poolen](load-balancer-outbound-connections.md#preallocatedports) genom att ange 0 för antal portar. I så fall kommer de första 50 VM-instanserna att få 1024 portar, 51-100 VM-instanser får 512 och så vidare enligt [tabellen](#snatporttable).|
| IIIa| Aktivera endast utgående | Du kan använda en offentlig Standard Load Balancer för att tillhandahålla utgående NAT för en grupp med virtuella datorer. I det här scenariot kan du använda en utgående regel i sig självt, utan att behöva några ytterligare regler.|
| A | Utgående NAT enbart för virtuella datorer (ingen inkommande) | Definiera en offentlig Standard Load Balancer, placera de virtuella datorerna i backend-poolen och konfigurera en utgående regel för att program mera NAT och rensa utgående anslutningar så att de härstammar från en speciell offentlig IP-adress. Du kan också använda ett offentligt IP-prefix för att förenkla White-List källan för utgående anslutningar. 1. Skapa en offentlig Standard Load Balancer. 2. Skapa en backend-pool och placera de virtuella datorerna i en backend-pool för den offentliga Load Balancer. 3. Konfigurera en utgående regel för den offentliga Load Balancer till program utgående NAT för dessa virtuella datorer.
| V| Utgående NAT för interna Standard Load Balancer scenarier| När du använder en intern Standard Load Balancer är utgående NAT inte tillgängligt förrän den utgående anslutningen uttryckligen har deklarerats. Du kan definiera utgående anslutningar med en utgående regel för att skapa utgående anslutningar för virtuella datorer bakom en intern Standard Load Balancer med följande steg: 1. Skapa en offentlig Standard Load Balancer. 2. Skapa en backend-pool och placera de virtuella datorerna i en backend-pool för de offentliga Load Balancer utöver de interna Load Balancer. 3. Konfigurera en utgående regel för den offentliga Load Balancer till program utgående NAT för dessa virtuella datorer.|
| VI | Aktivera både TCP & UDP-protokoll för utgående NAT med en offentlig Standard Load Balancer | När du använder en offentlig Standard Load Balancer matchas den automatiska utgående NAT-programmeringen med transport protokollet i belastnings Utjämnings regeln. 1. Inaktivera utgående SNAT i belastnings Utjämnings regeln. 2. Konfigurera en utgående regel på samma Load Balancer. 3. Återanvänd backend-poolen som redan används av dina virtuella datorer. 4. Ange "protokoll": "alla" som en del av regeln för utgående trafik. När endast inkommande NAT-regler används tillhandahålls ingen utgående NAT. 1. Placera virtuella datorer i en backend-pool. 2. Definiera en eller flera IP-konfigurationer för klient delen med offentliga IP-adresser eller offentlig IP-prefix 3. Konfigurera en utgående regel på samma Load Balancer. 4. Ange "protokoll": "alla" som en del av regeln för utgående trafik |


## <a name="limitations"></a>Begränsningar

- Det maximala antalet användbara tillfälliga portar per IP-adress för klient delen är 64 000.
- Intervallet för den konfigurerbara tids gränsen för utgående inaktivitet är 4 till 120 minuter (240 till 7200 sekunder).
- Load Balancer stöder inte ICMP för utgående NAT.
- Utgående regler kan bara tillämpas på en primär IP-konfiguration för ett nätverkskort.  Det finns stöd för flera nätverkskort.
- Webb arbets roller utan ett virtuellt nätverk och andra Microsofts plattforms tjänster kan nås när bara en intern Standard Load Balancer används på grund av en sido effekt från hur för-VNet-tjänster och andra plattforms tjänster fungerar. Förlita dig inte på denna sido effekt eftersom själva respektive tjänst eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du behöver skapa utgående anslutningar om du vill när du bara använder en intern Standard Load Balancer. Scenario 3 som beskrivs i den här artikeln är inte tillgängligt.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md).
- Se våra [vanliga frågor och svar om Azure Load Balancer](load-balancer-faqs.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md) för offentliga standard Load Balancer.
- Läs mer om [Load Balancer](load-balancer-overview.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
- Lär dig mer om några av de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
