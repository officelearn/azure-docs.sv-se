---
title: Utgående anslutningar i Azure
titlesuffix: Azure Load Balancer
description: Den här artikeln förklarar hur Azure kan virtuella datorer kommunicera med offentliga internet-tjänster.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: kumud
ms.openlocfilehash: d5f52829f5895b30afd160cc8ded755332aca5c5
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190171"
---
# <a name="outbound-connections-in-azure"></a>Utgående anslutningar i Azure

Azure ger utgående anslutning för distributioner med flera olika sätt. Den här artikeln beskrivs vad de är, när de använder, hur de fungerar och hur du hanterar dem.

>[!NOTE] 
>Den här artikeln beskriver Resource Manager-distributioner. Granska [utgående anslutningar (klassiska)](load-balancer-outbound-connections-classic.md) för alla distributionsscenarier för klassisk i Azure.

En distribution i Azure kan kommunicera med slutpunkter utanför Azure i det offentliga IP-adressutrymmet. När en instans initierar en utgående flöde till ett mål i det offentliga IP-adressutrymmet kan matchar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När du har skapat den här mappningen kan Returtrafiken för det här utgående Start flödet också nå privata IP-adress som flödet kom från.

Översättning av källnätverksadresser (SNAT) använder Azure för att utföra den här funktionen. När flera privata IP-adresser låtsas bakom en enda offentlig IP-adress, använder Azure [port adressöversättning (PAT)](#pat) att maskeras privata IP-adresser. Tillfälliga portar används för PATRIK och är [förallokerade](#preallocatedports) baserat på poolstorlek.

Det finns flera [utgående scenarier](#scenarios). Du kan kombinera dessa scenarier efter behov. Granska dem noggrant för att förstå de funktioner, begränsningar och mönster som de gäller för din distributionsmodell och programscenariot. Granska vägledning för [hantera dessa scenarier](#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer och offentlig IP innehåller nya funktioner och olika beteenden till utgående anslutning.  De är inte samma som grundläggande SKU: er.  Om du vill utgående anslutning när du arbetar med Standard-SKU: er, måste du explicit definiera den antingen med offentliga IP-adresser eller offentlig Load Balancer.  Detta innefattar att skapa utgående anslutning när du använder och interna Standard Load Balancer.  Vi rekommenderar att du alltid använda regler för utgående trafik på en offentlig Standardbelastningsutjämnare.  [Scenario 3](#defaultsnat) är inte tillgänglig med Standard-SKU.  Det innebär att när en intern Standardbelastningsutjämnare används, måste du vidta åtgärder för att skapa utgående anslutning för de virtuella datorerna i serverdelspoolen om utgående anslutningar är det önskade.  I samband med utgående anslutning, en enda fristående virtuell dator, alla Virtuella datorer i en Tillgänglighetsuppsättning, fungerar alla instanser i en VMSS som en grupp. Det innebär att om en enskild virtuell dator i en Tillgänglighetsuppsättning är associerad med en Standard-SKU, alla VM-instanser i den här Tillgänglighetsuppsättningen nu bete sig av samma som om de är associerade med Standard-SKU, även om en enskild instans inte är direkt kopplade till den.  Noga igenom hela dokumentet att förstå de övergripande begrepp, granska [Standardbelastningsutjämnare](load-balancer-standard-overview.md) efter skillnader mellan SKU: er och granska [utgående regler](load-balancer-outbound-rules-overview.md).  Med utgående regler kan du de detaljerade kontroll över alla aspekter av utgående anslutning.

## <a name="scenarios"></a>Scenarioöversikt

Azure Load Balancer och relaterade resurser definieras uttryckligen när du använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Azure tillhandahåller för närvarande tre olika metoder för att uppnå utgående anslutning för Azure Resource Manager-resurser. 

| SKU:er | Scenario | Metod | IP-protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Standard, Basic | [1. Virtuell dator med en offentlig IP på instansnivå-adress (med eller utan belastningsutjämnaren)](#ilpip) | SNAT, port låtsas inte används | TCP, UDP, ICMP, ESP | Azure använder offentlig IP-adress som tilldelats IP-adresskonfigurationen för nätverkskortet för den instansen. Instansen har alla tillfälliga portar som är tillgängliga. När du använder Standard Load Balancer, bör du använda [utgående regler](load-balancer-outbound-rules-overview.md) att explicit definiera utgående anslutning |
| Standard, Basic | [2. Offentlig Load Balancer som är associerade med en virtuell dator (ingen offentlig IP på instansnivå adress på-instansen)](#lb) | SNAT med port låtsas (PAT) med Load Balancer-klienter | TCP, UDP |Azure delar offentliga IP-adressen för den offentliga belastningsutjämnare klienter med flera privata IP-adresser. Azure använder tillfälliga portar för klienter att TILLDELNINGEN. |
| Ingen eller Basic | [3. Standalone VM (någon belastningsutjämnare, ingen offentlig IP på instansnivå adress)](#defaultsnat) | SNAT med port låtsas (PAT) | TCP, UDP | Azure automatiskt utser en offentlig IP-adress för SNAT, delar den här offentliga IP-adressen med flera privata IP-adresser för tillgänglighetsuppsättningen och använder tillfälliga portar för den här offentliga IP-adressen. Det här scenariot är en reservprincip för föregående scenarier. Det rekommenderas inte om du behöver synlighet och kontroll. |

Om du inte vill att en virtuell dator för att kommunicera med slutpunkter utanför Azure i offentliga IP-adressutrymme, kan du använda nätverkssäkerhetsgrupper (NSG) för att blockera åtkomst efter behov. Avsnittet [förhindrar utgående anslutning](#preventoutbound) beskriver NSG: er i mer detalj. Vägledning för designa, implementera och hantera ett virtuellt nätverk utan någon utgående åtkomst är utanför omfånget för den här artikeln.

### <a name="ilpip"></a>Scenario 1: Virtuell dator med en offentlig IP på instansnivå-adress

Den virtuella datorn har en instans på offentliga IP (ILPIP) tilldelade till den i det här scenariot. Utgående anslutningar är fråga det spelar ingen roll om den virtuella datorn är belastningsutjämnad eller inte. Det här scenariot har företräde framför de andra. När du använder en ILPIP, använder den virtuella datorn ILPIP för alla utgående flöden.  

En offentlig IP-adress som tilldelats till en virtuell dator är en 1:1-relation (i stället för 1: många) och implementeras som en tillståndslös 1:1-NAT.  Port maskering (PAT) används inte och den virtuella datorn har alla tillfälliga portar som är tillgängliga för användning.

Om ditt program initierar många utgående flöden och du får använda SNAT portöverbelastning kan vara bra att tilldela en [ILPIP minimera SNAT begränsningar](#assignilpip). Granska [hantera SNAT överbelastning](#snatexhaust) i sin helhet.

### <a name="lb"></a>Scenario 2: Belastningsutjämnad virtuell dator utan en offentlig IP på instansnivå-adress

Den virtuella datorn är en del av en offentlig Load Balancer-backend-adresspool i det här scenariot. Den virtuella datorn har inte en offentlig IP-adress som tilldelats. Load Balancer-resursen måste konfigureras med en regel för belastningsutjämnaren för att skapa en länk mellan den offentliga IP-klientdelen till backend-poolen.

Om du inte slutföra den här regelkonfigurationen, beteendet är enligt beskrivningen i scenariot för [Standalone VM med ingen offentlig IP på instansnivå](#defaultsnat). Det är inte nödvändigt för att regeln ska ha en fungerande lyssnare i serverdelspoolen för hälsoavsökningen ska lyckas.

När den belastningsutjämnade virtuella datorn skapar en flöde för utgående, omvandlar Azure privata IP-källadressen för utgående flödet till offentliga IP-adressen för offentlig Load Balancer klientdelen. Azure använder SNAT för att utföra den här funktionen. Azure använder också [känna dig NÖJD](#pat) att maskeras flera privata IP-adresser bakom en offentlig IP-adress. 

Tillfälliga belastningsutjämnarens offentliga IP-adress frontend-portar används för att särskilja enskilda flöden som har sitt ursprung av den virtuella datorn. SNAT använder dynamiskt [förallokerade tillfälliga portar](#preallocatedports) när utgående flöden skapas. I det här sammanhanget kallas tillfälliga portarna som används för SNAT SNAT portar.

Tilldelas före SNAT portar som beskrivs i den [förstå SNAT och PAT](#snat) avsnittet. Det är en begränsad resurs som kan vara förbrukat. Det är viktigt att förstå hur de är [förbrukas](#pat). Om du vill lära dig mer om att utforma för den här förbrukning och minimera efter behov, granska [hantera SNAT överbelastning](#snatexhaust).

När [flera offentliga IP-adresser som är associerade med Load Balancer grundläggande](load-balancer-multivip-overview.md)något av de här offentliga IP-adresser är en kandidat för utgående flöden och en väljs slumpmässigt.  

Du kan använda för att övervaka hälsotillståndet för utgående anslutningar med Load Balancer grundläggande [Azure Monitor-loggar för belastningsutjämnaren](load-balancer-monitor-log.md) och [Avisera händelseloggar](load-balancer-monitor-log.md#alert-event-log) att övervaka SNAT port överbelastning meddelanden.

### <a name="defaultsnat"></a>Scenario 3: Standalone VM utan en offentlig IP på instansnivå-adress

I det här scenariot den virtuella datorn är inte en del av en offentlig belastningsutjämnare pool (och inte en del av en intern Standard Load Balancer-pool) och har inte tilldelats en ILPIP-adress. När den virtuella datorn skapar en flöde för utgående, omvandlar Azure privata IP-källadressen för utgående flödet till en offentlig IP-källadressen. Offentliga IP-adress som används för det här utgående flödet kan inte konfigureras och räknas inte mot prenumerationens gräns för offentlig IP. Den här offentliga IP-adressen hör inte till dig och kan inte reserverade. Om du distribuerar om virtuell dator eller Tillgänglighetsuppsättning eller VM-skalningsuppsättning, den här offentliga IP-adressen kommer att släppas och en ny offentlig IP-adress som efterfrågas. Använd inte det här scenariot för lista över tillåtna IP-adresser. Använd istället en av de andra två scenarierna där du uttryckligen deklarera utgående scenariot och offentliga IP-adress som ska användas för utgående anslutningar.

>[!IMPORTANT] 
>Det här scenariot gäller även när __endast__ en intern belastningsutjämnare är ansluten. Scenario 3 är __inte tillgänglig__ när en intern Standard Load Balancer är kopplad till en virtuell dator.  Du måste uttryckligen skapa [scenario 1](#ilpip) eller [scenario 2](#lb) förutom att använda en intern Standard Load Balancer.

Azure använder SNAT med port låtsas ([känna dig NÖJD](#pat)) att utföra den här funktionen. Det här scenariot liknar [scenario 2](#lb), förutom att det finns ingen kontroll över IP-adress som används. Detta är en återställningsplats scenariot för när scenarier 1 och 2 finns inte. Vi rekommenderar inte det här scenariot om du vill ha kontroll över den utgående adressen. Om utgående anslutningar är en viktig del av ditt program, bör du välja ett annat scenario.

SNAT portar är förallokerade enligt beskrivningen i den [förstå SNAT och PAT](#snat) avsnittet.  Hur många virtuella datorer delar en Tillgänglighetsuppsättning avgör vilken Förallokering nivå gäller.  En fristående virtuell dator utan en Tillgänglighetsuppsättning är en pool 1 för att fastställa Förallokering (1024 SNAT portar). Det finns en begränsad resurs som kan vara förbrukat SNAT portar. Det är viktigt att förstå hur de är [förbrukas](#pat). Om du vill lära dig mer om att utforma för den här förbrukning och minimera efter behov, granska [hantera SNAT överbelastning](#snatexhaust).

### <a name="combinations"></a>Scenarier med flera, kombinerade

Du kan kombinera de scenarier som beskrivs i föregående avsnitt för att uppnå ett visst resultat. När det finns flera scenarier, gäller en prioritetsordning: [scenario 1](#ilpip) har företräde framför [scenario 2](#lb) och [3](#defaultsnat). [Scenario 2](#lb) åsidosätter [scenario 3](#defaultsnat).

Ett exempel är en Azure Resource Manager-distribution där programmet kraftigt beroende av utgående anslutningar till ett begränsat antal mål men också tar emot inkommande flöden via en klientdel för belastningsutjämnare. I det här fallet kan du kombinera scenarier 1 och 2 för befrielse. För ytterligare mönster över [hantera SNAT överbelastning](#snatexhaust).

### <a name="multife"></a> Flera klienter för utgående flöden

#### <a name="standard-load-balancer"></a>Standard Load Balancer

Standard Load Balancer använder alla kandidater för utgående flöden på samma tid då [flera (offentliga) IP-klienter](load-balancer-multivip-overview.md) finns. Varje klientdel multiplicerar antalet tillgängliga portar för förallokerade SNAT om en belastningsutjämningsregel har aktiverats för utgående anslutningar.

Du kan välja att ignorera en frontend IP-adress används för utgående anslutningar med ett nytt belastningen belastningsutjämning regelalternativ:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalt sett den `disableOutboundSnat` alternativet standard _FALSKT_ och innebär det att du som den här regeln program utgående SNAT för de associera virtuella datorerna i serverdelspoolen för regel för belastningsutjämning. Den `disableOutboundSnat` kan ändras till _SANT_ att förhindra att Load Balancer med associerade klientdelens IP-adress för utgående anslutningar för de virtuella datorerna i serverdelspoolen för den här regeln för belastningsutjämning.  Och du kan fortfarande också ange en specifik IP-adress för utgående flöden enligt beskrivningen i [flera kombineras scenarier](#combinations) samt.

#### <a name="load-balancer-basic"></a>Load Balancer Basic

Load Balancer grundläggande väljer en enda klientdel som ska användas för utgående flöden när [flera (offentliga) IP-klienter](load-balancer-multivip-overview.md) lämpar sig för utgående flöden. Det går inte att konfigurera det här alternativet, och du bör överväga val av algoritmen är slumpmässig. Du kan ange en specifik IP-adress för utgående flöden enligt beskrivningen i [flera kombineras scenarier](#combinations).

### <a name="az"></a> Tillgänglighetszoner

När du använder [Standardbelastningsutjämnare med Tillgänglighetszoner](load-balancer-standard-availability-zones.md), zonredundanta klientdelar kan ge zonredundant utgående SNAT-anslutningar och SNAT programmering överlever nedgångar zon fel.  När zonindelad klienter används, dela utgående SNAT-anslutningar öde med zonen som de tillhör.

## <a name="snat"></a>Förstå SNAT och PAT

### <a name="pat"></a>Port maskering SNAT (PAT)

När en offentlig belastningsutjämnare resurs är associerad med VM-instanser, skrivs om varje utgående anslutningskälla. Källan är har skrivits från virtuellt nätverk privat IP-adressutrymme klientdelen offentliga IP-adressen för belastningsutjämnaren. 5-tuppel av flödet (källans IP-adress, källport, IP-transportprotokollet, målets IP-adress, målport) måste vara unikt i det offentliga IP-adressutrymmet.  Port maskering SNAT kan användas med antingen TCP eller UDP IP-protokoll.

Tillfälliga portar (SNAT) används för att uppnå detta efter att skriva om privata källans IP-adress, eftersom flera flöden kommer från en offentlig IP-adress. Algoritmen port maskering SNAT allokerar SNAT portar på olika sätt för UDP och TCP.

#### <a name="tcp"></a>SNAT-TCP-portar

En SNAT port förbrukas per flöde till en enda mål IP-adress, port. För flera TCP-flöden till samma mål-IP-adress, port och protokoll förbrukar varje TCP-flöde en enskild SNAT-port. Detta säkerställer att flöden är unika när de kommer från samma offentliga IP-adress och går till samma mål-IP-adress, port och protokoll. 

Flera flöden, var och en till en annan målets IP-adress, port och protokoll, dela en enda SNAT-port. Den målets IP-adress, port och protokoll göra flöden unikt utan att behöva ytterligare källa portar för att skilja flöden i det offentliga IP-adressutrymmet.

#### <a name="udp"></a> UDP-portar som SNAT

SNAT UDP-portar som hanteras av en annan algoritm än TCP SNAT portar.  Belastningsutjämnaren använder en algoritm som kallas ”begränsade via port kon NAT” för UDP.  En SNAT port används för varje flöde, oavsett målets IP-adress, port.

#### <a name="exhaustion"></a>Resursuttömning

När SNAT port resurser tömts misslyckas utgående flöden tills befintliga flöden släpper SNAT portar. Belastningsutjämnaren återtar SNAT portar när flödet stängs och använder en [4-minuters timeout för inaktivitet](#idletimeout) Frigör SNAT portar från inaktiv flöden.

UDP SNAT portar använt vanligtvis mycket snabbare än TCP SNAT portar på grund av skillnader i algoritmen. Du måste design och skala testa med denna skillnad i åtanke.

Mönster att minimera villkor som ofta leder till att använda SNAT portöverbelastning, granska de [hantera SNAT](#snatexhaust) avsnittet.

### <a name="preallocatedports"></a>Tillfällig port Förallokering för port låtsas SNAT (PAT)

Azure använder en algoritm och fastställa antalet förallokerade SNAT portar som är tillgängliga baserat på storleken på backend-poolen när du använder port maskering SNAT ([känna dig NÖJD](#pat)). Det finns tillfälliga portar som är tillgängliga för en viss offentliga IP-källadress SNAT portar.

Samma antal SNAT portar förallokerade för UDP och TCP respektive och förbrukas separat per IP-transport-protokoll.  Men är portanvändning SNAT olika beroende på om flödet är UDP eller TCP.

>[!IMPORTANT]
>Standard-SKU SNAT programming per IP-transportprotokollet och härleds från belastningsutjämningsregel.  Om det bara finns en TCP-belastningsutjämningsregel är SNAT endast tillgängligt för TCP. Om du har endast en TCP regel för belastningsutjämning och behöver utgående SNAT för UDP, skapar du en UDP belastningsutjämningsregel från samma klientdelen i samma backend-poolen.  Detta utlöser SNAT programmera för UDP.  En fungerande regel eller hälsotillstånd avsökning krävs inte.  Grundläggande SKU SNAT program alltid SNAT för båda IP-transport-protokoll, oavsett vilken transportprotokoll som anges i regeln för belastningsutjämning.

Azure preallocates SNAT portar till IP-konfigurationen för nätverkskortet för varje virtuell dator. När en IP-konfiguration läggs till i poolen, förallokerade SNAT-portar för den här IP-konfigurationen baserat på backend-pool-storlek. När utgående flöden har skapats, [känna dig NÖJD](#pat) dynamiskt förbrukar (högst förallokerade) och släpper dessa portar när flödet stängs eller [inaktiv tidsgränser](#idletimeout) inträffa.

I följande tabell visas SNAT port preallocations för nivåerna för backend-poolstorlekar:

| Poolstorlek (VM-instanser) | Den förallokerade SNAT portar per IP-konfiguration|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> När du använder Standard Load Balancer med [flera klienter](load-balancer-multivip-overview.md), varje frontend IP-adress multiplicerar antalet tillgängliga SNAT portar i föregående tabell. Till exempel använder en serverdelspool med 50 Virtuella datorer med 2 belastningsutjämningsregler, var och en med en separat frontend IP-adress, 2048 (2 x 1 024) SNAT portar per IP-konfiguration. Visa detaljer för [flera klienter](#multife).

Kom ihåg att antalet tillgängliga portar för SNAT inte översätter direkt till antal flöden. En enskild SNAT-port kan återanvändas för flera unika mål. Portar används endast om det är nödvändigt att göra flöden som är unikt. Riktlinjer för design och problemlösning, finns i avsnittet om [hur du hanterar icke förnybara resursen](#snatexhaust) och de avsnitt som beskriver [känna dig NÖJD](#pat).

Ändra storlek på backend-pool kan påverka vissa av dina etablerade flöden. Om backend-pool-storlek ökar och övergår till nästa nivå, är hälften av din förallokerade SNAT portar frigöras under övergången till nästa större serverdelen pool nivå. Flöden som är associerade med en återvunnet SNAT port når tidsgränsen och måste återskapas. Om ett nytt flöde utförs lyckas flödet omedelbart så länge det finns tillgängliga förallokerade portar.

Om backend-pool-storlek minskas och övergår till en lägre nivå, ökar antalet tillgängliga SNAT-portar. I det här fallet befintliga tilldelade SNAT portar och deras respektive flöden påverkas inte.

SNAT portar allokeringar är IP-transportprotokollet specifika (TCP och UDP underhålls separat) och frigörs under följande förhållanden:

### <a name="tcp-snat-port-release"></a>Versionen för SNAT TCP-port

- Om antingen servern/klienten skickar FINACK, kommer SNAT port att släppas efter 240 sekunder.
- Om en RSTA visas kommer SNAT port att släppas efter 15 sekunder.
- Om tidsgränsen för inaktivitet har uppnåtts, släpps port.

### <a name="udp-snat-port-release"></a>Versionen för SNAT UDP-port

- Om tidsgränsen för inaktivitet har uppnåtts, släpps port.

## <a name="problemsolving"></a> Problemlösning 

Det här avsnittet är avsedd att hjälpa dig att undvika överbelastning av SNAT och som kan uppstå med utgående anslutningar i Azure.

### <a name="snatexhaust"></a> Hantera portöverbelastning SNAT (PAT)
[Tillfälliga portar](#preallocatedports) används för [känna dig NÖJD](#pat) är en icke förnybara resurs, enligt beskrivningen i [Standalone VM utan en offentlig IP på instansnivå adress](#defaultsnat) och [belastningsutjämnad virtuell dator utan en Instans på offentlig IP-adress](#lb).

Om du vet att du initierar många utgående TCP eller UDP-anslutningar till samma IP-måladress och port och du se misslyckade utgående anslutningar eller bör av support som du är förbrukar SNAT-portar (förallokerade [tillfälliga portar](#preallocatedports) används av [känna dig NÖJD](#pat)), har du flera alternativ för Allmänt skydd. Granska alternativen och bestämma vad som är tillgängliga och bäst för ditt scenario. Det är möjligt att en eller flera kan hantera det här scenariot.

Om du har problem med att förstå beteendet för utgående anslutningar kan använda du IP-stack-statistik (netstat). Eller det kan vara bra att Observera anslutningslägen genom att använda infångade paket. Du kan utföra dessa infångade paket i gästoperativsystemet för din instans eller använda [Network Watcher för paketfångsten](../network-watcher/network-watcher-packet-capture-manage-portal.md).

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
Tilldela en ILPIP ändras ditt scenario till [offentlig IP på instansnivå till en virtuell dator](#ilpip). Alla tillfälliga portar för den offentliga IP-Adressen som används för varje virtuell dator är tillgängliga för den virtuella datorn. (Till skillnad från scenarier där tillfälliga portar på en offentlig IP-adress som delas med alla virtuella datorer som är associerade med respektive serverdelspoolen.) Det finns avvägningarna att tänka på, till exempel den extra kostnaden för offentliga IP-adresser och den potentiella effekten av ett stort antal enskilda IP-adresser för listan över tillåtna program.

>[!NOTE] 
>Det här alternativet är inte tillgänglig för web worker-roller.

#### <a name="multifesnat"></a>Använd flera klienter

När du använder offentlig Standard Load Balancer kan du tilldela [flera frontend-IP-adresser för utgående anslutningar](#multife) och [multiplicera antalet SNAT-portar som är tillgängliga](#preallocatedports).  Skapa en klientdelens IP-konfiguration och regeln serverdelspool att utlösa programmeringen av SNAT till offentliga IP-Adressen för klientdelen.  Regeln behöver inte funktionen och en hälsoavsökning behöver inte lyckas.  Om du använder flera klienter för inkommande samt (i stället för bara för utgående), bör du använda anpassade hälsotillståndsavsökningar bra för att säkerställa tillförlitligheten.

>[!NOTE]
>I de flesta fall är överbelastning av SNAT portar ett tecken på designen.  Kontrollera att du förstår varför du finns förbrukar portar innan du använder flera klienter för att lägga till SNAT portar.  Du kan maskera problem som kan leda till fel senare.

#### <a name="scaleout"></a>Skala ut

[Förallokerade portar](#preallocatedports) tilldelas baserat på storleken för backend-poolen och grupperade i nivåerna för att minimera störningar om några av portarna måste allokeras om för att tillgodose nästa större serverdelen pool storlek nivå.  Du kan ha ett alternativ för att öka intensiteten av användning av SNAT-port för en given frontend genom att skala backend-pool till den maximala storleken för en viss nivå.  Detta kräver att programmet effektivt skala ut.

Två virtuella datorer i serverdelspoolen skulle till exempel ha 1024 SNAT-portar som är tillgängliga per IP-konfiguration, vilket gör att totalt 2048 SNAT portar för distributionen.  Om distributionen ökas till 50 virtuella kan datorer, även om antalet förallokerade portar är konstant per virtuell dator, totalt 51,200 (50 x 1 024) SNAT portar användas för distributionen.  Om du vill skala upp distributionen kontrollera antalet [förallokerade portar](#preallocatedports) per nivå för att kontrollera att du formar din skala ut till den maximala storleken för respektive nivå.  I föregående exempel, om du har valt att skala ut till 51 i stället för 50 instanser skulle du vidare till nästa nivå och slutar upp med färre SNAT portar per virtuell dator samt som totalt.

Om du skala ut till nästa större serverdelen pool storlek nivå finns potentiella för några av dina utgående anslutningar till timeout om allokerade portar måste allokeras.  Om du bara använder vissa av dina SNAT-portar, har skala ut över nästa större serverdelen poolstorleken ingen betydelse.  Halva befintliga portar allokeras till varje gång som du flyttar till nästa nivå för backend-poolen.  Om du inte vill att äga rum, måste du utformar din distribution till nivån storlek.  Eller kontrollera att ditt program kan identifiera och försök igen efter behov.  TCP keepalive-överföringar kan hjälpa dig i identifiera när SNAT portar inte längre funktion på grund av att tilldelas på nytt.

### <a name="idletimeout"></a>Använda keepalive-överföringar för att återställa utgående tidsgränsen för inaktivitet

Utgående anslutningar har ett 4-minuters timeout för inaktivitet. Den här timeouten kan inte ställas in. Du kan dock använda transport (till exempel TCP keepalive-överföringar) eller programnivå keepalive-överföringar för att uppdatera ett flöde för inaktiva och återställer den här tidsgränsen för inaktivitet om det behövs.  

När du använder TCP keepalive-överföringar, räcker det att aktivera dem på ena sidan av anslutningen. Till exempel räcker det att aktivera dem på serversidan endast att återställa timern för flödet och det är inte nödvändigt för båda sidorna att initierad TCP keepalive-överföringar.  Det finns liknande koncept för programnivån, inklusive konfigurationer för klient-server.  Kontrollera serversidan för vilka alternativ finns för programmet specifika keepalive-överföringar.

## <a name="discoveroutbound"></a>Identifiera den offentliga IP-Adressen som använder en virtuell dator
Det finns många sätt att avgöra offentliga IP-källadressen för en utgående anslutning. OpenDNS är en tjänst som kan visa offentliga IP-adressen för den virtuella datorn. 

Du kan skicka en DNS-fråga för namnet myip.opendns.com till OpenDNS matcharen genom att använda nslookup-kommando. Tjänsten returnerar källans IP-adress som används för att skicka frågan. När du kör följande fråga från den virtuella datorn är svaret offentlig IP-adress som används för den virtuella datorn:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Det gick inte att utgående anslutning
Ibland är det oönskade för en virtuell dator för att kunna skapa en flöde för utgående. Det kan finnas ett krav att hantera vilka mål som kan nås med utgående flöden eller vilka mål kan börja inkommande flöden. I det här fallet kan du använda [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) att hantera de mål som kan nå ut till den virtuella datorn. Du kan också använda NSG: er för att hantera vilka offentliga mål kan initiera inkommande flöden.

När du använder en NSG till en belastningsutjämnad virtuell dator så kan du vara uppmärksam på den [tjänsttaggar](../virtual-network/security-overview.md#service-tags) och [standardsäkerhetsregler](../virtual-network/security-overview.md#default-security-rules). Du måste se till att den virtuella datorn kan ta emot hälsotillstånd avsökningen begäranden från Azure-belastningsutjämnaren. 

Om en Nätverkssäkerhetsgrupp blockerar hälsotillstånd avsökningen begäranden från Standardetiketten AZURE_LOADBALANCER, dina VM-hälsoavsökning misslyckas och den virtuella datorn markeras. Slutar belastningsutjämnaren om du vill att skicka nya flöden till den virtuella datorn.

## <a name="limitations"></a>Begränsningar
- DisableOutboundSnat är inte tillgänglig som ett alternativ när du konfigurerar en belastningsutjämningsregel i portalen.  Använd REST, mallen eller klienten verktyg i stället.
- Webbarbetsroller utan ett virtuellt nätverk och andra Microsoft-plattformstjänster kan vara tillgängliga när bara en intern Standardbelastningsutjämnare används på grund av en sidoeffekt från hur pre-VNet-tjänster och andra plattformar tjänster funktion. Förlita dig inte på den här sidoeffekt som respektive själva tjänsten och den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du behöver skapa utgående anslutning uttryckligen om du vill när du använder en intern Standard Load Balancer endast. Den [standard SNAT](#defaultsnat) scenario 3 i den här artikeln är inte tillgänglig.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md) för offentlig Load Balancer.
- Läs mer om [belastningsutjämnaren](load-balancer-overview.md).
- Läs mer om [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).
- Lär dig mer om den andra nyckeln [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
