---
title: Förstå mått för Azure våren Cloud
description: Lär dig hur du granskar mått i Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: 746366675501c80776f0a4308e22250f344a00c9
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343446"
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
>| Tomcat globalt fel (inaktuellt)<br><br>(som ska tas bort efter den 1 juli 2020)  | tomcat. global. error | Antal | Antal fel som inträffar för bearbetade begär Anden |
>| tomcat. global. error | tomcat. global. error | Antal | Antal fel som inträffar för bearbetade begär Anden |

### <a name="performance"></a>Prestanda
>[!div class="mx-tdCol2BreakAll"]
>| Name | Mått namn för våren-motstånd | Enhet | Information |
>|----|----|----|------------|
>| Procent andel system CPU-användning (inaktuell)<br><br>(som ska tas bort efter den 1 juli 2020) | system. CPU. Usage | Procent | Senaste CPU-användning för hela systemet. Det här värdet är ett dubbelt i [0,0, 1,0]-intervallet. Värdet 0,0 innebär att alla processorer var inaktiva under den senaste tids perioden, medan värdet 1,0 innebär att alla processorer kördes aktivt 100% av tiden under den senaste perioden.|
>| system. CPU. Usage | system. CPU. Usage | Procent | Senaste CPU-användning för hela systemet. Det här värdet är ett dubbelt i [0,0, 1,0]-intervallet. Värdet 0,0 innebär att alla processorer var inaktiva under den senaste tids perioden, medan värdet 1,0 innebär att alla processorer kördes aktivt 100% av tiden under den senaste perioden.|
>| Procent andel program CPU-användning (inaktuell)<br><br>(borttaget efter den 1 juli 2020) | Procent andel CPU-användning för app | Procent | Senaste CPU-användning för Java Virtual Machine processen. Det här värdet är ett dubbelt i [0,0, 1,0]-intervallet. Värdet 0,0 innebär att ingen av processorerna körde trådar från JVM-processen under den senaste tids perioden, medan värdet 1,0 innebär att alla processorer aktivt körde trådar från JVM 100% av tiden under den senaste perioden. Trådar från JVM omfattar både program trådar och JVM interna trådar.|
>| process. CPU. Usage | Procent andel CPU-användning för app | Procent | Senaste CPU-användning för Java Virtual Machine processen. Det här värdet är ett dubbelt i [0,0, 1,0]-intervallet. Värdet 0,0 innebär att ingen av processorerna körde trådar från JVM-processen under den senaste tids perioden, medan värdet 1,0 innebär att alla processorer aktivt körde trådar från JVM 100% av tiden under den senaste perioden. Trådar från JVM omfattar både program trådar och JVM interna trådar.|
>| Tilldelad app-minne (inaktuellt)<br><br>(som ska tas bort efter den 1 juli 2020) | JVM. Memory. dedikerat | Byte | Representerar mängden minne som garanterat kan användas av JVM. JVM kan frigöra minne till systemet och dedikerat kan vara mindre än init. bekräftat är alltid större än eller lika med används. |
>| JVM. Memory. dedikerat | JVM. Memory. dedikerat | Byte | Representerar mängden minne som garanterat kan användas av JVM. JVM kan frigöra minne till systemet och dedikerat kan vara mindre än init. bekräftat är alltid större än eller lika med används. |
>| Använt program minne (inaktuellt)<br><br>(som ska tas bort efter den 1 juli 2020) | JVM. Memory. används | Byte | Representerar mängden minne som för närvarande används i byte. |
>| JVM. Memory. används | JVM. Memory. används | Byte | Representerar mängden minne som för närvarande används i byte. |
>| App-minnets Max (inaktuellt)<br><br>(som ska tas bort efter den 1 juli 2020) | JVM. Memory. Max | Byte | Representerar den maximala mängd minne som kan användas för minnes hantering. Mängden använt och allokerat minne är alltid mindre än eller lika med max om max definieras. En minnesallokering kan Miss lyckas om det försöker öka det använda minnet som används > dedikerat, även om det används <= Max skulle fortfarande vara sant (till exempel när systemet har ont om virtuellt minne). |
>| JVM. Memory. Max | JVM. Memory. Max | Byte | Representerar den maximala mängd minne som kan användas för minnes hantering. Mängden använt och allokerat minne är alltid mindre än eller lika med max om max definieras. En minnesallokering kan Miss lyckas om det försöker öka det använda minnet som används > dedikerat, även om det används <= Max skulle fortfarande vara sant (till exempel när systemet har ont om virtuellt minne). |
>| Högsta tillgängliga storlek för gammal generations data (inaktuell)<br><br>(som ska tas bort efter den 1 juli 2020) | JVM. gc. max. data storlek | Byte | Högsta minnes användning för den gamla generationens lagringspool sedan den virtuella Java-datorn startades. |
>| JVM. gc. max. data storlek | JVM. gc. max. data storlek | Byte | Högsta minnes användning för den gamla generationens lagringspool sedan den virtuella Java-datorn startades. |
>| Gammal generations data storlek (inaktuell)<br><br>(som ska tas bort efter den 1 juli 2020) | JVM. gc. live. data. size | Byte | Storleken på den gamla generationens lagringspool efter en fullständig GC. |
>| JVM. gc. live. data. size | JVM. gc. live. data. size | Byte | Storleken på den gamla generationens lagringspool efter en fullständig GC. |
>| Gammal generations data storlek (inaktuell)<br><br>(som ska tas bort efter den 1 juli 2020) | JVM. gc. Memory. upphöjt | Byte | Antalet positiva ökningar i storleken på den gamla generationens minnesallokering innan GC till efter GC. |
>| JVM. gc. Memory. upphöjt | JVM. gc. Memory. upphöjt | Byte | Antalet positiva ökningar i storleken på den gamla generationens minnesallokering innan GC till efter GC. |
>| Höj data storleken för unga generationer (inaktuell)<br><br>(som ska tas bort efter den 1 juli 2020) | JVM. gc. Memory. allokerat | Byte | Ökas för en ökning av storleken på den unga generationens minnesbuffert efter en GC till innan nästa. |
>| JVM. gc. Memory. allokerat | JVM. gc. Memory. allokerat | Byte | Ökas för en ökning av storleken på den unga generationens minnesbuffert efter en GC till innan nästa. |
>| GC pausar antal (inaktuellt)<br><br>(som ska tas bort efter den 1 juli 2020) | JVM. gc. PAUSE (totalt antal) | Antal | Totalt antal GC efter att denna JMV har startats, inklusive unga och gamla GC. |
>| JVM. gc. Pause. total. Count | JVM. gc. PAUSE (totalt antal) | Antal | Totalt antal GC efter att denna JMV har startats, inklusive unga och gamla GC. |
>| GC PAUSE total tid (inaktuell)<br><br>(som ska tas bort efter den 1 juli 2020) | JVM. gc. PAUSE (total tid) | Millisekunder | Totalt antal GC-tid som förbrukas efter att denna JMV startades, inklusive unga och gamla GC. |
>| JVM. gc. Pause. total. Time | JVM. gc. PAUSE (total tid) | Millisekunder | Totalt antal GC-tid som förbrukas efter att denna JMV startades, inklusive unga och gamla GC. |

