---
title: Använd Azure Load Balancer hälso avsökningar för att skala och tillhandahålla hög tillgänglighet för din tjänst
titlesuffix: Azure Load Balancer
description: Lär dig hur du använder hälsoavsökningar för att övervaka instanser bakom belastningsutjämnare
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: allensu
ms.openlocfilehash: 75009530940a0cce7adb8469ead5f55f509a1faa
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275342"
---
# <a name="load-balancer-health-probes"></a>Läsa in Belastningsutjämnarens hälsotillståndsavsökningar

Azure Load Balancer tillhandahåller hälso avsökningar för användning med regler för belastnings utjämning.  Konfiguration av hälso avsökning och avsöknings svar avgör vilka instanser av backend-pool som får nya flöden. Du kan använda hälsoavsökningar för att upptäcka fel på ett program på en backend-instans. Du kan också skapa ett anpassat svar på en hälso avsökning och använda hälso avsökningen för flödes kontroll för att hantera inläsning eller planerad stillestånds tid. När en hälsoavsökning inte slutar belastningsutjämnaren att skicka nya flöden till respektive feltillstånd instansen.

Hälso avsökningar stöder flera protokoll. Tillgängligheten för en speciell typ av hälso avsökning som stöder ett speciellt protokoll varierar beroende på Load Balancer SKU.  Dessutom varierar funktions sättet för tjänsten med Load Balancer SKU.

| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| [Avsökningen typer](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Avsökning av beteende](#probedown) | Alla avsökningar ned alla TCP-flöden fortsätter. | Alla avsökningar, alla TCP-flöden upphör att gälla. | 

> [!IMPORTANT]
> Load Balancer hälso avsökningar kommer från IP-168.63.129.16 och får inte blockeras för avsökningar för att markera instansen.  Granska [avsökning källans IP-adress](#probesource) mer information.

## <a name="types"></a>Avsökningen typer

Hälso avsökningen kan konfigureras för lyssnare med följande tre protokoll:

- [TCP-lyssnare](#tcpprobe)
- [HTTP-slutpunkter](#httpprobe)
- [HTTPS-slutpunkter](#httpsprobe)

De tillgängliga typerna av hälsoavsökningar varierar beroende på vilken Load Balancer SKU som valts:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard-SKU |    &#9989; |   &#9989; |   &#9989; |
| Grundläggande SKU |   &#9989; |   &#9989; | &#10060; |

Oavsett vilken avsöknings typ du väljer kan hälso avsökningar Observera vilken port som helst på en server dels instans, inklusive den port där den faktiska tjänsten tillhandahålls.

### <a name="tcpprobe"></a> TCP-avsökning

TCP-avsökningar initiera en anslutning genom att utföra en 3-vägs öppna TCP-handskakningen med definierade porten.  TCP-avsökningar avslutar en anslutning med en fyra vägs stängnings-TCP-handskakning.

Minsta avsökningsintervallet är 5 sekunder och det minsta antalet felaktiga svar är 2.  Den totala varaktigheten för alla intervall får inte överstiga 120 sekunder.

En TCP-avsökning misslyckas när:
* TCP-lyssnaren på-instansen svarar inte under tidsperioden.  En avsökning har marker ATS baserat på antalet misslyckade avsöknings begär Anden, som har kon figurer ATS för att svara före avsökningen.
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

HTTP-och HTTPS-avsökningar bygger på TCP-avsökningen och utfärdar en HTTP-hämtning med den angivna sökvägen. Båda dessa avsökningar stöder relativa sökvägar för HTTP GET. HTTPS-avsökningar är samma som HTTP-avsökningar och Lägg till en Transport Layer Security (TLS, tidigare känd som SSL) omslutning. Hälsoavsökningen markeras när instansen svarar med en HTTP-statuskod 200 inom tidsgränsen.  Hälso avsökningen försöker kontrol lera den konfigurerade hälso avsöknings porten var 15: e sekund som standard. Minsta avsökningsintervallet är 5 sekunder. Den totala varaktigheten för alla intervall får inte överstiga 120 sekunder.

HTTP/HTTPS-avsökningar kan också vara användbara om du vill uttrycka hälso avsökningen.  implementera din egen logik för att ta bort instanser från belastnings Utjämnings rotation om avsöknings porten också är lyssnaren för själva tjänsten. Du kan till exempel vill ta bort en instans om den är över 90% CPU och returnera en icke - 200 HTTP-status. 

Om du använder Cloud Services och har web-roller som använder w3wp.exe kan få du också automatisk övervakning av din webbplats. Fel i koden webbplats tillbaka statusen icke-200 till belastningsutjämnaravsökningen.

HTTP / HTTPS-avsökning misslyckas när:
* Avsökningen slutpunkten returnerar ett HTTP-svarskoden än 200 (till exempel 403, 404 eller 500). Hälso avsökningen markeras omedelbart. 
* Avsöknings slut punkten svarar inte alls under tids gränsen på 31 sekunder. Flera avsöknings begär Anden kan bli obesvarade innan avsökningen markeras som ej igång och tills summan av alla timeout-intervall har uppnåtts.
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

Molntjänstroller (arbetarroller och webbroller) använda en gästagenten för avsökning övervakning som standard.  En gästa Gent avsökning är en sista utväg-konfiguration.  Använd alltid en hälso avsökning uttryckligen med en TCP-eller HTTP-avsökning. En avsökning för gäst-agenten är inte så effektiv som uttryckligen definierade avsökningar de flesta scenarier med programmet.

En avsökning för gäst-agenten är en kontroll av gästagenten på den virtuella datorn. Sedan lyssnar och svarar med ett HTTP 200 OK-svar endast när instansen är i tillståndet klar. (Andra tillstånd är upptagen, återanvändning, eller stoppas.)

Mer information finns i [konfigurera tjänstdefinitionsfilen (csdef) för hälsoavsökningar](https://msdn.microsoft.com/library/azure/ee758710.aspx) eller [Kom igång genom att skapa en offentlig belastningsutjämnare för cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Om gästagenten inte kan svara med HTTP 200 OK, markerar belastningsutjämnaren instans som inte svarar. Den stoppas skicka flöden till den instansen. Belastningsutjämnaren fortsätter att kontrollera instansen. 

Om gästagenten svarar med ett 200 HTTP, skickar belastningsutjämnaren nya flöden till den instansen igen.

När du använder en webbroll webbplats koden vanligtvis körs i w3wp.exe som inte övervakas av Azure fabric eller gäst-agenten. Fel i w3wp.exe (till exempel HTTP 500-svar) inte har rapporterats till gästagenten. Belastningsutjämnaren tar därför inte den instansen bort från roteringen.

<a name="health"></a>
## <a name="probehealth"></a>Avsökningen hälsotillstånd

TCP, HTTP och HTTPS hälsoavsökningar anses vara felfria och markera rollinstans som felfri när:

* Hälso avsökningen lyckas när den virtuella datorn startas.
* Det angivna antalet avsökningar som krävs för att markera roll instansen som felfri har uppnåtts.

> [!NOTE]
> Om hälso avsökningen fluktuerar väntar belastningsutjämnaren längre innan den placerar roll instansen igen i felfritt tillstånd. Den här extra väntetid skyddar användaren och infrastrukturen och är en avsiktlig princip.

## <a name="probe-count-and-timeout"></a>Avsökningen antal och timeout

Avsökningen beteende beror på:

* Antal lyckade avsökningar som gör att en instans har markerats som drift.
* Antal misslyckade avsökningar som orsakar en instans är markerad som inaktiv.

De angivna värdena för timeout och intervall avgör om en instans är markerad som aktiv eller inte.

## <a name="probedown"></a>Avsökning av beteende

### <a name="tcp-connections"></a>TCP-anslutningar

Nya TCP-anslutningar kommer att lyckas på de återstående felfria Server dels instanserna.

Om en backend-instans hälsoavsökning misslyckas, fortsätter du etablerade TCP-anslutningar till den här backend-instansen.

Om alla avsökningar för alla instanser i en serverdelspool misslyckas, skickas inga nya flöden till i serverdelspoolen. Standard Load Balancer tillåter etablerad TCP-flöden för att fortsätta.  Basic Load Balancer avslutas alla befintliga TCP-flöden till i serverdelspoolen.
 
Load Balancer är en direkt tjänst (avslutar inte TCP-anslutningar) och flödet är alltid mellan klienten och den virtuella datorns gäst operativ system och program. En pool med alla avsökningar gör att en klient del inte svarar på TCP-anslutningens öppna försök (SYN) eftersom det inte finns någon felfri backend-instans för att ta emot flödet och svara med en SYN-ACK.

### <a name="udp-datagrams"></a>UDP-datagram

UDP-datagram levereras till Felfri backend-instanser.

UDP anslutningslös och det finns inga läget för energiflöde som spåras för UDP. Om en backend-instans hälsoavsökning misslyckas kan befintliga UDP-flöden flytta till en annan felfri instans i serverdelspoolen.

Om alla avsökningar för alla instanser i en serverdelspool misslyckas avslutas befintliga UDP-flöden för Basic och Standard belastningsutjämnare.

<a name="source"></a>
## <a name="probesource"></a>Avsökning för källans IP-adress

Belastningsutjämnaren använder en distribuerad avsöknings tjänst för sin interna hälsomodellen. Tjänsten för avsökning finns på varje värd där virtuella datorer och kan köras på begäran för att generera hälso avsökningar enligt kundens konfiguration. Hälso avsöknings trafiken är direkt mellan avsöknings tjänsten som genererar hälso avsökningen och den virtuella kunden. Alla Load Balancers hälsoavsökningar kommer från IP-adressen 168.63.129.16 som källa.  Du kan använda IP-adressutrymmet i ett VNet som inte är RFC1918 utrymme.  Med en globalt reserverad Microsoft-adress som ägs av Microsoft minskar risken för en IP-adresskonflikt med det IP-adressutrymme som du använder i VNet.  Den här IP-adressen är samma i alla regioner och ändras inte och är inte en säkerhets risk eftersom endast den interna Azure Platform-komponenten kan käll paket från den här IP-adressen. 

AzureLoadBalancer service tag identifierar den här käll-IP-adressen i dina [nätverks säkerhets grupper](../virtual-network/security-overview.md) och tillåter hälso avsöknings trafik som standard.

Utöver Load Balancer hälso avsökningar [använder följande åtgärder följande IP-adress](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Gör det möjligt för VM-agenten till att kommunicera med plattformen att signalera att den är i tillståndet ”klar”
- Möjliggör kommunikation med den virtuella DNS-servern att tillhandahålla filtrerade namnmatchning för kunder som inte definierar anpassade DNS-servrar.  Den här filtreringen garanterar att kunderna endast kan matcha värdnamnen för deras distribution.
- Gör det möjligt för den virtuella datorn att hämta en dynamisk IP-adress från DHCP-tjänsten i Azure.

## <a name="design"></a>Design vägledning

Hälso avsökningar används för att göra tjänsten elastisk och kan skalas. En felaktig konfiguration eller ett dåligt design mönster kan påverka tjänstens tillgänglighet och skalbarhet. Granska hela dokumentet och fundera över vad som påverkar ditt scenario när avsöknings svaret är markerat eller markerat och hur det påverkar tillgängligheten för ditt program scenario.

När du utformar hälso modellen för ditt program bör du avsöka en port på en server dels instans som återspeglar hälso tillståndet för den instansen __och__ den program tjänst som du tillhandahåller.  Program porten och avsöknings porten måste inte vara samma.  I vissa fall kan det vara önskvärt att avsöknings porten är annorlunda än porten som programmet tillhandahåller tjänsten på.  

Ibland kan det vara användbart för ditt program att generera ett hälso avsöknings svar för att inte bara identifiera ditt programs hälsa, utan också signaler direkt till Load Balancer om din instans ska ta emot eller inte ta emot nya flöden.  Du kan ändra avsöknings svaret så att ditt program kan skapa mottryck och begränsa leveransen av nya flöden till en instans genom att inte godkänna hälso avsökningen eller förbereda för underhåll av ditt program och börja tömma ditt scenario.  När du använder Standard Load Balancer kommer [](#probedown) en avsöknings signal att tillåta att TCP-flöden fortsätter tills tids gränsen för inaktivitet eller anslutning stängs. 

För UDP-belastnings utjämning bör du skapa en anpassad hälso avsöknings signal från backend-instansen och använda antingen en TCP-, HTTP-eller HTTPS-hälsoavsökning som riktar sig mot motsvarande lyssnare för att avspegla hälso tillståndet för ditt UDP-program.

När du använder [belastnings Utjämnings regler för ha-portar](load-balancer-ha-ports-overview.md) med [standard Load Balancer](load-balancer-standard-overview.md), är alla portar belastningsutjämnade och ett enda svar på hälso avsökning måste visa status för hela instansen.

Översätt eller Använd inte en hälso avsökning via instansen som tar emot hälso avsökningen till en annan instans i ditt VNet eftersom den här konfigurationen kan leda till sammanhängande fel i ditt scenario.  Tänk dig följande scenario: en uppsättning tredjeparts-enheter distribueras i backend-poolen för en Load Balancer resurs för att ge skalning och redundans för enheterna och hälso avsökningen är konfigurerad för att avsöka en port som används av den tredje partens proxyservrar eller översätts till andra virtuella datorer bakom enheten.  Om du avsöker samma port som du använder för att översätta eller proxyanslutningar till de andra virtuella datorerna bakom installationen, markerar alla avsöknings svar från en enskild virtuell dator bakom installations programmet själva enheten. Den här konfigurationen kan leda till ett överlappande avbrott i hela program scenariot som ett resultat av en enskild server dels instans bakom-enheten.  Utlösaren kan vara ett tillfälligt avsöknings fel som gör att Load Balancer kan markera det ursprungliga målet (installations instansen) och i sin tur inaktivera hela program scenariot. Avsök själva hälso tillståndet för själva enheten i stället. Valet av avsökning för att fastställa hälso signalen är ett viktigt övervägande för NVA-scenarier (Network Virtual Restore) och du måste kontakta din program varu leverantör för att få en lämplig hälso signal för sådana scenarier.

Om du inte tillåter avsökningens [käll-IP-adress](#probesource) i brand Väggs principerna, Miss söker hälso avsökningen eftersom den inte kan komma åt din instans.  I sin tur markerar belastningsutjämnare ned din instans på grund av uteblivna för avsökning.  Den här Felaktiga konfigurationen kan orsaka att ditt belastningsutjämnade program scenario inte fungerar.

För att Load Balancer hälso avsökningen för att märka upp din instans **måste** du tillåta den här IP-adressen i alla Azure- [nätverks säkerhets grupper](../virtual-network/security-overview.md) och lokala brand Väggs principer.  Som standard innehåller varje nätverks säkerhets grupp [service tag-](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer för att tillåta hälso avsöknings trafik.

Om du vill testa ett hälso avsöknings fel eller ange en enskild instans kan du använda en [nätverks säkerhets grupp](../virtual-network/security-overview.md) för att uttryckligen blockera hälso avsökningen (mål port eller [käll-IP](#probesource)) och simulera fel i en avsökning.

Konfigurera inte ditt VNet med det Microsoft-adressintervall som innehåller 168.63.129.16.  Sådana konfigurationer hamnar i en kollision med hälso avsökningens IP-adress och kan orsaka att ditt scenario inte fungerar.

Om du har flera gränssnitt på den virtuella datorn kan behöva du se till att du svara på avsökningen på gränssnittet som du fick på.  Du kan behöva källans nätverks adress översätter den här adressen i den virtuella datorn per gränssnitt.

Aktivera inte [TCP-tidsstämplar](https://tools.ietf.org/html/rfc1323).  Om du aktiverar TCP-tidsstämplar kan hälso avsökningar inte köras på grund av att TCP-paketen har släppts av den virtuella datorns gäst operativ system TCP-stack, vilket leder till att Load Balancer markerar respektive slut punkt.  TCP-tidsstämplar aktive ras rutinmässigt som standard på säkerhets härdning av virtuella dator avbildningar och måste inaktive ras.

## <a name="monitoring"></a>Övervakning

Både offentliga och interna [Standardbelastningsutjämnare](load-balancer-standard-overview.md) exponera per slutpunkt och backend-instans avsökningen hälsostatus som flerdimensionella mätvärden via Azure Monitor. Dessa mått kan utnyttjas av andra Azure-tjänster eller partner program. 

Grundläggande offentliga Load Balancer visar status för hälso avsökningar som sammanfattas per backend-pool via Azure Monitor loggar.  Azure Monitors loggar är inte tillgängliga för interna Basic Load Balancer.  Du kan använda [Azure Monitor loggar](load-balancer-monitor-log.md) för att kontrol lera hälso statusen för avsökningen av offentliga belastningsutjämnare och avsöknings antal. Loggning kan användas med Power BI eller Azure Operational Insights för att tillhandahålla statistik om hälsostatusen för belastningsutjämnaren.

## <a name="limitations"></a>Begränsningar

- HTTPS-avsökningar stöder inte ömsesidig autentisering med ett klientcertifikat.
- Hälso avsökningar fungerar inte när TCP-tidsstämplar är aktiverade.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md)
- [Komma igång och skapa en offentlig belastningsutjämnare i Resource Manager med hjälp av PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [REST API för hälsoavsökningar](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Begär nya hälsotillstånd avsökningen funktioner med [Belastningsutjämnarens Uservoice](https://aka.ms/lbuservoice)
