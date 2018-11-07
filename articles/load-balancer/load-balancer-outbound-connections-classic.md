---
title: Utgående anslutningar i Azure (klassisk) | Microsoft Docs
description: Den här artikeln förklarar hur Azure kan molntjänster att kommunicera med offentliga internet-tjänster.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: kumud
ms.openlocfilehash: 5cb0647148d2cd90ad4cce6e16de30b72fff8429
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219672"
---
# <a name="outbound-connections-classic"></a>Utgående anslutningar (klassiska)

Azure ger utgående anslutning för distributioner med flera olika sätt. Den här artikeln beskrivs vad de är, när de använder, hur de fungerar och hur du hanterar dem.

>[!NOTE]
>Den här artikeln beskriver endast klassiska distributioner.  Granska [utgående anslutningar](load-balancer-outbound-connections.md) för alla distributionsscenarier för resurshanteraren i Azure.

En distribution i Azure kan kommunicera med slutpunkter utanför Azure i det offentliga IP-adressutrymmet. När en instans initierar en utgående flöde till ett mål i det offentliga IP-adressutrymmet kan matchar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När du har skapat den här mappningen kan Returtrafiken för det här utgående Start flödet också nå privata IP-adress som flödet kom från.

Översättning av källnätverksadresser (SNAT) använder Azure för att utföra den här funktionen. När flera privata IP-adresser låtsas bakom en enda offentlig IP-adress, använder Azure [port adressöversättning (PAT)](#pat) att maskeras privata IP-adresser. Tillfälliga portar används för PATRIK och är [förallokerade](#preallocatedports) baserat på poolstorlek.

Det finns flera [utgående scenarier](#scenarios). Du kan kombinera dessa scenarier efter behov. Granska dem noggrant för att förstå de funktioner, begränsningar och mönster som de gäller för din distributionsmodell och programscenariot. Granska vägledning för [hantera dessa scenarier](#snatexhaust).

## <a name="scenarios"></a>Scenarioöversikt

Azure tillhandahåller tre olika metoder för att uppnå utgående anslutning klassisk distribution.  Inte alla klassiska distributioner har alla tre scenarier som är tillgängliga för dem:

| Scenario | Metod | IP-protokoll | Beskrivning | Web Worker-roll | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. Virtuell dator med en offentlig IP på instansnivå-adress](#ilpip) | SNAT, port låtsas inte används | TCP, UDP, ICMP, ESP | Azure använder offentlig IP-adress som tilldelats virtuella datorer. Instansen har alla tillfälliga portar som är tillgängliga. | Nej | Ja |
| [2. offentlig slutpunkt för Utjämning av nätverksbelastning](#publiclbendpoint) | SNAT med port låtsas (PAT) på den offentliga slutpunkten | TCP, UDP | Azure delar den offentliga IP-adress offentliga slutpunkten med flera privata slutpunkter. Azure använder tillfälliga portar för den offentliga slutpunkten för PATRIK. | Ja | Ja |
| [3. Fristående virtuell dator ](#defaultsnat) | SNAT med port låtsas (PAT) | TCP, UDP | Azure automatiskt utser en offentlig IP-adress för SNAT, delar den här offentliga IP-adressen med hela distributionen och använder tillfälliga portar för offentlig slutpunkt IP-adressen för PATRIK. Detta är en återställningsplats scenariot för föregående scenarier. Det rekommenderas inte om du behöver synlighet och kontroll. | Ja | Ja |

Det här är en delmängd av utgående anslutning funktioner tillgängliga för Resource Manager-distributioner i Azure.  

Olika distributioner i den klassiska har olika funktioner:

| Klassisk distribution | Funktioner som är tillgängliga | 
| --- | --- |
| Virtuell dator | scenariot [1](#ilpip), [2](#publiclbendpoint), eller [3](#defaultsnat) |
| Web Worker-roll | endast scenariot [2](#publiclbendpoint), [3](#defaultsnat) | 

[Begränsningsstrategier](#snatexhaust) också ha samma skillnaderna.

Den [algoritmen som används för Förallokering av tillfälliga portar](#ephemeralports) för PAT för klassiska distributioner är desamma som för Azure Resource Manager resource-distributioner.

### <a name="ilpip"></a>Scenario 1: Virtuell dator med en offentlig IP på instansnivå-adress

Den virtuella datorn har en instans på offentliga IP (ILPIP) tilldelade till den i det här scenariot. Utgående anslutningar är fråga det spelar ingen roll om den virtuella datorn har en belastningsutjämnad slutpunkt eller inte. Det här scenariot har företräde framför de andra. När du använder en ILPIP, använder den virtuella datorn ILPIP för alla utgående flöden.  

En offentlig IP som tilldelats till en virtuell dator är en 1:1-relation (snarare än 1:many) och implementeras som en tillståndslös 1:1-NAT.  Port maskering (PAT) används inte och den virtuella datorn har alla tillfälliga portar som är tillgängliga för användning.

Om ditt program initierar många utgående flöden och du får använda SNAT portöverbelastning kan vara bra att tilldela en [ILPIP minimera SNAT begränsningar](#assignilpip). Granska [hantera SNAT överbelastning](#snatexhaust) i sin helhet.

### <a name="publiclbendpoint"></a>Scenario 2: Offentlig belastningsutjämnad slutpunkt

I det här scenariot är den virtuella datorn eller Web Worker-roll associerad med en offentlig IP-adress via slutpunkt för Utjämning av nätverksbelastning. Den virtuella datorn har inte en offentlig IP-adress som tilldelats. 

När den belastningsutjämnade virtuella datorn skapar en flöde för utgående, omvandlar Azure privata IP-källadressen för utgående flödet till den offentliga IP-adressen för den offentliga slutpunkten för Utjämning av nätverksbelastning. Azure använder SNAT för att utföra den här funktionen. Azure använder också [känna dig NÖJD](#pat) att maskeras flera privata IP-adresser bakom en offentlig IP-adress. 

Tillfälliga belastningsutjämnarens offentliga IP-adress frontend-portar används för att särskilja enskilda flöden som har sitt ursprung av den virtuella datorn. SNAT använder dynamiskt [förallokerade tillfälliga portar](#preallocatedports) när utgående flöden skapas. I det här sammanhanget kallas tillfälliga portarna som används för SNAT SNAT portar.

SNAT portar är förallokerade enligt beskrivningen i den [förstå SNAT och PAT](#snat) avsnittet. Det är en begränsad resurs som kan vara förbrukat. Det är viktigt att förstå hur de är [förbrukas](#pat). Om du vill lära dig mer om att utforma för den här förbrukning och minimera efter behov, granska [hantera SNAT överbelastning](#snatexhaust).

När [flera offentliga slutpunkter för Utjämning av nätverksbelastning](load-balancer-multivip.md) finns, något av de här offentliga IP-adresser är en [kandidat för utgående flöden](#multivipsnat), och en väljs slumpmässigt.  

### <a name="defaultsnat"></a>Scenario 3: Ingen offentlig IP-adress som är associerade

I det här scenariot är den virtuella datorn eller Web Worker-rollen inte en del av en offentlig slutpunkt för Utjämning av nätverksbelastning.  Och när det gäller virtuella datorn har inte tilldelats en ILPIP-adress. När den virtuella datorn skapar en flöde för utgående, omvandlar Azure privata IP-källadressen för utgående flödet till en offentlig IP-källadressen. Offentliga IP-adress som används för det här utgående flödet kan inte konfigureras och räknas inte mot prenumerationens gräns för offentlig IP.  Azure tilldelar automatiskt den här adressen.

Azure använder SNAT med port låtsas ([känna dig NÖJD](#pat)) att utföra den här funktionen. Det här scenariot liknar [scenario 2](#lb), förutom att det finns ingen kontroll över IP-adress som används. Detta är en återställningsplats scenariot för när scenarier 1 och 2 finns inte. Vi rekommenderar inte det här scenariot om du vill ha kontroll över den utgående adressen. Om utgående anslutningar är en viktig del av ditt program, bör du välja ett annat scenario.

SNAT portar är förallokerade enligt beskrivningen i den [förstå SNAT och PAT](#snat) avsnittet.  Antalet virtuella datorer eller Webbarbetsroller delning offentliga IP-adress anger antalet förallokerade tillfälliga portar.   Det är viktigt att förstå hur de är [förbrukas](#pat). Om du vill lära dig mer om att utforma för den här förbrukning och minimera efter behov, granska [hantera SNAT överbelastning](#snatexhaust).

## <a name="snat"></a>Förstå SNAT och PAT

### <a name="pat"></a>Port maskering SNAT (PAT)

När en distribution gör en utgående anslutning, skrivs om varje utgående anslutningskälla. Källan skrivs om från det privata IP-adressutrymmet till den offentliga IP-Adressen kopplad till distributionen (baserat på scenario som beskrivs ovan). 5-tuppel av flödet (källans IP-adress, källport, IP-transportprotokollet, målets IP-adress, målport) måste vara unikt i det offentliga IP-adressutrymmet.  

Tillfälliga portar (SNAT) används för att uppnå detta efter att skriva om privata källans IP-adress, eftersom flera flöden kommer från en offentlig IP-adress. 

En SNAT port förbrukas per flöde till en enda målets IP-adress, port och protokoll. Varje flöde förbrukar en enskild port SNAT för flera flöden till samma målets IP-adress, port och protokoll. Detta säkerställer att flöden är unika när de kommer från samma offentliga IP-adress och går till samma mål-IP-adress, port och protokoll. 

Flera flöden, var och en till en annan målets IP-adress, port och protokoll, dela en enda SNAT-port. Den målets IP-adress, port och protokoll göra flöden unikt utan att behöva ytterligare källa portar för att skilja flöden i det offentliga IP-adressutrymmet.

När SNAT port resurser tömts misslyckas utgående flöden tills befintliga flöden släpper SNAT portar. Belastningsutjämnaren återtar SNAT portar när flödet stängs och använder en [4-minuters timeout för inaktivitet](#idletimeout) Frigör SNAT portar från inaktiv flöden.

Mönster att minimera villkor som ofta leder till att använda SNAT portöverbelastning, granska de [hantera SNAT](#snatexhaust) avsnittet.

### <a name="preallocatedports"></a>Tillfällig port Förallokering för port låtsas SNAT (PAT)

Azure använder en algoritm och fastställa antalet förallokerade SNAT portar som är tillgängliga baserat på storleken på backend-poolen när du använder port maskering SNAT ([känna dig NÖJD](#pat)). Det finns tillfälliga portar som är tillgängliga för en viss offentliga IP-källadress SNAT portar.

Azure preallocates SNAT portar när en instans har distribuerats utifrån hur många instanser av virtuell dator eller Web Worker-roll har en viss offentlig IP-adress.  När utgående flöden har skapats, [känna dig NÖJD](#pat) dynamiskt förbrukar (högst förallokerade) och släpper dessa portar när flödet stängs eller [inaktiv tidsgränser](#ideltimeout) inträffa.

I följande tabell visas SNAT port preallocations för nivåerna för backend-poolstorlekar:

| Instanser | Den förallokerade SNAT portar per instans |
| --- | --- |
| 1-50 | 1,024 |
| 51 – 100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Kom ihåg att antalet tillgängliga portar för SNAT inte översätter direkt till antal flöden. En enskild SNAT-port kan återanvändas för flera unika mål. Portar används endast om det är nödvändigt att göra flöden som är unikt. Riktlinjer för design och problemlösning, finns i avsnittet om [hur du hanterar icke förnybara resursen](#snatexhaust) och de avsnitt som beskriver [känna dig NÖJD](#pat).

Ändra storlek på din distribution kan påverka vissa av dina etablerade flöden. Om backend-pool-storlek ökar och övergår till nästa nivå, är hälften av din förallokerade SNAT portar frigöras under övergången till nästa större serverdelen pool nivå. Flöden som är associerade med en återvunnet SNAT port når tidsgränsen och måste återskapas. Om ett nytt flöde utförs lyckas flödet omedelbart så länge det finns tillgängliga förallokerade portar.

Om distributionsstorlek minskas och övergår till en lägre nivå, ökar antalet tillgängliga SNAT-portar. I det här fallet befintliga tilldelade SNAT portar och deras respektive flöden påverkas inte.

Om en tjänst i molnet är omdistribueras eller ändrats infrastrukturen kan tillfälligt rapportera serverdelspoolen vara upp till två gånger så stor som faktiska och Azure kommer i sin tur Förallokera mindre SNAT portar per instans än förväntat.  Detta kan tillfälligt öka sannolikheten för att använda SNAT portöverbelastning. Så småningom poolstorleken övergår till faktisk storlek och Azure kommer automatiskt att öka den förallokerade SNAT portar med det förväntade antalet enligt tabellen ovan.  Det här beteendet är avsiktligt och kan inte konfigureras.

SNAT portar allokeringar är IP-transportprotokollet specifika (TCP och UDP underhålls separat) och frigörs under följande förhållanden:

### <a name="tcp-snat-port-release"></a>Versionen för SNAT TCP-port

- Om både servern/klienten skickar FIN/ACK, kommer SNAT port att släppas efter 240 sekunder.
- Om en RSTA visas kommer SNAT port att släppas efter 15 sekunder.
- timeout för inaktivitet har uppnåtts

### <a name="udp-snat-port-release"></a>Versionen för SNAT UDP-port

- timeout för inaktivitet har uppnåtts

## <a name="problemsolving"></a> Problemlösning 

Det här avsnittet är avsedd att hjälpa dig att undvika överbelastning av SNAT och andra scenarier som kan uppstå med utgående anslutningar i Azure.

### <a name="snatexhaust"></a> Hantera portöverbelastning SNAT (PAT)
[Tillfälliga portar](#preallocatedports) används för [känna dig NÖJD](#pat) är en icke förnybara resurs, enligt beskrivningen i [ingen offentlig IP-adress som är associerade](#defaultsnat) och [offentlig slutpunkt för Utjämning av nätverksbelastning](#publiclbendpoint).

Om du vet att du initierar många utgående TCP eller UDP-anslutningar till samma IP-måladress och port och du se misslyckade utgående anslutningar eller bör av support som du är förbrukar SNAT-portar (förallokerade [tillfälliga portar](#preallocatedports) används av [känna dig NÖJD](#pat)), har du flera alternativ för Allmänt skydd. Granska alternativen och bestämma vad som är tillgängliga och bäst för ditt scenario. Det är möjligt att en eller flera kan hantera det här scenariot.

Om du har problem med att förstå beteendet för utgående anslutningar kan använda du IP-stack-statistik (netstat). Eller det kan vara bra att Observera anslutningslägen genom att använda infångade paket.

#### <a name="connectionreuse"></a>Ändra programmet så att återanvända anslutningar 
Du kan minska behovet av tillfälliga portar som används för SNAT genom att återanvända anslutningar i ditt program. Detta gäller särskilt för protokoll som HTTP/1.1, där återanvändning av anslutningar är standard. Och andra protokoll som använder HTTP för transport (till exempel REST) kan i sin tur. 

Återanvändning är alltid bättre än enskilda, atomiska TCP-anslutningar för varje begäran. Återanvända resulterar i bättre, på ett mycket effektivt TCP-transaktioner.

#### <a name="connection pooling"></a>Ändra programmet till att använda anslutningspooler
Du kan använda en anslutningspoolning schema i ditt program, där internt begäranden distribueras till en fast uppsättning anslutningar (varje återanvända där det är möjligt). Det här schemat begränsar antalet tillfälliga portar används och skapar en mer förutsägbar miljö. Det här schemat kan också öka genomflödet av begäranden genom att tillåta flera samtidiga åtgärder när en enda anslutning blockerar på svaret för en åtgärd.  

Anslutningspooler kanske redan finns inom ramen för som du använder för att utveckla ditt program eller konfigurationsinställningarna för ditt program. Du kan kombinera anslutningspooler med återanvändning av anslutningar. Dina flera begäranden kan sedan använda ett fast, förutsebart antal portar till samma mål-IP-adress och port. Begäranden kan också dra nytta av effektiv användning av TCP-transaktioner som minskar svarstiden och Resursanvändning. UDP-transaktioner kan också dra eftersom hantera antal UDP flöden kan i sin tur undvika avgaser villkor och hantera hur SNAT port.

#### <a name="retry logic"></a>Ändra att programmet använder mindre aggressiva logik för omprövning
När [förallokerade tillfälliga portar](#preallocatedports) används för [känna dig NÖJD](#pat) är slut eller ett program fel uppstår aggressivt eller brute force försöker utan decay och backoff logic orsakar överbelastning att inträffa eller kvar. Du kan minska behovet av tillfälliga portar genom att använda en mindre aggressiva logik för omprövning. 

Tillfälliga portar har en 4-minuters timeout för inaktivitet (ej justerbar). Om nya försök för aggressiva, har stoppas någon möjlighet att rensa på egen hand. Därför är överväger hur – och hur ofta – ditt program försöker transaktioner en viktig del av designen.

#### <a name="assignilpip"></a>Tilldela en instans på offentlig IP-adress till varje virtuell dator
Tilldela en ILPIP ändras ditt scenario till [offentlig IP på instansnivå till en virtuell dator](#ilpip). Alla tillfälliga portar för den offentliga IP-Adressen som används för varje virtuell dator är tillgängliga för den virtuella datorn. (Till skillnad från scenarier där tillfälliga portar på en offentlig IP-adress som delas med alla virtuella datorer som är associerade med respektive distribution.) Det finns avvägningarna att tänka på, till exempel den potentiella effekten av ett stort antal enskilda IP-adresser för listan över tillåtna program.

>[!NOTE] 
>Det här alternativet är inte tillgänglig för web worker-roller.

### <a name="idletimeout"></a>Använda keepalive-överföringar för att återställa utgående tidsgränsen för inaktivitet

Utgående anslutningar har ett 4-minuters timeout för inaktivitet. Den här timeouten kan inte ställas in. Du kan dock använda transport (till exempel TCP keepalive-överföringar) eller programnivå keepalive-överföringar för att uppdatera ett flöde för inaktiva och återställer den här tidsgränsen för inaktivitet om det behövs.  Kontrollera med leverantören av all paketerad programvara på om detta stöds eller om hur du aktiverar det.  Endast en sida måste vanligtvis att generera keepalive-överföringar för att återställa tidsgränsen för inaktivitet. 

## <a name="discoveroutbound"></a>Identifiera den offentliga IP-Adressen som använder en virtuell dator
Det finns många sätt att avgöra offentliga IP-källadressen för en utgående anslutning. OpenDNS är en tjänst som kan visa offentliga IP-adressen för den virtuella datorn. 

Du kan skicka en DNS-fråga för namnet myip.opendns.com till OpenDNS matcharen genom att använda nslookup-kommando. Tjänsten returnerar källans IP-adress som används för att skicka frågan. När du kör följande fråga från den virtuella datorn är svaret offentlig IP-adress som används för den virtuella datorn:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Nästa steg

- Läs mer om [belastningsutjämnaren](load-balancer-overview.md) används i Resource Manager-distributioner.
- Inlärningsläge om [utgående anslutning](load-balancer-outbound-connections.md) scenarier som är tillgängliga i Resource Manager-distributioner.
