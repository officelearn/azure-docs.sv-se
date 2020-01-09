---
title: Förstå mått för Azure våren Cloud
description: Lär dig hur du granskar mått i Azure våren Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 682cc8383db5704b28b9f4d6b8c22c4bbfc3f1ab
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75528206"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Förstå mått för Azure våren Cloud

Azure Metrics Explorer är en komponent i Microsoft Azure-portalen som gör det möjligt att rita diagram, visuellt korrelera trender och undersöka toppar och DIP i mått. Använd Mät Utforskaren för att undersöka hälso tillståndet och användningen av dina resurser. 

I Azure våren Cloud finns det två visnings punkter för mått.
* Diagram på varje program översikts sida
* Sidan gemensamma mått

 ![Mått diagram](media/metrics/metrics-1.png)

Diagram i program **översikten** ger snabba status kontroller för varje program. Sidan gemensamma **mått** innehåller alla mått som är tillgängliga för referens. Du kan bygga egna diagram på sidan gemensamma mått och fästa dem på instrument panelen.

## <a name="application-overview-page"></a>Sidan program översikt
Välj en app i **app Management** för att hitta diagram på sidan Översikt.  

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

Välj sedan sammansättnings typ för varje mått:

![Mått agg regering](media/metrics/metrics-5.png)

Sammansättnings typen anger hur lång tid det ska sammanställas. Det finns en mått punkt varje minut.
* Totalt: summera alla mått som mål utdata.
* Genomsnittligt värde: Använd medelvärdet i perioden som mål för utdata.
* Max/min: Använd max-/min-värdet i perioden som mål utdata.

Tidsintervallet som ska visas kan också ändras.  Tidsintervallet kan väljas från de senaste 30 minuterna till de senaste 30 dagarna, eller ett anpassat tidsintervall.

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

## <a name="user-portal-metrics-options"></a>Mått alternativ för användar Portal

