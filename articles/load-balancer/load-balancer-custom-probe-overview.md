---
title: Använd Load Balancers hälsoavsökningar för att skydda din tjänst | Microsoft Docs
description: Lär dig hur du använder hälsoavsökningar för att övervaka instanser bakom belastningsutjämnare
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/04/2018
ms.author: kumud
ms.openlocfilehash: ecc33fc6078dac4affe3942f1be7e039ae9e9e70
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43695433"
---
# <a name="load-balancer-health-probes"></a>Läsa in Belastningsutjämnarens hälsotillståndsavsökningar

Azure Load Balancer använder hälsoavsökningar för att avgöra vilka serverdelsinstanser för poolen får nya flöden. Du kan använda hälsoavsökningar för att upptäcka fel på ett program på en backend-instans. Du kan också generera en anpassad åtgärd till en hälsoavsökning och använda hälsoavsökningen för flödeskontroll och skicka en signal till belastningsutjämnaren om du vill fortsätta att skicka nya flöden eller sluta skicka nya flöden till en backend-instans. Detta kan användas för att hantera belastningen eller planerade driftstopp. När en hälsoavsökning inte slutar belastningsutjämnaren att skicka nya flöden till respektive feltillstånd instansen.

Vilka typer av hälsoavsökningar som är tillgängliga och hur hälsotillståndet avsökningar beter sig beror på vilken av belastningsutjämnaren med SKU som du använder. Till exempel beror beteendet för nya och befintliga flöden på om ett flöde är TCP eller UDP som du använder samt vilken SKU för Load Balancer.

| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| [Avsökningen typer](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Avsökning av beteende](#probedown) | Alla avsökningar ned alla TCP-flöden fortsätter. | Alla avsökningar av Avsluta för alla TCP-flöden. | 

> [!IMPORTANT]
> Belastningsutjämnarens hälsotillståndsavsökningar kommer från IP-adressen 168.63.129.16 och får inte vara blockerad vid avsökningar för att märka din instans.  Granska [avsökning källans IP-adress](#probesource) mer information.

## <a name="types"></a>Avsökningen typer

Hälsokontroller av slutpunkter kan se alla portar på en backend-instans, inklusive den port där faktiska tjänsten tillhandahålls. Protokollet för avsökningen hälsa kan konfigureras för tre olika typer av hälsotillståndsavsökningar:

- [TCP-lyssnare](#tcpprobe)
- [HTTP-slutpunkter](#httpprobe)
- [HTTPS-slutpunkter](#httpsprobe)

De tillgängliga typerna av hälsoavsökningar varierar beroende på vilken Load Balancer SKU som valts:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard-SKU |    &#9989; |   &#9989; |   &#9989; |
| Grundläggande SKU |   &#9989; |   &#9989; | &#10060; |

Du bör skapa en anpassad hälsotillståndssignal avsökning för backend-instansen med hjälp av en TCP-, HTTP, UDP-belastningsutjämning, eller HTTPS-avsökning.

När du använder [HA Ports belastningsutjämningsregler](load-balancer-ha-ports-overview.md) med [Standardbelastningsutjämnare](load-balancer-standard-overview.md), alla portar belastningsutjämnas och ett enda hälsotillstånd avsökningen svar måste spegla status för en hel instans.  

Du bör inte NAT och proxy som en hälsoavsökning via den instans som tar emot hälsoavsökningen till en annan instans i ditt virtuella nätverk som detta kan leda till kaskadfel i ditt scenario.

Om du vill testa en avsökning uteblivna eller skriva ned en enskild instans kan du använda en säkerhetsgrupp för explicit block hälsoavsökningen (mål eller [källa](#probesource)).

### <a name="tcpprobe"></a> TCP-avsökning

TCP-avsökningar initiera en anslutning genom att utföra en 3-vägs öppna TCP-handskakningen med definierade porten.  Detta är sedan följt av en fyra sätt Stäng TCP-handskakning.

Minsta avsökningsintervallet är 5 sekunder och det minsta antalet felaktiga svar är 2.  Den totala varaktigheten får inte överstiga 120 sekunder.

En TCP-avsökning misslyckas när:
* TCP-lyssnaren på-instansen svarar inte under tidsperioden.  En avsökning har markerats baserat på antalet misslyckade avsökningen begäranden som har konfigurerats för att gå obesvarade innan du markerar avsökningen.
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

HTTP och HTTPS-avsökningar upprätta en TCP-anslutning och utfärda en HTTP GET med den angivna sökvägen. Båda dessa avsökningar stöder relativa sökvägar för HTTP GET. HTTPS-avsökningar är samma som HTTP-avsökningar och Lägg till en Transport Layer Security (TLS, tidigare känd som SSL) omslutning. Hälsoavsökningen markeras när instansen svarar med en HTTP-statuskod 200 inom tidsgränsen.  Dessa hälsotillstånd avsökningar försöker checka avsökningsporten konfigurerade hälsotillstånd var 15: e sekund som standard. Minsta avsökningsintervallet är 5 sekunder. Den totala varaktigheten får inte överstiga 120 sekunder. 

HTTP / HTTPS-avsökningar kan också vara användbart om du vill implementera en egen logik för att ta bort instanser från belastningsutjämnarens rotation. Du kan till exempel vill ta bort en instans om den är över 90% CPU och returnera en icke - 200 HTTP-status. 

Om du använder Cloud Services och har web-roller som använder w3wp.exe kan få du också automatisk övervakning av din webbplats. Fel i koden webbplats tillbaka statusen icke-200 till belastningsutjämnaravsökningen.  HTTP-avsökning åsidosätter standard gäst agenten avsökningen. 

HTTP / HTTPS-avsökning misslyckas när:
* Avsökningen slutpunkten returnerar ett HTTP-svarskoden än 200 (till exempel 403, 404 eller 500). Detta markerar hälsoavsökningen omedelbart. 
* Slutpunkten för avsökning inte svarar under den en tidsgräns för 31 sekund. Beroende på timeout-värde som har angetts flera avsökningen begäranden kan gå obesvarade innan avsökningen ska markeras som inte körs (det vill säga innan SuccessFailCount avsökningar skickas).
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

Molntjänstroller (arbetarroller och webbroller) använda en gästagenten för avsökning övervakning som standard.   Du bör överväga att detta ett alternativ av sista utväg.  Du bör alltid definiera en hälsoavsökning explicit med en TCP eller HTTP-avsökning. En avsökning för gäst-agenten är inte så effektiv som uttryckligen definierade avsökningar de flesta scenarier med programmet.  

En avsökning för gäst-agenten är en kontroll av gästagenten på den virtuella datorn. Sedan lyssnar och svarar med ett HTTP 200 OK-svar endast när instansen är i tillståndet klar. (Andra tillstånd är upptagen, återanvändning, eller stoppas.)

Mer information finns i [konfigurera tjänstdefinitionsfilen (csdef) för hälsoavsökningar](https://msdn.microsoft.com/library/azure/ee758710.aspx) eller [Kom igång genom att skapa en offentlig belastningsutjämnare för cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Om gästagenten inte kan svara med HTTP 200 OK, markerar belastningsutjämnaren instans som inte svarar. Den stoppas skicka flöden till den instansen. Belastningsutjämnaren fortsätter att kontrollera instansen. 

Om gästagenten svarar med ett 200 HTTP, skickar belastningsutjämnaren nya flöden till den instansen igen.

När du använder en webbroll webbplats koden vanligtvis körs i w3wp.exe som inte övervakas av Azure fabric eller gäst-agenten. Fel i w3wp.exe (till exempel HTTP 500-svar) inte har rapporterats till gästagenten. Belastningsutjämnaren tar därför inte den instansen bort från roteringen.

## <a name="probehealth"></a>Avsökningen hälsotillstånd

TCP, HTTP och HTTPS hälsoavsökningar anses vara felfria och markera rollinstans som felfri när:

* Hälsoavsökningen genomförs första gången som den virtuella datorn startar.
* Talet för SuccessFailCount (beskrivs ovan) anger värdet för lyckad avsökningar som krävs för att markera rollinstans som felfri. Om en rollinstans har tagits bort, måste antal lyckades, efterföljande avsökningar vara lika med eller överskrider värdet för SuccessFailCount att markera rollinstans som körs.

> [!NOTE]
> Om hälsotillståndet för en rollinstans varierar belastningsutjämnaren ska vänta längre innan rollinstansen flyttas tillbaka till felfritt tillstånd. Den här extra väntetid skyddar användaren och infrastrukturen och är en avsiktlig princip.

## <a name="probe-count-and-timeout"></a>Avsökningen antal och timeout

Avsökningen beteende beror på:

* Antal lyckade avsökningar som gör att en instans har markerats som drift.
* Antal misslyckade avsökningar som orsakar en instans är markerad som inaktiv.

De värden för timeout och frekvens som anges i SuccessFailCount avgör om en instans är bekräftas som körs eller inte körs. Timeout-värdet har angetts till två gånger värdet för frekvensen i Azure-portalen.

En belastningsutjämningsregel har en enda hälsoavsökning definierats respektive serverdelspoolen.

## <a name="probedown"></a>Avsökning av beteende

### <a name="tcp-connections"></a>TCP-anslutningar

Ny TCP-anslutningar lyckas till backend-instans som är felfri och har ett gästoperativsystem och program som kan ta emot ett nytt flöde.

Om en backend-instans hälsoavsökning misslyckas, fortsätter du etablerade TCP-anslutningar till den här backend-instansen.

Om alla avsökningar för alla instanser i en serverdelspool misslyckas, skickas inga nya flöden till i serverdelspoolen. Standard Load Balancer tillåter etablerad TCP-flöden för att fortsätta.  Basic Load Balancer avslutas alla befintliga TCP-flöden till i serverdelspoolen.
 
Eftersom flödet är alltid mellan klienten och den Virtuella datorns gästoperativsystem, genereras en pool med alla avsökningar av en klientdel inte svarar på Öppna TCP-anslutningsförsök eftersom det inte finns någon felfri backend-instans för att ta emot flödet.

### <a name="udp-datagrams"></a>UDP-datagram

UDP-datagram levereras till Felfri backend-instanser.

UDP anslutningslös och det finns inga läget för energiflöde som spåras för UDP. Om en backend-instans hälsoavsökning misslyckas kan befintliga UDP-flöden flytta till en annan felfri instans i serverdelspoolen.

Om alla avsökningar för alla instanser i en serverdelspool misslyckas avslutas befintliga UDP-flöden för Basic och Standard belastningsutjämnare.

## <a name="probesource"></a>Avsökning för källans IP-adress

Belastningsutjämnaren använder en distribuerad avsöknings tjänst för sin interna hälsomodellen. Varje värd där virtuella datorer finns kan programmeras för att generera hälsoavsökningar per kundens konfiguration. Hälsotillstånd avsökningen trafiken är direkt mellan infrastrukturkomponenter som genererar hälsoavsökningen och kunden VM. Alla Load Balancers hälsoavsökningar kommer från IP-adressen 168.63.129.16 som källa.  När du vill aktivera dina egna IP-adresser till Azure Virtual Network, garanteras den här hälsotillstånd avsökningen källans IP-adress vara unikt eftersom det globalt är reserverade för Microsoft.  Den här adressen är samma i alla regioner och ändras inte. Det ska inte betraktas som en säkerhetsrisk eftersom endast interna Azure-plattformen kan styra ett paket från den här IP-adress. 

Utöver Load Balancers hälsoavsökningar använda följande åtgärder den här IP-adress:

- Gör det möjligt för VM-agenten till att kommunicera med plattformen att signalera att den är i tillståndet ”klar”
- Möjliggör kommunikation med den virtuella DNS-servern att tillhandahålla filtrerade namnmatchning för kunder som inte definierar anpassade DNS-servrar.  Den här filtreringen garanterar att kunderna endast kan matcha värdnamnen för deras distribution.

För Belastningsutjämnarens hälsoavsökning att markera din instans, du **måste** tillåter denna IP-adress i alla Azure [säkerhetsgrupper](../virtual-network/security-overview.md) och lokala Brandväggsprinciper.

Om du inte tillåter den här IP-adressen i din Brandväggsprinciper, misslyckas hälsoavsökningen eftersom det inte går att nå din instans.  I sin tur markerar belastningsutjämnare ned din instans på grund av uteblivna för avsökning.  Detta kan orsaka belastningsutjämnade tjänsten misslyckas. 

Du bör också inte konfigurera ditt virtuella nätverk med Microsoft som ägs av IP-adressintervall som innehåller 168.63.129.16.  Detta ska vara i konflikt med IP-adressen för hälsoavsökningen.

Om du har flera gränssnitt på den virtuella datorn kan behöva du se till att du svara på avsökningen på gränssnittet som du fick på.  Du kan behöva unikt källa NAT'ing den här adressen på den virtuella datorn på basis av per gränssnitt.

## <a name="monitoring"></a>Övervakning

Både offentliga och interna [Standardbelastningsutjämnare](load-balancer-standard-overview.md) exponera per slutpunkt och backend-instans avsökningen hälsostatus som flerdimensionella mätvärden via Azure Monitor. Detta kan sedan användas av andra Azure-tjänster eller program med 3 part. 

Offentliga belastningsutjämnare visar avsökningen hälsostatus sammanfattas per serverdelspool via Log Analytics.  Detta är inte tillgänglig för interna grundläggande belastningsutjämnare.  Du kan använda [logganalys](load-balancer-monitor-log.md) att söka på den offentliga avsökningen hälsostatusen för belastningsutjämnaren och avsökning antal. Loggning kan användas med Power BI eller Azure Operational Insights för att tillhandahålla statistik om hälsostatusen för belastningsutjämnaren.

## <a name="limitations"></a>Begränsningar

-  HTTPS-avsökningar stöder inte ömsesidig autentisering med ett klientcertifikat.
-  SDK, PowerShell har inte stöd för HTTPS-avsökningar just nu.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md)
- [Komma igång och skapa en offentlig belastningsutjämnare i Resource Manager med hjälp av PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [REST API för hälsoavsökningar](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Begär nya hälsotillstånd avsökningen funktioner med [Belastningsutjämnarens Uservoice](https://aka.ms/lbuservoice)
