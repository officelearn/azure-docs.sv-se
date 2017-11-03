---
title: "Läsa in belastningsutjämning anpassade avsökningar och övervaka hälsostatus | Microsoft Docs"
description: "Lär dig hur du använder anpassade avsökningar för Azure belastningsutjämnare för att övervaka instanser bakom belastningsutjämnaren"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 102c07ff0994b3b411f2a13d7a43c5398d5dfd42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="understand-load-balancer-probes"></a>Förstå avsökningar av belastningsutjämnare

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure belastningsutjämnare ger möjlighet att övervaka hälsotillståndet för server-instanser med hjälp av avsökningar. När en avsökning inte svarar, stoppas belastningsutjämnaren skickar nya anslutningar till feltillstånd-instansen. Befintliga anslutningar påverkas inte och nya anslutningar skickas till Felfri instanser.

Molntjänstroller (arbetsroller och webbroller) kan du använda gästagent för avsökning övervakning. Anpassade TCP eller HTTP-avsökningar måste konfigureras när du använder virtuella datorer bakom belastningsutjämnaren.

## <a name="understand-probe-count-and-timeout"></a>Förstå avsökningen antal och timeout

Avsökningen beteende beror på:

* Antal lyckade avsökningar som gör att en instans som är märkt som upp.
* Antal misslyckade avsökningar som orsakar en instans som är märkt som inaktiv.

Timeout och frekvens värdet i SuccessFailCount avgör om en instans bekräftas till körs eller inte körs. Timeout-värdet har angetts till två gånger värdet för frekvensen i Azure-portalen.

Avsökningen konfigurationen av alla belastningsutjämnade instanser för en slutpunkt (det vill säga en belastningsutjämnad uppsättning) måste vara samma. Det innebär att du inte kan ha en annan avsökningen konfiguration för varje instans av serverroll eller virtuell dator i samma värdbaserad tjänst för en viss slutpunkt-kombination. Varje instans måste till exempel ha identiska lokala portar och timeout.

> [!IMPORTANT]
> En belastningsutjämnare avsökning använder IP-adressen 168.63.129.16. Den här offentliga IP-adressen underlättar kommunikationen till interna plattform resurser för den bring-your-äger-IP-scenariot Azure virtuella. Den virtuella offentliga IP-adressen 168.63.129.16 används i alla regioner och ändras inte. Vi rekommenderar att du IP-adressen i alla lokala Brandväggsprinciper. Det bör inte anses en säkerhetsrisk eftersom endast intern Azure-plattformen kan styra ett meddelande från den adressen. Om du inte gör det, kommer det att oväntade resultat i en mängd olika scenarier exempelvis konfigurera samma IP-adressintervall 168.63.129.16 och har dubblerats IP-adresser.

## <a name="learn-about-the-types-of-probes"></a>Lär dig mer om vilka typer av avsökningar

### <a name="guest-agent-probe"></a>Gästen agent avsökning

Den här avsökningen är endast tillgängligt för Azure-molntjänster. Belastningsutjämnare använder gästagenten på den virtuella datorn lyssnar och svarar med ett HTTP-200 OK svar endast när instansen är i tillståndet redo (det vill säga inte i ett annat tillstånd, till exempel upptagen återvinning eller stoppar).

