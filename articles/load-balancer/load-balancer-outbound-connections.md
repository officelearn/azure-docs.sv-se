---
title: Utgående anslutningar i Azure
titleSuffix: Azure Load Balancer
description: I den här artikeln beskrivs hur Azure gör det möjligt för virtuella datorer att kommunicera med offentliga internettjänster.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 411c06e19b932b441f27a3c7578d847c6dfc1f7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336992"
---
# <a name="outbound-connections-in-azure"></a>Utgående anslutningar i Azure

Azure tillhandahåller utgående anslutning för kunddistributioner via flera olika mekanismer. I den här artikeln beskrivs vad scenarierna är, när de gäller, hur de fungerar och hur de ska hanteras.

>[!NOTE] 
>Den här artikeln omfattar endast Resurshanterarens distributioner. Granska [utgående anslutningar (Klassisk)](load-balancer-outbound-connections-classic.md) för alla klassiska distributionsscenarier i Azure.

En distribution i Azure kan kommunicera med slutpunkter utanför Azure i det offentliga IP-adressutrymmet. När en instans initierar ett utgående flöde till ett mål i det offentliga IP-adressutrymmet mappar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När den här mappningen har skapats kan returtrafik för det här utgående flödet också nå den privata IP-adressen där flödet har sitt ursprung.

