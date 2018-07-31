---
title: Använda anpassade avsökningar för belastningsutjämnaren för att övervaka hälsostatus | Microsoft Docs
description: Lär dig hur du använder anpassade avsökningar för Azure Load Balancer för att övervaka instanser bakom belastningsutjämnare
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
ms.date: 07/20/2018
ms.author: kumud
ms.openlocfilehash: afe46cf9fc710decba4524bd5a0fe1e73804f636
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344172"
---
# <a name="load-balancer-health-probes"></a>Läsa in Belastningsutjämnarens hälsotillståndsavsökningar

Azure Load Balancer använder hälsoavsökningar för att avgöra vilka serverdelsinstanser för poolen får nya flöden. Du kan använda hälsoavsökningar för att upptäcka fel på ett program på en backend-instans. Du kan också generera en anpassad åtgärd till en hälsoavsökning och använda hälsoavsökningen för flödeskontroll och skicka en signal till belastningsutjämnaren om du vill fortsätta att skicka nya flöden eller sluta skicka nya flöden till en backend-instans. Detta kan användas för att hantera belastningen eller planerade driftstopp.

När en hälsoavsökning inte slutar belastningsutjämnaren att skicka nya flöden till respektive feltillstånd instansen. Nya och befintliga flöden beror på om ett flöde är TCP eller UDP som du använder samt vilken SKU för Load Balancer.  Granska [avsökning av beteendet för information om](#probedown).

## <a name="health-probe-types"></a>Avsökningen hälsotyper

Hälsokontroller av slutpunkter kan se alla portar på en backend-instans, inklusive den port där faktiska tjänsten tillhandahålls. Hälsoavsökningen stöder TCP-lyssnare eller HTTP-slutpunkter. 

UDP-belastningsutjämning, ska du skapa en anpassad hälsotillståndssignal avsökning för backend-instansen med hjälp av en TCP eller HTTP-hälsokontroll.

När du använder [HA Ports belastningsutjämningsregler](load-balancer-ha-ports-overview.md) med [Standardbelastningsutjämnare](load-balancer-standard-overview.md), alla portar belastningsutjämnas och ett enda hälsotillstånd avsökningen svar bör återspegla status för en hel instans.  

Du bör inte NAT och proxy som en hälsoavsökning via den instans som tar emot hälsoavsökningen till en annan instans i ditt virtuella nätverk som detta kan leda till kaskadfel i ditt scenario.

### <a name="tcp-probe"></a>TCP-avsökning

TCP-avsökningar initiera en anslutning genom att utföra en 3-vägs öppna TCP-handskakningen med definierade porten.  Detta är sedan följt av en fyra sätt Stäng TCP-handskakning.

Minsta avsökningsintervallet är 5 sekunder och det minsta antalet felaktiga svar är 2.  Den totala varaktigheten får inte överstiga 120 sekunder.

En TCP-avsökning misslyckas när:
* TCP-lyssnaren på-instansen svarar inte under tidsperioden.  En avsökning har markerats baserat på antalet misslyckade avsökningen begäranden som har konfigurerats för att gå obesvarade innan du markerar avsökningen.
* Avsökningen tar emot en TCP återställa från instansen.

### <a name="http-probe"></a>HTTP-avsökning

HTTP-avsökningar upprätta en TCP-anslutning och utfärda en HTTP GET med den angivna sökvägen. HTTP-avsökningar stöder relativa sökvägar för HTTP GET. Hälsoavsökningen markeras när instansen svarar med en HTTP-statuskod 200 inom tidsgränsen.  HTTP-hälsa avsökningar försöket att kontrollera avsökningsporten konfigurerade hälsotillstånd var 15: e sekund som standard. Minsta avsökningsintervallet är 5 sekunder. Den totala varaktigheten får inte överstiga 120 sekunder. 


HTTP-avsökningar kan också vara användbart om du vill implementera en egen logik för att ta bort instanser från belastningsutjämnarens rotation. Du kan till exempel vill ta bort en instans om den är över 90% CPU och returnera en icke - 200 HTTP-status. 

Om du använder Cloud Services och har web-roller som använder w3wp.exe kan få du också automatisk övervakning av din webbplats. Fel i koden webbplats tillbaka statusen icke-200 till belastningsutjämnaravsökningen.  HTTP-avsökning åsidosätter standard gäst agenten avsökningen. 

En HTTP-avsökning misslyckas när:
* HTTP-avsökning slutpunkten returnerar ett HTTP-svarskoden än 200 (till exempel 403, 404 eller 500). Detta markerar hälsoavsökningen omedelbart. 
* HTTP-slutpunkt för avsökning inte svarar under den en tidsgräns för 31 sekund. Beroende på timeout-värde som har angetts flera avsökningen begäranden kan gå obesvarade innan avsökningen ska markeras som inte körs (det vill säga innan SuccessFailCount avsökningar skickas).
* HTTP-avsökning slutpunkt stänger anslutningen via en TCP-återställning.

### <a name="guest-agent-probe-classic-only"></a>Gästen agenten avsökning (klassisk)

Molntjänstroller (arbetarroller och webbroller) använda en gästagenten för avsökning övervakning som standard.   Du bör överväga att detta ett alternativ av sista utväg.  Du bör alltid definiera en hälsoavsökning explicit med en TCP eller HTTP-avsökning. En avsökning för gäst-agenten är inte så effektiv som uttryckligen definierade avsökningar de flesta scenarier med programmet.  

En avsökning för gäst-agenten är en kontroll av gästagenten på den virtuella datorn. Sedan lyssnar och svarar med ett HTTP 200 OK-svar endast när instansen är i tillståndet klar. (Andra tillstånd är upptagen, återanvändning, eller stoppas.)

Mer information finns i [konfigurera tjänstdefinitionsfilen (csdef) för hälsoavsökningar](https://msdn.microsoft.com/library/azure/ee758710.aspx) eller [Kom igång genom att skapa en offentlig belastningsutjämnare för cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Om gästagenten inte kan svara med HTTP 200 OK, markerar belastningsutjämnaren instans som inte svarar. Den stoppas skicka flöden till den instansen. Belastningsutjämnaren fortsätter att kontrollera instansen. 

Om gästagenten svarar med ett 200 HTTP, skickar belastningsutjämnaren nya flöden till den instansen igen.

När du använder en webbroll webbplats koden vanligtvis körs i w3wp.exe som inte övervakas av Azure fabric eller gäst-agenten. Fel i w3wp.exe (till exempel HTTP 500-svar) inte har rapporterats till gästagenten. Belastningsutjämnaren tar därför inte den instansen bort från roteringen.

## <a name="probe-health"></a>Avsökningen hälsotillstånd

TCP- och HTTP-hälsokontroller av slutpunkter anses vara felfria och markera rollinstans som felfri när:

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

> [!IMPORTANT]
> En hälsoavsökning för belastningsutjämnaren använder IP-adressen 168.63.129.16. Den här offentliga IP-adressen underlättar kommunikationen till intern resurser för den bring-your-own-IP scenariot Azure virtuella. Den virtuella offentliga IP-adressen 168.63.129.16 används i alla regioner och ändras inte. Vi rekommenderar att du tillåter denna IP-adress i valfri Azure [säkerhetsgrupper](../virtual-network/security-overview.md) och lokala Brandväggsprinciper. Det ska inte betraktas som en säkerhetsrisk eftersom endast interna Azure-plattformen kan styra ett paket från den här adressen. Om du inte tillåter den här IP-adressen i din Brandväggsprinciper uppstår oväntade resultat i en mängd olika scenarier, belastningsutjämnad inklusive fel vid inläsningen service. Du bör också inte konfigurera ditt virtuella nätverk med ett IP-adressintervall som innehåller 168.63.129.16.  Om du har flera gränssnitt på den virtuella datorn kan behöva du se till att du svara på avsökningen på gränssnittet som du fick på.  Du kan behöva unikt källa NAT'ing den här adressen på den virtuella datorn på basis av per gränssnitt.

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

## <a name="monitoring"></a>Övervakning

Alla [Standardbelastningsutjämnare](load-balancer-standard-overview.md) visar avsökningen hälsostatus som flerdimensionella mått per instans via Azure Monitor.

Grundläggande belastningsutjämnare visar avsökningen hälsostatus per serverdelspool via Log Analytics.  Detta är endast tillgänglig för offentlig grundläggande belastningsutjämnare och inte tillgängliga för interna grundläggande belastningsutjämnare.  Du kan använda [logganalys](load-balancer-monitor-log.md) att söka på den offentliga avsökningen hälsostatusen för belastningsutjämnaren och avsökning antal. Loggning kan användas med Power BI eller Azure Operational Insights för att tillhandahålla statistik om hälsostatusen för belastningsutjämnaren.

## <a name="limitations"></a>Begränsningar

-  HTTP-hälsokontroll har inte stöd för TLS (HTTPS).  Använd en TCP-avsökning på port 443 i stället.

## <a name="next-steps"></a>Nästa steg

- [Komma igång och skapa en offentlig belastningsutjämnare i Resource Manager med hjälp av PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [REST API för hälsoavsökningar](https://docs.microsoft.com/en-us/rest/api/load-balancer/loadbalancerprobes/get)