### <a name="request"></a>Förfrågan
>[!div class="mx-tdCol2BreakAll"]
>| Name | Mått namn för våren-motstånd | Enhet | Information |
>|----|----|----|------------|
>| Totalt antal skickade byte för Tomcat (inaktuellt)<br><br>(borttaget efter den 1 juli 2020) | tomcat. global. skickat | Byte | Mängden data Tomcat-webbserver som skickats |
>| tomcat. global. skickat | tomcat. global. skickat | Byte | Mängden data Tomcat-webbserver som skickats |
>| Totalt antal mottagna byte i Tomcat (inaktuellt)<br><br>(borttaget efter den 1 juli 2020) | tomcat. global. mottagen | Byte | Mängden data Tomcat-webb servern tog emot |
>| tomcat. global. mottagen | tomcat. global. mottagen | Byte | Mängden data Tomcat-webb servern tog emot |
>| Total tid för Tomcat-begäran (inaktuell)<br><br>(borttaget efter den 1 juli 2020) | tomcat. global. Request (total tid) | Millisekunder | Total tid för Tomcat-webbservern för behandling av begär Anden |
>| Totalt antal Tomcat-begäranden (inaktuellt)<br><br>(borttaget efter den 1 juli 2020) | tomcat. global. Request (totalt antal) | Antal | Totalt antal behandlade Tomcat webb server begär Anden |
>| tomcat. global. Request. total. Count | tomcat. global. Request (totalt antal) | Antal | Totalt antal behandlade Tomcat webb server begär Anden |
>| Maximal tid för Tomcat-begäran (inaktuell)<br><br>(borttaget efter den 1 juli 2020) | tomcat. global. Request. Max | Millisekunder | Maximal tid som Tomcat webb server kan bearbeta en begäran |
>| tomcat. global. Request. Max | tomcat. global. Request. Max | Millisekunder | Maximal tid som Tomcat webb server kan bearbeta en begäran |

