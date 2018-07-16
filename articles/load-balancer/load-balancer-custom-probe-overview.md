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
ms.date: 07/13/2018
ms.author: kumud
ms.openlocfilehash: 69991a0b805b5502fc96fab4ce902b3d8bc77baf
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056366"
---
# <a name="understand-load-balancer-probes"></a>Förstå avsökningar av belastningsutjämnare

Azure Load Balancer använder hälsoavsökningar för att avgöra vilka serverdels-serverpoolinstans ska ta emot nya flöden.   Du kan använda hälsoavsökningar för att upptäcka fel på ett program på en backend-instans.  Du kan också använda hälsotillstånd avsökningen svaret från ditt program för att signalera att Load Balancer om du vill fortsätta att skicka nya flöden eller sluta skicka nya flöden till en backend-instans för att hantera belastningen eller planerade driftstopp.

Hälsoavsökningar styr huruvida nya flöden upprättas till Felfri backend-instanser. När en hälsoavsökning inte slutar belastningsutjämnaren att skicka nya flöden till respektive feltillstånd instansen.  Etablerad TCP-anslutningar fortsätter efter uteblivna för avsökning.  Befintliga UDP flöden kommer att flytta den från den felaktiga instansen till en annan instans i serverdelspoolen.

Om alla avsökningar för en serverdelspool misslyckas avslutas grundläggande belastningsutjämnare alla befintliga TCP-flöden till serverdelspoolen, medan Standard Load balancer tillåter etablerad TCP-flöden för att fortsätta; Inga nya flöden kommer att skickas till i serverdelspoolen.

Molntjänstroller (arbetarroller och webbroller) använda en gästagenten för avsökning övervakning. TCP eller HTTP anpassade hälsoavsökningar måste konfigureras när du använder Cloud Services med virtuella IaaS-datorer bakom belastningsutjämnaren.

## <a name="understand-probe-count-and-timeout"></a>Förstå avsökningen antal och timeout

Avsökningen beteende beror på:

* Antal lyckade avsökningar som gör att en instans ska förses med som drift.
* Antal misslyckade avsökningar som orsakar en instans ska förses med igång.

De värden för timeout och frekvens som anges i SuccessFailCount avgör om en instans är bekräftas som körs eller inte körs. Timeout-värdet har angetts till två gånger värdet för frekvensen i Azure-portalen.

Avsökningskonfiguration över alla belastningsutjämnade instanser för en slutpunkt (det vill säga en belastningsutjämnad uppsättning) måste vara samma. Du kan inte ha en annan avsökningskonfiguration för varje instans av serverroll eller virtuell dator i samma värdbaserad tjänst för en viss slutpunkt-kombination. Varje instans måste till exempel ha identiska lokala portar och tidsgränser.

> [!IMPORTANT]
> En belastningsutjämnaravsökning använder IP-adressen 168.63.129.16. Den här offentliga IP-adressen underlättar kommunikationen till intern resurser för den bring-your-own-IP scenariot Azure virtuella. Den virtuella offentliga IP-adressen 168.63.129.16 används i alla regioner och ändras inte. Vi rekommenderar att du tillåter denna IP-adress i alla lokala Brandväggsprinciper. Det ska inte betraktas som en säkerhetsrisk eftersom endast interna Azure-plattformen kan styra ett meddelande från den adressen. Om du inte tillåter den här IP-adressen i din Brandväggsprinciper uppstår oväntade resultat i en mängd olika scenarier. Beteendet innefattar att konfigurera samma IP-adressintervallet för 168.63.129.16 och duplicera IP-adresser.

## <a name="learn-about-the-types-of-probes"></a>Lär dig mer om vilka typer av avsökningar

### <a name="guest-agent-probe"></a>Avsökning för gäst-agent

En avsökning för gäst-agenten är endast tillgängligt för Azure Cloud Services. Belastningsutjämnaren använder sig av gästagenten på den virtuella datorn. Sedan lyssnar och svarar med ett HTTP 200 OK-svar endast när instansen är i tillståndet klar. (Andra tillstånd är upptagen, återanvändning, eller stoppas.)