Azure använder SNAT (Source Network Address Translation) för att utföra den här funktionen. När flera privata IP-adresser maskeras bakom en enda offentlig IP-adress använder Azure [PAT (Port Address Translation)](#pat) för att maskera privata IP-adresser. Efemära portar används för PAT och är [förallokerade](#preallocatedports) baserat på poolstorlek.

Det finns flera [utgående scenarier](#scenarios). Du kan kombinera dessa scenarier efter behov. Granska dem noggrant för att förstå funktioner, begränsningar och mönster när de gäller för distributionsmodellen och programscenariot. Granska vägledning för [hantering av dessa scenarier](#snatexhaust).

>[!IMPORTANT] 
>Standard belastningsutjämnare och standard offentlig IP introducerar nya förmågor och olika beteenden till utgående anslutning.  De är inte samma sak som grundläggande SKU: er.  Om du vill ha utgående anslutning när du arbetar med standardSku: er måste du uttryckligen definiera den antingen med vanliga offentliga IP-adresser eller standard offentlig belastningsutjämnare.  Detta inkluderar att skapa utgående anslutning när du använder en intern standardbelastningsutjämning.  Vi rekommenderar att du alltid använder utgående regler för en vanlig vanlig belastningsutjämnare.  [Scenario 3](#defaultsnat) är inte tillgängligt med Standard SKU.  Det innebär att när en intern standardbelastningsutjämnare används måste du vidta åtgärder för att skapa utgående anslutning för de virtuella datorerna i serverdapoolen om utgående anslutning önskas.  I kontexten för utgående anslutning, en enda fristående virtuell dator, alla virtuella datorer i en tillgänglighetsuppsättning, fungerar alla instanser i en VMSS som en grupp. Det innebär att om en enda virtuell dator i en tillgänglighetsuppsättning är associerad med en standard-SKU, beter sig nu alla VM-instanser i den här tillgänglighetsuppsättningen enligt samma regler som om de är associerade med Standard SKU, även om en enskild instans inte är direkt associerad med den. Detta observeras också när det gäller en fristående virtuell dator med flera nätverkskort kopplade till en belastningsutjämnare. Om ett nätverkskort läggs till som ett fristående, kommer det att ha samma beteende. Granska noggrant hela det här dokumentet för att förstå de övergripande begreppen, granska [Standardbelastningsutjämning](load-balancer-standard-overview.md) för skillnader mellan SKU:er och granska [utgående regler](load-balancer-outbound-rules-overview.md).  Med hjälp av utgående regler kan du finkornig kontroll över alla aspekter av utgående anslutning.

## <a name="scenario-overview"></a><a name="scenarios"></a>Scenarioöversikt

Azure Load Balancer och relaterade resurser definieras uttryckligen när du använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Azure innehåller för närvarande tre olika metoder för att uppnå utgående anslutning för Azure Resource Manager-resurser. 

| SKU: er | Scenario | Metod | IP-protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Standard, Grundläggande | [1. VIRTUELL med en offentlig IP-adress på instansnivå (med eller utan belastningsutjämnare)](#ilpip) | SNAT, portmaskerating används inte | TCP, UDP, ICMP, ESP | Azure använder den offentliga IP som tilldelats IP-konfigurationen för instansens nätverkskort. Instansen har alla tillfälliga portar tillgängliga. När du använder Standard belastningsutjämnare stöds inte [utgående regler](load-balancer-outbound-rules-overview.md) om en offentlig IP tilldelas den virtuella datorn. |
| Standard, Grundläggande | [2. Offentlig belastningsutjämnare som är associerad med en virtuell dator (ingen offentlig IP-adress på instansen)](#lb) | SNAT med portmaskerat (PAT) med belastningsutjämnaderna | TCP, UDP |Azure delar den offentliga IP-adressen för de offentliga belastningsutjämnarbehållen med flera privata IP-adresser. Azure använder tillfälliga portar för klientdelserna till PAT. Du bör använda [utgående regler](load-balancer-outbound-rules-overview.md) för att uttryckligen definiera utgående anslutning. |
| ingen eller Grundläggande | [3. Fristående virtuell dator (ingen belastningsutjämnare, ingen offentlig IP-adress)](#defaultsnat) | SNAT med portmaskerating (PAT) | TCP, UDP | Azure anger automatiskt en offentlig IP-adress för SNAT, delar den här offentliga IP-adressen med flera privata IP-adresser för tillgänglighetsuppsättningen och använder tillfälliga portar för den här offentliga IP-adressen. Det här scenariot är en återgång för föregående scenarier. Vi rekommenderar det inte om du behöver synlighet och kontroll. |

Om du inte vill att en virtuell dator ska kommunicera med slutpunkter utanför Azure i offentligt IP-adressutrymme kan du använda nätverkssäkerhetsgrupper (NSG) för att blockera åtkomst efter behov. I avsnittet [Förhindra utgående anslutning](#preventoutbound) beskrivs NSG:er mer i detalj. Vägledning om hur du utformar, implementerar och hanterar ett virtuellt nätverk utan utgående åtkomst ligger utanför den här artikelns omfattning.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scenario 1: VIRTUELL dator med offentlig IP-adress

I det här fallet har den virtuella datorn en offentlig IP tilldelats den. När det gäller utgående anslutningar spelar det ingen roll om den virtuella datorn är belastningsbalanserad eller inte. Det här scenariot har företräde framför de andra. När en offentlig IP-adress används använder den virtuella datorn den offentliga IP-adressen för alla utgående flöden.  

En offentlig IP som tilldelats en virtuell dator är en 1:1-relation (i stället för 1: många) och implementeras som en tillståndslös 1:1 NAT.  Portmaskrading (PAT) används inte och den virtuella datorn har alla tillfälliga portar tillgängliga för användning.

Om ditt program initierar många utgående flöden och du upplever SNAT-portutmattning kan du överväga att tilldela en [offentlig IP-adress för att minska SNAT-begränsningar](#assignilpip). Granska [Hantera SNAT utmattning](#snatexhaust) i sin helhet.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scenario 2: Belastningsbalanserad virtuell dator utan offentlig IP-adress

I det här fallet är den virtuella datorn en del av en offentlig load balancer-backend-pool. Den virtuella datorn har ingen offentlig IP-adress tilldelad. Load Balancer-resursen måste konfigureras med en belastningsutjämnadsregel för att skapa en länk mellan den offentliga IP-klientdelen med serverdelspoolen.

Om du inte slutför den här regelkonfigurationen är beteendet som beskrivs i scenariot för [fristående virtuell dator utan offentlig IP](#defaultsnat). Det är inte nödvändigt för regeln att ha en fungerande lyssnare i serverda poolen för hälso-sonden att lyckas.

När den belastningsbalanserade virtuella datorn skapar ett utgående flöde översätter Azure den privata käll-IP-adressen för det utgående flödet till den offentliga IP-adressen för den offentliga belastningsutjämningsfronten. Azure använder SNAT för att utföra den här funktionen. Azure använder också [PAT](#pat) för att maskera flera privata IP-adresser bakom en offentlig IP-adress. 

Tillfälliga portar för belastningsutjämnarens offentliga IP-adress frontend används för att skilja enskilda flöden som har sitt ursprung i den virtuella datorn. SNAT använder dynamiskt [förallokerade tillfälliga portar](#preallocatedports) när utgående flöden skapas. I detta sammanhang kallas de tillfälliga portar som används för SNAT SNAT-portar.

SNAT-portar förallokeras enligt beskrivningen i avsnittet [Förstå SNAT och PAT.](#snat) De är en ändlig resurs som kan vara uttömda. Det är viktigt att förstå hur de [konsumeras.](#pat) Om du vill förstå hur du utformar för den här förbrukningen och mildrar vid behov genom [att granska Hantera SNAT-utmattning](#snatexhaust).

När [flera offentliga IP-adresser associeras med Load Balancer Basic](load-balancer-multivip-overview.md)är någon av dessa offentliga IP-adresser en kandidat för utgående flöden och en väljs slumpmässigt.  

Om du vill övervaka hälsotillståndet för utgående anslutningar med Load Balancer Basic kan du använda [Azure Monitor-loggar för belastningsutjämnad](load-balancer-monitor-log.md) och [händelseloggar](load-balancer-monitor-log.md#alert-event-log) för aviseringar för att övervaka för SNAT-portutmattningsmeddelanden.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scenario 3: Fristående virtuell dator utan offentlig IP-adress

I det här fallet är den virtuella datorn inte en del av en offentlig belastningsutjämnad (och inte en del av en intern standardbelastningsutjämningspool) och har ingen offentlig IP-adress tilldelad till den. När den virtuella datorn skapar ett utgående flöde översätter Azure den privata käll-IP-adressen för det utgående flödet till en offentlig källa IP-adress. Den offentliga IP-adress som används för det här utgående flödet kan inte konfigureras och räknas inte mot prenumerationens offentliga IP-resursgräns. Den här offentliga IP-adressen tillhör inte dig och kan inte reserveras. Om du distribuerar om den virtuella datorn eller tillgänglighetsuppsättningen eller skalningsuppsättningen för virtuella datorer, kommer den här offentliga IP-adressen att släppas och en ny offentlig IP-adress begärs. Använd inte det här scenariot för vitlistning av IP-adresser. Använd i stället ett av de andra två scenarierna där du uttryckligen deklarerar det utgående scenariot och den offentliga IP-adressen som ska användas för utgående anslutning.

>[!IMPORTANT] 
>Det här scenariot gäller även när __endast__ en intern grundläggande belastningsutjämnare är ansluten. Scenario 3 är __inte tillgängligt__ när en intern standardbelastningsutfärdare är kopplad till en virtuell dator.  Du måste uttryckligen skapa [scenario 1](#ilpip) eller [scenario 2](#lb) förutom att använda en intern standardbelastningsutjämning.

Azure använder SNAT med portutmaskering[(PAT)](#pat)för att utföra den här funktionen. Det här scenariot liknar [scenario 2,](#lb)förutom att det inte finns någon kontroll över den IP-adress som används. Detta är ett reservscenario för när scenarierna 1 och 2 inte finns. Vi rekommenderar inte det här scenariot om du vill ha kontroll över den utgående adressen. Om utgående anslutningar är en viktig del av ditt program bör du välja ett annat scenario.

SNAT-portar är förallokerade enligt beskrivningen i avsnittet [Förstå SNAT och PAT.](#snat)  Antalet virtuella datorer som delar en tillgänglighetsuppsättning avgör vilken förallokeringsnivå som gäller.  En fristående virtuell dator utan tillgänglighetsuppsättning är i själva verket en pool med 1 för att bestämma förallokering (1024 SNAT-portar). SNAT-portar är en begränsad resurs som kan vara uttömd. Det är viktigt att förstå hur de [konsumeras.](#pat) Om du vill förstå hur du utformar för den här förbrukningen och mildrar vid behov genom [att granska Hantera SNAT-utmattning](#snatexhaust).

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Flera kombinerade scenarier

Du kan kombinera de scenarier som beskrivs i föregående avsnitt för att uppnå ett visst resultat. När flera scenarier finns gäller en prioritetsordning: [scenario 1](#ilpip) har företräde framför [scenario 2](#lb) och [3](#defaultsnat). [Scenario 2](#lb) åsidosätter [scenario 3](#defaultsnat).

Ett exempel är en Azure Resource Manager-distribution där programmet är starkt beroende av utgående anslutningar till ett begränsat antal destinationer men även tar emot inkommande flöden via en belastningsutjämnad frontend. I det här fallet kan du kombinera scenarier 1 och 2 för avlastning. Om du vill ha ytterligare mönster kan du granska [Hantera SNAT-utmattning](#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Flera frontends för utgående flöden

#### <a name="standard-load-balancer"></a>Standard belastningsutjämnare

Standardbelastningsutjämnaren använder alla kandidater för utgående flöden samtidigt som [flera (offentliga) IP-frontends](load-balancer-multivip-overview.md) finns. Varje klientdel multiplicerar antalet tillgängliga förallokerade SNAT-portar om en belastningsutjämningsregel är aktiverad för utgående anslutningar.

Du kan välja att förhindra att en ip-adress för klientdel används för utgående anslutningar med ett nytt alternativ för belastningsutjämningsregel:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalt innebär `disableOutboundSnat` alternativet som standard _false_ och innebär att den här regeln programmerar utgående SNAT för associerade virtuella datorer i backend-poolen för belastningsutjämningsregeln. Kan `disableOutboundSnat` ändras till _true_ för att förhindra belastningsutjämnare från att använda den associerade klientdelens IP-adress för utgående anslutningar för de virtuella datorerna i backend-poolen för den här belastningsutjämningsregeln.  Och du kan också fortfarande ange en specifik IP-adress för utgående flöden som beskrivs i [flera, kombinerade scenarier](#combinations) också.

#### <a name="load-balancer-basic"></a>Grundläggande belastningsutjämning

Load Balancer Basic väljer en enda frontend som ska användas för utgående flöden när [flera (offentliga) IP-frontends](load-balancer-multivip-overview.md) är kandidater för utgående flöden. Det här valet kan inte konfigureras och du bör betrakta urvalsalgoritmen som slumpmässig. Du kan ange en specifik IP-adress för utgående flöden enligt beskrivningen i [Flera kombinerade scenarier](#combinations).

### <a name="availability-zones"></a><a name="az"></a>Tillgänglighetszoner

När du använder [Standard belastningsutjämnare med tillgänglighetszoner](load-balancer-standard-availability-zones.md)kan zonupptrakta frontends tillhandahålla zonupptrakta utgående SNAT-anslutningar och SNAT-programmering överlever zonfel.  När zonindelning frontends används, utgående SNAT-anslutningar dela öde med den zon de tillhör.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Förstå SNAT och PAT

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Port maskerad SNAT (PAT)

När en offentlig belastningsutjämnad resurs är associerad med VM-instanser skrivs varje utgående anslutningskälla om. Källan skrivs om från det virtuella nätverkets privata IP-adressutrymme till den första offentliga IP-adressen för belastningsutjämnaren. I det offentliga IP-adressutrymmet måste flödets 5-tuppel (käll-IP-adress, källport, IP-transportprotokoll, mål-IP-adress, målport) vara unik.  Portmaskrading SNAT kan användas med antingen TCP- eller UDP IP-protokoll.

Efemära portar (SNAT-portar) används för att uppnå detta efter att ha skrivit om den privata käll-IP-adressen, eftersom flera flöden kommer från en enda offentlig IP-adress. Portmaskrading SNAT-algoritmen allokerar SNAT-portar på olika sätt för UDP kontra TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>TCP SNAT-portar

En SNAT-port förbrukas per flöde till en enda mål-IP-adress, port. För flera TCP-flöden till samma mål-IP-adress, port och protokoll förbrukar varje TCP-flöde en enda SNAT-port. Detta säkerställer att flödena är unika när de kommer från samma offentliga IP-adress och går till samma mål-IP-adress, port och protokoll. 

Flera flöden, var och en till en annan mål-IP-adress, port och protokoll, delar en enda SNAT-port. Mål-IP-adressen, porten och protokollet gör flöden unika utan att ytterligare källportar behöver särskilja flöden i det offentliga IP-adressutrymmet.

#### <a name="udp-snat-ports"></a><a name="udp"></a>UDP SNAT-portar

UDP SNAT-portar hanteras av en annan algoritm än TCP SNAT-portar.  Belastningsutjämnare använder en algoritm som kallas "portbegränsad kon NAT" för UDP.  En SNAT-port förbrukas för varje flöde, oavsett mål-IP-adress, port.

#### <a name="snat-port-reuse"></a>Återanvändning av SNAT-port

När en port har släppts är porten tillgänglig för återanvändning efter behov.  Du kan se SNAT-portar som en sekvens från lägsta till högsta tillgängliga för ett visst scenario, och den första tillgängliga SNAT-porten används för nya anslutningar. 
 
#### <a name="exhaustion"></a>Utmattning

När SNAT-portresurser är uttömda misslyckas utgående flöden tills befintliga flöden släpper SNAT-portar. Belastningsutjämnaren återtar SNAT-portar när flödet stängs och använder en [4-minuters inaktiv timeout](#idletimeout) för att återta SNAT-portar från inaktiva flöden.

UDP SNAT-portar avgaser i allmänhet mycket snabbare än TCP SNAT-portar på grund av skillnaden i algoritm som används. Du måste designa och skala test med denna skillnad i åtanke.

Om du vill minska mönster som ofta leder till utmattning av SNAT-portar läser du avsnittet [Hantera SNAT.](#snatexhaust)

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Efemära portpreallocation för port maskerad SNAT (PAT)

Azure använder en algoritm för att bestämma antalet förallokerade SNAT-portar som är tillgängliga baserat på storleken på serverdelspoolen när du använder portmaskerad SNAT ([PAT](#pat)). SNAT-portar är tillfälliga portar som är tillgängliga för en viss offentlig IP-källa.SNAT ports are ehemeral ports available for a particular public IP source address.

Samma antal SNAT-portar är förallokerade för UDP respektive TCP och förbrukas oberoende av ip-transportprotokoll.  SNAT-portanvändningen är dock olika beroende på om flödet är UDP eller TCP.

>[!IMPORTANT]
>Standard SKU SNAT-programmering är per IP-transportprotokoll och härleds från belastningsutjämningsregeln.  Om det bara finns en TCP-belastningsutjämningsregel är SNAT endast tillgängligt för TCP. Om du bara har en TCP-belastningsutjämningsregel och behöver utgående SNAT för UDP skapar du en UDP-belastningsutjämningsregel från samma klientdel till samma backend-pool.  Detta utlöser SNAT-programmering för UDP.  En arbetsregel eller hälsoavsökning krävs inte.  Grundläggande SKU SNAT programmerar alltid SNAT för båda IP-transportprotokollet, oavsett vilket transportprotokoll som anges i belastningsutjämningsregeln.

Azure förallrar SNAT-portar till IP-konfigurationen för nätverkskortet för varje virtuell dator. När en IP-konfiguration läggs till i poolen förallokeras SNAT-portarna för den här IP-konfigurationen baserat på serverdelspoolens storlek. När utgående flöden skapas förbrukar [PAT](#pat) dynamiskt (upp till den förallokerade gränsen) och släpper dessa portar när flödet stängs eller [inaktiva tidsutgångar](#idletimeout) inträffar.

I följande tabell visas förallokering av SNAT-port för nivåer med backend-poolstorlekar:

| Poolstorlek (VM-instanser) | Förallokerade SNAT-portar per IP-konfiguration|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> När du använder Standard belastningsutjämnare med [flera frontend-IP-adress](load-balancer-multivip-overview.md)multiplicerar varje ip-adress i frontend antalet tillgängliga SNAT-portar i föregående tabell. En serverdelspool med 50 virtuella datorer med 2 belastningsutjämningsregler, var och en med en separat IP-adress för klientdel, använder till exempel 2048 (2x 1024) SNAT-portar per IP-konfiguration. Se information om [flera frontends](#multife).

Kom ihåg att antalet SNAT-portar som är tillgängliga inte översätter direkt till antalet flöden. En enda SNAT-port kan återanvändas för flera unika destinationer. Portar förbrukas endast om det är nödvändigt att göra flöden unika. Information om design- och begränsningsvägledning finns i avsnittet om hur du hanterar den [här uttömliga resursen](#snatexhaust) och avsnittet som beskriver [PAT](#pat).

Om du ändrar storleken på backend-poolen kan det påverka vissa av dina etablerade flöden. Om serverdelspoolens storlek ökar och övergår till nästa nivå återinsätts hälften av de förallokerade SNAT-portarna under övergången till nästa större serverdelspoolnivå. Flöden som är associerade med en återvunnen SNAT-port tar time out och måste återupprättas. Om ett nytt flöde försöker, kommer flödet att lyckas omedelbart så länge förallokerade portar är tillgängliga.

Om serverdelspoolens storlek minskar och övergår till en lägre nivå ökar antalet tillgängliga SNAT-portar. I det här fallet påverkas inte befintliga tilldelade SNAT-portar och deras respektive flöden.

Tilldelningar av SNAT-portar är IP-transportprotokollspecifika (TCP och UDP underhålls separat) och frisläpps på följande villkor:

### <a name="tcp-snat-port-release"></a>TCP SNAT-portsläpp

- Om någon av servrarna/klienten skickar FINACK kommer SNAT-porten att släppas efter 240 sekunder.
- Om en RST visas kommer SNAT-porten att släppas efter 15 sekunder.
- Om inaktiv timeout har nåtts släpps porten.

### <a name="udp-snat-port-release"></a>UDP SNAT-portsläpp

- Om inaktiv timeout har nåtts släpps porten.

## <a name="problem-solving"></a><a name="problemsolving"></a>Problemlösning 

Det här avsnittet är avsett att hjälpa till att minska SNAT-utmattning och som kan uppstå med utgående anslutningar i Azure.

### <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a>Hantera utmattning av SNAT-portar
[Efemära portar](#preallocatedports) som används för [PAT](#pat) är en uttömbar resurs, enligt beskrivningen i [fristående virtuell dator utan en offentlig IP-adress](#defaultsnat) och [belastningsbalanserad virtuell dator utan en offentlig IP-adress](#lb).

Om du vet att du initierar många utgående TCP- eller UDP-anslutningar till samma mål-IP-adress och port, och du observerar misslyckade utgående anslutningar eller får råd av stöd om att du uttömmer SNAT-portar (förallokerade [efemära portar](#preallocatedports) som används av [PAT)](#pat)har du flera allmänna begränsningsalternativ. Granska dessa alternativ och bestäm vad som är tillgängligt och bäst för ditt scenario. Det är möjligt att en eller flera kan hjälpa till att hantera det här scenariot.

Om du har problem med att förstå beteendet för utgående anslutning kan du använda IP-stackstatistik (netstat). Eller det kan vara bra att observera anslutningsbeteenden med hjälp av paketinfångningar. Du kan utföra dessa paketinsamlingar i gästoperativsystemet för din instans eller använda [Network Watcher för paketfångst](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Ändra programmet för återanvändning av anslutningar 
Du kan minska efterfrågan på tillfälliga portar som används för SNAT genom att återanvända anslutningar i ditt program. Detta gäller särskilt för protokoll som HTTP/1.1, där återanvändning av anslutningar är standard. Och andra protokoll som använder HTTP som transport (till exempel REST) kan dra nytta i sin tur. 

Återanvändning är alltid bättre än enskilda, atomära TCP-anslutningar för varje begäran. Återanvändning resulterar i mer högpresterande, mycket effektiva TCP-transaktioner.

#### <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Ändra programmet så att det använder anslutningspooler
Du kan använda ett anslutningspoolningsschema i ditt program, där begäranden är internt fördelade över en fast uppsättning anslutningar (varje återanvändning där det är möjligt). Det här schemat begränsar antalet tillfälliga portar som används och skapar en mer förutsägbar miljö. Det här schemat kan också öka dataflödet för begäranden genom att tillåta flera samtidiga åtgärder när en enda anslutning blockerar svaret på en åtgärd.  

Anslutningspoolning kanske redan finns inom det ramverk som du använder för att utveckla ditt program eller konfigurationsinställningarna för ditt program. Du kan kombinera anslutningspoolning med återanvändning av anslutningar. Dina flera begäranden förbrukar sedan ett fast, förutsägbart antal portar till samma mål-IP-adress och port. Begäranden drar också nytta av effektiv användning av TCP-transaktioner som minskar svarstid och resursutnyttjande. UDP-transaktioner kan också dra nytta, eftersom hantering av antalet UDP-flöden i sin tur kan undvika avgasförhållanden och hantera SNAT-portutnyttjandet.

#### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Ändra programmet så att det använder mindre aggressiv logik för återförsök
När [förallokerade efemära portar](#preallocatedports) som används för [PAT](#pat) är uttömda eller programfel inträffar, aggressiva eller brute force återförsök utan sönderfall och backoff logik orsaka utmattning att inträffa eller kvarstår. Du kan minska efterfrågan på tillfälliga portar genom att använda en mindre aggressiv logik för återförsök. 

Efemära portar har en 4-minuters inaktiv timeout (ej justerbar). Om återförsöken är för aggressiva har utmattningen ingen möjlighet att klara upp på egen hand. Därför är det en viktig del av designen med tanke på hur --- och hur ofta dina programförsökstransaktioner.

#### <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Tilldela en offentlig IP till varje virtuell dator
Om du tilldelar en offentlig IP-adress ändras ditt scenario till [offentlig IP till en virtuell dator](#ilpip). Alla tillfälliga portar för den offentliga IP-adressen som används för varje virtuell dator är tillgängliga för den virtuella datorn. (I motsats till scenarier där tillfälliga portar för en offentlig IP delas med alla virtuella datorer som är associerade med respektive serverdelspool.) Det finns kompromisser att tänka på, till exempel merkostnader för offentliga IP-adresser och den potentiella effekten av vitlistning av ett stort antal enskilda IP-adresser.

>[!NOTE] 
>Det här alternativet är inte tillgängligt för webbarbetareroller.

#### <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Använda flera frontends

När du använder offentliga standardbelastningsutjämningsadresser tilldelar du [flera IP-adresser för frontend för utgående anslutningar](#multife) och [multiplicerar antalet SNAT-portar som är tillgängliga](#preallocatedports).  Skapa en klientdel IP-konfiguration, regel och serverdelspool för att utlösa programmeringen av SNAT till den offentliga IP-adressen för klientdelen.  Regeln behöver inte fungera och en hälsoavsökning behöver inte lyckas.  Om du använder flera frontends för inkommande också (snarare än bara för utgående), bör du använda anpassade hälsoavsökningar väl för att säkerställa tillförlitlighet.

>[!NOTE]
>I de flesta fall är utmattning av SNAT-portar ett tecken på dålig design.  Se till att du förstår varför du uttömmer portar innan du använder fler frontends för att lägga till SNAT-portar.  Du kan maskera ett problem som kan leda till fel senare.

#### <a name="scale-out"></a><a name="scaleout"></a>Skala ut

[Förallokerade portar](#preallocatedports) tilldelas baserat på serverdelspoolens storlek och grupperas i nivåer för att minimera störningar när vissa portar måste omfördelas för att rymma nästa större serverdelspoolstorleksnivå.  Du kan ha ett alternativ för att öka intensiteten i SNAT-portanvändning för en viss klientdel genom att skala backend-poolen till den maximala storleken för en viss nivå.  Detta kräver att programmet skalas ut effektivt.

Till exempel skulle två virtuella datorer i serverda poolen ha 1024 SNAT-portar tillgängliga per IP-konfiguration, vilket ger totalt 2048 SNAT-portar för distributionen.  Om distributionen skulle ökas till 50 virtuella datorer, även om antalet förallokerade portar förblir konstant per virtuell dator, kan totalt 51 200 (50 x 1024) SNAT-portar användas av distributionen.  Om du vill skala ut distributionen kontrollerar du antalet [förallokerade portar](#preallocatedports) per nivå för att se till att du utformar skalan till det maximala för respektive nivå.  I föregående exempel, om du hade valt att skala ut till 51 i stället för 50 instanser, skulle du gå vidare till nästa nivå och sluta med färre SNAT-portar per virtuell dator samt totalt.

Om du skalar ut till nästa större serverdelspoolstorleksnivå finns det potential för vissa av dina utgående anslutningar att time out om allokerade portar måste omfördelas.  Om du bara använder några av dina SNAT-portar är det oviktigt att skala ut över nästa större serverda poolstorlek.  Hälften av de befintliga portarna omfördelas varje gång du flyttar till nästa serverdelspoolnivå.  Om du inte vill att detta ska ske måste du forma distributionen till nivåstorleken.  Eller se till att ditt program kan identifiera och försöka igen efter behov.  TCP keepalives kan hjälpa till att upptäcka när SNAT-portar inte längre fungerar på grund av att de omfördelas.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Använd keepalives för att återställa den utgående tidsgränsen för inaktiv

Utgående anslutningar har en 4-minuters tidsgränsen för inaktiv tidsutskrift. Den här timeouten kan justeras via [utgående regler.](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout) Du kan också använda transport (till exempel TCP keepalives) eller programlager keepalives för att uppdatera ett inaktivt flöde och återställa den här inaktiva tidsgränsen om det behövs.  

När du använder TCP-keepalives är det tillräckligt att aktivera dem på ena sidan av anslutningen. Det är till exempel tillräckligt att aktivera dem på serversidan bara för att återställa inaktiv timer av flödet och det är inte nödvändigt för båda sidor att initierade TCP keepalives.  Liknande begrepp finns för programlager, inklusive konfigurationer av klientserver för databas.  Kontrollera om det finns vilka alternativ som finns för programspecifika keepalives på serversidan.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Identifiera den offentliga IP som en virtuell dator använder
Det finns många sätt att fastställa den offentliga källan IP-adressen för en utgående anslutning. OpenDNS tillhandahåller en tjänst som kan visa den offentliga IP-adressen för din virtuella dator. 

Genom att använda kommandot nslookup kan du skicka en DNS-fråga för namnet myip.opendns.com till OpenDNS-matcharen. Tjänsten returnerar källans IP-adress som användes för att skicka frågan. När du kör följande fråga från den virtuella datorn är svaret den offentliga IP som används för den virtuella datorn:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Förhindra utgående anslutning
Ibland är det inte önskvärt att en virtuell dator tillåts skapa ett utgående flöde. Eller så kan det finnas ett krav på att hantera vilka destinationer som kan nås med utgående flöden eller vilka destinationer som kan börja inkommande flöden. I det här fallet kan du använda [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) för att hantera de destinationer som den virtuella datorn kan nå. Du kan också använda NSG:er för att hantera vilket offentligt mål som kan initiera inkommande flöden.

När du använder en NSG på en belastningsbalanserad virtuell dator bör du vara uppmärksam på [tjänsttaggarna](../virtual-network/security-overview.md#service-tags) och [standardsäkerhetsreglerna](../virtual-network/security-overview.md#default-security-rules). Du måste se till att den virtuella datorn kan ta emot hälsoavsökningsbegäranden från Azure Load Balancer. 

Om en NSG blockerar hälsoavsökningsbegäranden från AZURE_LOADBALANCER standardtaggen misslyckas hälsoavsökningen för den virtuella datorn och den virtuella datorn markeras nedåt. Belastningsutjämnaren slutar skicka nya flöden till den virtuella datorn.

## <a name="limitations"></a>Begränsningar
- Webbarbetareroller utan ett virtuella nätverk och andra Microsoft-plattformstjänster kan vara tillgängliga när endast en intern standardbelastningsutfärdare används på grund av en bieffekt från hur pre-VNet-tjänster och andra plattformstjänster fungerar. Lita inte på denna biverkning eftersom respektive tjänst själv eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du måste skapa utgående anslutning uttryckligen om så önskas när du endast använder en intern standardbelastningsutjämningsfaktor. Standardscenariot [för SNAT](#defaultsnat) som beskrivs i den här artikeln är inte tillgängligt.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md) för standardpublikerad belastningsutjämnare.
- Läs mer om [belastningsutjämnare](load-balancer-overview.md).
- Läs mer om [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).
- Lär dig mer om några av de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
