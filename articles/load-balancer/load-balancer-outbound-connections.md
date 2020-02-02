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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: f9135d0a602bfa1f36f9723311e82a4d26abe6c9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934558"
---
# <a name="outbound-connections-in-azure"></a>Utgående anslutningar i Azure

Azure tillhandahåller utgående anslutning för kund distributioner via flera olika mekanismer. I den här artikeln beskrivs vad scenarierna är, när de tillämpas, hur de fungerar och hur du hanterar dem.

>[!NOTE] 
>Den här artikeln beskriver endast Resource Manager-distributioner. Granska [utgående anslutningar (klassiska)](load-balancer-outbound-connections-classic.md) för alla klassiska distributions scenarier i Azure.

En distribution i Azure kan kommunicera med slut punkter utanför Azure i det offentliga IP-adressutrymmet. När en instans initierar ett utgående flöde till ett mål i det offentliga IP-adressutrymmet, mappar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När den här mappningen har skapats kan returnera trafik för det här utgående flödet även komma åt den privata IP-adressen som flödet kommer från.

Azure använder käll Network Address Translation (SNAT) för att utföra den här funktionen. När flera privata IP-adresser imiteras bakom en enskild offentlig IP-adress använder Azure [port adress översättning (Pat)](#pat) för att maskera privata IP-adresser. Tillfälliga portar används för PAT och är [förallokerade](#preallocatedports) baserat på Poolens storlek.

Det finns flera [utgående scenarier](#scenarios). Du kan kombinera dessa scenarier efter behov. Granska dem noggrant för att förstå funktionerna, begränsningarna och mönstren som de gäller för distributions modellen och program scenariot. Läs igenom rikt linjerna för att [hantera dessa scenarier](#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer och allmän standard-IP ger nya möjligheter och olika beteenden för utgående anslutning.  De är inte samma som för grundläggande SKU: er.  Om du vill använda utgående anslutningar när du arbetar med standard-SKU: er måste du uttryckligen definiera det antingen med offentliga IP-adresser eller offentliga standard Load Balancer.  Detta inkluderar att skapa utgående anslutningar när du använder en intern Standard Load Balancer.  Vi rekommenderar att du alltid använder utgående regler på en offentlig standard Load Balancer.  [Scenario 3](#defaultsnat) är inte tillgängligt med standard-SKU.  Det innebär att när en intern Standard Load Balancer används, måste du vidta åtgärder för att skapa utgående anslutningar för de virtuella datorerna i backend-poolen om den utgående anslutningen önskas.  I kontexten för utgående anslutning, en enda fristående virtuell dator, alla virtuella datorer i en tillgänglighets uppsättning, fungerar alla instanser i en VMSS som en grupp. Det innebär att om en enskild virtuell dator i en tillgänglighets uppsättning är associerad med en standard-SKU fungerar alla VM-instanser i den här tillgänglighets uppsättningen med samma regler som om de är associerade med standard-SKU: n, även om en enskild instans inte är direkt kopplad till den.  Läs noggrant igenom hela dokumentet för att förstå de övergripande begreppen, granska [standard Load Balancer](load-balancer-standard-overview.md) för skillnader mellan SKU: er och granska [utgående regler](load-balancer-outbound-rules-overview.md).  Med hjälp av utgående regler kan du få en detaljerad kontroll över alla aspekter av utgående anslutningar.

## <a name="scenarios"></a>Scenario översikt

Azure Load Balancer och relaterade resurser definieras explicit när du använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Azure tillhandahåller för närvarande tre olika metoder för att uppnå utgående anslutning för Azure Resource Manager-resurser. 

| Enheter | Scenario | Metod | IP-protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Standard, Basic | [1. virtuell dator med offentlig IP-adress (med eller utan Load Balancer)](#ilpip) | SNAT, Port maskerad, används inte | TCP, UDP, ICMP, ESP | Azure använder den offentliga IP-adress som tilldelats IP-konfigurationen av instansens nätverkskort. Instansen har alla tillfälliga portar tillgängliga. När du använder Standard Load Balancer bör du använda [utgående regler](load-balancer-outbound-rules-overview.md) för att uttryckligen definiera utgående anslutning |
| Standard, Basic | [2. offentlig Load Balancer associerad med en virtuell dator (ingen offentlig IP-adress på instansen)](#lb) | SNAT med port maskerad (PAT) med hjälp av Load Balancer-frontend | TCP, UDP |Azure delar den offentliga IP-adressen för offentliga Load Balancer-frontend med flera privata IP-adresser. Azure använder tillfälliga portar för frontend-PAT. |
| ingen eller Basic | [3. fristående virtuell dator (inga Load Balancer, ingen offentlig IP-adress)](#defaultsnat) | SNAT med port maskerad (PAT) | TCP, UDP | Azure utser automatiskt en offentlig IP-adress för SNAT, delar denna offentliga IP-adress med flera privata IP-adresser för tillgänglighets uppsättningen och använder tillfälliga portar för den här offentliga IP-adressen. Det här scenariot är en återgång för föregående scenarier. Vi rekommenderar inte det om du behöver synlighet och kontroll. |

Om du inte vill att en virtuell dator ska kommunicera med slut punkter utanför Azure i det offentliga IP-adressutrymmet, kan du använda nätverks säkerhets grupper (NSG: er) för att blockera åtkomsten vid behov. Avsnittet [förhindra utgående anslutning](#preventoutbound) diskuterar NSG: er mer detaljerat. Vägledning om hur du utformar, implementerar och hanterar ett virtuellt nätverk utan någon utgående åtkomst omfattas inte av den här artikeln.

### <a name="ilpip"></a>Scenario 1: virtuell dator med offentlig IP-adress

I det här scenariot har den virtuella datorn en offentlig IP-adress tilldelad till den. Så långt som utgående anslutningar berörs spelar det ingen roll om den virtuella datorn är belastningsutjämnad eller inte. Det här scenariot prioriteras framför de andra. När en offentlig IP-adress används används den offentliga IP-adressen för alla utgående flöden i den virtuella datorn.  

En offentlig IP-adress som tilldelas till en virtuell dator är en 1:1-relation (i stället för 1: många) och implementeras som en tillstånds Lös 1:1 NAT.  Port maskering (PAT) används inte och den virtuella datorn har alla tillfälliga portar som är tillgängliga för användning.

Om ditt program initierar många utgående flöden och du upplever antalet SNAT-portar, bör du överväga att tilldela en [offentlig IP-adress för att minimera SNAT-begränsningar](#assignilpip). Granska [hantering av SNAT-överbelastning](#snatexhaust) i sin helhet.

### <a name="lb"></a>Scenario 2: belastningsutjämnad virtuell dator utan en offentlig IP-adress

I det här scenariot ingår den virtuella datorn i en offentlig Load Balancer backend-pool. Den virtuella datorn har ingen tilldelad offentlig IP-adress. Load Balancer resursen måste konfigureras med en belastnings Utjämnings regel för att skapa en länk mellan den offentliga IP-adresspoolen med backend-poolen.

Om du inte slutför den här regel konfigurationen är beteendet som beskrivs i scenariot för [fristående VM utan offentlig IP-adress](#defaultsnat). Det är inte nödvändigt att regeln har en fungerande lyssnare i backend-poolen för att hälso avsökningen ska lyckas.

När den belastningsutjämnade virtuella datorn skapar ett utgående flöde översätter Azure den privata käll-IP-adressen för det utgående flödet till den offentliga IP-adressen för den offentliga Load Balancer-frontend-modulen. Azure använder SNAT för att utföra den här funktionen. Azure använder också [Pat](#pat) för att maskera flera privata IP-adresser bakom en offentlig IP-adress. 

Tillfälliga portar för belastningsutjämnarens offentliga IP-adress-frontend används för att särskilja enskilda flöden från den virtuella datorn. SNAT använder dynamiskt [förallokerade portar](#preallocatedports) när utgående flöden skapas. I det här sammanhanget kallas de tillfälliga portarna som används för SNAT som SNAT-portar.

SNAT-portar är fördelade enligt beskrivningen i avsnittet om [SNAT och Pat](#snat) . De är en begränsad resurs som kan vara förbrukad. Det är viktigt att förstå hur de används [.](#pat) För att förstå hur du utformar för den här förbrukningen och minimera vid behov, granska [hanteringen av SNAT-belastningen](#snatexhaust).

När [flera offentliga IP-adresser är associerade med Load Balancer Basic](load-balancer-multivip-overview.md), är alla dessa offentliga IP-adresser en kandidat för utgående flöden och en väljs slumpmässigt.  

Om du vill övervaka hälso tillståndet för utgående anslutningar med Load Balancer Basic kan du använda [Azure Monitor loggar för Load Balancer](load-balancer-monitor-log.md) och [varnings händelse loggar](load-balancer-monitor-log.md#alert-event-log) för att övervaka meddelanden om SNAT-port överbelastning.

### <a name="defaultsnat"></a>Scenario 3: fristående virtuell dator utan en offentlig IP-adress

I det här scenariot är den virtuella datorn inte en del av en offentlig Load Balancer pool (och inte en del av en intern Standard Load Balancer pool) och har ingen tilldelad offentlig IP-adress. När den virtuella datorn skapar ett utgående flöde översätter Azure den privata käll-IP-adressen för det utgående flödet till en offentlig käll-IP-adress. Den offentliga IP-adress som används för det här utgående flödet kan inte konfigureras och räknas inte mot prenumerationens offentliga IP-adressresurs. Den här offentliga IP-adressen tillhör inte dig och kan därför inte reserveras. Om du distribuerar om den virtuella datorn eller tillgänglighets uppsättningen eller den virtuella datorns skalnings uppsättning frigörs den här offentliga IP-adressen och en ny offentlig IP-adress begärs. Använd inte det här scenariot för vit listning IP-adresser. Använd i stället ett av de två andra scenarier där du explicit deklarerar det utgående scenariot och den offentliga IP-adress som ska användas för utgående anslutningar.

>[!IMPORTANT] 
>Det här scenariot gäller även när __bara__ en intern Basic-Load Balancer bifogas. Scenario 3 är __inte tillgängligt__ när en intern standard Load Balancer är kopplad till en virtuell dator.  Du måste uttryckligen skapa [Scenario 1](#ilpip) eller [Scenario 2](#lb) förutom att använda ett internt standard Load Balancer.

Azure använder SNAT med port maskerad ([Pat](#pat)) för att utföra den här funktionen. Det här scenariot liknar [Scenario 2](#lb), förutom att det inte finns någon kontroll över den IP-adress som används. Detta är ett återställnings scenario för när scenarierna 1 och 2 inte finns. Vi rekommenderar inte det här scenariot om du vill ha kontroll över den utgående adressen. Om utgående anslutningar är en viktig del av ditt program bör du välja ett annat scenario.

SNAT-portar är fördelade enligt beskrivningen i avsnittet om [SNAT och Pat](#snat) .  Antalet virtuella datorer som delar en tillgänglighets uppsättning bestämmer vilken förallokerings nivå som gäller.  En fristående virtuell dator utan en tillgänglighets uppsättning är en pool av 1 i syfte att fastställa Förallokering (1024 SNAT-portar). SNAT-portar är en begränsad resurs som kan vara förbrukad. Det är viktigt att förstå hur de används [.](#pat) För att förstå hur du utformar för den här förbrukningen och minimera vid behov, granska [hanteringen av SNAT-belastningen](#snatexhaust).

### <a name="combinations"></a>Flera kombinerade scenarier

Du kan kombinera scenarier som beskrivs i föregående avsnitt för att uppnå ett visst resultat. När det finns flera scenarier gäller prioritetsordningen: [Scenario 1](#ilpip) har företräde framför [Scenario 2](#lb) och [3](#defaultsnat). [Scenario 2](#lb) åsidosätter [Scenario 3](#defaultsnat).

Ett exempel är en Azure Resource Manager distribution där programmet förlitar sig mycket på utgående anslutningar till ett begränsat antal destinationer, men även tar emot inkommande flöden över en Load Balancer-frontend. I det här fallet kan du kombinera scenarierna 1 och 2 för befrielse. För ytterligare mönster, granska [hantering av SNAT-belastning](#snatexhaust).

### <a name="multife"></a>Flera klient delar för utgående flöden

#### <a name="standard-load-balancer"></a>Standard Load Balancer

Standard Load Balancer använder alla kandidater för utgående flöden samtidigt när [flera (offentliga) IP-frontend-](load-balancer-multivip-overview.md) enheter finns. Varje klient del multiplicerar antalet tillgängliga förallokerade SNAT-portar om en belastnings Utjämnings regel är aktive rad för utgående anslutningar.

Du kan välja att förhindra att en IP-adress för klient delen används för utgående anslutningar med ett nytt alternativ för belastnings Utjämnings regeln:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalt är `disableOutboundSnat` alternativet _falskt_ och indikerar att den här regeln program utgående SNAT för de associerade virtuella datorerna i backend-poolen för belastnings Utjämnings regeln. `disableOutboundSnat` kan ändras till _Sant_ för att förhindra Load Balancer från att använda den tillhör ande IP-adressen för klient delen för utgående anslutningar för de virtuella datorerna i backend-poolen för den här belastnings Utjämnings regeln.  Och du kan även ange en speciell IP-adress för utgående flöden enligt beskrivningen i [flera kombinerade scenarier](#combinations) .

#### <a name="load-balancer-basic"></a>Load Balancer Basic

Load Balancer Basic väljer en enda klient del som ska användas för utgående flöden när [flera (offentliga) IP-frontend-](load-balancer-multivip-overview.md) klienter är kandidater för utgående flöden. Det här alternativet kan inte konfigureras och du bör fundera på att val av algoritm ska vara slumpmässigt. Du kan ange en speciell IP-adress för utgående flöden enligt beskrivningen i [flera kombinerade scenarier](#combinations).

### <a name="az"></a>Tillgänglighetszoner

När du använder [standard Load Balancer med Tillgänglighetszoner](load-balancer-standard-availability-zones.md)kan zon-redundanta klient delar tillhandahålla zoner som är redundanta utgående SNAT-anslutningar och SNAT-programmerings zon haverier.  När zonindelade-frontend används delar utgående SNAT-anslutningar med den zon som de tillhör.

## <a name="snat"></a>Förstå SNAT och PAT

### <a name="pat"></a>Port maskerad SNAT (PAT)

När en offentlig Load Balancer-resurs är kopplad till virtuella dator instanser skrivs varje utgående anslutnings källa om. Källan skrivs från det virtuella nätverkets privata IP-adressutrymme till belastningsutjämnarens offentliga IP-adress för klient delen. I det offentliga IP-adressutrymmet måste en 5-tupel i flödet (Källans IP-adress, källport, IP-transportläge, målets IP-adress, målport) vara unik.  Port maskerade SNAT kan användas med TCP-eller UDP IP-protokoll.

Tillfälliga portar (SNAT-portar) används för att uppnå detta när den privata Källans IP-adress har skrivits om, eftersom flera flöden härstammar från en enda offentlig IP-adress. Porten som maskerade SNAT-algoritmen allokerar SNAT-portar på olika sätt för UDP jämfört med TCP.

#### <a name="tcp"></a>TCP SNAT-portar

En SNAT-port konsumeras per flöde till en IP-adress för ett enda mål, port. För flera TCP-flöden till samma mål-IP-adress, port och protokoll förbrukar varje TCP-flöde en enskild SNAT-port. Detta säkerställer att flödena är unika när de härstammar från samma offentliga IP-adress och går till samma mål-IP-adress, port och protokoll. 

Flera flöden, var och en till en annan mål-IP-adress, port och protokoll, delar en enda SNAT-port. Målets IP-adress, port och protokoll gör flöden unika utan behovet av ytterligare käll portar för att särskilja flöden i det offentliga IP-adressutrymmet.

#### <a name="udp"></a>UDP SNAT-portar

UDP SNAT-portar hanteras av en annan algoritm än TCP SNAT-portar.  Load Balancer använder en algoritm som kallas "Port-restricted kon NAT" för UDP.  En SNAT-port förbrukas för varje flöde, oavsett mål-IP-adress, port.

#### <a name="snat-port-reuse"></a>Åter användning av SNAT-portar

När en port har frigjorts är porten tillgänglig för åter användning vid behov.  Du kan tänka på SNAT-portar som en sekvens från lägsta till högsta tillgängliga för ett specifikt scenario och den första tillgängliga SNAT-porten används för nya anslutningar. 
 
#### <a name="exhaustion"></a>Uttömning

När SNAT-port resurserna är slut så att utgående flöden inte fungerar förrän befintliga flöden frigör SNAT-portar. Load Balancer frigör SNAT-portar när flödet stängs och använder en [tids gräns på 4 minuter](#idletimeout) för att frigöra SNAT-portar från inaktiva flöden.

UDP SNAT-portar är ofta mycket snabbare än TCP SNAT-portar på grund av skillnaden i algoritmen som används. Du måste utforma och skala test med den här skillnaden i åtanke.

För mönster för att minska de villkor som ofta leder till SNAT-portens belastning, se avsnittet [Hantera SNAT](#snatexhaust) .

### <a name="preallocatedports"></a>Förallokering av tillfällig port för port maskerad SNAT (PAT)

Azure använder en algoritm för att fastställa antalet förallokerade SNAT-portar som är tillgängliga baserat på storleken på backend-poolen när du använder port maskerad SNAT ([Pat](#pat)). SNAT-portar är tillfälliga portar som är tillgängliga för en viss offentlig IP-datakälla.

Samma antal SNAT-portar är förallokerade för UDP respektive TCP och konsumeras oberoende av varje IP-protokoll för transport.  Men användning av SNAT-porten skiljer sig åt beroende på om flödet är UDP eller TCP.

>[!IMPORTANT]
>Standard SKU för SKU: er är per IP-överförings protokoll och härleds från belastnings Utjämnings regeln.  Om det bara finns en belastnings Utjämnings regel för TCP, är SNAT bara tillgängligt för TCP. Om du bara har en TCP-belastnings Utjämnings regel och behöver utgående SNAT för UDP, skapar du en regel för belastnings utjämning för UDP från samma klient del till samma backend-pool.  Detta kommer att utlösa SNAT-programmering för UDP.  Ingen arbets regel eller hälso avsökning krävs.  Basic SKU SNAT är alltid SNAT för både IP-transport-protokollet, oavsett vilket transport protokoll som anges i belastnings Utjämnings regeln.

Azure förallokerar SNAT-portar till NÄTVERKSKORTets IP-konfiguration för varje virtuell dator. När en IP-konfiguration läggs till i poolen är SNAT-portarna förallokerade för den här IP-konfigurationen baserat på storleken på backend-poolen. När utgående flöden skapas, förbrukar [Pat](#pat) dynamiskt (upp till den förallokerade gränsen) och släpper dessa portar när flödet stängs eller när [tids gränsen för inaktivitet](#idletimeout) inträffar.

Följande tabell visar förallokeringar av SNAT-portar för nivåer av backend-poolens storlekar:

| Poolstorlek (VM-instanser) | Förallokerade SNAT-portar per IP-konfiguration|
| --- | --- |
| 1-50 | 1,024 |
| 51–100 | 512 |
| 101–200 | 256 |
| 201–400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 |

>[!NOTE]
> När du använder Standard Load Balancer med [flera klient](load-balancer-multivip-overview.md)delar multiplicerar varje IP-adress för klient delen antalet tillgängliga SNAT-portar i föregående tabell. Till exempel, en backend-pool med 50 VM: s med 2 belastnings Utjämnings regler, var och en med en separat IP-adress för klient delen, använder 2048 (2x 1024) SNAT-portar per IP-konfiguration. Se information om [flera klient](#multife)delar.

Kom ihåg att antalet SNAT-portar som är tillgängliga inte översätts direkt till antal flöden. En enda SNAT-port kan återanvändas för flera unika mål. Portar används bara om det är nödvändigt att göra flöden unika. Vägledning för design och minskning finns i avsnittet om [hur du hanterar den här exhaustible-resursen](#snatexhaust) och i avsnittet som beskriver [Pat](#pat).

Att ändra storleken på din backend-pool kan påverka vissa av dina upprättade flöden. Om storleken på backend-poolen ökar och övergår till nästa nivå, frigörs hälften av de förallokerade SNAT-portarna under över gången till nästa större nivå i Server delen. Flöden som är associerade med en representerad SNAT-port blir timeout och måste återupprättas. Om ett nytt flöde görs kommer flödet att lyckas omedelbart så länge som förallokerade portar är tillgängliga.

Om storleken på backend-poolen sänks och övergår till en lägre nivå, ökar antalet tillgängliga SNAT-portar. I det här fallet påverkas inte befintliga allokerade SNAT-portar och deras respektive flöden.

SNAT-portar tilldelningar är särskilda IP-protokoll (TCP och UDP underhålls separat) och släpps under följande förhållanden:

### <a name="tcp-snat-port-release"></a>Port version för TCP SNAT

- Om antingen servern/klienten skickar FINACK, frigörs SNAT-porten efter 240 sekunder.
- Om en min visas kommer SNAT-porten att lanseras efter 15 sekunder.
- Om tids gränsen för inaktivitet har nåtts släpps porten.

### <a name="udp-snat-port-release"></a>Port version för UDP SNAT

- Om tids gränsen för inaktivitet har nåtts släpps porten.

## <a name="problemsolving"></a>Problemlösning 

Det här avsnittet är avsett för att hjälpa till att minska SNAT-belastningen och som kan ske med utgående anslutningar i Azure.

### <a name="snatexhaust"></a>Hantera SNAT (PAT) port överbelastning
[Tillfälliga portar](#preallocatedports) som används för [Pat](#pat) är en exhaustible-resurs, som beskrivs i [en fristående virtuell dator utan en offentlig IP-adress](#defaultsnat) och [belastningsutjämnad virtuell dator utan en offentlig IP-adress](#lb).

Om du vet att du initierar flera utgående TCP-eller UDP-anslutningar till samma mål-IP-adress och port, och du ser att det inte går att använda utgående anslutningar eller om du får hjälp av stöd för att du tar slut på SNAT-portar (förallokerade [tillfälliga portar](#preallocatedports) som används av [Pat](#pat)) har du flera allmänna alternativ för att minska. Granska de här alternativen och Bestäm vad som är tillgängligt och bäst för ditt scenario. Det är möjligt att en eller flera kan hjälpa dig att hantera det här scenariot.

Om du har problem med att förstå beteendet för utgående anslutning kan du använda IP stack-statistik (netstat). Eller så kan det vara bra att följa anslutnings beteenden med hjälp av paket insamlingar. Du kan utföra dessa paket avbildningar i gäst operativ systemet på din instans eller använda [Network Watcher för paket fångst](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Ändra programmet för att återanvända anslutningar 
Du kan minska efter frågan för tillfälliga portar som används för SNAT genom att återanvända anslutningar i ditt program. Detta gäller särskilt för protokoll som HTTP/1.1, där åter användning av anslutningar är standard. Och andra protokoll som använder HTTP som transport (till exempel REST) kan ha nytta av turn. 

Åter användning är alltid bättre än enskilda, atomiska TCP-anslutningar för varje begäran. Återanvänd resultat i mer utförda, mycket effektiva TCP-transaktioner.

#### <a name="connection pooling"></a>Ändra programmet så att anslutningspoolen används
Du kan använda ett schema för anslutningspoolen i ditt program, där förfrågningar distribueras internt över en fast uppsättning anslutningar (varje återanvändning där det är möjligt). Det här schemat begränsar antalet tillfälliga portar som används och skapar en mer förutsägbar miljö. Det här schemat kan också öka genom strömningen av begär Anden genom att tillåta flera samtidiga åtgärder när en enskild anslutning blockeras när en åtgärd svarar.  

Anslutningspoolen kanske redan finns i ramverket som du använder för att utveckla ditt program eller konfigurations inställningarna för ditt program. Du kan kombinera anslutningspoolen med återkoppling av anslutning. Dina flera begär Anden använder sedan ett fast, förutsägbart antal portar till samma mål-IP-adress och port. Förfrågningarna drar också nytta av effektiv användning av TCP-transaktioner som minskar svars tiden och resursutnyttjande. UDP-transaktioner kan också dra nytta av, eftersom hantering av antalet UDP-flöden kan i sin tur undvika avgas villkor och hantera SNAT-port användningen.

#### <a name="retry logic"></a>Ändra programmet för att använda mindre aggressiva omprövnings logik
När [förallokerade tillfälliga portar](#preallocatedports) som används för [Pat](#pat) är förbrukade eller om program fel uppstår, kan aggressiva eller råa Force-försök utan minskning och backoff logik orsaka att inaktivitet inträffar eller är kvar. Du kan minska efter frågan för tillfälliga portar genom att använda en mindre aggressiva omprövnings logik. 

Tillfälliga portar har en timeoutvärde på 4 minuter (inte justerbar). Om de nya försöken är för aggressiva har uttömdheten ingen möjlighet att rensa upp sig själv. Därför är det viktigt att du överväger hur – och hur ofta – dina program återförsöks transaktioner är en viktig del av designen.

#### <a name="assignilpip"></a>Tilldela varje virtuell dator en offentlig IP-adress
Om du tilldelar en offentlig IP-adress ändras ditt scenario till [offentlig IP-adress till en virtuell dator](#ilpip). Alla tillfälliga portar i den offentliga IP-adressen som används för varje virtuell dator är tillgängliga för den virtuella datorn. (I stället för scenarier där tillfälliga portar i en offentlig IP-adress delas med alla virtuella datorer som är associerade med respektive backend-pool.) Det finns kompromisser att överväga, till exempel den extra kostnaden för offentliga IP-adresser och den potentiella effekten av vit listning ett stort antal enskilda IP-adresser.

>[!NOTE] 
>Det här alternativet är inte tillgängligt för webb arbets roller.

#### <a name="multifesnat"></a>Använd flera klient delar

När du använder offentliga Standard Load Balancer tilldelar du [flera IP-adresser för klient delen för utgående anslutningar](#multife) och [multiplicerar antalet tillgängliga SNAT-portar](#preallocatedports).  Skapa en IP-konfiguration, regel och backend-pool för klient delen för att utlösa programmeringen av SNAT till klient delens offentliga IP-adress.  Regeln behöver inte fungera och en hälso avsökning behöver inte lyckas.  Om du använder flera klient delar för inkommande och samma (i stället för utgående) bör du använda anpassade hälso avsökningar för att säkerställa tillförlitligheten.

>[!NOTE]
>I de flesta fall är uttömda SNAT-portar ett tecken på felaktig design.  Se till att du förstår varför du tar slut på portar innan du använder fler klient delar för att lägga till SNAT-portar.  Du kan maskera ett problem som kan leda till fel senare.

#### <a name="scaleout"></a>Skala ut

[Förallokerade portar](#preallocatedports) tilldelas baserat på storleken på backend-poolen och grupperas i nivåer för att minimera störningar när vissa portar måste allokeras om för att hantera den näst större storleks nivån för backend-poolen.  Du kan ha ett alternativ för att öka intensiteten hos SNAT-porten för en specifik klient del genom att skala din backend-pool till den maximala storleken för en specifik nivå.  Detta kräver att programmet skalas ut effektivt.

Två virtuella datorer i backend-poolen skulle t. ex. ha 1024 SNAT-portar tillgängliga per IP-konfiguration, vilket ger totalt 2048 SNAT-portar för distributionen.  Om distributionen skulle höjas till 50 virtuella datorer, även om antalet förallokerade portar är konstant per virtuell dator, kan totalt 51 200 (50 x 1024) SNAT-portar användas av distributionen.  Om du vill skala ut distributionen kontrollerar du antalet [förallokerade portar](#preallocatedports) per nivå för att se till att du delar ut din skala till maximalt för respektive nivå.  I föregående exempel, om du har valt att skala ut till 51 i stället för 50-instanser, skulle du gå vidare till nästa nivå och få färre SNAT-portar per virtuell dator och totalt.

Om du skalar ut till nästa större storleks nivå för backend-poolen finns det möjlighet för några av dina utgående anslutningar att ta tid ut om allokerade portar måste allokeras om.  Om du bara använder några av dina SNAT-portar är det inconsequential att skala ut över nästa större storlek för backend-poolen.  Hälften av de befintliga portarna allokeras om varje gång som du flyttar till nästa nivå i backend-poolen.  Om du inte vill att detta ska ske måste du forma distributionen till nivå storleken.  Eller kontrol lera att programmet kan identifiera och försök igen om det behövs.  TCP keepalive-tillägg kan hjälpa till att upptäcka när SNAT-portar inte längre fungerar på grund av att de allokeras om.

### <a name="idletimeout"></a>Använd keepalive för att återställa tids gränsen för utgående inaktivitet

Utgående anslutningar har en tids gräns på 4 minuter. Denna timeout är justerbar via [utgående regler](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout). Du kan också använda transport (till exempel TCP keepalive) eller keepalive för program lager för att uppdatera ett inaktivt flöde och återställa denna tids gräns vid behov.  

När du använder TCP keepalive-åtgärder räcker det att aktivera dem på ena sidan av anslutningen. Det räcker till exempel att aktivera dem på Server sidan för att återställa flödets inaktivitet och det är inte nödvändigt för båda sidorna att initiera TCP keepalive.  Det finns liknande koncept för program lager, inklusive databas klient server konfiguration.  Kontrol lera Server sidan för vilka alternativ som finns för programspecifika keepalive-objekt.

## <a name="discoveroutbound"></a>Identifiera den offentliga IP-adress som används av en virtuell dator
Det finns många sätt att fastställa IP-adressen för den offentliga källan för en utgående anslutning. OpenDNS tillhandahåller en tjänst som kan visa den offentliga IP-adressen för den virtuella datorn. 

Genom att använda nslookup-kommandot kan du skicka en DNS-fråga för namnet myip.opendns.com till OpenDNS-matcharen. Tjänsten returnerar käll-IP-adressen som användes för att skicka frågan. När du kör följande fråga från din virtuella dator, är svaret den offentliga IP-adress som används för den virtuella datorn:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Förhindra utgående anslutning
Ibland är det olämpligt för en virtuell dator att tillåtas att skapa ett utgående flöde. Eller så kan det finnas ett krav för att hantera vilka mål som kan nås med utgående flöden eller vilka destinationer som kan starta inkommande flöden. I det här fallet kan du använda [nätverks säkerhets grupper](../virtual-network/security-overview.md) för att hantera de destinationer som den virtuella datorn kan komma åt. Du kan också använda NSG: er för att hantera vilka offentliga destinationer som kan initiera inkommande flöden.

När du tillämpar en NSG på en belastningsutjämnad virtuell dator, bör du tänka på [tjänst taggarna](../virtual-network/security-overview.md#service-tags) och [Standard säkerhets reglerna](../virtual-network/security-overview.md#default-security-rules). Du måste se till att den virtuella datorn kan ta emot begär Anden om hälso avsökning från Azure Load Balancer. 

Om en NSG blockerar hälso avsöknings begär Anden från AZURE_LOADBALANCER standard tag gen inte din VM-hälsoavsökning och den virtuella datorn är markerad. Load Balancer slutar skicka nya flöden till den virtuella datorn.

## <a name="limitations"></a>Begränsningar
- DisableOutboundSnat är inte tillgängligt som ett alternativ när du konfigurerar en belastnings Utjämnings regel i portalen.  Använd REST-, mall-eller klient verktyg i stället.
- Webb arbets roller utan ett virtuellt nätverk och andra Microsofts plattforms tjänster kan nås när bara en intern Standard Load Balancer används på grund av en sido effekt från hur för-VNet-tjänster och andra plattforms tjänster fungerar. Förlita dig inte på denna sido effekt eftersom själva respektive tjänst eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du behöver skapa utgående anslutningar om du vill när du bara använder en intern Standard Load Balancer. [Standard SNAT](#defaultsnat) -scenario 3 som beskrivs i den här artikeln är inte tillgängligt.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md) för offentliga standard Load Balancer.
- Läs mer om [Load Balancer](load-balancer-overview.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
- Lär dig mer om några av de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
