---
title: Azure Monitor för behållare Health Monitors-konfiguration | Microsoft Docs
description: Den här artikeln innehåller innehåll som beskriver den detaljerade konfigurationen av hälso övervakare i Azure Monitor för behållare.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/12/2019
ms.author: magoedte
ms.openlocfilehash: b782581318751830ec47b9fecb056fecefb353eb
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134962"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Konfigurations guide för Azure Monitor för behållares hälso övervakning

Övervakare är det primära elementet för att mäta hälsa och identifiera fel i Azure Monitor för behållare. Den här artikeln hjälper dig att förstå koncepten för hur hälsa mäts och vilka element som utgör hälso modellen för att övervaka och rapportera om hälsan hos ditt Kubernetes-kluster med [hälso funktionen](container-insights-health.md).

## <a name="monitors"></a>Övervakare

En övervakare mäter hälsan för viss aspekt av ett hanterat objekt. Övervaka var och en har två eller tre hälso tillstånd. En övervakare är i en och endast en av dess potentiella tillstånd vid en bestämd tidpunkt. När en Övervakare som lästs in av behållaren agent, initieras den till felfritt tillstånd. Tillståndet ändras endast om de angivna villkoren för ett annat tillstånd upptäcks.

Det övergripande hälso tillståndet för ett visst objekt bestäms av hälso tillståndet för var och en av dess övervakare. Den här hierarkin illustreras i fönstret hälsohierarki i Azure Monitor for containers. Principen för hur hälso tillståndet samlas in är en del av konfigurationen av de sammanställda övervakarna.

## <a name="types-of-monitors"></a>Typer av övervakare

|Övervaka | Beskrivning | 
|--------|-------------|
| Enhets övervakare |En enhets övervakare mäter viss aspekt av en resurs eller ett program. Detta kan kontrol lera en prestanda räknare för att fastställa resursens prestanda eller dess tillgänglighet. |
|Sammanställd övervakare | Sammanställda övervakare grupperar flera Övervakare för att tillhandahålla ett hälso tillstånds sammanställt hälso tillstånd. Enhets övervakare konfigureras vanligt vis under en viss sammanställd övervakare. En mängd Övervakare för en nod samlar till exempel in statusen för nodens processor användning, minnes användning och nod status.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Sammanställd hälso princip för hälso sammanslagning

Varje Aggregate Monitor definierar en hälso sammanslagnings princip, vilket är den logik som används för att fastställa hälso tillståndet för den sammanställda övervakaren baserat på hälso tillståndet för övervakarna. De möjliga hälso sammanslagnings principerna för en sammanställd övervakare är följande:

#### <a name="worst-state-policy"></a>Princip för sämsta tillstånd

Tillståndet för den sammanställda övervakaren matchar tillståndet för den underordnade övervakaren med det sämsta hälso tillståndet. Det här är den vanligaste principen som används av sammanställda övervakare.