Mer information finns i [konfigurera tjänstdefinitionsfilen (csdef) för hälsoavsökningar](https://msdn.microsoft.com/library/azure/ee758710.aspx) eller [Kom igång genom att skapa en offentlig belastningsutjämnare för cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Det som gör en avsökning för gäst-agenten som Markera en instans som skadad?

Om gästagenten inte kan svara med HTTP 200 OK, markerar belastningsutjämnaren instans som inte svarar. Den stoppas skickar trafik till den instansen. Belastningsutjämnaren fortsätter att pinga instansen. Om gästagenten svarar med ett 200 HTTP, skickar belastningsutjämnaren trafik till den instansen igen.

När du använder en webbroll webbplats koden vanligtvis körs i w3wp.exe som inte övervakas av Azure fabric eller gäst-agenten. Fel i w3wp.exe (till exempel HTTP 500-svar) inte har rapporterats till gästagenten. Belastningsutjämnaren tar därför inte den instansen bort från roteringen.

### <a name="http-custom-probe"></a>Anpassade HTTP-avsökning

Den anpassade HTTP-avsökningen åsidosätter standard gäst agenten avsökningen. Du kan skapa en egen anpassad logik för att fastställa hälsotillståndet för rollinstansen. Load balancer kontrollerar slutpunkten var 15: e sekund som standard. Instansen anses vara i belastningsutjämnarens rotation om svarar den med en HTTP 200 inom tidsgränsen. Tidsgränsen är 31 sekunder som standard.

En anpassad HTTP-avsökning kan vara användbart om du vill implementera en egen logik för att ta bort instanser från belastningsutjämnarens rotation. Du kan exempelvis välja att ta bort en instans om den är över 90% CPU och returnerar en icke-200-status. Om du har web-roller som använder w3wp.exe kan du också få automatisk övervakning av din webbplats. Fel i koden webbplats tillbaka statusen icke-200 till belastningsutjämnaravsökningen.

> [!NOTE]
> Den anpassade HTTP-avsökningen stöder relativa sökvägar och HTTP-protokollet. HTTPS stöds inte.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Det som gör en anpassad HTTP-avsökning Markera en instans som skadad?

* HTTP-programmet returnerar ett HTTP-svarskoden än 200 (till exempel 403, 404 eller 500). Den här positiv bekräftelse varnar dig om du vill dra programinstansen ur direkt.
* HTTP-servern svarar inte alls efter tidsgränsen. Beroende på timeout-värde som har angetts flera avsökningen begäranden kan gå obesvarade innan avsökningen ska markeras som inte körs (det vill säga innan SuccessFailCount avsökningar skickas).
* Servern stänger anslutningen via en TCP-återställning.

### <a name="tcp-custom-probe"></a>Anpassade TCP-avsökning

TCP anpassade avsökningar initiera en anslutning genom att utföra en 3-vägs-handskakning med definierade porten.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Det som gör en anpassad TCP-avsökning Markera en instans som skadad?

* TCP-servern svarar inte alls efter tidsgränsen. När avsökningen är markerad som inte kör beror på antalet misslyckade avsökningen begäranden som har konfigurerats för att gå obesvarade innan du markerar avsökningen som inte körs.
* Avsökningen tar emot en TCP återställa från rollinstansen.

Läs mer om hur du konfigurerar en hälsoavsökning för HTTP eller TCP-avsökning [komma igång och skapa en offentlig belastningsutjämnare i Resource Manager med hjälp av PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Lägg till felfria instanser tillbaka till belastningsutjämnarens rotation

TCP- och HTTP-avsökningar anses vara felfria och markera rollinstans som felfri när:

* Belastningsutjämnaren hämtar en positiv avsökning startas för första gången den virtuella datorn.
* Talet för SuccessFailCount (beskrivs ovan) anger värdet för lyckad avsökningar som krävs för att markera rollinstans som felfri. Om en rollinstans har tagits bort, måste antal lyckades, efterföljande avsökningar vara lika med eller överskrider värdet för SuccessFailCount att markera rollinstans som körs.

> [!NOTE]
> Om hälsotillståndet för en rollinstans varierar belastningsutjämnaren ska vänta längre innan rollinstansen flyttas tillbaka till felfritt tillstånd. Den här extra väntetid skyddar användaren och infrastrukturen och är en avsiktlig princip.

## <a name="use-log-analytics-for-a-load-balancer"></a>Använda log analytics för en belastningsutjämnare

Du kan använda [logganalys](load-balancer-monitor-log.md) att söka på den avsökningen hälsostatusen för belastningsutjämnaren och avsökning antal. Loggning kan användas med Power BI eller Azure Operational Insights för att tillhandahålla statistik om hälsostatusen för belastningsutjämnaren.
