---
title: Utgående anslutningar i Azure | Microsoft Docs
description: Den här artikeln förklarar hur Azure kan användas för virtuella datorer att kommunicera med offentliga internet-tjänster.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2018
ms.author: kumud
ms.openlocfilehash: 5cff443ac3bbd89a2245e7adb21458ecc62fd494
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="outbound-connections-in-azure"></a>Utgående anslutningar i Azure

Azure tillhandahåller utgående anslutning för kunddistributioner med flera olika mekanismer. Den här artikeln beskriver vad de är, när de använder, hur de fungerar och hur de hanteras.

>[!NOTE] 
>Den här artikeln beskriver Resource Manager-distributioner. Granska [utgående anslutningar (klassisk)](load-balancer-outbound-connections-classic.md) för alla distributionsscenarier för klassisk i Azure.

En distribution i Azure kan kommunicera med slutpunkter utanför Azure i det offentliga IP-adressutrymmet. När en instans initierar ett utgående flöde till ett mål i det offentliga IP-adressutrymmet, matchar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När du har skapat den här mappningen kan returnerade trafik för den här utgående har sitt ursprung flöde också nå den privata IP-adressen som flödet kom från.

Azure använder källa nätverksadresser (SNAT) för att utföra den här funktionen. När flera privata IP-adresser utger sig bakom en offentlig IP-adress, använder Azure [port nätverksadresser (PATRIK)](#pat) till maskeras privata IP-adresser. Tillfälliga portar används för PATRIK och är [förallokerade](#preallocatedports) baserat på poolstorleken.

Det finns flera [utgående scenarier](#scenarios). Du kan kombinera dessa scenarier efter behov. Läs dem noga för att förstå funktioner, begränsningar och mönster som de gäller för din distributionsmodell och program. Granska vägledning för [hantera dessa scenarier](#snatexhaust).

>[!IMPORTANT] 
>Standard belastningsutjämnaren introduceras nya funktioner och olika beteenden till utgående anslutning.   Till exempel [scenario 3](#defaultsnat) finns inte när en intern Standard belastningsutjämnare och olika steg som krävs.   Granska noggrant hela dokumentet för att förstå den övergripande begrepp och skillnader mellan SKU: er.

## <a name="scenarios"></a>Scenario-översikt

Azure belastningsutjämnare och relaterade resurser definieras explicit när du använder [Azure Resource Manager](#arm).  Azure tillhandahåller tre olika metoder för att uppnå utgående anslutning för Azure Resource Manager-resurser för närvarande. 

| Scenario | Metod | IP-protokoll | Beskrivning |
| --- | --- | --- | --- |
| [1. Virtuell dator med en offentlig IP på instansnivå adress (med eller utan belastningsutjämnare)](#ilpip) | SNAT, utger sig för porten inte används | TCP, UDP, ICMP, ESP | Azure använder offentlig IP-adress som tilldelats IP-adresskonfigurationen för nätverkskortet för den instansen. Instansen har alla tillfälliga portar som är tillgängliga. |
| [2. Offentliga belastningsutjämnare som är kopplad till en virtuell dator (ingen offentlig IP på instansnivå adress på instansen)](#lb) | SNAT med port låtsas (PAT) med hjälp av belastningsutjämnaren frontends | TCP OCH UDP |Azure delar offentliga IP-adressen för den offentliga belastningsutjämnare frontends med flera privata IP-adresser. Azure använder tillfälliga portarna frontends att TILLDELNINGEN. |
| [3. Standalone VM (någon belastningsutjämnare, ingen offentlig IP på instansnivå adress)](#defaultsnat) | SNAT med port låtsas (PAT) | TCP OCH UDP | Azure automatiskt utser en offentlig IP-adress för SNAT delar den här offentliga IP-adressen med flera privata IP-adresser för tillgänglighetsuppsättningen och använder tillfälliga portar för den här offentliga IP-adressen. Det här är en återställningsplats scenario för föregående scenarier. Vi rekommenderar inte den om du behöver synlighet och kontroll. |

Du kan använda nätverkssäkerhetsgrupper (NSG: er) för att blockera åtkomst efter behov om du inte vill att en virtuell dator för att kommunicera med slutpunkter utanför Azure i offentliga IP-adressutrymme. Avsnittet [förhindrar en utgående anslutning](#preventoutbound) NSG: er beskrivs i detalj. Anvisningar om hur man designar, implementera och hantera ett virtuellt nätverk utan utgående åtkomst är utanför omfånget för den här artikeln.

### <a name="ilpip"></a>Scenario 1: Virtuell dator med en offentlig IP på instansnivå adress

Den virtuella datorn har en instans nivå offentliga IP-går kopplade till den i det här scenariot. Utgående anslutningar är fråga det spelar ingen roll om den virtuella datorn är belastningsutjämnad eller inte. Det här scenariot har företräde framför andra. När en går används för den virtuella datorn använder går för alla utgående flöden.  

En offentlig IP som tilldelats till en virtuell dator är en 1:1 relation (inte 1:many) och implementeras som en tillståndslös 1:1-NAT.  Port imiterade (PATRIK) används inte och den virtuella datorn har alla tillfälliga portar som är tillgängliga för användning.

Om ditt program initierar många utgående flöden och SNAT port resursöverbelastning uppstår, kan du överväga att tilldela en [går att minimera SNAT begränsningar](#assignilpip). Granska [hantera SNAT resursuttömning](#snatexhaust) i sin helhet.

### <a name="lb"></a>Scenario 2: Belastningsutjämnade virtuella datorn utan en offentlig IP på instansnivå adress

I det här scenariot är en del av en offentlig belastningsutjämnare serverdelspool i den virtuella datorn. Den virtuella datorn har inte en offentlig IP-adress som tilldelats. Resursen belastningsutjämnare måste konfigureras med en regel för belastningsutjämnare för att skapa en länk mellan den offentliga IP-klientdelen med serverdelspoolen.

Om du inte slutföra den här regelkonfigurationen, beteendet är enligt beskrivningen i scenariot för [Standalone VM med ingen offentlig IP på instansnivå](#defaultsnat). Det är inte nödvändigt för att regeln ska ha en fungerande lyssnare i serverdelspoolen för hälsoavsökningen ska lyckas.

När den belastningsutjämnade virtuella datorn skapar ett utgående flöde, översätter Azure privata källans IP-adress för utgående offentliga belastningsutjämnare klientdel offentliga IP-adressen. Azure använder SNAT för att utföra den här funktionen. Azure använder också [klappa lätt](#pat) ska maskeras flera privata IP-adresser bakom en offentlig IP-adress. 

Tillfälliga portar för belastningsutjämnarens offentliga IP-adress klientdel används för att särskilja enskilda flöden av den virtuella datorn har sitt ursprung. SNAT dynamiskt använder [förallokerade tillfälliga portar](#preallocatedports) när utgående flöden skapas. I den här kontexten kallas tillfälliga portarna som används för SNAT SNAT portar.

SNAT portar är förallokerade enligt beskrivningen i den [förstå SNAT och PATRIK](#snat) avsnitt. Det är en begränsad resurs som kan vara slut. Det är viktigt att förstå hur de är [används](#pat). För att förstå hur du designar för denna förbrukning och minska efter behov, granska [hantera SNAT resursuttömning](#snatexhaust).

När [flera (offentliga) IP-adresser som är associerade med Load Balancer grundläggande](load-balancer-multivip-overview.md), någon av dessa offentliga IP-adresser är en [kandidat för utgående flöden](#multivipsnat), och en har valts.  

Du kan använda för att övervaka hälsotillståndet för utgående anslutningar med Load Balancer grundläggande [Log Analytics för belastningsutjämnaren](load-balancer-monitor-log.md) och [Varna händelseloggar](load-balancer-monitor-log.md#alert-event-log) att övervaka SNAT port uttömning meddelanden.

### <a name="defaultsnat"></a>Scenario 3: Standalone VM utan en offentlig IP på instansnivå adress

I det här scenariot den virtuella datorn är inte en del av offentliga belastningsutjämnare pool (och inte en del av en intern Standard belastningsutjämnaren pool) och har inte tilldelats går adress. När den virtuella datorn skapar ett utgående flöde, översätter Azure privata källans IP-adress för utgående begränsas till en offentlig IP-källadress. Den offentliga IP-adressen som används för detta utgående flöde kan inte konfigureras och räknas inte mot prenumerationens offentliga IP-resursgräns.

>[!IMPORTANT] 
>Det här scenariot gäller när __endast__ en enkel intern belastningsutjämnare är ansluten. Scenario 3 är __inte tillgänglig__ när en intern Standard belastningsutjämnare är kopplad till en virtuell dator.  Du måste uttryckligen skapa [scenario 1](#ilpip) eller [scenario 2](#lb) förutom att använda en intern Standard belastningsutjämnare.

Azure använder SNAT med utger sig för port ([klappa lätt](#pat)) att utföra den här funktionen. Det här scenariot liknar [scenario 2](#lb), förutom att det finns ingen kontroll över IP-adress som används. Det här är en återställningsplats scenario för när scenarier 1 och 2 finns inte. Vi rekommenderar inte det här scenariot om du vill ha kontroll över utgående adressen. Om utgående anslutningar är en viktig del av ditt program, bör du har valt ett annat scenario.

SNAT portar är förallokerade enligt beskrivningen i den [förstå SNAT och PATRIK](#snat) avsnitt.  Antalet virtuella datorer som delar en Tillgänglighetsuppsättning avgör vilken Förallokering nivå gäller.  En fristående virtuell dator utan en Tillgänglighetsuppsättning är en pool 1 för att fastställa Förallokering (1024 SNAT portar). Det finns en begränsad resurs som kan vara förbrukat SNAT portar. Det är viktigt att förstå hur de är [används](#pat). För att förstå hur du designar för denna förbrukning och minska efter behov, granska [hantera SNAT resursuttömning](#snatexhaust).

### <a name="combinations"></a>Flera kombinerade scenarier

Du kan kombinera scenarier som beskrivs i föregående avsnitt för att uppnå ett visst resultat. När det finns flera scenarier, gäller en prioritetsordning: [scenario 1](#ilpip) företräde framför [scenario 2](#lb) och [3](#defaultsnat). [Scenario 2](#lb) åsidosätter [scenario 3](#defaultsnat).

Ett exempel är en Azure Resource Manager distribution där programmet kraftigt beroende av utgående anslutningar till ett begränsat antal mål men också tar emot inkommande flöden via en klientdel på belastningsutjämnaren. I det här fallet kan du kombinera scenarier 1 och 2 för befrielse. Ytterligare mönster, granska [hantera SNAT resursuttömning](#snatexhaust).

### <a name="multife"></a> Flera frontends för utgående flöden

#### <a name="load-balancer-standard"></a>Load Balancer Standard

Load Balancer Standard använder alla kandidater för utgående flöden vid samma tidpunkt då [flera (offentliga) IP-frontends](load-balancer-multivip-overview.md) finns. Varje frontend multiplicerar antalet tillgängliga portar för förallokerade SNAT om en belastningsutjämningsregel har aktiverats för utgående anslutningar.

Du kan välja att ignorera en IP-adress för klientdel används för utgående anslutningar med en ny belastningen belastningsutjämning regelalternativ:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalt sett det här alternativet som standard _FALSKT_ och innebär att den här regeln program utgående SNAT för de associerade virtuella datorerna i serverdelspoolen av regeln för belastningsutjämning.  Detta kan ändras till _SANT_ att förhindra belastningsutjämnare använder associerade klientdelens IP-adress för utgående anslutningar för den virtuella datorn är i serverdelspoolen av den här regeln för belastningsutjämning.  Och du kan också ange en specifik IP-adress för utgående flöden enligt beskrivningen i [flera kombineras scenarier](#combinations) samt.

#### <a name="load-balancer-basic"></a>Läsa in belastningsutjämning Basic

Load Balancer grundläggande väljer en enda klientdel som ska användas för utgående flöden när [flera (offentliga) IP-frontends](load-balancer-multivip-overview.md) lämpar sig för utgående flöden. Det går inte att konfigurera det här alternativet och bör du val-algoritmen för att vara slumpmässigt. Du kan ange en specifik IP-adress för utgående flöden enligt beskrivningen i [flera kombineras scenarier](#combinations).

### <a name="az"></a> Tillgänglighet zoner

När du använder [Standard belastningsutjämnare med tillgänglighet zoner](load-balancer-standard-availability-zones.md)zonredundant frontends kan ge zonredundant utgående SNAT anslutningar och SNAT programmering FRISKRIVNINGEN zon fel.  När zonal frontends används dela utgående SNAT anslutningar omvandling med zonen som de tillhör.

## <a name="snat"></a>Förstå SNAT och PATRIK

### <a name="pat"></a>Port imiterade SNAT (PATRIK)

När en offentlig belastningsutjämnare resurs associeras med VM-instanser, om varje utgående anslutningskälla. Källan om från det virtuella privata IP-adressutrymmet till klientdel offentliga IP-adressen för belastningsutjämnaren. 5-tuppel i flöde (källans IP-adress, källport, IP-transportprotokoll, mål-IP-adress, målport) måste vara unikt i det offentliga IP-adressutrymmet.  Port imiterade SNAT kan användas med TCP eller UDP IP-protokoll.

Tillfälliga portar SNAT används för att uppnå detta efter att skriva om privata källans IP-adress, eftersom flera flöden kommer från en offentlig IP-adress. 

En SNAT port används per flöde till en enda IP-adress, port och protokoll. För flera flödar till samma mål-IP-adress, port och protokoll använder varje flöde en enda SNAT port. Detta säkerställer att flödena är unika när de kommer från samma offentliga IP-adress och gå till samma mål-IP-adress, port och protokoll. 

Flera flöden, till en annan IP-adress, port och protokoll, dela en enda SNAT port. Mål-IP-adress, port och protokoll gör flöden unikt utan att behöva ytterligare källa portar för att skilja flödena i det offentliga IP-adressutrymmet.

När SNAT port resurserna är uttömda misslyckas utgående flöden tills befintliga flöden släpper SNAT portar. Belastningsutjämnaren återtar SNAT portar när flödet stängs och använder en [4-minuters timeout för inaktivitet](#idletimeout) frigöra SNAT portar från inaktiv flödena.

Mönster för att minimera villkor som vanligtvis leda till uttömning av SNAT port, granska den [hantera SNAT](#snatexhaust) avsnitt.

### <a name="preallocatedports"></a>Tillfälliga Förallokering för porten som utger sig för SNAT (PATRIK)

Azure använder en algoritm och fastställa antalet förallokerade SNAT tillgängliga portar baserat på storleken på serverdelspoolen när du använder port imiterade SNAT ([klappa lätt](#pat)). SNAT portar är tillfälliga portar som är tillgängliga för en viss offentlig IP-källadress.

Samma antal SNAT portar förallokerade för UDP och TCP respektive och konsumeras oberoende per IP-transportprotokollet. 

>[!IMPORTANT]
>Standard-SKU SNAT programmering per IP-transportprotokoll och härleds från belastningsutjämningsregel.  Om det bara finns en TCP-belastningsutjämningsregel är SNAT endast tillgängligt för TCP. Om du har bara en TCP regel för belastningsutjämning och behöver utgående SNAT för UDP, skapa en regel från samma klient till samma serverdelspoolen för UDP av belastningsutjämning.  Detta utlöser SNAT programmering för UDP.  En fungerande regeln eller hälsa avsökning krävs inte.  Grundläggande SKU SNAT program alltid SNAT för båda IP-transportprotokollet oavsett transportprotokoll som angetts i regeln för belastningsutjämning.

Azure preallocates SNAT portar till IP-konfigurationen för nätverkskort på varje virtuell dator. När en IP-konfiguration läggs till i poolen, förallokerade SNAT portar för den här IP-konfigurationen som baseras på backend-poolstorleken. När utgående flöden skapas [klappa lätt](#pat) dynamiskt förbrukar (högst förallokerade) och släpper de här portarna när flödet stängs eller [timeout vid inaktivitet](#ideltimeout) inträffa.

I följande tabell visas SNAT port preallocations för nivåerna för backend-pool storlekar:

| Poolstorleken (VM-instanser) | Förallokerade SNAT portar per IP-konfiguration|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101 200 | 256 |
| 201 400 | 128 |
| 401-800 | 64 |
| 801-1 000 | 32 |

>[!NOTE]
> När du använder Standard belastningsutjämnare med [flera frontends](load-balancer-multivip-overview.md), [varje IP-adress för klientdel multiplicerar antalet tillgängliga portar för SNAT](#multivipsnat) i föregående tabell. Till exempel använder en serverdelspool av 50 VM med 2 belastningsutjämningsregler, var och en med en separat klientdelens IP-adresser, 2048 (2 x 1 024) SNAT portar per IP-konfiguration. Visa detaljer för [flera frontends](#multife).

Kom ihåg att antalet tillgängliga portar för SNAT inte översätta direkt till antal flöden. En enskild port SNAT kan återanvändas för flera unika mål. Portar förbrukas endast om det är nödvändigt att göra flöden unika. Design och minskning instruktioner finns i avsnittet om [hantera icke förnybara resursen](#snatexhaust) och det avsnitt som beskriver [klappa lätt](#pat).

Ändra storlek på din serverdelspool kan påverka vissa av dina etablerade flöden. Om poolstorleken backend ökar och övergår till nästa nivå, är hälften av din förallokerade SNAT portar frigöras under övergången till nästa större backend poolen nivå. Flöden som är associerade med en återvunnet SNAT port gör timeout och måste återskapas. Om ett nytt flöde görs lyckas flödet omedelbart som finns tillgängliga förallokerade portar.

Om backend-poolstorleken minskar och övergår till en lägre nivå, ökar antalet tillgängliga SNAT portar. I det här fallet befintliga tilldelade SNAT portar och deras respektive flöden påverkas inte.

SNAT portar allokeringar är IP-transportprotokollet specifika (TCP och UDP underhålls separat) och publiceras på följande villkor:

### <a name="tcp-snat-port-release"></a>Versionen för SNAT TCP-port

- Om både servern eller-klienten skickar Finland-ACK, släpps SNAT port efter 240 sekunder.
- Om en RST visas släpps SNAT port efter 15 sekunder.
- Tidsgränsen för inaktivitet har nåtts

### <a name="udp-snat-port-release"></a>Versionen för SNAT UDP-port

- Tidsgränsen för inaktivitet har nåtts

## <a name="problemsolving"></a> Problemlösning 

Det här avsnittet är avsedd för att minska SNAT resursuttömning och andra scenarier som kan uppstå med utgående anslutningar i Azure.

### <a name="snatexhaust"></a> Hantera SNAT (PATRIK) port resursuttömning
[Tillfälliga portar](#preallocatedports) används för [klappa lätt](#pat) är en icke förnybara resurs, enligt beskrivningen i [Standalone VM utan en offentlig IP på instansnivå adress](#defaultsnat) och [belastningsutjämnade virtuella datorn utan en Instansen nivå offentliga IP-adressen](#lb).

Om du vet att du initiera många utgående TCP eller UDP-anslutningar till samma mål-IP-adress och port och du Observera misslyckas utgående anslutningar eller bör av support att du är lång körningstid förbrukar SNAT portar (förallokerade [tillfälliga portar](#preallocatedports) används av [klappa lätt](#pat)), har du flera alternativ för Allmänt skydd. Granska alternativen och bestämma vad som är tillgängliga och bäst för ditt scenario. Det är möjligt att en eller flera kan hantera det här scenariot.

Om du har svårt att förstå beteendet utgående anslutning kan använda du IP-stack-statistik (netstat). Eller så kan vara bra att se anslutningen beteenden med hjälp av paket insamlingar. Du kan utföra dessa paket insamlingar i gästoperativsystemet för din instans eller använda [Nätverksbevakaren för paketinsamling](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Ändra programmet att återanvända anslutningar 
Du kan minska behovet av tillfälliga portar som används för SNAT genom att återanvända anslutningar i ditt program. Detta gäller särskilt för protokoll som HTTP/1.1, där återanvändning av anslutningar är standard. Och andra protokoll som använder HTTP transport (till exempel REST) kan dra i sin tur. 

Återanvändning är alltid bättre än enskilda, atomiska TCP-anslutningar för varje begäran. Återanvända resulterar i flera performant, mycket effektivt TCP transaktioner.

#### <a name="connection pooling"></a>Ändra program att använda anslutningspooler
Du kan använda en anslutningspoolen schema i ditt program där förfrågningar internt är fördelade på en fast uppsättning anslutningar (varje återanvända där det är möjligt). Det här schemat begränsar antalet tillfälliga portar används och skapar en mer förutsägbar miljö. Det här schemat kan också öka genomflödet av begäranden genom att låta flera samtidiga åtgärder när en anslutning blockerar i svar för en åtgärd.  

Anslutningspooler kanske redan finns inom ramen som du använder för att utveckla ditt program eller konfigurationsinställningarna för tillämpningsprogrammet. Du kan kombinera anslutningspooler med återanvändning av anslutningar. Din flera begäranden sedan använda en fast, förutsägbar antalet portar till samma mål-IP-adress och port. Begäranden kan också dra nytta av effektiv användning av TCP-transaktioner som minskar svarstiden och Resursanvändning. UDP-transaktioner kan också dra eftersom hantera antal UDP flöden kan i sin tur undvika avgaser villkor och hantera hur SNAT port.

#### <a name="retry logic"></a>Ändra program att använda mindre aggressivt logik
När [förallokerade tillfälliga portar](#preallocatedports) används för [klappa lätt](#pat) är slut eller ett program fel uppstår, aggressivt eller brute force återförsök utan decay och backoff logik orsaka resursuttömning sker eller spara. Du kan minska behovet av tillfälliga portar med hjälp av en mindre aggressivt logik. 

Tillfälliga portar har ett 4-minuters timeout vid inaktivitet (inte justerbara). Om de nya försök för Aggressivt, har stoppas någon möjlighet att rensa på sin egen. Därför är överväger hur – och hur ofta--programmet försöker transaktioner en viktig del av designen.

#### <a name="assignilpip"></a>Tilldela en nivå offentliga IP-instans på varje virtuell dator
Tilldela en går ändras ditt scenario till [offentlig IP på instansnivå till en virtuell dator](#ilpip). Alla tillfälliga portar för den offentliga IP-Adressen som används för varje virtuell dator är tillgängliga för den virtuella datorn. (I stället för scenarier där tillfälliga portar på en offentlig IP-adress som delas med alla virtuella datorer som är associerade med respektive serverdelspoolen.) Det finns avvägningarna att tänka på, till exempel den extra kostnaden för offentliga IP-adresser och den potentiella effekten av vitlistning av ett stort antal enskilda IP-adresser.

>[!NOTE] 
>Det här alternativet är inte tillgängligt för web arbetsroller.

#### <a name="multifesnat"></a>Använda flera frontends

När du använder offentliga belastningsutjämnare som Standard kan du tilldela [flera klientdelens IP-adresser för utgående anslutningar](#multife) och [multiplicera antalet tillgängliga portar för SNAT](#preallocatedports).  Du behöver skapa en klientdelens IP-konfiguration och regeln serverdelspool som utlöser programmeringen av SNAT till offentliga IP-Adressen för klientdelen.  Regeln behöver inte fungerar och en hälsoavsökningen behöver inte lyckas.  Om du använder flera frontends för inkommande samt (i stället för bara för utgående), bör du använda anpassade hälsoavsökning bra för att försäkra dig om tillförlitlighet.

>[!NOTE]
>I de flesta fall är förbrukat SNAT portar för ett tecken på designen.  Kontrollera att du förstår varför du är lång körningstid förbrukar portar innan du använder flera frontends att lägga till SNAT portar.  Du kan maskning problem som kan orsaka fel senare.

#### <a name="scaleout"></a>Skala ut

[Förallokerade portar](#preallocatedports) tilldelas baserat på backend-poolstorleken och grupperade i nivåer för att minimera störningar när några av portarna måste allokeras om för att hantera de med nästa större backend pool storlek nivå.  Du kan ha ett alternativ för att öka intensiteten på SNAT port användningen för en given klientdel genom att skala din serverdelspool till maximal storlek för en viss nivå.  Detta krävs för programmet för att skala ut effektivt.

Till exempel skulle 2 virtuella datorer i serverdelspoolen ha 1024 SNAT portar som är tillgängliga per IP-konfiguration, så att summan av 2048 SNAT portar för distributionen.  Om distributionen ökas till 50 virtuella kan datorer, även om antalet förallokerade portar är konstant per virtuell dator, totalt 51,200 (50 x 1 024) SNAT portar användas i distributionen.  Om du vill skala upp distributionen kontrollera antalet [förallokerade portar](#preallocatedports) per nivån för att se till att du formar din skala ut till den maximala storleken för respektive nivå.  I föregående exempel om du har valt att skala ut till 51 i stället för 50 instanser, din skulle gå vidare till nästa nivå och slutet av med mindre SNAT portar per virtuell dator samt som totalt.

Skala ut till nästa större backend poolen storlek nivå potentiellt utgående anslutningar om tilldelade portar måste däremot allokeras.  Om du inte vill att detta ska ske, måste du formar distributionen till nivån storlek.  Eller kontrollera att programmet kan identifiera och försök igen efter behov.  TCP keepalive-överföringar kan hjälpa i identifiera när SNAT portarna längre funktion på grund av att omfördelats.

### <a name="idletimeout"></a>Använda keepalive-överföringar för att återställa utgående tidsgränsen för inaktivitet

Utgående anslutningar har ett 4-minuters timeout för inaktivitet. Det här kan inte ställas in. Du kan dock använda transport (exempelvis TCP keepalive-överföringar)- eller programnivå keepalive-överföringar för att uppdatera en inaktiv flödet och Återställ den här tidsgränsen för inaktivitet om det behövs.  

När du använder TCP keepalive-överföringar, räcker det att ge dem på ena sidan av anslutningen. Till exempel det räcker att ge dem på serversidan bara för att återställa timern i flöde och det är inte nödvändigt för båda sidorna till initierat TCP keepalive-överföringar.  Det finns liknande koncept för programnivå, inklusive databas klient / server-konfigurationer.  Kontrollera server-side för vilka alternativ som finns för programmet specifika keepalive-överföringar.

## <a name="discoveroutbound"></a>Identifiering av offentliga IP-Adressen som använder en virtuell dator
Det finns många sätt att avgöra den offentliga IP-källadressen för en utgående anslutning. OpenDNS är en tjänst som kan visa den offentliga IP-adressen på den virtuella datorn. 

Du kan skicka en DNS-fråga för namnet myip.opendns.com till OpenDNS matcharen med hjälp av nslookup-kommando. Källans IP-adress som används för att skicka frågan returnerar tjänsten. När du kör följande fråga från den virtuella datorn är svaret offentliga IP-Adressen som används för den virtuella datorn:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Förhindrar en utgående anslutning
Ibland kan det vara önskvärt en virtuell dator för att kunna skapa ett utgående flöde. Det kan finnas ett krav för att hantera vilka destinationer kan nås med utgående flöden eller vilka destinationer kan börja inkommande flöden. I det här fallet kan du använda [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md) att hantera mål som den virtuella datorn kan nå. Du kan också använda NSG: er för att hantera vilka offentliga mål kan initiera inkommande flöden. 

När du kopplar en NSG till en belastningsutjämnad VM ta hänsyn till den [standard taggar](../virtual-network/virtual-networks-nsg.md#default-tags) och [standard regler](../virtual-network/virtual-networks-nsg.md#default-rules). Du måste se till att den virtuella datorn kan ta emot hälsa avsökningen begäranden från Azure-belastningsutjämnaren. 

Om en NSG blockerar hälsa avsökningen begäranden från Standardetiketten AZURE_LOADBALANCER, din VM hälsoavsökningen misslyckas och den virtuella datorn markeras. Belastningsutjämnaren slutar att skicka nya flödar till den virtuella datorn.

## <a name="limitations"></a>Begränsningar
- DisableOutboundSnat är inte tillgängligt som ett alternativ när du konfigurerar en regel i portalen för belastningsutjämning.  Använd REST, mall eller klienten verktyg i stället.
- Web arbetsroller utan ett VNet och andra Microsoft-tjänster för plattformen kan vara tillgänglig när bara en intern Standard belastningsutjämnare används på grund av en sidoeffekt från hur pre-VNet-tjänster och andra platform services-funktionen. Du måste inte förlita sig på detta som respektive tjänst sig själv eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du behöver skapa utgående anslutning uttryckligen om du vill när du använder en intern Standard belastningsutjämnare endast. Den [standard SNAT](#defaultsnat) scenario 3 som beskrivs i den här artikeln är inte tillgänglig.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [belastningsutjämnaren](load-balancer-overview.md).
- Mer information finns i [Standardbelastningsutjämnare](load-balancer-standard-overview.md).
- Lär dig mer om [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md).
- Lär dig mer om den andra nyckeln [nätverk](../networking/networking-overview.md) i Azure.
