---
title: Utgående anslutningar i Azure (klassisk)
titlesuffix: Azure Load Balancer
description: Den här artikeln förklarar hur Azure gör det möjligt för moln tjänster att kommunicera med offentliga Internet tjänster.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: allensu
ms.openlocfilehash: 10af3b4838aae1565bac1d996997c117a74cedbc
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274673"
---
# <a name="outbound-connections-classic"></a>Utgående anslutningar (klassiska)

Azure tillhandahåller utgående anslutning för kund distributioner via flera olika mekanismer. I den här artikeln beskrivs vad scenarierna är, när de tillämpas, hur de fungerar och hur du hanterar dem.

>[!NOTE]
>Den här artikeln beskriver endast klassiska distributioner.  Granska [utgående anslutningar](load-balancer-outbound-connections.md) för alla distributions scenarier i Resource Manager i Azure.

En distribution i Azure kan kommunicera med slut punkter utanför Azure i det offentliga IP-adressutrymmet. När en instans initierar ett utgående flöde till ett mål i det offentliga IP-adressutrymmet, mappar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När den här mappningen har skapats kan returnera trafik för det här utgående flödet även komma åt den privata IP-adressen som flödet kommer från.

Azure använder käll Network Address Translation (SNAT) för att utföra den här funktionen. När flera privata IP-adresser imiteras bakom en enskild offentlig IP-adress använder Azure [port adress översättning (Pat)](#pat) för att maskera privata IP-adresser. Tillfälliga portar används för PAT och är förallokerade baserat på Poolens storlek. [](#preallocatedports)

Det finns flera [utgående scenarier](#scenarios). Du kan kombinera dessa scenarier efter behov. Granska dem noggrant för att förstå funktionerna, begränsningarna och mönstren som de gäller för distributions modellen och program scenariot. Läs igenom rikt linjerna för att [hantera dessa scenarier](#snatexhaust).

## <a name="scenarios"></a>Scenario översikt

Azure tillhandahåller tre olika metoder för att uppnå klassiska installationer av de klassiska anslutningarna.  Alla klassiska distributioner har inte alla tre scenarion tillgängliga:

| Scenario | Metod | IP-protokoll | Beskrivning | Webb arbets roll | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. Virtuell dator med en offentlig IP-adress på instans nivå](#ilpip) | SNAT, Port maskerad, används inte | TCP, UDP, ICMP, ESP | Azure använder den offentliga IP-tilldelade virtuella datorn. Instansen har alla tillfälliga portar tillgängliga. | Nej | Ja |
| [2. offentlig belastningsutjämnad slut punkt](#publiclbendpoint) | SNAT med port maskerad (PAT) till den offentliga slut punkten | TCP, UDP | Azure delar den offentliga IP-adressens offentliga slut punkt med flera privata slut punkter. Azure använder tillfälliga portar för den offentliga slut punkten för PAT. | Ja | Ja |
| [3. Fristående virtuell dator](#defaultsnat) | SNAT med port maskerad (PAT) | TCP, UDP | Azure utser automatiskt en offentlig IP-adress för SNAT, delar denna offentliga IP-adress med hela distributionen och använder tillfälliga portar i den offentliga slut punktens IP-adress för PAT. Detta är ett återställnings scenario för föregående scenarier. Vi rekommenderar inte det om du behöver synlighet och kontroll. | Ja | Ja |

Detta är en delmängd av funktionerna för utgående anslutningar som är tillgängliga för Resource Manager-distributioner i Azure.  

Olika distributioner i klassisk har olika funktioner:

| Klassisk distribution | Tillgängliga funktioner | 
| --- | --- |
| Virtuell dator | scenario [1](#ilpip), [2](#publiclbendpoint)eller [3](#defaultsnat) |
| Webb arbets roll | endast scenario [2](#publiclbendpoint), [3](#defaultsnat) | 

Skydds [strategier](#snatexhaust) har också samma skillnader.

Den algoritm som används för att förallokera tillfälliga portar för PAT för klassiska distributioner är samma som för Azure Resource Manager resurs distributioner.

### <a name="ilpip"></a>Scenario 1: Virtuell dator med en offentlig IP-adress på instans nivå

I det här scenariot har den virtuella datorn en offentlig IP-adress (ILPIP) på instans nivå som är tilldelad till den. Så långt som utgående anslutningar berörs spelar det ingen roll om den virtuella datorn har en belastningsutjämnad slut punkt eller inte. Det här scenariot prioriteras framför de andra. När en ILPIP används använder den virtuella datorn ILPIP för alla utgående flöden.  

En offentlig IP-adress som tilldelas till en virtuell dator är en 1:1-relation (i stället för 1: många) och implementeras som en tillstånds Lös 1:1 NAT.  Port maskering (PAT) används inte och den virtuella datorn har alla tillfälliga portar som är tillgängliga för användning.

Om ditt program initierar många utgående flöden och du upplever antalet SNAT-portar, bör du överväga att tilldela en [ILPIP för att åtgärda SNAT-begränsningar](#assignilpip). Granska [hantering av SNAT](#snatexhaust) -överbelastning i sin helhet.

### <a name="publiclbendpoint"></a>Scenario 2: Offentlig belastningsutjämnad slut punkt

I det här scenariot är den virtuella dator rollen eller webb arbets rollen associerad med en offentlig IP-adress via den belastningsutjämnade slut punkten. Den virtuella datorn har ingen tilldelad offentlig IP-adress. 

När den belastningsutjämnade virtuella datorn skapar ett utgående flöde översätter Azure den privata käll-IP-adressen för det utgående flödet till den offentliga IP-adressen för den offentliga belastningsutjämnade slut punkten. Azure använder SNAT för att utföra den här funktionen. Azure använder också [Pat](#pat) för att maskera flera privata IP-adresser bakom en offentlig IP-adress. 

Tillfälliga portar för belastningsutjämnarens offentliga IP-adress-frontend används för att särskilja enskilda flöden från den virtuella datorn. SNAT använder dynamiskt [förallokerade portar](#preallocatedports) när utgående flöden skapas. I det här sammanhanget kallas de tillfälliga portarna som används för SNAT som SNAT-portar.

SNAT-portar är fördelade enligt beskrivningen i avsnittet om [SNAT och Pat](#snat) . De är en begränsad resurs som kan vara förbrukad. Det är viktigt att förstå hur de används [.](#pat) För att förstå hur du utformar för den här förbrukningen och minimera vid behov, granska [hanteringen av SNAT](#snatexhaust)-belastningen.

När det finns [flera offentliga belastningsutjämnade slut punkter](load-balancer-multivip.md) , är någon av dessa offentliga IP-adresser en kandidat för utgående flöden och en väljs slumpmässigt.  

### <a name="defaultsnat"></a>Scenario 3: Ingen offentlig IP-adress har associerats

I det här scenariot ingår inte den virtuella dator rollen eller webb arbets rollen som en offentlig belastningsutjämnad slut punkt.  Och om den virtuella datorn inte har någon tilldelad ILPIP-adress. När den virtuella datorn skapar ett utgående flöde översätter Azure den privata käll-IP-adressen för det utgående flödet till en offentlig käll-IP-adress. Den offentliga IP-adress som används för det här utgående flödet kan inte konfigureras och räknas inte mot prenumerationens offentliga IP-adressresurs.  Azure allokerar den här adressen automatiskt.

Azure använder SNAT med port maskerad ([Pat](#pat)) för att utföra den här funktionen. Det här scenariot liknar scenario 2, förutom att det inte finns någon kontroll över den IP-adress som används. Detta är ett återställnings scenario för när scenarierna 1 och 2 inte finns. Vi rekommenderar inte det här scenariot om du vill ha kontroll över den utgående adressen. Om utgående anslutningar är en viktig del av ditt program bör du välja ett annat scenario.

SNAT-portar är fördelade enligt beskrivningen i avsnittet om [SNAT och Pat](#snat) .  Antalet virtuella datorer eller webb arbets roller som delar den offentliga IP-adressen bestämmer antalet förallokerade tillfälliga portar.   Det är viktigt att förstå hur de används [.](#pat) För att förstå hur du utformar för den här förbrukningen och minimera vid behov, granska [hanteringen av SNAT](#snatexhaust)-belastningen.

## <a name="snat"></a>Förstå SNAT och PAT

### <a name="pat"></a>Port maskerad SNAT (PAT)

När en distribution gör en utgående anslutning, skrivs varje utgående anslutnings källa över. Källan skrivs från det privata IP-adressutrymmet till den offentliga IP-adress som är kopplad till distributionen (baserat på scenarier som beskrivs ovan). I det offentliga IP-adressutrymmet måste en 5-tupel i flödet (Källans IP-adress, källport, IP-transportläge, målets IP-adress, målport) vara unik.  

Tillfälliga portar (SNAT-portar) används för att uppnå detta när den privata Källans IP-adress har skrivits om, eftersom flera flöden härstammar från en enda offentlig IP-adress. 

En SNAT-port förbrukas per flöde till en IP-adress, port och protokoll för en enda mål. För flera flöden till samma mål-IP-adress, port och protokoll förbrukar varje flöde en enda SNAT-port. Detta säkerställer att flödena är unika när de härstammar från samma offentliga IP-adress och går till samma mål-IP-adress, port och protokoll. 

Flera flöden, var och en till en annan mål-IP-adress, port och protokoll, delar en enda SNAT-port. Målets IP-adress, port och protokoll gör flöden unika utan behovet av ytterligare käll portar för att särskilja flöden i det offentliga IP-adressutrymmet.

När SNAT-port resurserna är slut så att utgående flöden inte fungerar förrän befintliga flöden frigör SNAT-portar. Load Balancer frigör SNAT-portar när flödet stängs och använder en [tids gräns på 4 minuter](#idletimeout) för att frigöra SNAT-portar från inaktiva flöden.

För mönster för att minska de villkor som ofta leder till SNAT-portens belastning, se avsnittet [Hantera SNAT](#snatexhaust) .

### <a name="preallocatedports"></a>Förallokering av tillfällig port för port maskerad SNAT (PAT)

Azure använder en algoritm för att fastställa antalet förallokerade SNAT-portar som är tillgängliga baserat på storleken på backend-poolen när du använder port maskerad SNAT ([Pat](#pat)). SNAT-portar är tillfälliga portar som är tillgängliga för en viss offentlig IP-datakälla.

Azure preallokerar SNAT-portar när en instans distribueras baserat på hur många virtuella datorer eller webb arbetares roll instanser delar en specifik offentlig IP-adress.  När utgående flöden skapas, förbrukar [Pat](#pat) dynamiskt (upp till den förallokerade gränsen) och släpper dessa portar när flödet stängs eller när tids gränsen för inaktivitet inträffar.

Följande tabell visar förallokeringar av SNAT-portar för nivåer av backend-poolens storlekar:

| instanser | Förallokerade SNAT-portar per instans |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Kom ihåg att antalet SNAT-portar som är tillgängliga inte översätts direkt till antal flöden. En enda SNAT-port kan återanvändas för flera unika mål. Portar används bara om det är nödvändigt att göra flöden unika. Vägledning för design och minskning finns i avsnittet om [hur du hanterar den här exhaustible-resursen](#snatexhaust) och i avsnittet som beskriver [Pat](#pat).

Att ändra storleken på distributionen kan påverka några av dina upprättade flöden. Om storleken på backend-poolen ökar och övergår till nästa nivå, frigörs hälften av de förallokerade SNAT-portarna under över gången till nästa större nivå i Server delen. Flöden som är associerade med en representerad SNAT-port blir timeout och måste återupprättas. Om ett nytt flöde görs kommer flödet att lyckas omedelbart så länge som förallokerade portar är tillgängliga.

Om distributions storleken minskar och övergår till en lägre nivå ökar antalet tillgängliga SNAT-portar. I det här fallet påverkas inte befintliga allokerade SNAT-portar och deras respektive flöden.

Om en moln tjänst omdistribueras eller ändras kan infrastrukturen tillfälligt rapportera backend-poolen så att den är upp till två gånger så stor som faktiskt och Azure kommer att göra en Förallokering av färre SNAT-portar per instans än förväntat.  Detta kan tillfälligt öka sannolikheten för antalet SNAT-portar. Slutligen kommer Poolens storlek att övergå till faktisk storlek och Azure ökar automatiskt förallokerade SNAT-portar till det förväntade antalet enligt tabellen ovan.  Det här beteendet är avsiktligt och kan inte konfigureras.

SNAT-portar tilldelningar är särskilda IP-protokoll (TCP och UDP underhålls separat) och släpps under följande förhållanden:

### <a name="tcp-snat-port-release"></a>Port version för TCP SNAT

- Om både servern/klienten skickar FIN/ACK frigörs SNAT-porten efter 240 sekunder.
- Om en min visas kommer SNAT-porten att lanseras efter 15 sekunder.
- tids gränsen för inaktivitet har uppnåtts

### <a name="udp-snat-port-release"></a>Port version för UDP SNAT

- tids gränsen för inaktivitet har uppnåtts

## <a name="problemsolving"></a>Problemlösning 

Det här avsnittet är avsett för att hjälpa till att minska SNAT-belastningen och andra scenarier som kan uppstå med utgående anslutningar i Azure.

### <a name="snatexhaust"></a>Hantera SNAT (PAT) port överbelastning
[Tillfälliga portar](#preallocatedports) som används för [Pat](#pat) är en exhaustible-resurs, enligt beskrivningen i [ingen offentlig IP-associerad](#defaultsnat) och [offentlig belastningsutjämnad slut punkt](#publiclbendpoint).

Om du vet att du initierar flera utgående TCP-eller UDP-anslutningar till samma mål-IP-adress och port, och du ser att du inte har utgående anslutningar eller om du har meddelat support om att du tar slut på SNAT-portar (förallokerade [tillfälliga portar](#preallocatedports) används av [Pat](#pat)) och du har flera allmänna alternativ för att minska. Granska de här alternativen och Bestäm vad som är tillgängligt och bäst för ditt scenario. Det är möjligt att en eller flera kan hjälpa dig att hantera det här scenariot.

Om du har problem med att förstå beteendet för utgående anslutning kan du använda IP stack-statistik (netstat). Eller så kan det vara bra att följa anslutnings beteenden med hjälp av paket insamlingar.

#### <a name="connectionreuse"></a>Ändra programmet för att återanvända anslutningar 
Du kan minska efter frågan för tillfälliga portar som används för SNAT genom att återanvända anslutningar i ditt program. Detta gäller särskilt för protokoll som HTTP/1.1, där åter användning av anslutningar är standard. Och andra protokoll som använder HTTP som transport (till exempel REST) kan ha nytta av turn. 

Åter användning är alltid bättre än enskilda, atomiska TCP-anslutningar för varje begäran. Återanvänd resultat i mer utförda, mycket effektiva TCP-transaktioner.

#### <a name="connection pooling"></a>Ändra programmet så att anslutningspoolen används
Du kan använda ett schema för anslutningspoolen i ditt program, där förfrågningar distribueras internt över en fast uppsättning anslutningar (varje återanvändning där det är möjligt). Det här schemat begränsar antalet tillfälliga portar som används och skapar en mer förutsägbar miljö. Det här schemat kan också öka genom strömningen av begär Anden genom att tillåta flera samtidiga åtgärder när en enskild anslutning blockeras när en åtgärd svarar.  

Anslutningspoolen kanske redan finns i ramverket som du använder för att utveckla ditt program eller konfigurations inställningarna för ditt program. Du kan kombinera anslutningspoolen med återkoppling av anslutning. Dina flera begär Anden använder sedan ett fast, förutsägbart antal portar till samma mål-IP-adress och port. Förfrågningarna drar också nytta av effektiv användning av TCP-transaktioner som minskar svars tiden och resursutnyttjande. UDP-transaktioner kan också dra nytta av, eftersom hantering av antalet UDP-flöden kan i sin tur undvika avgas villkor och hantera SNAT-port användningen.

#### <a name="retry logic"></a>Ändra programmet för att använda mindre aggressiva omprövnings logik
När [förallokerade tillfälliga portar](#preallocatedports) som används för [Pat](#pat) är förbrukade eller om program fel uppstår, kan aggressiva eller råa Force-försök utan minskning och backoff logik orsaka att inaktivitet inträffar eller är kvar. Du kan minska efter frågan för tillfälliga portar genom att använda en mindre aggressiva omprövnings logik. 

Tillfälliga portar har en timeoutvärde på 4 minuter (inte justerbar). Om de nya försöken är för aggressiva har uttömdheten ingen möjlighet att rensa upp sig själv. Därför är det viktigt att du överväger hur – och hur ofta – dina program återförsöks transaktioner är en viktig del av designen.

#### <a name="assignilpip"></a>Tilldela en offentlig IP-adress på instans nivå till varje virtuell dator
Genom att tilldela en ILPIP ändrar du ditt scenario till [en offentlig virtuell IP-adress på instans nivå](#ilpip). Alla tillfälliga portar i den offentliga IP-adressen som används för varje virtuell dator är tillgängliga för den virtuella datorn. (I stället för scenarier där tillfälliga portar för en offentlig IP-adress delas med alla de virtuella datorer som är associerade med respektive distribution.) Det finns kompromisser att överväga, till exempel den potentiella effekten av vit listning ett stort antal enskilda IP-adresser.

>[!NOTE] 
>Det här alternativet är inte tillgängligt för webb arbets roller.

### <a name="idletimeout"></a>Använd keepalive för att återställa tids gränsen för utgående inaktivitet

Utgående anslutningar har en tids gräns på 4 minuter. Denna timeout är inte justerbar. Du kan dock använda transport (till exempel TCP keepalive) eller keepalive för program lager för att uppdatera ett inaktivt flöde och återställa denna tids gräns vid behov.  Kontakta leverantören av en paketerad program vara på om detta stöds eller aktivera det.  I allmänhet behöver bara en sida generera keepalive för att återställa tids gränsen för inaktivitet. 

## <a name="discoveroutbound"></a>Identifiera den offentliga IP-adress som används av en virtuell dator
Det finns många sätt att fastställa IP-adressen för den offentliga källan för en utgående anslutning. OpenDNS tillhandahåller en tjänst som kan visa den offentliga IP-adressen för den virtuella datorn. 

Genom att använda nslookup-kommandot kan du skicka en DNS-fråga för namnet myip.opendns.com till OpenDNS-matcharen. Tjänsten returnerar käll-IP-adressen som användes för att skicka frågan. När du kör följande fråga från din virtuella dator, är svaret den offentliga IP-adress som används för den virtuella datorn:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Nästa steg

- Läs mer om [Load Balancer](load-balancer-overview.md) som används i Resource Manager-distributioner.
- Lär dig läget om scenarier för [utgående anslutningar](load-balancer-outbound-connections.md) som är tillgängliga i Resource Manager-distributioner.
