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
ms.openlocfilehash: 64acfcffed597640402df557ae419a67ff1e0dcb
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170400"
---
# <a name="outbound-connections-in-azure"></a>Utgående anslutningar i Azure

Azure Load Balancer tillhandahåller utgående anslutning för kund distributioner via flera olika mekanismer. I den här artikeln beskrivs vad scenarierna är, när de tillämpas, hur de fungerar och hur du hanterar dem. Om du har problem med utgående anslutning via en Azure Load Balancer kan du läsa [fel söknings guide för utgående anslutningar] (.. /load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE] 
>Den här artikeln beskriver endast Resource Manager-distributioner. Granska [utgående anslutningar (klassiska)](load-balancer-outbound-connections-classic.md) för alla klassiska distributions scenarier i Azure.

En distribution i Azure kan kommunicera med slut punkter utanför Azure i det offentliga IP-adressutrymmet. När en instans initierar ett utgående flöde till ett mål i det offentliga IP-adressutrymmet, mappar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När den här mappningen har skapats kan returnera trafik för det här utgående flödet även komma åt den privata IP-adressen som flödet kommer från.

Azure använder käll Network Address Translation (SNAT) för att utföra den här funktionen. När flera privata IP-adresser imiteras bakom en enskild offentlig IP-adress använder Azure [port adress översättning (Pat)](#pat) för att maskera privata IP-adresser. Tillfälliga portar används för PAT och är [förallokerade](#preallocatedports) baserat på Poolens storlek.

Det finns flera [utgående scenarier](#scenarios). Du kan kombinera dessa scenarier efter behov. Granska dem noggrant för att förstå funktionerna, begränsningarna och mönstren som de gäller för distributions modellen och program scenariot. Läs igenom rikt linjerna för att [hantera dessa scenarier](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer och allmän standard-IP ger nya möjligheter och olika beteenden för utgående anslutning.  De är inte samma som för grundläggande SKU: er.  Om du vill använda utgående anslutningar när du arbetar med standard-SKU: er måste du uttryckligen definiera det antingen med offentliga IP-adresser eller offentliga standard Load Balancer.  Detta inkluderar att skapa utgående anslutningar när du använder en intern Standard Load Balancer.  Vi rekommenderar att du alltid använder utgående regler på en offentlig standard Load Balancer.  [Scenario 3](#defaultsnat) är inte tillgängligt med standard-SKU.  Det innebär att när en intern Standard Load Balancer används, måste du vidta åtgärder för att skapa utgående anslutningar för de virtuella datorerna i backend-poolen om den utgående anslutningen önskas.  I kontexten för utgående anslutning, en enda fristående virtuell dator, alla virtuella datorer i en tillgänglighets uppsättning, fungerar alla instanser i en VMSS som en grupp. Det innebär att om en enskild virtuell dator i en tillgänglighets uppsättning är associerad med en standard-SKU fungerar alla VM-instanser i den här tillgänglighets uppsättningen med samma regler som om de är associerade med standard-SKU: n, även om en enskild instans inte är direkt kopplad till den. Det här beteendet observeras även när det gäller en fristående virtuell dator med flera nätverkskort kopplade till en belastningsutjämnare. Om ett nätverkskort läggs till som fristående har det samma beteende. Läs noggrant igenom hela dokumentet för att förstå de övergripande begreppen, granska [standard Load Balancer](load-balancer-standard-overview.md) för skillnader mellan SKU: er och granska [utgående regler](load-balancer-outbound-rules-overview.md).  Med hjälp av utgående regler kan du få en detaljerad kontroll över alla aspekter av utgående anslutningar.

## <a name="scenario-overview"></a><a name="scenarios"></a>Scenarioöversikt

Azure Load Balancer och relaterade resurser definieras explicit när du använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Azure tillhandahåller för närvarande tre olika metoder för att uppnå utgående anslutning för Azure Resource Manager-resurser. 

| SKU:er | Scenario | Metod | IP-protokoll | Description |
| --- | --- | --- | --- | --- |
| Standard, Basic | [1. virtuell dator med en offentlig IP-adress på instans nivå (med eller utan Load Balancer)](#ilpip) | SNAT, Port maskerad, används inte | TCP, UDP, ICMP, ESP | Azure använder den offentliga IP-adress som tilldelats IP-konfigurationen av instansens nätverkskort. Instansen har alla tillfälliga portar tillgängliga. När du använder Standard Load Balancer stöds inte [utgående regler](load-balancer-outbound-rules-overview.md) om en offentlig IP-adress tilldelas den virtuella datorn. |
| Standard, Basic | [2. offentlig Load Balancer associerad med en virtuell dator (ingen offentlig IP-adress på instansen)](#lb) | SNAT med port maskerad (PAT) med hjälp av Load Balancer-frontend | TCP, UDP |Azure delar den offentliga IP-adressen för offentliga Load Balancer-frontend med flera privata IP-adresser. Azure använder tillfälliga portar för frontend-PAT. Du bör använda [utgående regler](load-balancer-outbound-rules-overview.md) för att uttryckligen definiera utgående anslutningar. |
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

## <a name="limitations"></a>Begränsningar
- Webb arbets roller utan ett virtuellt nätverk och andra Microsofts plattforms tjänster kan nås när bara en intern Standard Load Balancer används på grund av en sido effekt från hur för-VNet-tjänster och andra plattforms tjänster fungerar. Förlita dig inte på denna sido effekt eftersom själva respektive tjänst eller den underliggande plattformen kan ändras utan föregående meddelande. Du måste alltid anta att du behöver skapa utgående anslutningar om du vill när du bara använder en intern Standard Load Balancer. [Standard SNAT](#defaultsnat) -scenario 3 som beskrivs i den här artikeln är inte tillgängligt.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md) för offentliga standard Load Balancer.
- Läs mer om [Load Balancer](load-balancer-overview.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).
- Lär dig mer om några av de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
