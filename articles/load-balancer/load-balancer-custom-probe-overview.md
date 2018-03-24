---
title: Använda anpassade avsökningar i belastningsutjämnaren för att övervaka hälsostatus | Microsoft Docs
description: Lär dig hur du använder anpassade avsökningar för Azure belastningsutjämnare för att övervaka instanser bakom belastningsutjämnaren
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
ms.date: 03/8/2018
ms.author: kumud
ms.openlocfilehash: 0aab72fdf48589a72707ae87f90af11f65f35088
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="understand-load-balancer-probes"></a>Förstå belastningsfördelningen avsökningar

Azure belastningsutjämnare använder hälsoavsökningar för att avgöra vilken serverdel pool-instans som ska ta emot nya flöden. När en hälsoavsökningen inte belastningsutjämnaren slutar att skicka nya flöden till respektive feltillstånd instansen och påverkas inte befintliga flöden på instansen.  När alla instanser för backend-adresspool avsökning ned, kommer alla befintliga flöden timeout på alla instanser i serverdelspoolen.

Molntjänstroller (arbetsroller och webbroller) kan du använda gästagent för avsökning övervakning. TCP eller HTTP anpassade hälsoavsökningar måste konfigureras när du använder virtuella datorer bakom belastningsutjämnaren.

## <a name="understand-probe-count-and-timeout"></a>Förstå avsökningen antal och timeout

Avsökningen beteende beror på:

* Antal lyckade avsökningar som gör att en instans som är märkt som upp.
* Antal misslyckade avsökningar som orsakar en instans som är märkt som inaktiv.

Timeout och frekvens värdena i SuccessFailCount avgör om en instans bekräftas till körs eller inte körs. Timeout-värdet har angetts till två gånger värdet för frekvensen i Azure-portalen.

Avsökningen konfigurationen av alla belastningsutjämnade instanser för en slutpunkt (det vill säga en belastningsutjämnad uppsättning) måste vara samma. Du kan inte ha en annan avsökningen konfiguration för varje instans av serverroll eller VM i samma värdbaserad tjänst för en viss slutpunkt-kombination. Varje instans måste till exempel ha identiska lokala portar och timeout.

> [!IMPORTANT]
> En belastningsutjämningsavsökning använder IP-adressen 168.63.129.16. Den här offentliga IP-adressen underlättar kommunikationen till interna plattform resurser för den bring-your-äger-IP-scenariot Azure virtuella. Den virtuella offentliga IP-adressen 168.63.129.16 används i alla regioner och ändras inte. Vi rekommenderar att du IP-adressen i alla lokala Brandväggsprinciper. Det bör inte anses en säkerhetsrisk eftersom endast intern Azure-plattformen kan styra ett meddelande från den adressen. Om du inte tillåter denna IP-adress i principer för brandväggen oväntade problem uppstår i en mängd olika scenarier. Beteendet bland annat konfigurera samma IP-adressintervall 168.63.129.16 och duplicera IP-adresser.

## <a name="learn-about-the-types-of-probes"></a>Lär dig mer om vilka typer av avsökningar

### <a name="guest-agent-probe"></a>Gästen agent avsökning

En avsökning för gäst-agenten är endast tillgängligt för Azure-molntjänster. Belastningsutjämnare använder gästagenten inuti den virtuella datorn. Sedan lyssnar och svarar med ett HTTP-200 OK svar endast när instansen är i tillståndet redo. (Andra tillstånd är upptagen, återvinning eller stoppar.)

