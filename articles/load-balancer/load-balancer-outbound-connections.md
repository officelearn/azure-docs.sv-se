---
title: Utgående anslutningar
titleSuffix: Azure Load Balancer
description: Den här artikeln förklarar hur Azure gör det möjligt för virtuella datorer att kommunicera med offentliga Internet tjänster.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2020
ms.author: allensu
ms.openlocfilehash: 0fcd0315afcbf38af2b8175deda748522cb335ec
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996851"
---
# <a name="outbound-connections"></a>Utgående anslutningar

Azure Load Balancer tillhandahåller utgående anslutning via olika mekanismer. I den här artikeln beskrivs scenarier och hur du hanterar dem. 


## <a name="scenarios"></a>Scenarier

* Virtuell dator med offentlig IP-adress.
* Virtuell dator utan offentlig IP-adress.
* Virtuell dator utan offentlig IP-adress och utan standard belastnings utjämning.

### <a name="virtual-machine-with-public-ip"></a><a name="scenario1"></a>Virtuell dator med offentlig IP

| Typer | Metod | IP-protokoll |
| ---------- | ------ | ------------ |
| Offentlig belastningsutjämnare eller fristående | [SNAT (käll nätverks adress översättning)](#snat) </br> [Pat (port maskerad)](#pat) används inte. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (Encapsulating Security Payload) |

#### <a name="description"></a>Beskrivning

Azure använder den offentliga IP-adress som tilldelats IP-konfigurationen av instansens nätverkskort för alla utgående flöden. Instansen har alla tillfälliga portar tillgängliga. Det spelar ingen roll om den virtuella datorn är belastningsutjämnad eller inte. Det här scenariot prioriteras framför de andra. 

En offentlig IP-adress som tilldelas till en virtuell dator är en 1:1-relation (i stället för 1: många) och implementeras som en tillstånds Lös 1:1 NAT.

### <a name="virtual-machine-without-public-ip"></a><a name="scenario2"></a>Virtuell dator utan offentlig IP

| Typer | Metod | IP-protokoll |
| ------------ | ------ | ------------ |
| Offentlig lastbalanserare | Användning av belastnings Utjämnings klient del för [SNAT](#snat) med [Pat (port maskerad)](#pat).| TCP </br> UDP |

#### <a name="description"></a>Beskrivning

Belastnings Utjämnings resursen har kon figurer ATS med en belastnings Utjämnings regel. Den här regeln används för att skapa en länk mellan den offentliga IP-klient delen med backend-poolen. 

Om du inte slutför den här regel konfigurationen är beteendet som beskrivs i scenario 3. 

En regel med en lyssnare krävs inte för att hälso avsökningen ska lyckas.

När en virtuell dator skapar ett utgående flöde översätter Azure käll-IP-adressen till den offentliga IP-adressen för den offentliga belastnings Utjämnings klient delen. Den här översättningen görs via [SNAT](#snat). 

Tillfälliga portar för den offentliga IP-adressen för belastningsutjämnaren i klient delen används för att särskilja enskilda flöden från den virtuella datorn. SNAT använder dynamiskt [förallokerade portar](#preallocatedports) när utgående flöden skapas. 

I det här sammanhanget kallas de tillfälliga portarna som används för SNAT som SNAT-portar. SNAT-portar är förallokerade enligt beskrivningen i [standard tilldelnings tabellen för SNAT-portar](#snatporttable).

### <a name="virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a>Virtuell dator utan offentlig IP-adress och utan standard belastnings utjämning

| Typer | Metod | IP-protokoll |
| ------------ | ------ | ------------ |
|Inget </br> Basic Load Balancer | [SNAT](#snat) med [port maskerad (Pat)](#pat)| TCP </br> UDP | 

#### <a name="description"></a>Beskrivning

När den virtuella datorn skapar ett utgående flöde översätter Azure käll-IP-adressen till en offentlig käll-IP-adress. Den här offentliga IP-adressen kan **inte konfigureras** och kan inte reserveras. Den här adressen räknas inte mot prenumerationens offentliga IP-adressresurs. 

Den offentliga IP-adressen kommer att släppas och en ny offentlig IP-adress krävs om du distribuerar om: 

* Virtuell dator
* Tillgänglighetsuppsättning
* Skaluppsättning för virtuella datorer  

Använd inte det här scenariot för att lägga till IP-adresser i en lista över tillåtna. Använd scenario 1 eller 2 där du explicit deklarerar utgående beteende. [SNAT](#snat) -portar är fördelade enligt beskrivningen i [standard tilldelnings tabellen för SNAT-portar](#snatporttable).



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algoritm för port tilldelning

Azure använder en algoritm för att fastställa antalet förallokerade [SNAT](#snat) -portar som är tillgängliga. Algoritmen baserar antalet portar på backend-Poolens storlek. 

För varje offentlig IP-adress som är associerad med en belastningsutjämnare är 64 000 portar tillgängliga som [SNAT](#snat) -portar. Samma antal [SNAT](#snat) -portar är förallokerade för UDP och TCP. Portarna används oberoende av varandra per IP-protokoll. 

Användningen av [SNAT](#snat) -porten är olika beroende på om flödet är UDP eller TCP. 

Portar förbrukas dynamiskt upp till den förallokerade gränsen.  Portarna släpps när flödet stängs eller när tids gränsen för inaktivitet inträffar.

Mer information om tids gränser för inaktivitet finns i [Felsöka utgående anslutningar i Azure Load Balancer](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 

Portar används bara om det är nödvändigt att göra flöden unika.

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> Förallokering av dynamiska SNAT-portar

Följande tabell visar förallokeringar av [SNAT](#snat) -portar för nivåer av backend-poolens storlekar:

| Poolstorlek (VM-instanser) | Förallokerade SNAT-portar per IP-konfiguration |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 |

Att ändra storleken på din backend-pool kan påverka vissa av dina upprättade flöden:

- Storleken på backend-poolen ökar utlösare över gångar till nästa nivå. Hälften av de förallokerade [SNAT](#snat) -portarna återtas under över gången till nästa nivå. 

- Flöden som är kopplade till en återtad [SNAT](#snat) -ports-timeout och Stäng. Dessa flöden måste återupprättas. Om ett nytt flöde görs kommer flödet att lyckas omedelbart så länge som förallokerade portar är tillgängliga.

- Om storleken på backend-poolen sänks och övergår till en lägre nivå, ökar antalet tillgängliga [SNAT](#snat) -portar. De befintliga [SNAT](#snat) -portarna och deras respektive flöden påverkas inte.

## <a name="outbound-rules"></a><a name="outboundrules"></a>Regler för utgående trafik

 Utgående regler gör det möjligt att konfigurera offentliga [standard Load Balancer](load-balancer-standard-overview.md)-Network Address Translation.  

> [!NOTE]
> **Azure Virtual Network NAT** kan tillhandahålla utgående anslutning för virtuella datorer i ett virtuellt nätverk.  Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

Du har fullständig deklarativ kontroll över utgående anslutning för att skala och finjustera den här kapaciteten efter dina behov.

![Utgående regler för belastningsutjämnare](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Med utgående regler kan du använda Load Balancer för att definiera utgående NAT från grunden. Du kan också skala och finjustera beteendet för befintlig utgående NAT.

Med utgående regler kan du styra:

- Vilka virtuella datorer som ska översättas till vilka offentliga IP-adresser.
- Hur utgående [SNAT](#snat) -portar ska anges.
- Vilka protokoll som ska tillhandahålla utgående översättning för.
- Vilken varaktighet som ska användas för utgående timeout för utgående anslutning (4-100 minuter).
- Om du vill skicka en TCP-återställning vid inaktivitet
- Både TCP-och UDP-transport protokoll med en enda regel

### <a name="outbound-rule-definition"></a>Regel definition för utgående trafik

Utgående regler följer samma välbekanta syntax som belastnings utjämning och inkommande NAT- **regler:**  +  Server dels**parametrar**i  +  **backend-poolen**. En utgående regel konfigurerar utgående NAT för _alla virtuella datorer som identifieras av backend-poolen_ som ska översättas till _klient delen_.  _Parametrarna_ ger ytterligare detaljerad kontroll över den utgående NAT-algoritmen.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Skala utgående NAT med flera IP-adresser

Varje ytterligare IP-adress som tillhandahålls av en klient del ger ytterligare 64 000 tillfälliga portar för belastningsutjämnare som ska användas som SNAT-portar. 

Använd flera IP-adresser för att planera för storskaliga scenarier. Använd utgående regler för att minska [SNAT-belastningen](troubleshoot-outbound-connection.md#snatexhaust). 

Du kan också använda ett [offentligt IP-prefix](https://aka.ms/lbpublicipprefix) direkt med en utgående regel. 

Ett offentligt IP-prefix ökar skalningen av distributionen. Prefixet kan läggas till i listan över tillåtna flöden för flöden som kommer från dina Azure-resurser. Du kan konfigurera en IP-konfiguration för klient delen i belastningsutjämnaren för att referera till ett offentligt IP-adressprefix.  

Belastningsutjämnaren har kontroll över det offentliga IP-prefixet. Regeln för utgående trafik använder automatiskt alla offentliga IP-adresser som finns i det offentliga IP-prefixet för utgående anslutningar. 

Var och en av IP-adresserna inom det offentliga IP-prefixet ger ytterligare 64 000 tillfälliga portar per IP-adress för belastningsutjämnare som ska användas som SNAT-portar.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Utgående tids gräns för flödes inaktivitet och TCP-återställning

Utgående regler tillhandahåller en konfigurations parameter för att kontrol lera utgående tids gräns för utgående trafik och matcha den mot ditt programs behov. Timeout för utgående inaktivitet är som standard 4 minuter. Mer information finns i [Konfigurera tids gränser för inaktivitet](load-balancer-tcp-idle-timeout.md). 

Standard beteendet för belastningsutjämnare är att släppa flödet tyst när tids gränsen för utgående inaktivitet har nåtts. `enableTCPReset`Parametern möjliggör ett förutsägbart program beteende och kontroll. Parametern avgör om dubbelriktad TCP-återställning (TCP per-) ska skickas vid tids gränsen för timeout vid utgående inaktivitet. 

Granska [TCP-återställning vid inaktivitet](https://aka.ms/lbtcpreset) för mer information, inklusive region tillgänglighet.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Förhindra utgående anslutning

Regler för belastnings utjämning ger automatisk programmering av utgående NAT. Vissa scenarier eller kräver att du inaktiverar automatisk programmering av utgående NAT med belastnings Utjämnings regeln. Genom att inaktivera via regeln kan du styra eller förfina beteendet.  

Du kan använda den här parametern på två sätt:

1. Förebyggande av inkommande IP-adress för utgående SNAT. Inaktivera utgående SNAT i belastnings Utjämnings regeln.
  
2. Justera de utgående [SNAT](#snat) -parametrarna för en IP-adress som används för inkommande och utgående samtidigt. Den automatiska utgående NAT-filen måste inaktive ras för att tillåta en utgående regel att ta kontroll. Om du vill ändra SNAT-port tilldelningen för en adress som också används för inkommande `disableOutboundSnat` måste parametern vara inställd på True. 

Det går inte att konfigurera en utgående regel om du försöker omdefiniera en IP-adress som används för inkommande.  Inaktivera utgående NAT för belastnings Utjämnings regeln först.

>[!IMPORTANT]
> Den virtuella datorn har inte någon utgående anslutning om du anger den här parametern till true och inte har någon utgående regel för att definiera utgående anslutning.  Vissa åtgärder för din virtuella dator eller ditt program kan bero på att det finns en utgående anslutning tillgänglig. Se till att du förstår beroendena för ditt scenario och att du anser att du har gjort den här ändringen.

Ibland är det inte oönskat för en virtuell dator att skapa ett utgående flöde. Det kan finnas ett krav för att hantera vilka destinationer som tar emot utgående flöden eller vilka destinationer som startar inkommande flöden. Använd [nätverks säkerhets grupper](../virtual-network/security-overview.md) för att hantera de destinationer som den virtuella datorn når. Använd NSG: er för att hantera vilka offentliga destinationer som startar inkommande flöden.

När du tillämpar en NSG på en belastningsutjämnad virtuell dator, bör du tänka på [tjänst taggarna](../virtual-network/security-overview.md#service-tags) och [Standard säkerhets reglerna](../virtual-network/security-overview.md#default-security-rules). Se till att den virtuella datorn kan ta emot hälso avsöknings begär Anden från Azure Load Balancer.

Om en NSG blockerar hälso avsöknings begär Anden från AZURE_LOADBALANCER-Standardtaggen Miss lyckas din VM-avsökning och den virtuella datorn är markerad som ej tillgänglig. Load Balancer slutar skicka nya flöden till den virtuella datorn.

## <a name="scenarios-with-outbound-rules"></a>Scenarier med utgående regler

### <a name="outbound-rules-scenarios"></a>Scenarier för utgående regler

* Konfigurera utgående anslutningar till en speciell uppsättning offentliga IP-adresser eller prefix.
* Ändra [SNAT](#snat) -port tilldelning.
* Aktivera endast utgående.
* Utgående NAT enbart för virtuella datorer (ingen inkommande).
* Utgående NAT för intern standard belastningsutjämnare.
* Aktivera både TCP & UDP-protokoll för utgående NAT med en offentlig standard belastnings utjämning.

### <a name="configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Konfigurera utgående anslutningar till en speciell uppsättning offentliga IP-adresser eller prefix

#### <a name="details"></a>Information

Använd det här scenariot för att skräddarsy utgående anslutningar till härstammar från en uppsättning offentliga IP-adresser. Lägg till offentliga IP-adresser eller prefix till en lista över tillåtna eller nekade baserade på ursprung.

Den här offentliga IP-adressen eller prefixet kan vara samma som används av en belastnings Utjämnings regel. 

Använda en annan offentlig IP-adress eller prefix än vad som används av en belastnings Utjämnings regel:  

1. Skapa ett offentligt IP-prefix eller en offentlig IP-adress.
2. Skapa en offentlig standard Load Balancer 
3. Skapa en klient del som refererar till det offentliga IP-prefixet eller den offentliga IP-adress som du vill använda. 
4. Återanvänd en backend-pool eller skapa en backend-pool och placera de virtuella datorerna i en backend-pool för den offentliga belastningsutjämnaren
5. Konfigurera en utgående regel på den offentliga belastningsutjämnaren för att aktivera utgående NAT för de virtuella datorerna med hjälp av klient delen. Om du inte vill att belastnings Utjämnings regeln ska användas för utgående trafik inaktiverar du utgående SNAT i belastnings Utjämnings regeln.

### <a name="modify-snat-port-allocation"></a><a name="scenario2out"></a>Ändra [SNAT](#snat) -port tilldelning

#### <a name="details"></a>Information

Du kan använda utgående regler för att finjustera den [automatiska SNAT-port tilldelningen baserat på storleken på backend-poolen](load-balancer-outbound-connections.md#preallocatedports). 

Om du upplever SNAT-belastning ökar du antalet [SNAT](#snat) -portar som tilldelas från standardvärdet 1024. 

Varje offentlig IP-adress bidrar med upp till 64 000 tillfälliga portar. Antalet virtuella datorer i backend-poolen bestämmer antalet portar som distribueras till varje virtuell dator. En virtuell dator i backend-poolen har åtkomst till maximalt 64 000 portar. För två virtuella datorer kan högst 32 000 [SNAT](#snat) -portar ges med en utgående regel (2x 32 000 = 64 000). 

Du kan använda utgående regler för att finjustera SNAT-portarna som har angetts som standard. Du får mer eller mindre än standardvärdet för [SNAT](#snat) -port tilldelning. Varje offentlig IP-adress från en klient del för en utgående regel bidrar upp till 64 000 tillfälliga portar för användning som [SNAT](#snat) -portar.  

Load Balancer ger [SNAT](#snat) -portar i multipler av 8. Om du anger ett värde som inte är delbart med 8 avvisas konfigurations åtgärden. 

Om du försöker ge fler [SNAT](#snat) -portar än vad som är tillgängligt baserat på antalet offentliga IP-adresser avvisas konfigurations åtgärden.

Om du ger 10 000-portar per virtuell dator och sju virtuella datorer i en backend-pool delar en enskild offentlig IP-adress, så avvisas konfigurationen. Sju multiplicerat med 10 000 överskrider gränsen på 64 000-porten. Lägg till fler offentliga IP-adresser till klient delen för den utgående regeln för att aktivera scenariot. 

Återgå till [standard port tilldelningen](load-balancer-outbound-connections.md#preallocatedports) genom att ange 0 för antalet portar. De första 50 VM-instanserna får 1024 portar, 51-100 VM-instanser kommer att få 512 upp till maximalt antal instanser.  Mer information om standard tilldelning av SNAT-portar finns i [tilldelnings tabellen för SNAT-portar](#snatporttable).

### <a name="enable-outbound-only"></a><a name="scenario3out"></a>Aktivera endast utgående

#### <a name="details"></a>Information

Använd en offentlig standard belastningsutjämnare för att tillhandahålla utgående NAT för en grupp med virtuella datorer. I det här scenariot använder du en utgående regel i sig själv, utan att behöva några ytterligare regler.

> [!NOTE]
> **Azure Virtual Network NAT** kan tillhandahålla utgående anslutningar för virtuella datorer utan att det krävs någon belastningsutjämnare.  Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

### <a name="outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Utgående NAT enbart för virtuella datorer (ingen inkommande)

> [!NOTE]
> **Azure Virtual Network NAT** kan tillhandahålla utgående anslutningar för virtuella datorer utan att det krävs någon belastningsutjämnare.  Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

#### <a name="details"></a>Information

För det här scenariot:

1. Skapa en offentlig IP-adress eller ett prefix.
2. Skapa en offentlig standard Load Balancer. 
3. Skapa en klient del som är kopplad till den offentliga IP-adressen eller prefixet dedikerat för utgående.
4. Skapa en backend-pool för de virtuella datorerna.
5. Placera de virtuella datorerna i backend-poolen.
6. Konfigurera en regel för utgående trafik för att aktivera utgående NAT.

Använd ett prefix eller en offentlig IP-adress för att skala [SNAT](#snat) -portar. Lägg till källan för utgående anslutningar till en lista över tillåtna eller nekade.

### <a name="outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Utgående NAT för intern standard belastnings utjämning

> [!NOTE]
> **Azure Virtual Network NAT** kan tillhandahålla utgående anslutning för virtuella datorer som använder en intern standard belastnings utjämning.  Se [Vad är Azure Virtual Network NAT?](../virtual-network/nat-overview.md) för mer information.

#### <a name="details"></a>Information

Utgående anslutningar är inte tillgängliga för en intern standard belastningsutjämnare förrän den uttryckligen har deklarerats. 

Mer information finns i [konfiguration av utgående belastnings utjämning](https://docs.microsoft.com/azure/load-balancer/egress-only).


### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Aktivera både TCP & UDP-protokoll för utgående NAT med en offentlig standard belastnings utjämning

#### <a name="details"></a>Information

När du använder en offentlig standard belastningsutjämnare, matchar den automatiskt utgående NAT-överförings protokollet för belastnings Utjämnings regeln. 

1. Inaktivera utgående [SNAT](#snat) i belastnings Utjämnings regeln. 
2. Konfigurera en utgående regel på samma belastningsutjämnare.
3. Återanvänd backend-poolen som redan används av dina virtuella datorer. 
4. Ange "protokoll": "alla" som en del av regeln för utgående trafik. 

När endast inkommande NAT-regler används tillhandahålls ingen utgående NAT. 

1. Placera virtuella datorer i en backend-pool.
2. Definiera en eller flera klient dels-IP-konfigurationer med offentliga IP-adresser eller offentliga IP-prefix 
3. Konfigurera en utgående regel på samma belastningsutjämnare. 
4. Ange "protokoll": "alla" som en del av regeln för utgående trafik

## <a name="terminology"></a><a name="terms"></a> Terminologi

### <a name="source-network-address-translation"></a><a name="snat"></a>Käll Network Address Translation

| Tillämpligt protokoll |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Information

En distribution i Azure kan kommunicera med slut punkter utanför Azure i det offentliga IP-adressutrymmet.

När en instans startar utgående trafik till ett mål med en offentlig IP-adress, mappar Azure dynamiskt den privata IP-adressen för resursen till en offentlig IP-adress. 

När den här mappningen har skapats når retur trafik för det här utgående flödet den privata IP-adressen som flödet kommer från. 

Azure använder **käll Network Address Translation (SNAT)** för att utföra den här funktionen.

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Port maskerad SNAT (PAT)

| Tillämpligt protokoll |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Information

När privata IP-adresser ligger bakom en enda offentlig IP-adress använder Azure **port adress översättning (Pat)** för att dölja de privata IP-adresserna. 

Tillfälliga portar används för PAT och är [förallokerade](#preallocatedports) baserat på Poolens storlek. 

När en offentlig belastningsutjämnare är kopplad till virtuella datorer utan offentliga IP-adresser, skrivs varje utgående anslutnings källa om. 

Källan skrivs från den privata IP-adressen för det virtuella nätverket till belastningsutjämnarens offentliga IP-adress för klient delen. 

I det offentliga IP-adressutrymmet måste det fem-tuple-flödet vara unikt:

* Källans IP-adress
* Källport
* IP-överförings protokoll
* Mål-IP-adress
* Målport 

Port maskerade SNAT kan användas med antingen TCP-eller UDP-protokoll. SNAT-portar används efter att ha skrivit om den privata Källans IP-adress eftersom flera flöden härstammar från en enda offentlig IP-adress. Porten som maskerade SNAT-algoritmen ger SNAT-portar på olika sätt för UDP jämfört med TCP.

### <a name="snat-ports-tcp"></a>SNAT-portar (TCP)

| Tillämpligt protokoll |
|------------------------|
| TCP |

#### <a name="details"></a>Information

SNAT-portar är tillfälliga portar som är tillgängliga för en offentlig IP-datakälla. En SNAT-port förbrukas per flöde till en IP-adress och port för ett enda mål. 

För flera TCP-flöden till samma mål-IP-adress, port och protokoll förbrukar varje TCP-flöde en enskild SNAT-port. 

Den här förbrukningen säkerställer att flödena är unika när de härstammar från samma offentliga IP-adress och reser till:

* Samma mål-IP-adress
* Port
* Protokoll 

Flera flöden delar en enda SNAT-port. 

Målets IP-adress, port och protokoll gör flöden unika utan behovet av ytterligare käll portar för att särskilja flöden i det offentliga IP-adressutrymmet.


### <a name="snat-ports-udp"></a>SNAT-portar (UDP)

| Tillämpligt protokoll |
|------------------------|
| UDP |

#### <a name="details"></a>Information

UDP SNAT-portar hanteras av en annan algoritm än TCP SNAT-portar.  Load Balancer använder en algoritm med namnet port-restricted kon NAT för UDP.

En SNAT-port används för vilken mål-IP-adress och port som ska användas för varje flöde.


### <a name="exhaustion"></a><a name="exhaustion"></a>Uttömning

| Tillämpligt protokoll |
|------------------------|
| Saknas |

#### <a name="details"></a>Information

När SNAT-port resurserna är slut så att utgående flöden inte fungerar förrän befintliga flöden frigör SNAT-portar. Load Balancer återtar SNAT-portar när flödet stängs.

En [timeout för inaktivitet](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) på fyra minuter används av belastningsutjämnaren för att frigöra SNAT-portar.

UDP SNAT-portar är ofta mycket snabbare än TCP SNAT-portar på grund av skillnaden i algoritmen som används. Design-och skalnings test på grund av den här skillnaden.

### <a name="snat-port-release-behavior-tcp"></a>SNAT-port versions beteende (TCP)

| Tillämpligt protokoll |
|------------------------|
| TCP |

#### <a name="details"></a>Information

När antingen en server eller en klient skickar en FINACK, frigörs en SNAT-port efter 240 sekunder. I händelse av en per man visas en SNAT-port efter 15 sekunder. Om tids gränsen för inaktivitet har nåtts släpps porten.

### <a name="snat-port-release-behavior-udp"></a>SNAT-port versions beteende (UDP)

| Tillämpligt protokoll |
|------------------------|
| TCP |

#### <a name="details"></a>Information

Om tids gränsen för inaktivitet har nåtts släpps porten.

### <a name="snat-port-reuse"></a>Åter användning av SNAT-portar

| Tillämpligt protokoll |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>Information

När en port har frigjorts är porten tillgänglig för åter användning. SNAT-portar är en sekvens från lägsta till högsta tillgängligt för ett angivet scenario och den första tillgängliga SNAT-porten används för nya anslutningar.

## <a name="limitations"></a>Begränsningar

- Det maximala antalet användbara tillfälliga portar per IP-adress för klient delen är 64 000.
- Intervallet för den konfigurerbara tids gränsen för utgående inaktivitet är 4 till 120 minuter (240 till 7200 sekunder).
- Load Balancer stöder inte ICMP för utgående NAT.
- Utgående regler kan bara tillämpas på en primär IP-konfiguration för ett nätverkskort.  Du kan inte skapa en utgående regel för den sekundära IP-adressen för en virtuell dator eller NVA. Det finns stöd för flera nätverkskort.
- Webb arbets roller utan ett virtuellt nätverk och andra Microsofts plattforms tjänster kan nås när en intern standard belastningsutjämnare används. Den här åtkomsten beror på en sido effekt på hur för-VNet-tjänster och andra plattforms tjänster fungerar. Förlita dig inte på denna sido effekt eftersom själva respektive tjänst eller den underliggande plattformen kan ändras utan föregående meddelande. Anta alltid att du behöver skapa utgående anslutningar, om det behövs, om du bara vill använda en intern standard belastnings utjämning. Scenario 3 som beskrivs i den här artikeln är inte tillgängligt.

## <a name="next-steps"></a>Nästa steg

Om du har problem med utgående anslutning via en Azure Load Balancer kan du läsa [fel söknings guiden för utgående anslutningar](../load-balancer/troubleshoot-outbound-connection.md).

- Läs mer om [standard Load Balancer](load-balancer-standard-overview.md).
- Se våra [vanliga frågor och svar om Azure Load Balancer](load-balancer-faqs.md).
- Lär dig mer om [utgående regler](load-balancer-outbound-rules-overview.md) för standard offentlig belastningsutjämnare.