### <a name="session"></a>Session
>[!div class="mx-tdCol2BreakAll"]
>| Name | Mått namn för våren-motstånd | Enhet | Information |
>|----|----|----|------------|
>| Högsta antal aktiva Tomcat-sessioner (inaktuella)<br><br>(borttaget efter den 1 juli 2020) | tomcat. sessions. Active. Max | Antal | Maximalt antal sessioner som har varit aktiva samtidigt |
>| tomcat. sessions. Active. Max | tomcat. sessions. Active. Max | Antal | Maximalt antal sessioner som har varit aktiva samtidigt |
>| Tomcat-session, maximal Alive-tid (inaktuell)<br><br>(borttaget efter den 1 juli 2020) | tomcat. sessions. Alive. Max | Millisekunder | Längsta tid (i sekunder) som en utgången session hade varit aktiv |
>| tomcat. sessions. Alive. Max | tomcat. sessions. Alive. Max | Millisekunder | Längsta tid (i sekunder) som en utgången session hade varit aktiv |
>| Antal skapade Tomcat-sessioner (inaktuellt)<br><br>(borttaget efter den 1 juli 2020) | tomcat. sessions. created | Antal | Antal sessioner som har skapats |
>| tomcat. sessions. created | tomcat. sessions. created | Antal | Antal sessioner som har skapats |
>| Antalet utgångna Tomcat session (inaktuellt)<br><br>(borttaget efter den 1 juli 2020) | tomcat. sessions. har gått ut | Antal | Antal sessioner som har upphört att gälla |
>| tomcat. sessions. har gått ut | tomcat. sessions. har gått ut | Antal | Antal sessioner som har upphört att gälla |
>| Antal avvisade Tomcat-sessioner (inaktuellt)<br><br>(borttaget efter den 1 juli 2020) | tomcat. sessions. avvisad | Antal | Antal sessioner som inte skapades eftersom det maximala antalet aktiva sessioner har uppnåtts. |
>| tomcat. sessions. avvisad | tomcat. sessions. avvisad | Antal | Antal sessioner som inte skapades eftersom det maximala antalet aktiva sessioner har uppnåtts. |
>| tomcat. sessions. Active. Current | tomcat. sessions. Active. Current | Antal | Aktivt antal Tomcat-sessioner |

## <a name="see-also"></a>Se även
* [Komma igång med Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analysera loggar och mått med diagnostikinställningar](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Nästa steg
* [Självstudie: övervaka våren moln resurser med hjälp av aviseringar och åtgärds grupper](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Kvoter och service planer för Azure våren Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

