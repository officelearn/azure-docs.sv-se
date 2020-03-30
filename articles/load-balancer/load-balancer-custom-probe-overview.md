---
title: Hälsoavsökningar för att skala och tillhandahålla HA för din tjänst
titleSuffix: Azure Load Balancer
description: I den här artikeln kan du lära dig hur du använder hälsoavsökningar för att övervaka instanser bakom Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: ec1507e09a183f8d466a456b70151861f5f0e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159446"
---
# <a name="load-balancer-health-probes"></a>Hälsoavsökningar för Load Balancer

När du använder belastningsutjämningsregler med Azure Load Balancer måste du ange hälsoavsökningar så att belastningsutjämnaren kan identifiera serverdelsslutpunktsstatus.  Konfigurationen av hälsoavsökningen och avsökningssvaren avgör vilka servergruppsinstanser som ska ta emot nya flöden. Du kan använda hälsoavsökningar för att identifiera fel på ett program på en serverdelsslutpunkt. Du kan också generera ett anpassat svar på en hälsoavsökning och använda hälsoavsökningen för flödeskontroll för att hantera belastning eller planerade driftstopp. När en hälsoavsökning misslyckas slutar belastningsutjämnaren att skicka nya flöden till respektive felinstans. Utgående anslutning påverkas inte, endast inkommande anslutning påverkas.

Hälsoavsökningar stöder flera protokoll. Tillgängligheten för ett specifikt hälsoavsökningsprotokoll varierar beroende på belastningsutjämnare SKU.  Dessutom varierar tjänstens beteende beroende på belastningsutjämnad SKU som visas i den här tabellen:

| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| [Avsökningstyper](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Söka nedåt beteende](#probedown) | Alla sonder ner, alla TCP-flöden fortsätter. | Alla avsökningar nedåt, alla TCP-flöden upphör att gälla. | 


>[!IMPORTANT]
>Granska det här dokumentet i sin helhet, inklusive viktig [designvägledning](#design) nedan för att skapa en tillförlitlig tjänst.

>[!IMPORTANT]
>Belastningsutjämnarhälsoavsökningar kommer från IP-adressen 168.63.129.16 och får inte blockeras för att avsökningar ska markera din instans.  Granska [avsökningskällans IP-adress](#probesource) för mer information.

>[!IMPORTANT]
>Oavsett konfigurerad timeout-tröskel, kommer HTTP(S) Belastningsutjämnings hälsoavsökningar automatiskt avsöka en instans om servern returnerar någon statuskod som inte är HTTP 200 OK eller om anslutningen avslutas via TCP-återställning.

## <a name="probe-configuration"></a><a name="probes"></a>Avsökningskonfiguration

Hälsoavsökningskonfigurationen består av följande element:

- Varaktighet för intervallet mellan enskilda sonder
- Antal sondsvar som måste observeras innan sonden övergår till ett annat tillstånd
- Protokoll av sonden
- Sondens port
- HTTP-sökväg som ska användas för HTTP GET vid användning av HTTP(S) avsökningar

>[!NOTE]
>En avsökningsdefinition är inte obligatorisk eller kontrolleras när du använder Azure PowerShell, Azure CLI, Mallar eller API. Avsökningsverifieringstester görs endast när du använder Azure Portal.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Förstå applikationssignal, detektion av signalen och reaktionen på plattformen

Antalet sondsvar gäller både

- antalet lyckade avsökningar som gör att en instans kan markeras som upp, och
- antalet timed-out-avsökningar som gör att en instans markeras som ned.

De angivna tidsgräns- och intervallvärdena avgör om en instans ska markeras som upp eller ned.  Intervallets varaktighet multiplicerat med antalet avsökningssvar avgör hur länge avsökningssvaren måste identifieras.  Och tjänsten kommer att reagera efter de nödvändiga sonderna har uppnåtts.

Vi kan illustrera beteendet ytterligare med ett exempel. Om du har ställt in antalet avsökningssvar på 2 och intervallet till 5 sekunder betyder det att 2 time-out-avbrott för sonden måste observeras inom 10 sekunders intervall.  Eftersom tidpunkten för en avsökning inte synkroniseras när ditt program kan ändra tillstånd, kan vi binda tiden för att identifiera genom två scenarier:

1. Om ditt program börjar producera ett time-out-sondsvar strax innan den första sonden anländer, tar det att upptäcka dessa händelser 10 sekunder (2 x 5 sekunder) plus hur länge programmet börjar signalera en time-out till när den första sonden anländer.  Du kan anta att den här identifieringen tar drygt 10 sekunder.
2. Om ditt program börjar producera en time out-sondsvar strax efter att den första avsökningen anländer, kommer identifieringen av dessa händelser inte att påbörjas förrän nästa sond anländer (och time out) plus ytterligare 10 sekunder (2 x 5 sekunder).  Du kan anta att den här identifieringen tar knappt 15 sekunder.

I det här exemplet, när identifiering har inträffat, kommer plattformen att ta sedan en liten tid att reagera på den här ändringen.  Detta innebär en beroende på 

1. när programmet börjar ändra tillstånd och
2. när denna ändring upptäcks och uppfyllde de nödvändiga kriterierna (antal avsökningar som skickas med det angivna intervallet) och
3. när detektionen har kommunicerats över plattformen 

Du kan anta att reaktionen på en time out-sondsvar tar mellan minst drygt 10 sekunder och högst drygt 15 sekunder att reagera på en ändring av signalen från programmet.  Detta exempel ges för att illustrera vad som sker, men det är inte möjligt att förutse en exakt varaktighet utöver den ovan grova vägledning som illustreras i detta exempel.
 
## <a name="probe-types"></a><a name="types"></a>Avsökningstyper

Det protokoll som används av hälsoavsökningen kan konfigureras till något av följande:

- [TCP-lyssnare](#tcpprobe)
- [HTTP-slutpunkter](#httpprobe)
- [HTTPS-slutpunkter](#httpsprobe)

De tillgängliga protokollen beror på vilken belastningsutjämnad SKU som används:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard-SKU |    &#9989; |   &#9989; |   &#9989; |
| Grundläggande SKU |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a>TCP-sond

TCP-avsökningar initierar en anslutning genom att utföra ett trevägs öppet TCP-handslag med den definierade porten.  TCP-sonder avslutar en anslutning med ett fyrvägsslut TCP-handslag.

Det minsta avsökningsintervallet är 5 sekunder och det minsta antalet felaktiga svar är 2.  Den totala varaktigheten för alla intervall får inte överstiga 120 sekunder.

En TCP-avsökning misslyckas när:
* TCP-lyssnaren på instansen svarar inte alls under timeout-perioden.  En avsökning markeras baserat på antalet timed-out-avsökningsbegäranden, som har konfigurerats för att gå obesvarade innan du markerar ned avsökningen.
* Avsökningen tar emot en TCP-återställning från instansen.

Följande illustrerar hur du kan uttrycka den här typen av avsökningskonfiguration i en Resource Manager-mall:

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

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a> HTTP / HTTPS-avsökning

>[!NOTE]
>HTTPS-avsökningen är endast tillgänglig för [standardbelastningsutjämnare](load-balancer-standard-overview.md).

HTTP- och HTTPS-avsökningar bygger på TCP-avsökningen och utfärdar en HTTP GET med den angivna sökvägen. Båda dessa avsökningar stöder relativa sökvägar för HTTP GET. HTTPS-avsökningar är samma som HTTP-avsökningar med tillägg av ett TLS (Transport Layer Security), tidigare känt som SSL. Hälsoavsökningen markeras när instansen svarar med en HTTP-status 200 inom timeout-perioden.  Hälsoavsökningen försöker kontrollera den konfigurerade hälsoavsökningsporten var 15:e sekund som standard. Det minsta sondintervallet är 5 sekunder. Den totala varaktigheten för alla intervall får inte överstiga 120 sekunder.

HTTP / HTTPS-avsökningar kan också vara användbara för att implementera din egen logik för att ta bort instanser från belastningsutjämnadrotation om avsökningsporten också är lyssnaren för själva tjänsten. Du kan till exempel välja att ta bort en instans om den är över 90 % CPU och returnerar en HTTP-status som inte är 200. 

> [!NOTE] 
> HTTPS-avsökningen kräver användning av certifikat som baseras på en minsta signaturhh för SHA256 i hela kedjan.

Om du använder Molntjänster och har webbroller som använder w3wp.exe får du också automatisk övervakning av din webbplats. Fel i din webbplatskod returnerar en icke-200-status till belastningsutjämnaravsökningen.

En HTTP / HTTPS-avsökning misslyckas när:
* Avsökningsslutpunkten returnerar en annan HTTP-svarskod än 200 (till exempel 403, 404 eller 500). Detta kommer att markera ner hälsosonden omedelbart. 
* Avsökningsslutpunkten svarar inte alls under minsta möjliga avsökningsintervall och 30 sekunders timeout-period. Flera avsökningsbegäranden kan förbli obesvarade innan avsökningen markeras som inte igång och tills summan av alla timeoutintervall har uppnåtts.
* Avsökningsslutpunkten stänger anslutningen via en TCP-återställning.

Följande illustrerar hur du kan uttrycka den här typen av avsökningskonfiguration i en Resource Manager-mall:

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

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Sond för gästagent (endast klassiskt)

Molntjänstroller (arbetsroller och webbroller) använder en gästagent för avsökningsövervakning som standard.  En gästagentavsökning är en sista utväg konfiguration.  Använd alltid en hälsoavsökning explicit med en TCP- eller HTTP-avsökning. En gästagentavsökning är inte lika effektiv som uttryckligen definierade avsökningar för de flesta programscenarier.

En gästagentavsökning är en kontroll av gästagenten inuti den virtuella datorn. Den lyssnar och svarar sedan med ett HTTP 200 OK-svar endast när instansen är i läget Klar. (Andra stater är upptagen, återvinning eller stopp.)

Mer information finns i [Konfigurera tjänstdefinitionsfilen (csdef) för hälsoavsökningar](https://msdn.microsoft.com/library/azure/ee758710.aspx) eller [Kom igång genom att skapa en offentlig belastningsutjämnare för molntjänster](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Om gästagenten inte svarar med HTTP 200 OK markerar belastningsutjämnaren instansen som svarar inte. Det slutar sedan skicka flöden till den instansen. Belastningsutjämnaren fortsätter att kontrollera instansen. 

Om gästagenten svarar med en HTTP 200 skickar belastningsutjämnaren nya flöden till den instansen igen.

När du använder en webbroll körs webbplatskoden vanligtvis i w3wp.exe, som inte övervakas av Azure-infrastrukturen eller gästagenten. Fel i w3wp.exe (till exempel HTTP 500-svar) rapporteras inte till gästagenten. Belastningsutjämnaren tar därför inte den instansen ur rotation.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Söka upp beteende

TCP-, HTTP- och HTTPS-hälsoavsökningar anses vara felfria och markerar serverdelsslutpunkten som felfri när:

* Hälsoavsökningen lyckas en gång efter vm-stövlarna.
* Det angivna antalet avsökningar som krävs för att markera serverdelsslutpunkten som felfri har uppnåtts.

Alla backend-slutpunkter som har uppnått ett felfritt tillstånd kan ta emot nya flöden.  

> [!NOTE]
> Om hälsoavsökningen varierar väntar belastningsutjämnaren längre innan den återställer slutpunkten i felfritt tillstånd. Den här extra väntetiden skyddar användaren och infrastrukturen och är en avsiktlig princip.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Söka nedåt beteende

### <a name="tcp-connections"></a>TCP-anslutningar

Nya TCP-anslutningar lyckas förbli en felfri slutpunkt för säkerhetskopiering.

Om en serverdelsslutpunkts hälsoavsökning misslyckas fortsätter etablerade TCP-anslutningar till den här serverdelslutpunkten.

Om alla avsökningar för alla instanser i en serverd-pool misslyckas skickas inga nya flöden till serverdapoolen. Standardbelastningsutjämnaren gör det möjligt för etablerade TCP-flöden att fortsätta.  Grundläggande belastningsutjämnare avslutar alla befintliga TCP-flöden till backend-poolen.
 
Belastningsutjämnare är en pass through-tjänst (avslutar inte TCP-anslutningar) och flödet är alltid mellan klienten och den virtuella datorns gäst-OS och program. En pool med alla avsökningar nedåt gör att en klientdel inte svarar på TCP-anslutningsöppna försök (SYN) eftersom det inte finns någon felfri serverdelsslutpunkt för att ta emot flödet och svara med en SYN-ACK.

### <a name="udp-datagrams"></a>UDP-datagram

UDP-datagram levereras till felfria serverdelsslutpunkter.

UDP är anslutningslös och det finns inget flödestillstånd spårat för UDP. Om någon serverdelsslutpunkts hälsoavsökning misslyckas flyttas befintliga UDP-flöden till en annan felfri instans i serverdelspoolen.

Om alla avsökningar för alla instanser i en serverd-pool misslyckas, avslutas befintliga UDP-flöden för grundläggande och standardbelastningsutjämningsmedel.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>AVsökningskälla IP-adress

Belastningsutjämnaren använder en distribuerad avskrivningstjänst för sin interna hälsomodell. Avsökningstjänsten finns på varje värd där virtuella datorer och kan programmeras på begäran för att generera hälsoavsökningar per kundens konfiguration. Hälsoavsökningstrafiken är direkt mellan avsökningstjänsten som genererar hälsoavsökningen och kundens virtuella dator. Alla belastningsutjämna hälsoavsökningar kommer från IP-adressen 168.63.129.16 som källa.  Du kan använda IP-adressutrymme inuti ett virtuella nätverk som inte är RFC1918-blanksteg.  Om du använder en globalt reserverad microsoftägd IP-adress minskar risken för en IP-adresskonflikt med det IP-adressutrymme som du använder i det virtuella nätverket.  Den här IP-adressen är densamma i alla regioner och ändras inte och är inte en säkerhetsrisk eftersom endast den interna Azure-plattformskomponenten kan köpa ett paket från den här IP-adressen. 

AzureLoadBalancer-tjänsttaggen identifierar den här käll-IP-adressen i [nätverkssäkerhetsgrupperna](../virtual-network/security-overview.md) och tillåter hälsoavsökningstrafik som standard.

Förutom belastningsutjämnare hälsoavsökningar använder följande åtgärder den [här IP-adressen:](../virtual-network/what-is-ip-address-168-63-129-16.md)

- Gör det möjligt för VM-agenten att kommunicera med plattformen för att signalera att den är i "Redo" tillstånd
- Gör det möjligt för kommunikation med den virtuella DNS-servern att tillhandahålla filtrerad namnmatchning till kunder som inte definierar anpassade DNS-servrar.  Den här filtrningen säkerställer att kunder bara kan lösa värdnamnen för sin distribution.
- Aktiverar den virtuella datorn för att hämta en dynamisk IP-adress från DHCP-tjänsten i Azure.

## <a name="design-guidance"></a><a name="design"></a>Designvägledning

Hälsoavsökningar används för att göra tjänsten flexibel och låta den skalas. En felkonfiguration eller dålig design mönster kan påverka tillgängligheten och skalbarheten för din tjänst. Granska hela det här dokumentet och fundera över vad som påverkar scenariot när det här avsökningssvaret markeras nedåt eller markeras och hur det påverkar tillgängligheten för ditt programscenario.

När du utformar hälsomodellen för ditt program bör du avsöka en port på en serverdelsslutpunkt som återspeglar hälsotillståndet för den instansen __och__ den programtjänst du tillhandahåller.  Programporten och avsökningsporten behöver inte vara desamma.  I vissa fall kan det vara önskvärt att avsökningsporten skiljer sig från den port som programmet tillhandahåller tjänst på.  

Ibland kan det vara användbart för ditt program att generera en hälsoavsökning svar att inte bara upptäcka ditt program hälsa, men också signalera direkt till Load Balancer om din instans ska ta emot eller inte ta emot nya flöden.  Du kan ändra avsökningssvaret så att ditt program kan skapa mottryck och begränsa leverans av nya flöden till en instans genom att misslyckas med hälsoavsökningen eller förbereda för underhåll av ditt program och initiera tömning av ditt scenario.  När du använder standardbelastningsutjämnaren tillåter en [nedsökningssignal](#probedown) alltid att TCP-flöden fortsätter tills tidsgränsen för inaktiv eller anslutningen stängs. 

För UDP-belastningsutjämning bör du generera en anpassad hälsoavsökningssignal från serverdelsslutpunkten och använda antingen en TCP-, HTTP- eller HTTPS-hälsoavsökning som riktar sig till motsvarande lyssnare för att återspegla hälsotillståndet för ditt UDP-program.

När du använder [HA Ports belastningsutjämningsregler](load-balancer-ha-ports-overview.md) med [standardbelastningsutjämning](load-balancer-standard-overview.md)är alla portar belastningsbalanserade och ett enda hälsoavsökningssvar måste återspegla status för hela instansen.

Översätt eller proxy inte en hälsoavsökning genom den instans som tar emot hälsoavsökningen till en annan instans i ditt virtuella nätverk eftersom den här konfigurationen kan leda till kaskadfel i ditt scenario.  Tänk på följande scenario: en uppsättning tredjepartsinstallationer distribueras i serverdelspoolen för en load balancer-resurs för att tillhandahålla skalning och redundans för apparaterna och hälsoavsökningen är konfigurerad för att avsöka en port som den tredjepartsinstallationen proxyservrar eller översätts till andra virtuella datorer bakom apparaten.  Om du avsöker samma port som du använder för att översätta eller proxybegäranden till andra virtuella datorer bakom installationen, markeras alla avsökningssvar från en enda virtuell dator bakom apparaten som markerar själva apparaten som död. Den här konfigurationen kan leda till ett kaskadfel i hela programscenariot som ett resultat av en enda serverdelsslutpunkt bakom installationen.  Utlösaren kan vara ett intermittent avsökningsfel som gör att belastningsutjämnaren markerar det ursprungliga målet (apparatinstansen) och i sin tur kan inaktivera hela ditt programscenario. Undersök i stället själva apparatens hälsa. Valet av avsökningen för att bestämma hälsosignalen är en viktig faktor för nätverksvirta enheter (NVA) scenarier och du måste kontakta din programleverantör för vad lämplig hälsosignal är för sådana scenarier.

Om du inte tillåter [käll-IP](#probesource) för avsökningen i brandväggsprinciperna misslyckas hälsoavsökningen eftersom den inte kan nå din instans.  Belastningsutjämnaren markerar i sin tur din instans på grund av hälsoavsökningsfelet.  Den här felkonfigurationen kan orsaka att ditt belastningsbalanserade programscenario misslyckas.

För att belastningsutjämnarens hälsoavsökning ska kunna markera din instans **måste** du tillåta den här IP-adressen i alla [Azure-nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) och lokala brandväggsprinciper.  Som standard innehåller varje nätverkssäkerhetsgrupp [tjänsttaggen](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer för att tillåta hälsoavsökningstrafik.

Om du vill testa ett fel på hälsoavsökningen eller markera en enskild instans kan du använda en [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) för att uttryckligen blockera hälsoavsökningen (målport eller [käll-IP)](#probesource)och simulera felet för en avsökning.

Konfigurera inte ditt virtuella nätverk med det Microsoft-ägda IP-adressintervallet som innehåller 168.63.129.16.  Sådana konfigurationer kommer att kollidera med IP-adressen för hälsoavsökningen och kan orsaka att ditt scenario misslyckas.

Om du har flera gränssnitt på den virtuella datorn måste du försäkra dig om att du svarar på avsökningen i gränssnittet du fick den på.  Du kan behöva källnätverksadress översätta den här adressen i den virtuella datorn per gränssnitt.

Aktivera inte [TCP-tidsstämplar](https://tools.ietf.org/html/rfc1323).  Om du aktiverar TCP-tidsstämplar kan hälsoavsökningar misslyckas på grund av att TCP-paket tas bort av den virtuella datorns gäst-OS TCP-stack, vilket resulterar i att belastningsutjämnarmarkering respektive slutpunkt markeras.  TCP-tidsstämplar är rutinmässigt aktiverade som standard på säkerhetshärdade VM-avbildningar och måste inaktiveras.

## <a name="monitoring"></a>Övervakning

Både offentlig och intern [standardbelastningsutjämning](load-balancer-standard-overview.md) exponerar per slutpunkt och serverdelsavsökningsstatus som flerdimensionella mått via Azure Monitor. Dessa mått kan förbrukas av andra Azure-tjänster eller partnerprogram. 

Grundläggande offentlig belastningsutjämnare exponerar hälsoavsökningsstatus som summeras per serverd-pool via Azure Monitor-loggar.  Azure Monitor-loggar är inte tillgängliga för interna grundläggande belastningsutjämnare.  Du kan använda [Azure Monitor-loggar](load-balancer-monitor-log.md) för att kontrollera hälsotillståndet för den offentliga belastningsutjämnaravsökningen och avsökningsantalet. Loggning kan användas med Power BI eller Azure Operational Insights för att tillhandahålla statistik om belastningsutjämnad hälsotillstånd.

## <a name="limitations"></a>Begränsningar

- HTTPS-avsökningar stöder inte ömsesidig autentisering med ett klientcertifikat.
- Du bör anta att hälsoavsökningar misslyckas när TCP-tidsstämplar är aktiverade.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [standardbelastningsjämningsapparat](load-balancer-standard-overview.md)
- [Komma igång med att skapa en offentlig belastningsutjämnare i Resource Manager med hjälp av PowerShell](quickstart-create-standard-load-balancer-powershell.md)
- [REST API för hälsoavsökningar](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Begär nya hälsosondförmågor med [belastningsutjämnares användarröst](https://aka.ms/lbuservoice)
