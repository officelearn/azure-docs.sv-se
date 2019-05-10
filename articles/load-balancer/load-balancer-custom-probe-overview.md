---
title: Använda Azure Load Balancer hälsoavsökningar för att skala och ger hög tillgänglighet för din tjänst
titlesuffix: Azure Load Balancer
description: Lär dig hur du använder hälsoavsökningar för att övervaka instanser bakom belastningsutjämnare
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: kumud
ms.openlocfilehash: e488a4a6438279270f3d86dafa16c45eda184059
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415714"
---
# <a name="load-balancer-health-probes"></a>Läsa in Belastningsutjämnarens hälsotillståndsavsökningar

Azure Load Balancer innehåller hälsoavsökningar för användning med belastningsutjämningsregler.  Avsökningen konfiguration och avsökning hälsodeklarationssvar avgör vilka serverdelsinstanser för poolen får nya flöden. Du kan använda hälsoavsökningar för att upptäcka fel på ett program på en backend-instans. Du kan också skapa en anpassad svar på en hälsoavsökning och använda hälsoavsökningen för flödeskontroll för att hantera belastningen eller planerade driftstopp. När en hälsoavsökning inte slutar belastningsutjämnaren att skicka nya flöden till respektive feltillstånd instansen.

Hälsoavsökningar stöder flera protokoll. Tillgängligheten för en viss typ av hälsoavsökning som stöd för ett visst protokoll varierar Load Balancers SKU.  Dessutom varierar beteendet för tjänsten Load Balancers SKU.

| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| [Avsökningen typer](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Avsökning av beteende](#probedown) | Alla avsökningar ned alla TCP-flöden fortsätter. | Alla avsökningar av alla TCP-flöden går ut. | 

> [!IMPORTANT]
> Belastningsutjämnarens hälsotillståndsavsökningar kommer från IP-adressen 168.63.129.16 och får inte vara blockerad vid avsökningar för att märka din instans.  Granska [avsökning källans IP-adress](#probesource) mer information.

## <a name="types"></a>Avsökningen typer

Hälsoavsökningen kan konfigureras för lyssnare med hjälp av följande tre protokoll:

- [TCP-lyssnare](#tcpprobe)
- [HTTP-slutpunkter](#httpprobe)
- [HTTPS-slutpunkter](#httpsprobe)

De tillgängliga typerna av hälsoavsökningar varierar beroende på vilken Load Balancer SKU som valts:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard-SKU |    &#9989; |   &#9989; |   &#9989; |
| Grundläggande SKU |   &#9989; |   &#9989; | &#10060; |

Oavsett vilken avsökningen-typ som du väljer, kan hälsokontroller av slutpunkter se alla portar på en backend-instans, inklusive den port där faktiska tjänsten tillhandahålls.

### <a name="tcpprobe"></a> TCP-avsökning

TCP-avsökningar initiera en anslutning genom att utföra en 3-vägs öppna TCP-handskakningen med definierade porten.  TCP-avsökningar avsluta en anslutning med en fyra sätt Stäng TCP-handskakning.

Minsta avsökningsintervallet är 5 sekunder och det minsta antalet felaktiga svar är 2.  Den totala varaktigheten för alla intervall får inte överstiga 120 sekunder.

En TCP-avsökning misslyckas när:
* TCP-lyssnaren på-instansen svarar inte under tidsperioden.  En avsökning har markerats baserat på antalet misslyckade avsökningen begäranden som har konfigurerats för att gå obesvarade innan du markerar ned avsökningen.
* Avsökningen tar emot en TCP återställa från instansen.

#### <a name="resource-manager-template"></a>Resource Manager-mall

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP / HTTPS-avsökning

> [!NOTE]
> HTTPS-avsökning är endast tillgänglig för [Standardbelastningsutjämnare](load-balancer-standard-overview.md).

HTTP och HTTPS-avsökningar bygger på TCP-avsökning och utfärda en HTTP GET med den angivna sökvägen. Båda dessa avsökningar stöder relativa sökvägar för HTTP GET. HTTPS-avsökningar är samma som HTTP-avsökningar och Lägg till en Transport Layer Security (TLS, tidigare känd som SSL) omslutning. Hälsoavsökningen markeras när instansen svarar med en HTTP-statuskod 200 inom tidsgränsen.  Hälsoavsökningen försöker kontrollera avsökningsporten konfigurerade hälsotillstånd var 15: e sekund som standard. Minsta avsökningsintervallet är 5 sekunder. Den totala varaktigheten för alla intervall får inte överstiga 120 sekunder.

HTTP / HTTPS-avsökningar kan också vara användbart om du vill att uttrycka hälsoavsökning.  implementera en egen logik för att ta bort instanser från belastningsutjämnarens rotation om avsökningsporten också lyssnaren för själva tjänsten. Du kan till exempel vill ta bort en instans om den är över 90% CPU och returnera en icke - 200 HTTP-status. 

Om du använder Cloud Services och har web-roller som använder w3wp.exe kan få du också automatisk övervakning av din webbplats. Fel i koden webbplats tillbaka statusen icke-200 till belastningsutjämnaravsökningen.

HTTP / HTTPS-avsökning misslyckas när:
* Avsökningen slutpunkten returnerar ett HTTP-svarskoden än 200 (till exempel 403, 404 eller 500). Detta markerar ned hälsoavsökningen omedelbart. 
* Slutpunkten för avsökning svarar inte under tidsperioden 31 sekund. Flera avsökningen begäranden kan gå obesvarade innan avsökningen ska markeras som inte körs och förrän summan av alla timeoutintervall har uppnåtts.
* Avsökningen slutpunkten stängs anslutningen via en TCP-återställning.

#### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guestagent"></a>Gästen agenten avsökning (klassisk)

Molntjänstroller (arbetarroller och webbroller) använda en gästagenten för avsökning övervakning som standard.  En avsökning för gäst-agenten är en sista utväg-konfiguration.  Använd alltid en hälsoavsökning explicit med en TCP eller HTTP-avsökning. En avsökning för gäst-agenten är inte så effektiv som uttryckligen definierade avsökningar de flesta scenarier med programmet.

En avsökning för gäst-agenten är en kontroll av gästagenten på den virtuella datorn. Sedan lyssnar och svarar med ett HTTP 200 OK-svar endast när instansen är i tillståndet klar. (Andra tillstånd är upptagen, återanvändning, eller stoppas.)

Mer information finns i [konfigurera tjänstdefinitionsfilen (csdef) för hälsoavsökningar](https://msdn.microsoft.com/library/azure/ee758710.aspx) eller [Kom igång genom att skapa en offentlig belastningsutjämnare för cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Om gästagenten inte kan svara med HTTP 200 OK, markerar belastningsutjämnaren instans som inte svarar. Den stoppas skicka flöden till den instansen. Belastningsutjämnaren fortsätter att kontrollera instansen. 

Om gästagenten svarar med ett 200 HTTP, skickar belastningsutjämnaren nya flöden till den instansen igen.

När du använder en webbroll webbplats koden vanligtvis körs i w3wp.exe som inte övervakas av Azure fabric eller gäst-agenten. Fel i w3wp.exe (till exempel HTTP 500-svar) inte har rapporterats till gästagenten. Belastningsutjämnaren tar därför inte den instansen bort från roteringen.

<a name="health"></a>
## <a name="probehealth"></a>Avsökningen hälsotillstånd

TCP, HTTP och HTTPS hälsoavsökningar anses vara felfria och markera rollinstans som felfri när:

* Hälsoavsökningen genomförs en gång när den virtuella datorn har startats.
* Det angivna antalet avsökningar som krävs för att markera rollinstans som felfri har uppnåtts.

> [!NOTE]
> Om hälsoavsökningen varierar belastningsutjämnaren ska vänta längre innan rollinstansen flyttas tillbaka till felfritt tillstånd. Den här extra väntetid skyddar användaren och infrastrukturen och är en avsiktlig princip.

## <a name="probe-count-and-timeout"></a>Avsökningen antal och timeout

Avsökningen beteende beror på:

* Antal lyckade avsökningar som gör att en instans har markerats som drift.
* Antal misslyckade avsökningar som orsakar en instans är markerad som inaktiv.

Där värdena för timeout och intervall som anges avgör om en instans är markerad som upp eller ned.

## <a name="probedown"></a>Avsökning av beteende

### <a name="tcp-connections"></a>TCP-anslutningar

Ny TCP-anslutningar lyckas till återstående felfri backend-instanser.

Om en backend-instans hälsoavsökning misslyckas, fortsätter du etablerade TCP-anslutningar till den här backend-instansen.

Om alla avsökningar för alla instanser i en serverdelspool misslyckas, skickas inga nya flöden till i serverdelspoolen. Standard Load Balancer tillåter etablerad TCP-flöden för att fortsätta.  Basic Load Balancer avslutas alla befintliga TCP-flöden till i serverdelspoolen.
 
Belastningsutjämnare är en pass via tjänsten (inte avslutar TCP-anslutningar) och flödet är alltid mellan klienten och Virtuellt datorns gästoperativsystem och program. En pool med alla avsökningar av medför en klientdel inte svarar på TCP-anslutning öppen försök (SYN) eftersom det inte finns någon felfri backend-instans för att ta emot flödet och svara med en SYN-bekräftelse

### <a name="udp-datagrams"></a>UDP-datagram

UDP-datagram levereras till Felfri backend-instanser.

UDP anslutningslös och det finns inga läget för energiflöde som spåras för UDP. Om en backend-instans hälsoavsökning misslyckas kan befintliga UDP-flöden flytta till en annan felfri instans i serverdelspoolen.

Om alla avsökningar för alla instanser i en serverdelspool misslyckas avslutas befintliga UDP-flöden för Basic och Standard belastningsutjämnare.

<a name="source"></a>
## <a name="probesource"></a>Avsökning för källans IP-adress

Belastningsutjämnaren använder en distribuerad avsöknings tjänst för sin interna hälsomodellen. Den avsöknings finns på varje värd där virtuella datorer och kan vara programmerade på begäran för att generera hälsoavsökningar per kundens konfiguration. Hälsotillstånd avsökningen trafiken är direkt mellan avsöknings-tjänsten som genererar hälsoavsökningen och kunden VM. Alla Load Balancers hälsoavsökningar kommer från IP-adressen 168.63.129.16 som källa.  Du kan använda IP-adressutrymme i ett virtuellt nätverk som inte är RFC1918 blanksteg.  Använder ett globalt reserverade, som ägs av Microsoft IP-adress minskar risken för en IP-adresskonflikt med IP-adressutrymmet som du använder i det virtuella nätverket.  Den här IP-adressen är samma i alla regioner och ändras inte och är inte en säkerhetsrisk, eftersom endast komponenten som interna Azure-plattformen kan styra ett paket från den här IP-adress. 

Taggen AzureLoadBalancer tjänsten identifierar den här IP-källadressen i din [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) och tillåter hälsotillstånd avsökningen trafik som standard.

Förutom hälsoavsökningar för belastningsutjämnaren, den [följande åtgärder använder denna IP-adress](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Gör det möjligt för VM-agenten till att kommunicera med plattformen att signalera att den är i tillståndet ”klar”
- Möjliggör kommunikation med den virtuella DNS-servern att tillhandahålla filtrerade namnmatchning för kunder som inte definierar anpassade DNS-servrar.  Den här filtreringen garanterar att kunderna endast kan matcha värdnamnen för deras distribution.
- Gör det möjligt för den virtuella datorn att hämta en dynamisk IP-adress från DHCP-tjänsten i Azure.

## <a name="design"></a> Designriktlinjer

Hälsoavsökningar som används för att göra dina flexibel och att den kan skala. En felaktig konfiguration eller felaktig designmönster kan påverka tillgängligheten och skalbarheten för din tjänst. Läs igenom hela dokumentet och fundera över vad inverkan på ditt scenario är om den här avsökningen svar är eller markerats och hur det påverkar tillgängligheten för ditt program-scenario.

När du utformar hälsomodellen för ditt program bör du avsökning en port på en backend-instans som visar hälsotillståndet för den instansen __och__ programtjänsten som du tillhandahåller.  Programporten och avsökningsporten måste inte vara samma.  I vissa situationer kan det vara önskvärt för avsökningsporten att skilja sig från den port som ditt program innehåller tjänsten om.  

Ibland kan det vara praktiskt för ditt program att generera ett hälsotillstånd avsökningen svar för att inte bara identifiera appens hälsotillstånd, utan också signalera direkt till belastningsutjämnaren om din instans ska ta emot eller inte får nya flöden.  Du kan ändra avsökningen svaret så att ditt program att skapa givet och begränsa leverans av nya flöden till en instans genom att inte hälsoavsökningen eller förbereda för underhåll av ditt program och Initiera tömning ditt scenario.  När du använder Standard Load Balancer, en [avsökning av](#probedown) signalen alltid kommer tillåta TCP-flöden för att fortsätta förrän inaktiv timeout eller anslutningen avslutas. 

UDP-belastningsutjämning, bör du skapa en anpassad avsökning hälsotillståndssignal från backend-instans och använder en TCP, HTTP eller HTTPS hälsoavsökning som riktar in sig på motsvarande lyssnaren för att visa hälsotillståndet för UDP-programmet.

När du använder [HA Ports belastningsutjämningsregler](load-balancer-ha-ports-overview.md) med [Standardbelastningsutjämnare](load-balancer-standard-overview.md), alla portar belastningsutjämnas och ett enda hälsotillstånd avsökningen svar måste spegla status för en hel instans.

Översätter inte eller proxy en hälsoavsökning via den instans som tar emot hälsoavsökningen till en annan instans i ditt virtuella nätverk som den här konfigurationen kan leda till kaskadfel i ditt scenario.  Föreställ dig följande: en uppsättning enheter från tredje part har distribuerats i serverdelspoolen för en belastningsutjämnare resurs att tillhandahålla skalbarhet och redundans för installationer och hälsoavsökningen är konfigurerad för att en avsökningsport som tredjeparts-installation-proxyservrar eller översätts till andra virtuella datorer bakom installationen.  Om du avsökning samma port som du använder för att översätta eller Proxybegäranden till andra virtuella datorer bakom installationen att avsökningen svar från en virtuell dator bakom installationen Markera installationen själva döda. Den här konfigurationen kan leda till ett sammanhängande fel i scenariot för hela programmet på grund av en enda serverdel instans bakom installationen.  Utlösaren kan vara ett tillfälligt avsökningsfel som leder till belastningsutjämnaren att skriva ned det ursprungliga målet (installation-instans) och i sin tur kan inaktivera hela programmet scenariot. Avsöker hälsotillståndet för enheten som själva i stället. Val av om avsökningen för att fastställa hälsotillståndssignal är viktigt att känna till virtuella installationer (NVA)-scenarier för nätverk och du måste kontakta programleverantören för lämpliga hälsotillståndssignal är sådana scenarier.

Om du inte tillåter den [käll-IP](#probesource) sondens i din Brandväggsprinciper hälsoavsökningen fungerar inte eftersom det inte går att nå din instans.  I sin tur markerar belastningsutjämnare ned din instans på grund av uteblivna för avsökning.  Den här felkonfiguration kan orsaka belastningen belastningsutjämnade programscenariot misslyckas.

För Belastningsutjämnarens hälsoavsökning att märka din instans du **måste** tillåter denna IP-adress i alla Azure [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) och lokala Brandväggsprinciper.  Som standard varje grupp innehåller de [tjänsttaggen](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer att tillåta trafik för avsökning av hälsotillstånd.

Om du vill testa en avsökning uteblivna eller skriva ned en enskild instans kan du använda en [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) att uttryckligen blockera hälsoavsökningen (målport eller [käll-IP](#probesource)) och simulering av den fel på en avsökning.

Konfigurera inte ditt virtuella nätverk med Microsoft som ägs av IP-adressintervall som innehåller 168.63.129.16.  Sådana konfigurationer kommer står i konflikt med IP-adressen för hälsoavsökningen och kan orsaka scenariot misslyckas.

Om du har flera gränssnitt på den virtuella datorn kan behöva du se till att du svara på avsökningen på gränssnittet som du fick på.  Du kan behöva källnätverket adress Översätt den här adressen på den virtuella datorn på basis av per gränssnitt.

Aktivera inte [TCP tidsstämplar](https://tools.ietf.org/html/rfc1323).  Aktivera TCP tidsstämplar genereras hälsoavsökningar misslyckas på grund av TCP-paket som tas bort av den Virtuella datorns gästoperativsystem OS TCP-stacken, vilket resulterar i belastningsutjämnaren märkning av respektive slutpunkten.  TCP tidsstämplar rutinmässigt är aktiverade som standard på security härdade avbildningar av Virtuella datorer och måste inaktiveras.

## <a name="monitoring"></a>Övervakning

Både offentliga och interna [Standardbelastningsutjämnare](load-balancer-standard-overview.md) exponera per slutpunkt och backend-instans avsökningen hälsostatus som flerdimensionella mätvärden via Azure Monitor. De här måtten kan användas av andra Azure-tjänster eller program från partner. 

Offentliga belastningsutjämnare visar avsökningen hälsostatus sammanfattas per serverdelspool via Azure Monitor-loggar.  Azure Monitor-loggar är inte tillgängliga för interna grundläggande belastningsutjämnare.  Du kan använda [Azure Monitor loggar](load-balancer-monitor-log.md) att söka på den offentliga avsökningen hälsostatusen för belastningsutjämnaren och avsökning antal. Loggning kan användas med Power BI eller Azure Operational Insights för att tillhandahålla statistik om hälsostatusen för belastningsutjämnaren.

## <a name="limitations"></a>Begränsningar

- HTTPS-avsökningar stöder inte ömsesidig autentisering med ett klientcertifikat.
- Hälsoavsökningar misslyckas när TCP tidsstämplar är aktiverade.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md)
- [Komma igång och skapa en offentlig belastningsutjämnare i Resource Manager med hjälp av PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [REST API för hälsoavsökningar](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Begär nya hälsotillstånd avsökningen funktioner med [Belastningsutjämnarens Uservoice](https://aka.ms/lbuservoice)
