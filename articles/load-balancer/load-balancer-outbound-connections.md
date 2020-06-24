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
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0d16823f920695f84db74122c9a2ac07de0abdb2
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84907082"
---
# <a name="outbound-connections-in-azure"></a>Utgående anslutningar i Azure

Azure Load Balancer tillhandahåller utgående anslutning för kund distributioner via flera olika mekanismer. I den här artikeln beskrivs vad scenarierna är, när de tillämpas, hur de fungerar och hur du hanterar dem. Om du har problem med utgående anslutning via en Azure Load Balancer kan du läsa [fel söknings guiden för utgående anslutningar](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE] 
>Den här artikeln beskriver endast Resource Manager-distributioner. Granska [utgående anslutningar (klassiska)](load-balancer-outbound-connections-classic.md) för alla klassiska distributions scenarier i Azure.

En distribution i Azure kan kommunicera med slut punkter utanför Azure i det offentliga IP-adressutrymmet. När en instans initierar ett utgående flöde till ett mål i det offentliga IP-adressutrymmet, mappar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När den här mappningen har skapats kan returnera trafik för det här utgående flödet även komma åt den privata IP-adressen som flödet kommer från.

Azure använder käll Network Address Translation (SNAT) för att utföra den här funktionen. När flera privata IP-adresser imiteras bakom en enskild offentlig IP-adress använder Azure [port adress översättning (Pat)](#pat) för att maskera privata IP-adresser. Tillfälliga portar används för PAT och är [förallokerade](#preallocatedports) baserat på Poolens storlek.

Det finns flera [utgående scenarier](#scenarios). Du kan kombinera dessa scenarier efter behov. Granska dem noggrant för att förstå funktionerna, begränsningarna och mönstren som de gäller för distributions modellen och program scenariot. Läs igenom rikt linjerna för att [hantera dessa scenarier](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer och allmän standard-IP ger nya möjligheter och olika beteenden för utgående anslutning.  De är inte samma som för grundläggande SKU: er.  Om du vill använda utgående anslutningar när du arbetar med standard-SKU: er måste du uttryckligen definiera det antingen med offentliga IP-adresser eller offentliga standard Load Balancer.  Detta inkluderar att skapa utgående anslutningar när du använder en intern Standard Load Balancer.  Vi rekommenderar att du alltid använder utgående regler på en offentlig standard Load Balancer.  [Scenario 3](#defaultsnat) är inte tillgängligt med standard-SKU.  Det innebär att när en intern Standard Load Balancer används, måste du vidta åtgärder för att skapa utgående anslutningar för de virtuella datorerna i backend-poolen om den utgående anslutningen önskas.  I kontexten för utgående anslutning, en enda fristående virtuell dator, alla virtuella datorer i en tillgänglighets uppsättning, fungerar alla instanser i en VMSS som en grupp. Det innebär att om en enskild virtuell dator i en tillgänglighets uppsättning är associerad med en standard-SKU fungerar alla VM-instanser i den här tillgänglighets uppsättningen med samma regler som om de är associerade med standard-SKU: n, även om en enskild instans inte är direkt kopplad till den. Det här beteendet observeras även när det gäller en fristående virtuell dator med flera nätverkskort kopplade till en belastningsutjämnare. Om ett nätverkskort läggs till som fristående har det samma beteende. Läs noggrant igenom hela dokumentet för att förstå de övergripande begreppen, granska [standard Load Balancer](load-balancer-standard-overview.md) för skillnader mellan SKU: er och granska [utgående regler](load-balancer-outbound-rules-overview.md).  Med hjälp av utgående regler kan du få en detaljerad kontroll över alla aspekter av utgående anslutningar.

## <a name="scenario-overview"></a><a name="scenarios"></a>Scenarioöversikt

Azure Load Balancer och relaterade resurser definieras explicit när du använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Azure tillhandahåller för närvarande tre olika metoder för att uppnå utgående anslutning för Azure Resource Manager-resurser. 

| SKU:er | Scenario | Metod | IP-protokoll | Beskrivning |
| --- | --- | --- | --- | --- |
| Standard, Basic | [1. virtuell dator med en offentlig IP-adress på instans nivå (med eller utan Load Balancer)](#ilpip) | SNAT, Port maskerad, används inte | TCP, UDP, ICMP, ESP | Azure använder den offentliga IP-adress som tilldelats IP-konfigurationen av instansens nätverkskort. Instansen har alla tillfälliga portar tillgängliga. När du använder Standard Load Balancer stöds inte [utgående regler](load-balancer-outbound-rules-overview.md) om en offentlig IP-adress tilldelas den virtuella datorn. |
| Standard, Basic | [2. offentlig Load Balancer associerad med en virtuell dator (ingen offentlig IP-adress på instansen)](#lb) | SNAT med port maskerad (PAT) med hjälp av Load Balancer-frontend | TCP, UDP |Azure delar den offentliga IP-adressen för offentliga Load Balancer-frontend med flera privata IP-adresser. Azure använder tillfälliga portar för frontend-PAT. När du använder Standard Load Balancer bör du använda [utgående regler](load-balancer-outbound-rules-overview.md) för att uttryckligen definiera utgående anslutningar. |
| ingen eller Basic | [3. fristående virtuell dator (inga Load Balancer, ingen offentlig IP-adress)](#defaultsnat) | SNAT med port maskerad (PAT) | TCP, UDP | Azure utser automatiskt en offentlig IP-adress för SNAT, delar denna offentliga IP-adress med flera privata IP-adresser för tillgänglighets uppsättningen och använder tillfälliga portar för den här offentliga IP-adressen. Det här scenariot är en återgång för föregående scenarier. Vi rekommenderar inte det om du behöver synlighet och kontroll. |

Om du inte vill att en virtuell dator ska kommunicera med slut punkter utanför Azure i det offentliga IP-adressutrymmet, kan du använda nätverks säkerhets grupper (NSG: er) för att blockera åtkomsten vid behov. Avsnittet [förhindra utgående anslutning](#preventoutbound) diskuterar NSG: er mer detaljerat. Vägledning om hur du utformar, implementerar och hanterar ett virtuellt nätverk utan någon utgående åtkomst omfattas inte av den här artikeln.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scenario 1: virtuell dator med offentlig IP-adress

I det här scenariot har den virtuella datorn en offentlig IP-adress tilldelad till den. Så långt som utgående anslutningar berörs spelar det ingen roll om den virtuella datorn är belastningsutjämnad eller inte. Det här scenariot prioriteras framför de andra. När en offentlig IP-adress används används den offentliga IP-adressen för alla utgående flöden i den virtuella datorn.  

En offentlig IP-adress som tilldelas till en virtuell dator är en 1:1-relation (i stället för 1: många) och implementeras som en tillstånds Lös 1:1 NAT.  Port maskering (PAT) används inte och den virtuella datorn har alla tillfälliga portar som är tillgängliga för användning.

Om ditt program initierar många utgående flöden och du upplever antalet SNAT-portar, bör du överväga att tilldela en [offentlig IP-adress för att minimera SNAT-begränsningar](../load-balancer/troubleshoot-outbound-connection.md#assignilpip). Granska [hantering av SNAT-överbelastning](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) i sin helhet.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scenario 2: belastningsutjämnad virtuell dator utan en offentlig IP-adress

I det här scenariot ingår den virtuella datorn i en offentlig Load Balancer backend-pool. Den virtuella datorn har ingen tilldelad offentlig IP-adress. Load Balancer resursen måste konfigureras med en belastnings Utjämnings regel för att skapa en länk mellan den offentliga IP-adresspoolen med backend-poolen.

Om du inte slutför den här regel konfigurationen är beteendet som beskrivs i scenariot för [fristående VM utan offentlig IP-adress](#defaultsnat). Det är inte nödvändigt att regeln har en fungerande lyssnare i backend-poolen för att hälso avsökningen ska lyckas.

När den belastningsutjämnade virtuella datorn skapar ett utgående flöde översätter Azure den privata käll-IP-adressen för det utgående flödet till den offentliga IP-adressen för den offentliga Load Balancer-frontend-modulen. Azure använder SNAT för att utföra den här funktionen. Azure använder också [Pat](#pat) för att maskera flera privata IP-adresser bakom en offentlig IP-adress. 

Tillfälliga portar för belastningsutjämnarens offentliga IP-adress-frontend används för att särskilja enskilda flöden från den virtuella datorn. SNAT använder dynamiskt [förallokerade portar](#preallocatedports) när utgående flöden skapas. I det här sammanhanget kallas de tillfälliga portarna som används för SNAT som SNAT-portar.

SNAT-portar är fördelade enligt beskrivningen i avsnittet om [SNAT och Pat](#snat) . De är en begränsad resurs som kan vara förbrukad. Det är viktigt att förstå hur de används [.](#pat) För att förstå hur du utformar för den här förbrukningen och minimera vid behov, granska [hanteringen av SNAT-belastningen](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

När [flera offentliga IP-adresser är associerade med Load Balancer Basic](load-balancer-multivip-overview.md), är alla dessa offentliga IP-adresser en kandidat för utgående flöden och en väljs slumpmässigt.  

Om du vill övervaka hälso tillståndet för utgående anslutningar med Load Balancer Basic kan du använda [Azure Monitor loggar för Load Balancer](load-balancer-monitor-log.md) och [varnings händelse loggar](load-balancer-monitor-log.md#alert-event-log) för att övervaka meddelanden om SNAT-port överbelastning.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scenario 3: fristående virtuell dator utan en offentlig IP-adress

I det här scenariot är den virtuella datorn inte en del av en offentlig Load Balancer pool (och inte en del av en intern Standard Load Balancer pool) och har ingen tilldelad offentlig IP-adress. När den virtuella datorn skapar ett utgående flöde översätter Azure den privata käll-IP-adressen för det utgående flödet till en offentlig käll-IP-adress. Den offentliga IP-adress som används för det här utgående flödet kan inte konfigureras och räknas inte mot prenumerationens offentliga IP-adressresurs. Den här offentliga IP-adressen tillhör inte dig och kan därför inte reserveras. Om du distribuerar om den virtuella datorn eller tillgänglighets uppsättningen eller den virtuella datorns skalnings uppsättning frigörs den här offentliga IP-adressen och en ny offentlig IP-adress begärs. Använd inte det här scenariot för vit listning IP-adresser. Använd i stället ett av de två andra scenarier där du explicit deklarerar det utgående scenariot och den offentliga IP-adress som ska användas för utgående anslutningar.

>[!IMPORTANT] 
>Det här scenariot gäller även när __bara__ en intern Basic-Load Balancer bifogas. Scenario 3 är __inte tillgängligt__ när en intern standard Load Balancer är kopplad till en virtuell dator.  Du måste uttryckligen skapa [Scenario 1](#ilpip) eller [Scenario 2](#lb) förutom att använda ett internt standard Load Balancer.

Azure använder SNAT med port maskerad ([Pat](#pat)) för att utföra den här funktionen. Det här scenariot liknar [Scenario 2](#lb), förutom att det inte finns någon kontroll över den IP-adress som används. Detta är ett återställnings scenario för när scenarierna 1 och 2 inte finns. Vi rekommenderar inte det här scenariot om du vill ha kontroll över den utgående adressen. Om utgående anslutningar är en viktig del av ditt program bör du välja ett annat scenario.

SNAT-portar är fördelade enligt beskrivningen i avsnittet om [SNAT och Pat](#snat) .  Antalet virtuella datorer som delar en tillgänglighets uppsättning bestämmer vilken förallokerings nivå som gäller.  En fristående virtuell dator utan en tillgänglighets uppsättning är en pool av 1 i syfte att fastställa Förallokering (1024 SNAT-portar). SNAT-portar är en begränsad resurs som kan vara förbrukad. Det är viktigt att förstå hur de används [.](#pat) För att förstå hur du utformar för den här förbrukningen och minimera vid behov, granska [hanteringen av SNAT-belastningen](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Flera kombinerade scenarier

Du kan kombinera scenarier som beskrivs i föregående avsnitt för att uppnå ett visst resultat. När det finns flera scenarier gäller prioritetsordningen: [Scenario 1](#ilpip) har företräde framför [Scenario 2](#lb) och [3](#defaultsnat). [Scenario 2](#lb) åsidosätter [Scenario 3](#defaultsnat).

Ett exempel är en Azure Resource Manager distribution där programmet förlitar sig mycket på utgående anslutningar till ett begränsat antal destinationer, men även tar emot inkommande flöden över en Load Balancer-frontend. I det här fallet kan du kombinera scenarierna 1 och 2 för befrielse. För ytterligare mönster, granska [hantering av SNAT-belastning](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Flera klient delar för utgående flöden

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

Normalt är `disableOutboundSnat` alternativet _falskt_ och indikerar att den här regeln program utgående SNAT för de associerade virtuella datorerna i backend-poolen för belastnings Utjämnings regeln. `disableOutboundSnat`Kan ändras till _Sant_ för att förhindra Load Balancer från att använda den tillhör ande IP-adressen för klient delen för utgående anslutningar för de virtuella datorerna i backend-poolen för den här belastnings Utjämnings regeln.  Och du kan även ange en speciell IP-adress för utgående flöden enligt beskrivningen i [flera kombinerade scenarier](#combinations) .

#### <a name="load-balancer-basic"></a>Load Balancer Basic

Load Balancer Basic väljer en enda klient del som ska användas för utgående flöden när [flera (offentliga) IP-frontend-](load-balancer-multivip-overview.md) klienter är kandidater för utgående flöden. Det här alternativet kan inte konfigureras och du bör fundera på att val av algoritm ska vara slumpmässigt. Du kan ange en speciell IP-adress för utgående flöden enligt beskrivningen i [flera kombinerade scenarier](#combinations).

### <a name="availability-zones"></a><a name="az"></a>Tillgänglighetszoner

När du använder [standard Load Balancer med Tillgänglighetszoner](load-balancer-standard-availability-zones.md)kan zon-redundanta klient delar tillhandahålla zoner som är redundanta utgående SNAT-anslutningar och SNAT-programmerings zon haverier.  När zonindelade-frontend används delar utgående SNAT-anslutningar med den zon som de tillhör.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Förstå SNAT och PAT

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Port maskerad SNAT (PAT)

När en offentlig Load Balancer-resurs är kopplad till virtuella dator instanser, som inte har dedikerade offentliga IP-adresser, skrivs varje utgående anslutnings källa om. Källan skrivs från det virtuella nätverkets privata IP-adressutrymme till belastningsutjämnarens offentliga IP-adress för klient delen. I det offentliga IP-adressutrymmet måste en 5-tupel i flödet (Källans IP-adress, källport, IP-transportläge, målets IP-adress, målport) vara unik. Port maskerade SNAT kan användas med TCP-eller UDP IP-protokoll.

Tillfälliga portar (SNAT-portar) används för att uppnå detta när den privata Källans IP-adress har skrivits om, eftersom flera flöden härstammar från en enda offentlig IP-adress. Porten som maskerade SNAT-algoritmen allokerar SNAT-portar på olika sätt för UDP jämfört med TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>TCP SNAT-portar

En SNAT-port konsumeras per flöde till en IP-adress för ett enda mål, port. För flera TCP-flöden till samma mål-IP-adress, port och protokoll förbrukar varje TCP-flöde en enskild SNAT-port. Detta säkerställer att flödena är unika när de härstammar från samma offentliga IP-adress och går till samma mål-IP-adress, port och protokoll. 

Flera flöden, var och en till en annan mål-IP-adress, port och protokoll, delar en enda SNAT-port. Målets IP-adress, port och protokoll gör flöden unika utan behovet av ytterligare käll portar för att särskilja flöden i det offentliga IP-adressutrymmet.

#### <a name="udp-snat-ports"></a><a name="udp"></a>UDP SNAT-portar

UDP SNAT-portar hanteras av en annan algoritm än TCP SNAT-portar.  Load Balancer använder en algoritm som kallas "Port-restricted kon NAT" för UDP.  En SNAT-port förbrukas för varje flöde, oavsett mål-IP-adress, port.

#### <a name="snat-port-reuse"></a>Åter användning av SNAT-portar

När en port har frigjorts är porten tillgänglig för åter användning vid behov.  Du kan tänka på SNAT-portar som en sekvens från lägsta till högsta tillgängliga för ett specifikt scenario och den första tillgängliga SNAT-porten används för nya anslutningar. 
 
#### <a name="exhaustion"></a>Uttömning

När SNAT-port resurserna är slut så att utgående flöden inte fungerar förrän befintliga flöden frigör SNAT-portar. Load Balancer frigör SNAT-portar när flödet stängs och använder en [tids gräns på 4 minuter](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) för att frigöra SNAT-portar från inaktiva flöden.

UDP SNAT-portar är ofta mycket snabbare än TCP SNAT-portar på grund av skillnaden i algoritmen som används. Du måste utforma och skala test med den här skillnaden i åtanke.

För mönster för att minska de villkor som ofta leder till SNAT-portens belastning, se avsnittet [Hantera SNAT](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) .

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Förallokering av tillfällig port för port maskerad SNAT (PAT)

Azure använder en algoritm för att fastställa antalet förallokerade SNAT-portar som är tillgängliga baserat på storleken på backend-poolen när du använder port maskerad SNAT ([Pat](#pat)). SNAT-portar är tillfälliga portar som är tillgängliga för en viss offentlig IP-datakälla. För varje offentlig IP-adress som är associerad med en belastningsutjämnare finns 64 000-portar som är tillgängliga som SNAT-portar för varje IP-protokoll för transport.

Samma antal SNAT-portar är förallokerade för UDP respektive TCP och konsumeras oberoende av varje IP-protokoll för transport.  Men användning av SNAT-porten skiljer sig åt beroende på om flödet är UDP eller TCP.

>[!IMPORTANT]
>Standard SKU för SKU: er är per IP-överförings protokoll och härleds från belastnings Utjämnings regeln.  Om det bara finns en belastnings Utjämnings regel för TCP, är SNAT bara tillgängligt för TCP. Om du bara har en TCP-belastnings Utjämnings regel och behöver utgående SNAT för UDP, skapar du en regel för belastnings utjämning för UDP från samma klient del till samma backend-pool.  Detta kommer att utlösa SNAT-programmering för UDP.  Ingen arbets regel eller hälso avsökning krävs.  Basic SKU SNAT är alltid SNAT för både IP-transport-protokollet, oavsett vilket transport protokoll som anges i belastnings Utjämnings regeln.

Azure förallokerar SNAT-portar till NÄTVERKSKORTets IP-konfiguration för varje virtuell dator. När en IP-konfiguration läggs till i poolen är SNAT-portarna förallokerade för den här IP-konfigurationen baserat på storleken på backend-poolen. När utgående flöden skapas, förbrukar [Pat](#pat) dynamiskt (upp till den förallokerade gränsen) och släpper dessa portar när flödet stängs eller när [tids gränsen för inaktivitet](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) inträffar.

Följande tabell visar förallokeringar av SNAT-portar för nivåer av backend-poolens storlekar:

| Poolstorlek (VM-instanser) | Förallokerade SNAT-portar per IP-konfiguration|
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 |

>[!NOTE]
> När du använder Standard Load Balancer med [flera klient](load-balancer-multivip-overview.md)delar multiplicerar varje IP-adress för klient delen antalet tillgängliga SNAT-portar i föregående tabell. En backend-pool med 50 VM: s med 2 belastnings Utjämnings regler, var och en med en separat IP-adress för klient delen, använder till exempel 2048 (2x 1024) SNAT-portar per regel. Se information om [flera klient](#multife)delar.

Kom ihåg att antalet SNAT-portar som är tillgängliga inte översätts direkt till antal flöden. En enda SNAT-port kan återanvändas för flera unika mål. Portar används bara om det är nödvändigt att göra flöden unika. Vägledning för design och minskning finns i avsnittet om [hur du hanterar den här exhaustible-resursen](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) och i avsnittet som beskriver [Pat](#pat).

Att ändra storleken på din backend-pool kan påverka vissa av dina upprättade flöden. Om storleken på backend-poolen ökar och övergår till nästa nivå, frigörs hälften av de förallokerade SNAT-portarna under över gången till nästa större nivå i Server delen. Flöden som är associerade med en representerad SNAT-port blir timeout och måste återupprättas. Om ett nytt flöde görs kommer flödet att lyckas omedelbart så länge som förallokerade portar är tillgängliga.

Om storleken på backend-poolen sänks och övergår till en lägre nivå, ökar antalet tillgängliga SNAT-portar. I det här fallet påverkas inte befintliga allokerade SNAT-portar och deras respektive flöden.

SNAT-portar tilldelningar är särskilda IP-protokoll (TCP och UDP underhålls separat) och släpps under följande förhållanden:

### <a name="tcp-snat-port-release"></a>Port version för TCP SNAT

- Om antingen servern/klienten skickar FINACK, frigörs SNAT-porten efter 240 sekunder.
- Om en min visas kommer SNAT-porten att lanseras efter 15 sekunder.
- Om tids gränsen för inaktivitet har nåtts släpps porten.

### <a name="udp-snat-port-release"></a>Port version för UDP SNAT

- Om tids gränsen för inaktivitet har nåtts släpps porten.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Identifiera den offentliga IP-adress som används av en virtuell dator
Det finns många sätt att fastställa IP-adressen för den offentliga källan för en utgående anslutning. OpenDNS tillhandahåller en tjänst som kan visa den offentliga IP-adressen för den virtuella datorn. 

Genom att använda nslookup-kommandot kan du skicka en DNS-fråga för namnet myip.opendns.com till OpenDNS-matcharen. Tjänsten returnerar käll-IP-adressen som användes för att skicka frågan. När du kör följande fråga från din virtuella dator, är svaret den offentliga IP-adress som används för den virtuella datorn:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Förhindra utgående anslutning
Ibland är det olämpligt för en virtuell dator att tillåtas att skapa ett utgående flöde. Eller så kan det finnas ett krav för att hantera vilka mål som kan nås med utgående flöden eller vilka destinationer som kan starta inkommande flöden. I det här fallet kan du använda [nätverks säkerhets grupper](../virtual-network/security-overview.md) för att hantera de destinationer som den virtuella datorn kan komma åt. Du kan också använda NSG: er för att hantera vilka offentliga destinationer som kan initiera inkommande flöden.

När du tillämpar en NSG på en belastningsutjämnad virtuell dator, bör du tänka på [tjänst taggarna](../virtual-network/security-overview.md#service-tags) och [Standard säkerhets reglerna](../virtual-network/security-overview.md#default-security-rules). Du måste se till att den virtuella datorn kan ta emot begär Anden om hälso avsökning från Azure Load Balancer. 

Om en NSG blockerar hälso avsöknings begär Anden från AZURE_LOADBALANCER standard tag gen inte din VM-hälsoavsökning och den virtuella datorn är markerad. Load Balancer slutar skicka nya flöden till den virtuella datorn.

## <a name="connections-to-azure-storage-in-the-same-region"></a>Anslutningar till Azure Storage i samma region

Att ha utgående anslutningar via scenarierna ovan är inte nödvändigt för att ansluta till lagring i samma region som den virtuella datorn. Om du inte vill det använder du nätverks säkerhets grupper (NSG: er) enligt beskrivningen ovan. För anslutning till lagring i andra regioner krävs utgående anslutning. Observera att när du ansluter till lagring från en virtuell dator i samma region, kommer käll-IP-adressen i lagrings diagnostikloggar att vara en intern provideradress och inte den offentliga IP-adressen för den virtuella datorn. Om du vill begränsa åtkomsten till ditt lagrings konto till virtuella datorer i ett eller flera Virtual Network undernät i samma region, använder du [Virtual Network tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) och inte din offentliga IP-adress när du konfigurerar brand väggen för ditt lagrings konto. När tjänstens slut punkter har kon figurer ATS visas din Virtual Network privata IP-adress i dina lagrings diagnostikloggar och inte på den interna provideradress.

## <a name="azure-load-balancer-outbound-rules"></a><a name="outboundrules"></a>Azure Load Balancer utgående regler

Azure Load Balancer tillhandahåller utgående anslutning från ett virtuellt nätverk utöver inkommande.  Utgående regler gör det enkelt att konfigurera offentliga [standard load BALANCERS](load-balancer-standard-overview.md)utgående Network Address Translation.  Du har fullständig deklarativ kontroll över utgående anslutning för att skala och finjustera den här funktionen för dina behov.

![Load Balancer utgående regler](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Med utgående regler kan du använda Load Balancer för att: 
- definiera utgående NAT från grunden.
- skala och finjustera beteendet för befintlig utgående NAT. 

Med utgående regler kan du styra:
- vilka virtuella datorer som ska översättas till vilka offentliga IP-adresser. 
- hur [utgående SNAT-portar](load-balancer-outbound-connections.md#snat) ska allokeras.
- vilka protokoll som ska tillhandahålla utgående översättning för.
- vilken varaktighet som ska användas för utgående timeout för utgående anslutning (4-120 minuter).
- Om du vill skicka en TCP-återställning vid inaktivitet

Utgående regler expandera [Scenario 2](load-balancer-outbound-connections.md#lb) i beskrivningen i artikeln [utgående anslutningar](load-balancer-outbound-connections.md) och scenario prioriteten finns kvar.

### <a name="outbound-rule"></a>Utgående regel

Precis som alla Load Balancer regler följer utgående regler samma välkända syntax som belastnings utjämning och inkommande NAT-regler:

**klient**  +  del **parametrar**  +  **backend-pool**

En utgående regel konfigurerar utgående NAT för _alla virtuella datorer som identifieras av backend-poolen_ som ska översättas till _klient delen_.  _Parametrarna_ och ger ytterligare detaljerad kontroll över den utgående NAT-algoritmen.

Med API-versionen "2018-07-01" tillåts en utgående regel definition som är strukturerad enligt följande:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>Den effektiva utgående NAT-konfigurationen är sammansatt av alla regler för utgående trafik och belastnings utjämning. Utgående regler är stegvisa för belastnings Utjämnings regler. Granska [inaktivera utgående NAT för en belastnings Utjämnings regel](#disablesnat) för att hantera den effektiva utgående NAT-översättningen när flera regler gäller för en virtuell dator. Du måste [inaktivera utgående SNAT](#disablesnat) när du definierar en utgående regel som använder samma offentliga IP-adress som en belastnings Utjämnings regel.

#### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Skala utgående NAT med flera IP-adresser

Även om en utgående regel kan användas med bara en enda offentlig IP-adress, underlättar utgående regler konfigurations bördan för skalning av utgående NAT. Du kan använda flera IP-adresser för att planera för storskaliga scenarier och du kan använda utgående regler för att minska mönstren för [SNAT-överbelastningar](troubleshoot-outbound-connection.md#snatexhaust) .  

Varje ytterligare IP-adress som tillhandahålls av en klient del ger 64 000 tillfälliga portar för Load Balancer att använda sig av SNAT-portar. När belastnings utjämning eller inkommande NAT-regler har en enda klient del, expanderar regeln för utgående trafik klient dels begreppet och tillåter flera klient delar per regel.  Med flera klient delar per regel multipliceras antalet tillgängliga SNAT-portar med varje offentlig IP-adress och stora scenarier kan stödjas.

Dessutom kan du använda ett [offentligt IP-prefix](https://aka.ms/lbpublicipprefix) direkt med en utgående regel.  Med det offentliga IP-prefixet får du enklare skalning och förenklad, vit lista över flöden från din Azure-distribution. Du kan konfigurera en IP-konfiguration för klient delen i Load Balancer resurs för att referera till ett offentligt IP-adressprefix direkt.  Detta gör att Load Balancer exklusiv kontroll över det offentliga IP-prefixet och regeln för utgående trafik automatiskt använder alla offentliga IP-adresser som finns i det offentliga IP-prefixet för utgående anslutningar.  Var och en av IP-adresserna inom intervallet för det offentliga IP-prefixet ger 64 000 tillfälliga portar per IP-adress för Load Balancer som ska användas som SNAT-portar.   

Du kan inte ha enskilda offentliga IP-adressresurser som skapats från det offentliga IP-prefixet när du använder det här alternativet eftersom regeln för utgående trafik måste ha fullständig kontroll över det offentliga IP-prefixet.  Om du behöver mer detaljerad kontroll kan du skapa en individuell offentlig IP-adressresurs från det offentliga IP-prefixet och tilldela flera offentliga IP-adresser individuellt till klient delen för en utgående regel.

#### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Finjustera SNAT-port tilldelning

Du kan använda utgående regler för att finjustera den [automatiska SNAT-port tilldelningen baserat på storleken på backend-poolen](load-balancer-outbound-connections.md#preallocatedports) och allokera mer eller mindre än den automatiska SNAT-port tilldelningen ger.

Använd följande parameter för att allokera 10 000 SNAT-portar per virtuell dator (NIC IP-konfiguration).
 

          "allocatedOutboundPorts": 10000

Varje offentlig IP-adress från alla klient delar av en utgående regel bidrar upp till 64 000 tillfälliga portar för användning som SNAT-portar.  Load Balancer allokerar SNAT-portar i multipler av 8. Om du anger ett värde som inte är delbart med 8 avvisas konfigurations åtgärden.  Om du försöker allokera fler SNAT-portar än vad som är tillgängligt baserat på antalet offentliga IP-adresser avvisas konfigurations åtgärden.  Om du till exempel allokerar 10 000-portar per virtuell dator och 7 virtuella datorer i en backend-pool delar en enskild offentlig IP-adress, så avvisas konfigurationen (7 x 10 000/SNAT-portar > 64 000/SNAT-portar).  Du kan lägga till fler offentliga IP-adresser till klient delen för den utgående regeln för att aktivera scenariot.

Du kan återgå till [Automatisk SNAT-port tilldelning baserat på storlek på backend-poolen](load-balancer-outbound-connections.md#preallocatedports) genom att ange 0 för antal portar. I så fall kommer de första 50 VM-instanserna att få 1024 portar, 51-100 VM-instanser får 512 och så vidare enligt tabellen.

#### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Kontrol lera timeout för utgående inaktivt flöde

Utgående regler tillhandahåller en konfigurations parameter för att kontrol lera utgående tids gräns för utgående trafik och matcha den mot ditt programs behov.  Timeout för utgående inaktivitet är som standard 4 minuter.  Parametern accepterar ett värde från 4 till 120 till ett specifikt antal minuter för tids gränsen för inaktivitet för flöden som matchar den här regeln.

Använd följande parameter för att ange tids gränsen för utgående inaktivitet till 1 timme:

          "idleTimeoutInMinutes": 60

#### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a>Aktivera timeout för TCP-återställning vid inaktivitet

Standard beteendet för Load Balancer är att släppa flödet tyst när tids gränsen för utgående inaktivitet har nåtts.  Med parametern enableTCPReset kan du aktivera ett mer förutsägbart program beteende och kontrol lera om du vill skicka dubbelriktad TCP-återställning (TCP per-) vid tids gränsen för utgående inaktivitet. 

Använd följande parameter för att aktivera TCP-återställning för en utgående regel:

           "enableTcpReset": true

Granska [TCP-återställning vid inaktivitet](https://aka.ms/lbtcpreset) för mer information, inklusive region tillgänglighet.

#### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Stöd för både TCP-och UDP-transport protokoll med en enda regel

Du kommer förmodligen att vilja använda "alla" för transport protokollet i den utgående regeln, men du kan också använda regeln för utgående trafik till ett särskilt transport protokoll även om det behövs.

Använd följande parameter för att ange protokollet till TCP och UDP:

          "protocol": "All"

#### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Inaktivera utgående NAT för en belastnings Utjämnings regel

Som tidigare nämnts tillhandahåller belastnings Utjämnings regler automatisk programmering av utgående NAT. Vissa scenarier är dock förmånen eller kräver att du inaktiverar automatisk programmering av utgående NAT med belastnings Utjämnings regeln så att du kan styra eller förfina beteendet.  Utgående regler har scenarier där det är viktigt att stoppa den automatiska utgående NAT-programmeringen.

Du kan använda den här parametern på två sätt:
- Valfri Undertryckning av Använd den inkommande IP-adressen för utgående NAT.  Utgående regler är stegvisa för belastnings Utjämnings regler och med den här parameter uppsättningen är regeln för utgående trafik kontroll.
  
- Finjustera de utgående NAT-parametrarna för en IP-adress som används för inkommande och utgående samtidigt.  Automatisk utgående NAT-programmering måste inaktive ras för att tillåta en utgående regel att ta kontroll.  För att till exempel ändra SNAT-port tilldelningen för en adress som också används för inkommande, måste den här parametern anges till true.  Om du försöker använda en utgående regel för att omdefiniera parametrarna för en IP-adress som också används för inkommande och inte har publicerat ett utgående NAT-program för belastnings Utjämnings regeln, kommer åtgärden att konfigurera en utgående regel att Miss lyckas.

>[!IMPORTANT]
> Den virtuella datorn har ingen utgående anslutning om du anger den här parametern till true och inte har en utgående regel (eller ett [offentligt IP-scenario på instans nivå](load-balancer-outbound-connections.md#ilpip) för att definiera utgående anslutning.  Vissa åtgärder för din virtuella dator eller ditt program kan bero på att det finns en utgående anslutning tillgänglig. Se till att du förstår beroendena för ditt scenario och att du anser att du har gjort den här ändringen.

Du kan inaktivera utgående SNAT på belastnings Utjämnings regeln med denna konfigurations parameter:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametern disableOutboundSNAT **har** värdet false, vilket innebär att belastnings Utjämnings regeln tillhandahåller automatisk utgående NAT som en spegel avbildning av konfigurationen för belastnings Utjämnings regeln.  

Om du anger disableOutboundSnat till sant för belastnings Utjämnings regeln släpper regeln för belastnings utjämning kontrollen av den automatiska utgående NAT-programmeringen.  Utgående SNAT som ett resultat av belastnings Utjämnings regeln har inaktiverats.

#### <a name="reuse-existing-or-define-new-backend-pools"></a>Återanvänd befintliga eller definiera nya backend-pooler

Utgående regler introducerar inte ett nytt begrepp för att definiera gruppen med virtuella datorer som regeln ska gälla för.  De återanvänder i stället begreppet en backend-pool, som också används för belastnings Utjämnings regler. Du kan använda detta för att förenkla konfigurationen genom att antingen återanvända en befintlig definition för Server delens pool eller skapa en särskild för en utgående regel.

### <a name="scenarios"></a>Scenarier

#### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Rensa utgående anslutningar till en speciell uppsättning offentliga IP-adresser

Du kan använda en regel för utgående trafik för att rensa utgående anslutningar så att de kommer från en speciell uppsättning offentliga IP-adresser för att under lätta vit listning-scenarier.  Den här offentliga IP-adressen kan vara samma som används av en belastnings Utjämnings regel eller en annan uppsättning offentliga IP-adresser än vad som används av en belastnings Utjämnings regel.  

1. Skapa [offentliga IP-prefix](https://aka.ms/lbpublicipprefix) (eller offentliga IP-adresser från offentliga IP-prefix)
2. Skapa en offentlig Standard Load Balancer
3. Skapa frontend-klienter som refererar till det offentliga IP-prefix (eller offentliga IP-adresser) som du vill använda
4. Återanvänd en backend-pool eller skapa en backend-pool och placera de virtuella datorerna i en backend-pool för den offentliga Load Balancer
5. Konfigurera en regel för utgående trafik på den offentliga Load Balancer till program utgående NAT för de här virtuella datorerna med hjälp av klient datorerna
   
Om du inte vill att belastnings Utjämnings regeln ska användas för utgående trafik måste du [inaktivera utgående SNAT](#disablesnat) i belastnings Utjämnings regeln.

#### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Ändra SNAT-port tilldelning

Du kan använda utgående regler för att finjustera den [automatiska SNAT-port tilldelningen baserat på storleken på backend-poolen](load-balancer-outbound-connections.md#preallocatedports).

Om du till exempel har två virtuella datorer som delar en enda offentlig IP-adress för utgående NAT, kanske du vill öka antalet SNAT-portar som har allokerats från standard-1024-portarna om du upplever SNAT-belastning. Varje offentlig IP-adress kan bidra upp till 64 000 tillfälliga portar.  Om du konfigurerar en utgående regel med en enda offentlig IP-adresspool kan du distribuera totalt 64 000 SNAT-portar till virtuella datorer i backend-poolen.  För två virtuella datorer kan högst 32 000 SNAT-portar tilldelas med en utgående regel (2x 32 000 = 64 000).

Granska [utgående anslutningar](load-balancer-outbound-connections.md) och information om hur [SNAT](load-balancer-outbound-connections.md#snat) -portar allokeras och används.

#### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Aktivera endast utgående

Du kan använda en offentlig Standard Load Balancer för att tillhandahålla utgående NAT för en grupp med virtuella datorer. I det här scenariot kan du använda en utgående regel i sig självt, utan att behöva några ytterligare regler.

##### <a name="outbound-nat-for-vms-only-no-inbound"></a>Utgående NAT enbart för virtuella datorer (ingen inkommande)

Definiera en offentlig Standard Load Balancer, placera de virtuella datorerna i backend-poolen och konfigurera en utgående regel för att program mera NAT och rensa utgående anslutningar så att de härstammar från en speciell offentlig IP-adress. Du kan också använda ett offentligt IP-prefix för att förenkla White-List källan för utgående anslutningar.

1. Skapa en offentlig Standard Load Balancer.
2. Skapa en backend-pool och placera de virtuella datorerna i en backend-pool för den offentliga Load Balancer.
3. Konfigurera en utgående regel för den offentliga Load Balancer till program utgående NAT för dessa virtuella datorer.

##### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Utgående NAT för interna Standard Load Balancer scenarier

När du använder en intern Standard Load Balancer är utgående NAT inte tillgängligt förrän den utgående anslutningen uttryckligen har deklarerats. Du kan definiera utgående anslutningar med en utgående regel för att skapa utgående anslutningar för virtuella datorer bakom en intern Standard Load Balancer med dessa steg:

1. Skapa en offentlig Standard Load Balancer.
2. Skapa en backend-pool och placera de virtuella datorerna i en backend-pool för de offentliga Load Balancer utöver de interna Load Balancer.
3. Konfigurera en utgående regel för den offentliga Load Balancer till program utgående NAT för dessa virtuella datorer.

##### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Aktivera både TCP & UDP-protokoll för utgående NAT med en offentlig Standard Load Balancer

- När du använder en offentlig Standard Load Balancer matchas den automatiska utgående NAT-programmeringen med transport protokollet i belastnings Utjämnings regeln.  

   1. Inaktivera utgående SNAT i belastnings Utjämnings regeln.
   2. Konfigurera en utgående regel på samma Load Balancer.
   3. Återanvänd backend-poolen som redan används av dina virtuella datorer.
   4. Ange "protokoll": "alla" som en del av regeln för utgående trafik.

- När endast inkommande NAT-regler används tillhandahålls ingen utgående NAT.

   1. Placera virtuella datorer i en backend-pool.
   2. Definiera en eller flera klient dels-IP-konfigurationer med offentliga IP-adresser eller offentliga IP-prefix.
   3. Konfigurera en utgående regel på samma Load Balancer.
   4. Ange "protokoll": "alla" som en del av regeln för utgående trafik


## <a name="limitations"></a>Begränsningar
- Det maximala antalet användbara tillfälliga portar per IP-adress för klient delen är 64 000.
- Intervallet för den konfigurerbara tids gränsen för utgående inaktivitet är 4 till 120 minuter (240 till 7200 sekunder).
- Load Balancer stöder inte ICMP för utgående NAT.
- Utgående regler kan bara tillämpas på en primär IP-konfiguration för ett nätverkskort.  Det finns stöd för flera nätverkskort.
- Webb arbets roller utan ett virtuellt nätverk och andra Microsofts plattforms tjänster kan nås när bara en intern Standard Load Balancer används på grund av en sido effekt från hur för-VNet-tjänster och andra plattforms tjänster fungerar. Förlita dig inte på denna sido effekt eftersom själva respektive tjänst eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du behöver skapa utgående anslutningar om du vill när du bara använder en intern Standard Load Balancer. [Standard SNAT](#defaultsnat) -scenario 3 som beskrivs i den här artikeln är inte tillgängligt.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md) för offentliga standard Load Balancer.
- Läs mer om [Load Balancer](load-balancer-overview.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
- Lär dig mer om några av de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
