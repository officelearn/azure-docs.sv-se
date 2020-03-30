---
title: Azure Monitor för behållare hälsoövervakare konfiguration | Microsoft-dokument
description: Den här artikeln innehåller innehåll som beskriver den detaljerade konfigurationen av hälsoövervakare i Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055708"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Azure Monitor för behållare hälsoövervakarens konfigurationsguide

Övervakare är det primära elementet för att mäta hälsotillstånd och identifiera fel i Azure Monitor för behållare. Den här artikeln hjälper dig att förstå begreppen för hur hälsa mäts och de element som utgör hälsomodellen för att övervaka och rapportera om hälsotillståndet för kubernetes-klustret med funktionen [Hälsa (förhandsversion).](container-insights-health.md)

>[!NOTE]
>Hälsofunktionen är i offentlig förhandsversion just nu.
>

## <a name="monitors"></a>Övervakare

En övervakare mäter hälsan hos någon aspekt av ett hanterat objekt. Övervakare har antingen två eller tre hälsotillstånd vardera. En övervakare kommer att vara i ett och endast ett av dess potentiella tillstånd vid en given tidpunkt. När en övervakare som läses in av behållaren initieras den till ett felfritt tillstånd. Tillståndet ändras endast om de angivna villkoren för ett annat tillstånd identifieras.

Den allmänna hälsan hos ett visst objekt bestäms av hälsan hos var och en av dess övervakare. Den här hierarkin illustreras i fönstret Hälsohierarki i Azure Monitor för behållare. Principen för hur hälsa summeras är en del av konfigurationen av de sammanlagda övervakarna.

## <a name="types-of-monitors"></a>Typer av bildskärmar

|Övervaka | Beskrivning | 
|--------|-------------|
| Enhetsövervakare |En enhetsövervakare mäter någon aspekt av en resurs eller ett program. Detta kan vara att kontrollera en prestandaräknare för att fastställa resursens prestanda eller dess tillgänglighet. |
|Aggregerad bildskärm | Aggregerade övervakare grupperar flera övervakare för att tillhandahålla ett enda hälsoaggregerat hälsotillstånd. Enhetsövervakare konfigureras vanligtvis under en viss aggregerad övervakare. En nodaggregerad övervakare samlar till exempel status för nod-CPU-användning, minnesanvändning och nodstatus.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Aggregerad princip för samlad övervakare

Varje aggregerad övervakare definierar en hälsosammanslagningsprincip, vilket är den logik som används för att fastställa hälsotillståndet för den aggregerade övervakaren baserat på hälsotillståndet för övervakarna under den. De möjliga principer för sammanslagning av hälsotillstånd för en aggregerad övervakare är följande:

#### <a name="worst-state-policy"></a>Sämsta statliga policy

Tillståndet för den sammanlagda övervakaren matchar tillståndet för den underordnade övervakaren med det sämsta hälsotillståndet. Detta är den vanligaste principen som används av aggregerade övervakare.