I följande tabell visas tillgängliga mått och information.
>[!div class="mx-tdBreakAll"]
>| Namn | Visningsnamn | Mått namn för våren-motstånd | Enhet | Information |
>|----|----|----|----|------------|
>| SystemCpuUsagePercentage | Procent andel system CPU-användning | system. CPU. Usage | Procent | Senaste CPU-användning för hela systemet. Det här värdet är ett dubbelt i [0,0, 1,0]-intervallet. Värdet 0,0 innebär att alla processorer var inaktiva under den senaste tids perioden, medan värdet 1,0 innebär att alla processorer kördes aktivt 100% av tiden under den senaste perioden. Alla värden mellan 0,0 och 1,0 är möjliga beroende på vilka aktiviteter som finns i systemet. Om systemet senaste CPU-användning inte är tillgängligt returnerar metoden ett negativt värde. |
>| AppCpuUsagePercentage | Procent andel CPU-användning för app | Procent andel CPU-användning för app | Procent | Senaste CPU-användning för Java Virtual Machine processen. Det här värdet är ett dubbelt i [0,0, 1,0]-intervallet. Värdet 0,0 innebär att ingen av processorerna körde trådar från JVM-processen under den senaste tids perioden, medan värdet 1,0 innebär att alla processorer aktivt körde trådar från JVM 100% av tiden under den senaste perioden. Trådar från JVM omfattar både program trådar och JVM interna trådar. Alla värden mellan 0,0 och 1,0 är möjliga beroende på vilka aktiviteter som pågår i JVM-processen och hela systemet. Om den Java Virtual Machine senaste processor användningen inte är tillgänglig returnerar metoden ett negativt värde. |
>| AppMemoryCommitted | Tilldelad app-minne | JVM. Memory. dedikerat | Byte | Motsvarar den mängd minne (i byte) som garanterat är tillgängligt för användning av den virtuella Java-datorn. Mängden allokerat minne kan ändras över tid (ökning eller minskning). Den virtuella datorn i Java kan frigöra minne till systemet och allokeras kan vara mindre än init. bekräftat är alltid större än eller lika med används. |
>| AppMemoryUsed | Använt app-minne | JVM. Memory. används | Byte | Representerar mängden minne som för närvarande används i byte. |
>| AppMemoryMax | Max för app-minne | JVM. Memory. Max | Byte | Motsvarar den maximala mängd minne (i byte) som kan användas för minnes hantering. Värdet kan vara odefinierat. Den maximala mängden minne kan ändras med tiden om den har definierats. Mängden använt och allokerat minne är alltid mindre än eller lika med max om max definieras. En minnesallokering kan Miss lyckas om det försöker öka det använda minnet som används > dedikerat, även om det används < = Max skulle fortfarande vara sant (till exempel när systemet har ont om virtuellt minne). |
>| MaxOldGenMemoryPoolBytes | Maximal tillgänglig gammal generations data storlek | JVM. gc. max. data storlek | Byte | Högsta minnes användning för den gamla generationens lagringspool sedan den virtuella Java-datorn startades. |
>| OldGenMemoryPoolBytes | Gammal generations data storlek | JVM. gc. live. data. size | Byte | Storleken på den gamla generationens lagringspool efter en fullständig GC. |
>| OldGenPromotedBytes | Befordra till gammal generations data storlek | JVM. gc. Memory. upphöjt | Byte | Antalet positiva ökningar i storleken på den gamla generationens minnesallokering innan GC till efter GC. |
>| YoungGenPromotedBytes | Höj data storlek för unga generationer | JVM. gc. Memory. allokerat | Byte | Ökas för en ökning av storleken på den unga generationens minnesbuffert efter en GC till innan nästa. |
>| GCPauseTotalCount | Antal pauser för GC | JVM. gc. PAUSE (totalt antal) | Antal | Totalt antal GC efter att denna JMV har startats, inklusive unga och gamla GC. |
>| GCPauseTotalTime | Total tid för GC-paus | JVM. gc. PAUSE (total tid) | Millisekunder | Totalt antal GC-tid som förbrukas efter att denna JMV startades, inklusive unga och gamla GC. |
>| TomcatSentBytes | Totalt antal skickade byte i Tomcat | tomcat. global. skickat | Byte | Mängden data Tomcat webb server som skickats, i byte |
>| TomcatReceivedBytes | Totalt antal mottagna byte i Tomcat | tomcat. global. mottagen | Byte | Mängden data Tomcat webb servern tog emot, i byte |
>| TomcatRequestTotalTime | Total tid för Tomcat-begäran | tomcat. global. Request (total tid) | Millisekunder | Total tid för Tomcat-webbservern för bearbetning av begär Anden. |
>| TomcatRequestTotalCount | Totalt antal Tomcat-begäranden | tomcat. global. Request (totalt antal) | Antal | Totalt antal behandlade Tomcat webb server begär Anden |
>| TomcatRequestMaxTime | Max tid för Tomcat-begäran | tomcat. global. Request. Max | Millisekunder | Maximal tid som Tomcat webb server kan bearbeta en begäran |
>| TomcatErrorCount | Tomcat globalt fel | tomcat. global. error | Antal | Antal fel som inträffar för bearbetade begär Anden |
>| TomcatSessionActiveMaxCount | Antal aktiva Tomcat-sessioner | tomcat. sessions. Active. Max | Antal | Maximalt antal sessioner som har varit aktiva samtidigt |
>| TomcatSessionAliveMaxTime | Maximal Alive-tid för Tomcat-session | tomcat. sessions. Alive. Max | Millisekunder | Längsta tid (i sekunder) som en utgången session hade varit aktiv |
>| TomcatSessionCreatedCount | Antal skapade Tomcat-sessioner | tomcat. sessions. created | Antal | Antal sessioner som har skapats |
>| TomcatSessionExpiredCount | Antal utgångna Tomcat-sessioner | tomcat. sessions. har gått ut | Antal | Antal sessioner som har upphört att gälla |
>| TomcatSessionRejectedCount | Antal nekade Tomcat-sessioner | tomcat. sessions. avvisad | Antal | Antal sessioner som inte skapades eftersom det maximala antalet aktiva sessioner har uppnåtts. |

## <a name="see-also"></a>Se också
* [Komma igång med Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analysera loggar och mått med diagnostikinställningar](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Nästa steg
* [Självstudie: övervaka våren moln resurser med hjälp av aviseringar och åtgärds grupper](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Kvoter och service planer för Azure våren Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