Mer information finns i [konfigurera tjänstdefinitionsfilen (csdef) för hälsoavsökningar](https://msdn.microsoft.com/library/azure/ee758710.aspx) eller [Kom igång genom att skapa en offentlig belastningsutjämnare för molntjänster](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Vad gör en gäst agent avsökning Markera en instans som ohälsosamt?

Om gästagenten inte svarar med HTTP 200 OK, markerar belastningsutjämnaren instans som inte svarar. Den stoppar sedan skickar trafik till den instansen. Belastningsutjämnaren fortsätter att pinga instansen. Om gästagenten svarar med en HTTP-200, skickar belastningsutjämnaren trafik till instansen igen.

När du använder en webbroll webbplats koden vanligtvis körs i w3wp.exe som inte övervakas av Azure fabric eller gäst-agenten. Fel i w3wp.exe (till exempel HTTP 500-svar) rapporterats inte till gästagenten. Belastningsutjämnaren tar därför inte instansen av rotationen.

### <a name="http-custom-probe"></a>Anpassade HTTP-avsökningen

Den anpassa HTTP-avsökningen åsidosätter standard gäst agent avsökning. Du kan skapa egna anpassade logik för att fastställa hälsotillståndet för instansen. Belastningsutjämnaren avsökningar slutpunkten var 15: e sekund som standard. Instansen anses vara i belastningen belastningsutjämnaren rotationen om svarar den med en HTTP-200 inom den angivna tiden. Tidsgränsen är 31 sekunder som standard.

En anpassad HTTP-avsökningen kan vara användbart om du vill implementera egna logik för att ta bort instanser från belastningen belastningsutjämnaren rotation. Du kan till exempel välja att ta bort en instans om det är mer än 90% CPU och returnerar status-200. Om du har web-roller som använder w3wp.exe kan du också få automatisk övervakning av din webbplats. Fel i din webbplats kod returnerar status-200 till belastningsutjämnaravsökningen.

> [!NOTE]
> Den anpassa HTTP-avsökningen stöder relativa sökvägar och HTTP-protokollet. HTTPS stöds inte.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Vad gör en anpassad HTTP-avsökningen Markera en instans som ohälsosamt?

* HTTP-programmet returnerar ett HTTP-svarskoden än 200 (till exempel 403, 404 och 500). Den här positiv bekräftelse varnar dig om du vill ta programinstansen out-of-service direkt.
* HTTP-servern svarar inte när tidsgränsen. Beroende på timeout-värde som har angetts flera avsökningen begäranden kan gå obesvarade innan avsökningen hämtar markerad som inte körs (det vill säga innan SuccessFailCount avsökningar skickas).
* Servern stänger anslutningen via en TCP-återställning.

### <a name="tcp-custom-probe"></a>Anpassade TCP-avsökning

TCP anpassade avsökningar initiera en anslutning genom att utföra en trevägs handskakning med den angivna porten.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Vad gör en anpassad TCP-avsökning Markera en instans som ohälsosamt?

* TCP-servern svarar inte när tidsgränsen. När avsökningen är markerad som inte kör beror på antalet misslyckade avsökningen begäranden som har konfigurerats för att gå obesvarade innan du markerar avsökningen som inte körs.
* Avsökningen tar emot en TCP återställa från rollinstansen.

Mer information om hur du konfigurerar en HTTP-hälsoavsökningen eller en TCP-avsökning finns [komma igång med en offentlig belastningsutjämnare i Resource Manager med hjälp av PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Lägga till Felfri instanser till belastningen belastningsutjämnaren rotation

TCP- och HTTP-avsökningar betraktas felfritt och markera rollinstans som felfri när:

* Belastningsutjämnaren hämtar en positiv avsökning startar för första gången den virtuella datorn.
* Numret SuccessFailCount (beskrivs ovan) definierar värdet på lyckad avsökningar som krävs för att markera rollinstans som felfritt. Om en rollinstans har tagits bort, antalet lyckade, efterföljande avsökningar lika med eller överskrider värdet för SuccessFailCount Markera rollinstans som körs.

> [!NOTE]
> Om hälsotillståndet för en rollinstans fluktuerar väntar belastningsutjämnaren längre innan rollinstansen flyttas tillbaka till felfritt tillstånd. Den här extra väntetid skyddar användaren och infrastrukturen och är en avsiktlig princip.

## <a name="use-log-analytics-for-a-load-balancer"></a>Använd logganalys för belastningsutjämning

Du kan använda [logga analytics](load-balancer-monitor-log.md) att söka på belastningen belastningsutjämnaren avsökningen hälsostatus och avsökning count. Loggning kan användas med Power BI eller Azure-åtgärdsinformationen för att tillhandahålla statistik om hälsostatus för belastningen belastningsutjämnaren.
