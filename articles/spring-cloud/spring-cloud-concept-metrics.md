---
title: Förstå mått för Azure våren Cloud
description: Lär dig hur du granskar mått i Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: f84201de1f63704fefcf5de4041b95ed8c2122cd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094066"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Förstå mått för Azure våren Cloud

Azure Metrics Explorer är en komponent i Microsoft Azure-portalen som gör det möjligt att rita diagram, visuellt korrelera trender och undersöka toppar och DIP i mått. Använd Mät Utforskaren för att undersöka hälso tillståndet och användningen av dina resurser. 

I Azure våren-molnet finns det två synvinklar för mått.
* Diagram på varje program översikts sida
* Sidan gemensamma mått

 ![Mått diagram](media/metrics/metrics-1.png)

Diagram i program **översikten** ger snabba status kontroller för varje program. Sidan gemensamma **mått** innehåller alla mått som är tillgängliga för referens. Du kan bygga egna diagram på sidan gemensamma mått och fästa dem på instrument panelen.

## <a name="application-overview-page"></a>Sidan program översikt
Välj en app i **appar** för att hitta diagram på sidan Översikt.  

 ![Hantering av program mått](media/metrics/metrics-2.png)

På sidan **program översikt** för varje program visas ett mått diagram som gör att du kan utföra en snabb status kontroll av ditt program.  

 ![Översikt över program mått](media/metrics/metrics-3.png)

Azure våren Cloud tillhandahåller dessa fem diagram med mått som uppdateras varje minut:

* **Http-server fel**: fel antal för HTTP-begäranden till din app
* **Data i**: byte mottagna av appen
* **Data ut**: byte som har skickats av appen
* **Begär Anden**: begär Anden som tagits emot av din app
* **Genomsnittlig svars tid**: genomsnittlig svars tid från din app

I diagrammet kan du välja ett tidsintervall från en timme till sju dagar.

## <a name="common-metrics-page"></a>Sidan gemensamma mått

**Måtten** i det vänstra navigerings fönstret länkar till sidan gemensamma mått.

Välj först de mått som ska visas:

![Välj Metric-vy](media/metrics/metrics-4.png)