Mer information finns i [konfigurera tjänstdefinitionsfilen (csdef) för hälsoavsökningar](https://msdn.microsoft.com/library/azure/ee758710.aspx) eller [komma igång med en Internetriktade belastningsutjämnare för molntjänster](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Vad gör en gäst agent avsökning Markera en instans som ohälsosamt?

Om gästagenten inte svarar med HTTP 200 OK, markerar instansen som inte svarar belastningsutjämnaren och slutar skickar trafik till den instansen. Belastningsutjämnaren fortsätter att pinga instansen. Om gästagenten svarar med en HTTP-200, skickar belastningsutjämnaren trafik till instansen igen.

När du använder en webbroll webbplats koden vanligtvis körs i w3wp.exe som inte övervakas av Azure fabric eller gäst-agenten. Detta innebär att fel i w3wp.exe (till exempel HTTP 500-svar) inte kommer att rapporteras till gästagenten och den instansen utanför rotation träder inte i belastningsutjämnaren.

### <a name="http-custom-probe"></a>Anpassade HTTP-avsökningen

Anpassade HTTP-belastningsutjämnaren avsökningen åsidosätter standard gäst agent avsökningen, vilket innebär att du kan skapa egna anpassade logik för att fastställa hälsotillståndet för instansen. Belastningsutjämnaren avsökningar slutpunkten var 15: e sekund som standard. Instansen anses vara i belastningen belastningsutjämnaren rotationen om svarar den med en HTTP-200 inom tidsgränsen (31 sekunder som standard).

Detta kan vara användbart om du vill implementera egna logik för att ta bort instanser från belastningen belastningsutjämnaren rotation. Du kan till exempel välja att ta bort en instans om det är mer än 90% CPU och returnerar status-200. Om du har web-roller som använder w3wp.exe detta betyder också att du får automatisk övervakning av din webbplats, eftersom fel i din webbplats kod returnerar status-200 till belastningsutjämnaravsökningen.

> [!NOTE]
> Den anpassa HTTP-avsökningen stöder relativa sökvägar och HTTP-protokollet. HTTPS stöds inte.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Vad gör en anpassad HTTP-avsökningen Markera en instans som ohälsosamt?

* HTTP-programmet returnerar ett HTTP-svarskoden än 200 (till exempel 403, 404 och 500). Det här är en positiv bekräftelse programinstansen ska tas bort från tjänsten direkt.
* HTTP-servern svarar inte alls när tidsgränsen. Beroende på timeout-värde som anges detta kan innebära att flera avsökningen begär gå obehandlade innan avsökningen hämtar markerad som inte körs (det vill säga innan SuccessFailCount avsökningar skickas).
* Servern stänger anslutningen via en TCP-återställning.

### <a name="tcp-custom-probe"></a>Anpassade TCP-avsökning

TCP-avsökningar initiera en anslutning genom att utföra en trevägs handskakning med den angivna porten.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Vad gör en anpassad TCP-avsökning Markera en instans som ohälsosamt?

* TCP-servern svarar inte på alla när tidsgränsen. När avsökningen är markerad som inte kör beror på antalet misslyckade avsökningen begäranden som har konfigurerats för att gå obesvarade innan du markerar avsökningen som inte körs.
* Avsökningen tar emot en TCP återställa från rollinstansen.

Mer information om hur du konfigurerar en HTTP-hälsoavsökningen eller en TCP-avsökning finns [komma igång med att skapa en Internetriktade belastningsutjämnare i Resource Manager med hjälp av PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Lägga till Felfri instanser till belastningen belastningsutjämnaren rotation

TCP- och HTTP-avsökningar betraktas felfritt och markera rollinstans som felfri när:

* Belastningsutjämnaren hämtar en positiv avsökning startar för första gången den virtuella datorn.
* Antalet SuccessFailCount (beskrivs ovan) definierar värdet på lyckad avsökningar som krävs för att markera rollinstans som felfritt. Om en rollinstans har tagits bort, antalet lyckade, efterföljande avsökningar lika med eller överskrider värdet för SuccessFailCount Markera rollinstans som körs.

> [!NOTE]
> Om hälsotillståndet för en rollinstans fluktuerar, väntar belastningsutjämnaren längre innan du sätter rollinstansen tillbaka i felfritt tillstånd. Detta görs via Grupprincip för att skydda användaren och infrastruktur.

## <a name="use-log-analytics-for-load-balancer"></a>Använd logganalys för belastningsutjämnare

Du kan använda [logga analytics för belastningsutjämnaren](load-balancer-monitor-log.md) att kontrollera om hälsostatus för avsökning och avsökning antal. Loggning kan användas med Power BI eller Azure-åtgärdsinformationen för att tillhandahålla statistik om hälsostatus för belastningsutjämnaren.