![Exempel på värsta tillstånd för samlad mängd för aggregerad övervakare](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Procentuell policy

Källobjektet matchar det värsta tillståndet för en enskild medlem i en angiven procentandel målobjekt i bästa läge. Den här principen används när en viss procentandel av målobjekten måste vara felfria för att målobjektet ska betraktas som felfritt. Procentprincipen sorterar övervakarna i fallande ordning efter allvarlighetsgrad för tillstånd och den sammanlagda övervakarens tillstånd beräknas som det värsta tillståndet för N% (N dikteras av konfigurationsparametern *StateThresholdPercentage*).

Anta till exempel att det finns fem behållarinstanser av en behållaravbildning och deras enskilda tillstånd är **kritiska**, **Varning**, **Felfri**, **Felfri**, **Felfri**.  Statusen för behållaren CPU-användning övervaka kommer att vara **kritisk**, eftersom det värsta tillståndet för 90% av behållarna är **kritisk** när sorteras i fallande ordning allvarlighetsgrad.

## <a name="understand-the-monitoring-configuration"></a>Förstå övervakningskonfigurationen

Azure Monitor för behållare innehåller ett antal viktiga övervakningsscenarier som har konfigurerats enligt följande.

### <a name="unit-monitors"></a>Enhetsövervakare

|**Övervaka namn** | Typ av bildskärm | **Beskrivning** | **Parametern** | **Värde** |
|-----------------|--------------|-----------------|---------------|-----------|
|Nodminnesanvändning |Enhetsövervakare |Den här övervakaren utvärderar minnesanvändningen av en nod varje minut med hjälp av cadvisor-rapporterade data. |På varandra följandeSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterTrPercentage | 3<br> 90<br> 80  ||
|Cpu-användning för nod |Enhetsövervakare |Den här övervakaren kontrollerar CPU-användningen av noden varje minut med hjälp av cadvisor rapporterade data. | På varandra följandeSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterTrPercentage | 3<br> 90<br> 80  ||
|Nodstatus |Enhetsövervakare |Den här övervakaren kontrollerar nodvillkor som rapporterats av Kubernetes.<br> För närvarande kontrolleras följande nodvillkor: Disktryck, minnestryck, PID-tryck, på disk, Nätverket är inte tillgängligt, Klar status för noden.<br> Om antingen på *disk* eller *Nätverket inte är* **tillgängligt**beror det på ovan ändras övervakaren till **kritiskt** tillstånd.<br> Om några andra villkor är lika **med sant**, förutom **statusen Klar,** ändras övervakaren till ett **varningstillstånd.** | NodeConditionTypeForFailedState | outofdisk,nätverkotillgänglig ||
|Användning av behållarminne |Enhetsövervakare |Den här övervakaren rapporterar kombinerad hälsostatus för minnesutnyttjandet(RSS) för instanserna av behållaren.<br> Den utför en enkel jämförelse som jämför varje exempel med ett enda tröskelvärde och som anges av **konfigurationsparametern ConsecutiveSamplesForStateTransition**.<br> Dess tillstånd beräknas som det värsta tillståndet för 90% av behållaren (StateThresholdPercentage) instanser, sorterade i fallande ordning allvarlighetsgrad för behållarens hälsotillstånd (det vill säga Kritisk, Varning, Felfri).<br> Om ingen post tas emot från en behållarinstans rapporteras hälsotillståndet för behållarinstansen som **Okänd**och har högre prioritet i sorteringsordningen över **kritiskt** tillstånd.<br> Varje enskild behållarinstans tillstånd beräknas med de tröskelvärden som anges i konfigurationen. Om användningen är över kritiskt tröskelvärde (90%), är instansen i ett **kritiskt** tillstånd, om den är mindre än det kritiska tröskelvärdet (90 %) men större än varningströskeln (80 %), då är instansen i **ett varningstillstånd.** Annars är det i **hälsosamt** tillstånd. |På varandra följandeSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterTrPercentage| 3<br> 90<br> 90<br> 80 ||
|Cpu-användning för behållare |Enhetsövervakare |Den här övervakaren rapporterar kombinerad hälsostatus för CPU-användningen av instanserna av behållaren.<br> Den utför en enkel jämförelse som jämför varje exempel med ett enda tröskelvärde och som anges av **konfigurationsparametern ConsecutiveSamplesForStateTransition**.<br> Dess tillstånd beräknas som det värsta tillståndet för 90% av behållaren (StateThresholdPercentage) instanser, sorterade i fallande ordning allvarlighetsgrad för behållarens hälsotillstånd (det vill säga Kritisk, Varning, Felfri).<br> Om ingen post tas emot från en behållarinstans rapporteras hälsotillståndet för behållarinstansen som **Okänd**och har högre prioritet i sorteringsordningen över **kritiskt** tillstånd.<br> Varje enskild behållarinstans tillstånd beräknas med de tröskelvärden som anges i konfigurationen. Om användningen är över kritiskt tröskelvärde (90%), är instansen i ett **kritiskt** tillstånd, om den är mindre än det kritiska tröskelvärdet (90 %) men större än varningströskeln (80 %), då är instansen i **ett varningstillstånd.** Annars är det i **hälsosamt** tillstånd. |På varandra följandeSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterTrPercentage| 3<br> 90<br> 90<br> 80 ||
|Systemarbetsbelastningspoddar redo |Enhetsövervakare |Den här övervakaren rapporterar status baserat på procentandel av poddar i färdigt tillstånd i en viss arbetsbelastning. Dess tillstånd är inställt **på Kritisk** om mindre än 100% av skida är i ett **hälsosamt** tillstånd |På varandra följandeSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Kube API-status |Enhetsövervakare |Den här övervakaren rapporterar status för Kube API-tjänsten. Övervakaren är i kritiskt tillstånd om Kube API-slutpunkten inte är tillgänglig. För den här övervakaren bestäms tillståndet genom att göra en fråga till slutpunkten "noder" för kube-api-servern. Allt annat än en OK-svarskod ändrar övervakaren till ett **kritiskt** tillstånd. | Inga konfigurationsegenskaper |||

### <a name="aggregate-monitors"></a>Aggregerade bildskärmar

|**Övervaka namn** | **Beskrivning** | **Algoritm** |
|-----------------|-----------------|---------------|
|Node |Den här övervakaren är en mängd av alla nodövervakare. Det matchar tillståndet för den underordnade övervakaren med det värsta hälsotillståndet:<br> Nod CPU-användning<br> Användning av nodminne<br> Nodstatus | Värsta av|
|Nodpool |Den här övervakaren rapporterar kombinerad hälsostatus för alla noder i nodpoolen *agentpool*. Detta är en tretillståndsövervakare, vars tillstånd baseras på det värsta tillståndet för 80 % av noderna i nodpoolen, sorterade i fallande ordning för allvarlighetsgraden för nodtillstånd (det vill säga Kritisk, Varning, Felfri).|Procent |
|Noder (överordnad nodpool) |Detta är en aggregerad övervakare av alla nodpooler. Dess tillstånd baseras på det värsta tillståndet för dess underordnade övervakare (det vill säga nodpoolerna som finns i klustret). |Värsta av |
|Kluster (överordnat noder/<br> Kubernetes infrastruktur) |Det här är den överordnade övervakaren som matchar tillståndet för den underordnade övervakaren med det sämsta hälsotillståndet, det vill säga kubernetes-infrastruktur och noder. |Värsta av |
|Kubernetes infrastruktur |Den här övervakaren rapporterar kombinerad hälsostatus för de hanterade infrastrukturkomponenterna i klustret. dess status beräknas som den "värsta av" dess underordnade övervakare tillstånd dvs kube-system arbetsbelastningar och API Server status. |Värsta av|
|Systemets arbetsbelastning |Den här övervakaren rapporterar hälsostatus för en kube-system-arbetsbelastning. Den här övervakaren matchar tillståndet för den underordnade övervakaren med det sämsta hälsotillståndet, det vill **säga poddar i färdigt tillstånd** (övervaka och behållarna i arbetsbelastningen). |Värsta av |
|Container |Den här övervakaren rapporterar övergripande hälsostatus för en behållare i en viss arbetsbelastning. Den här övervakaren matchar tillståndet för den underordnade övervakaren med det sämsta hälsotillståndet, det vill säga övervakare av **CPU-användning** **och minnesanvändning.** |Värsta av |

## <a name="next-steps"></a>Nästa steg

Visa [övervakarkhälsa](container-insights-health.md) om du vill veta mer om hur du visar hälsostatusen för Kubernetes-klustret.