Information om alla mått alternativ finns i [avsnittet](#user-metrics-options) nedan.

Välj sedan sammansättnings typ för varje mått:

![Mått agg regering](media/metrics/metrics-5.png)

Sammansättnings typen visar hur du sammanställer mått punkter i diagrammet efter tid. Det finns en rå mått punkt varje minut och församlings typen inom en minut är fördefinierad av mått typen.
* Sum: summera alla värden som mål utdata.
* Genomsnittligt värde: Använd medelvärdet i perioden som mål för utdata.
* Max/min: Använd max-/min-värdet i perioden som mål utdata.

Tidsintervallet kan också justeras från de senaste 30 minuterna till de senaste 30 dagarna eller ett anpassat tidsintervall.

![Mått ändring](media/metrics/metrics-6.png)

Standardvyn innehåller alla application's mått för Azure våren Cloud service tillsammans. Mått för en app eller instans kan filtreras i visningen.  Klicka på **Lägg till filter**, ange egenskapen till **app**och välj det mål program som du vill övervaka i text rutan **värden** . 

Du kan använda två typer av filter (egenskaper):
* App: Filtrera efter app-namn
* Instans: Filtrera efter App-instans

![Mått filter](media/metrics/metrics-7.png)

Du kan också använda alternativet **Använd delning** , som ritar flera rader för en app:

![Mått delning](media/metrics/metrics-8.png)

>[!TIP]
> Du kan bygga egna diagram på sidan mått och fästa dem på din **instrument panel**. Börja med att namnge diagrammet.  Välj sedan **Fäst på instrument panelen i det övre högra hörnet**. Nu kan du kontrol lera ditt program på portalens **instrument panel**.

## <a name="user-metrics-options"></a>Alternativ för användar mått

I följande tabeller visas tillgängliga mått och information.

### <a name="error"></a>Fel
>[!div class="mx-tdCol2BreakAll"]
>| Name | Mått namn för våren-motstånd | Enhet | Information |
>|----|----|----|------------|
>| tomcat. global. error | tomcat. global. error | Count | Antal fel som inträffade i bearbetade begär Anden |

### <a name="performance"></a>Prestanda
>[!div class="mx-tdCol2BreakAll"]
>| Name | Mått namn för våren-motstånd | Enhet | Information |
>|----|----|----|------------|
>| system. CPU. Usage | system. CPU. Usage | Procent | Senaste CPU-användning för hela systemet. Det här värdet är ett dubbelt i [0,0, 1,0]-intervallet. Värdet 0,0 innebär att alla processorer var inaktiva under den senaste tids perioden, medan värdet 1,0 innebär att alla processorer kördes aktivt 100% av tiden under den senaste perioden.|
>| process. CPU. Usage | Procent andel CPU-användning för app | Procent | Senaste CPU-användning för Java Virtual Machine processen. Det här värdet är ett dubbelt i [0,0, 1,0]-intervallet. Värdet 0,0 innebär att ingen av processorerna körde trådar från JVM-processen under den senaste tids perioden, medan värdet 1,0 innebär att alla processorer aktivt körde trådar från JVM 100% av tiden under den senaste perioden. Trådar från JVM omfattar både program trådar och JVM interna trådar.|
>| JVM. Memory. dedikerat | JVM. Memory. dedikerat | Byte | Representerar mängden minne som garanterat kan användas av JVM. JVM kan frigöra minne till systemet och dedikerat kan vara mindre än init. bekräftat är alltid större än eller lika med används. |
>| JVM. Memory. används | JVM. Memory. används | Byte | Representerar mängden minne som för närvarande används i byte. |
>| JVM. Memory. Max | JVM. Memory. Max | Byte | Representerar den maximala mängd minne som kan användas för minnes hantering. Mängden använt och allokerat minne är alltid mindre än eller lika med max om max definieras. En minnesallokering kan Miss lyckas om det försöker öka det använda minnet som används > dedikerat, även om det används <= Max skulle fortfarande vara sant (till exempel när systemet har ont om virtuellt minne). |
>| JVM. gc. max. data storlek | JVM. gc. max. data storlek | Byte | Högsta minnes användning för den gamla generationens lagringspool sedan den virtuella Java-datorn startades. |
>| JVM. gc. live. data. size | JVM. gc. live. data. size | Byte | Storleken på den gamla generationens lagringspool efter en fullständig GC. |
>| JVM. gc. Memory. upphöjt | JVM. gc. Memory. upphöjt | Byte | Antalet positiva ökningar i storleken på den gamla generationens minnesallokering innan GC till efter GC. |
>| JVM. gc. Memory. allokerat | JVM. gc. Memory. allokerat | Byte | Ökas för en ökning av storleken på den unga generationens minnesbuffert efter en GC till innan nästa. |
>| JVM. gc. Pause. total. Count | JVM. gc. PAUSE (totalt antal) | Count | Totalt antal GC efter att denna JMV har startats, inklusive unga och gamla GC. |
>| JVM. gc. Pause. total. Time | JVM. gc. PAUSE (total tid) | Millisekunder | Totalt antal GC-tid som förbrukas efter att denna JMV startades, inklusive unga och gamla GC. |

::: zone pivot="programming-language-csharp"
### <a name="performance-net"></a>Prestanda (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Mått namn för våren-motstånd | Enhet | Information |
>|------|-----------------------------|------|---------|
>| CPU-användning       | CPU-användning      | Millisekunder | Den tid som processen har använt processorn. |
>| Arbets minne     | arbets uppsättning    | Megabyte    | Mängden arbets minne som används av processen. |
>| Storlek på GC-heap    | GC – Heap-storlek   | Megabyte    | Total Heap-storlek som rapporteras av skräp insamlaren. |
>| Antal start0 GC  | gen-0-GC-Count | Count        | Antal skräp insamlingar av generation 0 per sekund. |
>| Antal för gen 1 GC  | gen-1-GC-antal | Count        | Antalet skräp insamlingar av generation 1 per sekund. |
>| Antal gen 2 GC  | gen-2-GC – antal | Count        | Antal skräp insamlingar i generation 2 per sekund. |
>| Heap-storlek för generation 0 | gen-0-storlek     | Byte        | Heap-storlek för generation 0. |
>| Heap-storlek för generation 1 | gen-1-storlek     | Byte        | Heap-storlek för generation 1. |
>| Heap-storlek för generation 2 | gen-2-storlek     | Byte        | Heap-storlek för generation 2. |
>| LOH Heap-storlek   | Loh-storlek       | Byte        | Heap-storlek för stora objekt. |
>| Tilldelnings takt | tilldelnings pris     | Byte        | Antal allokerade byte per sekund. |
>| Antal sammansättningar  | antal sammansättningar | Count        | Antal inlästa sammansättningar. |
>| Antal undantag | undantag – antal | Count       | Antal undantag per sekund. |
>| Antal trådar i trådar      | trådpool-tråd antal              | Count | Antal trådar i trådpoolen. |
>| Övervaka antal lås i lås | övervaka-låser-Contents-Count        | Count | Antalet gånger per sekund som det uppstod en konkurrens vid försök att ta en skärms lås. |
>| Kölängd för trådpoolen      | trådpool-Kölängd              | Count | Kölängd för arbets objekt i trådpoolen. |
>| Antal slutförda objekt i trådpoolen för tråd pool | trådpool-slutfört-objekt-antal | Count | Antal slutförda arbets objekt i trådpoolen. |
>| Antal aktiva timers               | Antal aktiva timers               | Count | Antalet timers som är aktiva för närvarande. En aktiv timer är en som är registrerad för att ticka vid någon tidpunkt i framtiden och ännu inte har avbrutits. |

Mer information finns i [dotNet-räknare](/dotnet/core/diagnostics/dotnet-counters).
::: zone-end

### <a name="request"></a>Förfrågan
>[!div class="mx-tdCol2BreakAll"]
>| Name | Mått namn för våren-motstånd | Enhet | Information |
>|----|----|----|------------|
>| tomcat. global. skickat | tomcat. global. skickat | Byte | Mängden data Tomcat-webbserver som skickats |
>| tomcat. global. mottagen | tomcat. global. mottagen | Byte | Mängden data Tomcat-webb servern tog emot |
>| tomcat. global. Request. total. Count | tomcat. global. Request (totalt antal) | Count | Totalt antal behandlade Tomcat webb server begär Anden |
>| tomcat. global. Request. Max | tomcat. global. Request. Max | Millisekunder | Maximal tid som Tomcat webb server kan bearbeta en begäran |

::: zone pivot="programming-language-csharp"
### <a name="request-net"></a>Begäran (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Mått namn för våren-motstånd | Enhet | Information |
>|------|-----------------------------|------|---------|
>| Förfrågningar per sekund | begär Anden per sekund | Count | Begär ande frekvens. |
>| Totalt antal förfrågningar | Totalt-begär Anden | Count | Totalt antal begär Anden. |
>| Aktuella begär Anden | aktuella begär Anden | Count | Antal aktuella begär Anden. |
>| Misslyckade förfrågningar | misslyckade-begär Anden | Count | Antal misslyckade förfrågningar. |

Mer information finns i [dotNet-räknare](/dotnet/core/diagnostics/dotnet-counters).
::: zone-end

### <a name="session"></a>Session
>[!div class="mx-tdCol2BreakAll"]
>| Name | Mått namn för våren-motstånd | Enhet | Information |
>|----|----|----|------------|
>| tomcat. sessions. Active. Max | tomcat. sessions. Active. Max | Count | Maximalt antal sessioner som har varit aktiva samtidigt |
>| tomcat. sessions. Alive. Max | tomcat. sessions. Alive. Max | Millisekunder | Längsta tid (i sekunder) som en utgången session var Alive |
>| tomcat. sessions. created | tomcat. sessions. created | Count | Antal sessioner som har skapats |
>| tomcat. sessions. har gått ut | tomcat. sessions. har gått ut | Count | Antal sessioner som har upphört att gälla |
>| tomcat. sessions. avvisad | tomcat. sessions. avvisad | Count | Antal sessioner som inte skapades eftersom det maximala antalet aktiva sessioner har uppnåtts. |
>| tomcat. sessions. Active. Current | tomcat. sessions. Active. Current | Count | Aktivt antal Tomcat-sessioner |

## <a name="see-also"></a>Se även

* [Snabb start: övervaka Azure våren Cloud-appar med loggar, mått och spårning](spring-cloud-quickstart-logs-metrics-tracing.md)

* [Komma igång med Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md)

* [Analysera loggar och mått med diagnostikinställningar](./diagnostic-services.md)

## <a name="next-steps"></a>Nästa steg

* [Självstudie: övervaka våren moln resurser med hjälp av aviseringar och åtgärds grupper](./spring-cloud-tutorial-alerts-action-groups.md)

* [Kvoter och service planer för Azure våren Cloud](./spring-cloud-quotas.md)