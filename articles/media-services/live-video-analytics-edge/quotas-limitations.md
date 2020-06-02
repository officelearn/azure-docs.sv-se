---
title: Video analys i real tid för IoT Edge kvoter – Azure
description: I den här artikeln beskrivs video analys i real tid för IoT Edge kvoter och begränsningar.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 9b01db8f1120174806f4b687f7e9ebc4e2386f3d
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261053"
---
# <a name="quotas-and-limitations"></a>Kvoter och begränsningar

Den här artikeln räknar upp kvoter och begränsningar för live video analys i IoT Edge modul.

## <a name="maximum-period-of-disconnected-use"></a>Maximal period för frånkopplad användning

Edge-modulen kan upprätthålla tillfälligt förlust av nätverks anslutningen. Om modulen är frånkopplad i mer än 36 timmar inaktive ras alla diagram instanser som kördes och ytterligare direkta metod anrop blockeras.

Om du vill återuppta modulen Edge till ett användnings tillstånd måste du återställa nätverks anslutningen och modulen måste kunna kommunicera med Azure Media Service-kontot.

## <a name="maximum-number-of-graph-instances"></a>Maximalt antal diagram instanser

Du kan ha högst 1000 diagram instanser per modul (skapas via GraphInstanceSet).

## <a name="maximum-number-of-graph-topologies"></a>Maximalt antal diagram topologier

Du kan ha högst 50 graf-topologier per modul (skapad via GraphTopologySet).

## <a name="limitations-on-graph-topologies-at-preview"></a>Begränsningar för graf-topologier i för hands versionen

I för hands versionen finns det begränsningar för olika noder som kan anslutas tillsammans i en Media Graph-topologi.

* RTSP-källa
   * Endast en RTSP-källa tillåts per graf-topologi.
* Filter processor för RAM hastighet
   * Måste omedelbart underordnas från RTSP-käll-eller rörelse identifierings processorn.
   * Kan inte användas för en HTTP-tilläggsbegäranden.
   * Det går inte att strömma från en rörelse identifierings processor.
* Processor för HTTP-tillägg
   * Det får finnas högst en sådan processor per graf-topologi.
* Processor för rörelse identifiering
   * Måste omedelbart underordnas från RTSP-källan.
   * Det får finnas högst en sådan processor per graf-topologi.
   * Kan inte användas för en HTTP-tilläggsbegäranden.
* Signal grind processor
   * Måste omedelbart underordnas från RTSP-källan.
* Till gångs mottagare 
   * Det får finnas högst en sådan nod per graf-topologi.
      * Om en till gångs mottagare används kan det inte finnas någon fil mottagare eller vice versa.
   * Måste omedelbart underordnas från RTSP-källan eller signal grind processor.
* Fil mottagare
   * Det får finnas högst en sådan nod per diagram sto pol Ogin (se ovanstående anmärkning om till gångs mottagare).
   * Måste omedelbart underordnas från signal grind processor.
   * Får inte omedelbart underordnas av en HTTP-förlängning eller processor för rörelse identifiering
* IoT Hub mottagare
   * Får inte vara direkt underordnad en IoT Hub källa.

Om båda processorerna för rörelse identifiering och filter frekvens används ska de finnas i samma kedja av noder som leder till RTSP-Källnoden.

## <a name="limitations-on-media-service-operations-at-preview"></a>Begränsningar för media service-åtgärder i för hands versionen

I samband med för hands versionen stöder Live Video Analytics på IoT Edge inte följande:

* Möjlighet att migrera Media Service-kontot från en prenumeration till en annan utan avbrott.
* Möjligheten att använda mer än ett lagrings konto med Media Service-kontot.
* Möjligheten att ändra tjänstens huvud namns information i önskade egenskaper för modulen dynamiskt, utan att starta om.

## <a name="next-steps"></a>Nästa steg

[Översikt](overview.md)