![Exempel på insamlat tillstånd för samlad övervakare](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Procent princip

Källobjektet matchar sämsta tillstånd för en enskild medlem i en angiven procent andel mål objekt i det bästa läget. Den här principen används när en viss procent andel av mål objekt måste vara felfri för att målobjektet ska betraktas som felfri. Procent principen sorterar övervakarna i fallande ordning efter allvarlighets grad och den sammanställda övervaknings statusen beräknas som det sämsta värdet N% (N styrs av konfigurations parametern *StateThresholdPercentage*).

Anta till exempel att det finns fem behållar instanser av en behållar avbildning ochderas enskilda tillstånd är **kritiska**, **Varning**, felfria, felfria.  Statusen för övervakaren för container processor belastning är **kritisk**, eftersom det sämsta tillståndet på 90% av behållarna är **kritisk** vid sortering i fallande ordning i allvarlighets grad.

## <a name="understand-the-monitoring-configuration"></a>Förstå övervaknings konfigurationen

Azure Monitor för behållare innehåller ett antal nyckel övervaknings scenarier som är konfigurerade på följande sätt.

### <a name="unit-monitors"></a>Enhets övervakare

|**Övervakarens namn** | Övervaknings typ | **Beskrivning** | **Parametern** | **Värde** |
|-----------------|--------------|-----------------|---------------|-----------|
|Minnes användning för nod |Enhets övervakare |Den här övervakaren utvärderar minnes användningen för en nod varje minut med hjälp av cadvisor rapporterade data. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|CPU-användning för nod |Enhets övervakare |Den här övervakaren kontrollerar nodens processor användning varje minut med hjälp av cadvisor rapporterade data. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Nod status |Enhets övervakare |Den här övervakaren kontrollerar regler för noden som rapporteras av Kubernetes.<br> För närvarande kontrol leras följande förutsättningar: disk tryck, minnes belastning, PID-tryck, slut på disk, otillgängligt nätverk, klar läge för noden.<br> Om ingen *av diskarna* eller *nätverket är inaktiverat* är **True**, ändras övervakaren till **kritiskt** tillstånd.<br> Om andra villkor är **sanna**, förutom statusen **klar** , ändras övervakaren till ett **varnings** tillstånd. | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|Minnes användning för behållare |Enhets övervakare |Den här övervakaren rapporterar den kombinerade hälso statusen för minnes användningen (RSS) för instanser av containern.<br> Den utför en enkel jämförelse som jämför varje exempel med ett enda tröskelvärde och som anges av konfigurations parametern **ConsecutiveSamplesForStateTransition**.<br> Dess tillstånd beräknas som det sämsta tillståndet på 90% av containern (StateThresholdPercentage), sorterat i fallande ordning efter allvarlighets graden för behållar hälso tillståndet (det vill säga, kritiskt, varning, felfri).<br> Om ingen post tas emot från en behållar instans rapporteras hälso tillståndet för behållar instansen som **okänd**och har högre prioritet i sorterings ordningen över **kritiskt** tillstånd.<br> Varje enskild behållar instanss tillstånd beräknas med tröskelvärdena som anges i konfigurationen. Om användningen överskrider det kritiska tröskelvärdet (90%) är instansen i ett **kritiskt** tillstånd, om den är mindre än det kritiska tröskelvärdet (90%) men större än varnings tröskeln (80%) är instansen i ett **varnings** tillstånd. Annars är den i **felfritt** tillstånd. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|PROCESSOR användning i behållare |Enhets övervakare |Den här övervakaren rapporterar en kombinerad hälso status för processor användningen för instanser av containern.<br> Den utför en enkel jämförelse som jämför varje exempel med ett enda tröskelvärde och som anges av konfigurations parametern **ConsecutiveSamplesForStateTransition**.<br> Dess tillstånd beräknas som det sämsta tillståndet på 90% av containern (StateThresholdPercentage), sorterat i fallande ordning efter allvarlighets graden för behållar hälso tillståndet (det vill säga, kritiskt, varning, felfri).<br> Om ingen post tas emot från en behållar instans rapporteras hälso tillståndet för behållar instansen som **okänd**och har högre prioritet i sorterings ordningen över **kritiskt** tillstånd.<br> Varje enskild behållar instanss tillstånd beräknas med tröskelvärdena som anges i konfigurationen. Om användningen överskrider det kritiska tröskelvärdet (90%) är instansen i ett **kritiskt** tillstånd, om den är mindre än det kritiska tröskelvärdet (90%) men större än varnings tröskeln (80%) är instansen i ett **varnings** tillstånd. Annars är den i **felfritt** tillstånd. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Systemets arbets belastning poddar klar |Enhets övervakare |Den här övervakaren rapporterar status baserat på procent andelen poddar i klar läge i en viss arbets belastning. Tillståndet är inställt på **kritiskt** om mindre än 100% av poddar är i **felfritt** tillstånd |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Kube API-status |Enhets övervakare |Den här övervakaren rapporterar status för Kube-API-tjänsten. Övervakaren är i kritiskt tillstånd om API-slutpunkten för Kube inte är tillgänglig. För den här typen av övervakare bestäms tillstånd genom att göra en fråga till slut punkten "Nodes" för Kube-API-servern. Allt annat än en OK-svars kod ändrar övervakaren till **kritiskt** tillstånd. | Inga konfigurations egenskaper |||

### <a name="aggregate-monitors"></a>Sammanställda övervakare

|**Övervakarens namn** | **Beskrivning** | **Algoritm** |
|-----------------|-----------------|---------------|
|Node |Den här övervakaren är en mängd olika noder som övervakare. Den matchar tillståndet för den underordnade övervakaren med det sämsta hälso tillståndet:<br> CPU-användning för nod<br> Minnes användning för nod<br> Nod status | Sämsta av|
|Node-pool |Den här övervakaren rapporterar en kombinerad hälso status för alla noder i nodens *agentpoolegenskap*. Detta är en övervakare med tre tillstånd, vars tillstånd baseras på det sämsta tillståndet på 80% av noderna i Node-poolen, sorterade i fallande ordning efter allvarlighets grad för Node-tillstånd (det vill säga, kritisk, varning, felfri).|Procent |
|Noder (överordnad för Node-pool) |Detta är en sammanställd övervakare av alla Node-pooler. Dess tillstånd baseras på de sämsta tillstånden för dess underordnade övervakare (det vill säga de noder som finns i klustret). |Sämsta av |
|Kluster (överordnad av noder/<br> Kubernetes-infrastruktur) |Det här är den överordnade övervakaren som matchar tillståndet för den underordnade övervakaren med sämsta hälso tillstånd, som är Kubernetes-infrastruktur och noder. |Sämsta av |
|Kubernetes-infrastruktur |Den här övervakaren rapporterar den kombinerade hälso statusen för de hanterade infrastruktur komponenterna i klustret. dess status beräknas som sämsta av dess underordnade övervakare, dvs. Kube arbets belastningar och API-Server status. |Sämsta av|
|Systemets arbets belastning |Övervakaren rapporterar hälso status för en Kube arbets belastning. Den här övervakaren matchar tillståndet för den underordnade övervakaren med det sämsta hälso tillståndet, som är **poddar i Övervakaren för klar läge** och behållarna i arbets belastningen. |Sämsta av |
|Container |Den här övervakaren rapporterar övergripande hälso status för en behållare i en specifik arbets belastning. Den här övervakaren matchar tillståndet för den underordnade övervakaren med det sämsta hälso tillståndet, det vill säga **processor användning** och **minnes användnings** övervakare. |Sämsta av |

## <a name="next-steps"></a>Nästa steg

Se [övervaka kluster hälsa](container-insights-health.md) för att lära dig mer om att Visa hälso status för Kubernetes-klustret.