---
title: Förstå mått för Azure Spring Cloud
description: Lär dig hur du granskar mått i Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256762"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Förstå mått för Azure Spring Cloud

Azure Metrics Explorer är en komponent i Microsoft Azure-portalen som gör det möjligt att rita diagram, visuellt korrelera trender och undersöka toppar och dips i mått. Använd statistikutforskaren för att undersöka hälso- och användning av dina resurser. 

I Azure Spring Cloud finns det två vypunkter för mått.
* Diagram på varje programöversiktssida
* Sidan Vanliga mått

 ![Diagram över mått](media/metrics/metrics-1.png)

Diagram i **programöversikten** ger snabba statuskontroller för varje program. Den gemensamma **måttsidan** innehåller alla tillgängliga mått som referens. Du kan skapa egna diagram på sidan gemensamma mått och fästa dem i instrumentpanelen.

## <a name="application-overview-page"></a>Översiktssida för program
Välj en app i **AppHantering** för att hitta diagram på översiktssidan.  

 ![Hantering av programmått](media/metrics/metrics-2.png)

Varje programs **sida för programöversikt** visar ett måttdiagram som gör att du kan utföra en snabb statuskontroll av ditt program.  

 ![Översikt över programmått](media/metrics/metrics-3.png)

Azure Spring Cloud tillhandahåller dessa fem diagram med mått som uppdateras varje minut:

* **Http Server-fel:** Fel antal HTTP-begäranden till din app
* **Data i**: Byte som tas emot av din app
* **Data Out**: Byte som skickas av din app
* **Förfrågningar**: Förfrågningar som tas emot av din app
* **Genomsnittlig svarstid:** Genomsnittlig svarstid från din app

För diagrammet kan du välja ett tidsintervall från en timme till sju dagar.

## <a name="common-metrics-page"></a>Sidan Vanliga mått

Måtten i det vänstra **navigeringsfönstret** länkar till den gemensamma måttsidan.

Välj först mått som ska visas:

![Välj måttvy](media/metrics/metrics-4.png)

Information om alla mått alternativ finns i [avsnittet](#user-metrics-options) nedan.

Välj sedan aggregeringstyp för varje mått:

![Metrisk aggregering](media/metrics/metrics-5.png)

Aggregeringstypen anger hur du aggregerar måttpunkter i diagrammet efter tid. Det finns en rå måttpunkt varje minut och pre-aggregeringstypen inom några minuter är fördefinierad av måtttyp.
* Summa: Summera alla värden som målutdata.
* Medel: Använd medelvärdet i perioden som målutdata.
* Max/Min: Använd max/min-värdet i perioden som målutdata.

Tidsintervall som ska visas kan också ändras. Tidsintervallet kan väljas från de senaste 30 minuterna till de senaste 30 dagarna, eller ett anpassat tidsintervall.

![Måttändring](media/metrics/metrics-6.png)

Standardvyn innehåller alla azure Spring Cloud-tjänstens programs mått tillsammans. Mått för en app eller instans kan filtreras i displayen.  Klicka på **Lägg till filter,** ange egenskapen till **App**och välj det målprogram som du vill övervaka i textrutan **Värden.** 

Du kan använda två typer av filter (egenskaper):
* App: filtrera efter appnamn
* Instans: filtrera efter appinstans

![Måttfilter](media/metrics/metrics-7.png)

Du kan också använda alternativet **Använd delning,** som ritar flera rader för en app:

![Delning av metriskt](media/metrics/metrics-8.png)

>[!TIP]
> Du kan skapa egna diagram på sidan mått och fästa dem på **instrumentpanelen**. Börja med att namnge diagrammet.  Välj sedan **Fäst på instrumentpanelen i det övre högra hörnet**. Du kan nu kontrollera ditt program på **portalpanelen**.

## <a name="user-metrics-options"></a>Alternativ för användarmått

Följande tabeller visar tillgängliga mått och information.

### <a name="error"></a>Fel
>[!div class="mx-tdCol2BreakAll"]
>| Namn | Fjäder ställdon Metriska namn | Enhet | Information |
>|----|----|----|------------|
>| Tomcat globalt fel | tomcat.global.error | Antal | Antal fel som inträffar för bearbetade begäranden |

### <a name="performance"></a>Prestanda
>[!div class="mx-tdCol2BreakAll"]
>| Namn | Fjäder ställdon Metriska namn | Enhet | Information |
>|----|----|----|------------|
>|Procentandel av användningen av system-PROCESSOR | system.cpu.usage | Procent | Senaste CPU-användning för hela systemet. Det här värdet är en dubbel i intervallet [0,0,1.0]. Ett värde på 0,0 innebär att alla processorer var inaktiva under den senaste tidsperioden, medan ett värde på 1,0 innebär att alla processorer aktivt körde 100% av tiden under den senaste perioden som observerats.|
>| Procentandel av appens CPU-användning | Procentandel av appens CPU-användning | Procent | Senaste CPU-användning för Java Virtual Machine-processen. Det här värdet är en dubbel i intervallet [0,0,1.0]. Ett värde på 0,0 innebär att ingen av processorerna körde trådar från JVM-processen under den senaste tidsperioden, medan ett värde på 1,0 innebär att alla processorer aktivt körde trådar från JVM 100% av tiden under den senaste perioden som observerats. Trådar från JVM inkluderar applikationstrådarna samt JVM-interna trådar.|
>| Appminne tilldelas | jvm.memory.committed | Byte | Representerar mängden minne som garanterat är tillgängligt för användning av JVM. JVM kan frigöra minne till systemet och begått kan vara mindre än init. kommer alltid att vara större än eller lika med används. |
>| Appminne används | jvm.memory.used | Byte | Representerar den mängd minne som för närvarande används i byte. |
>| Max för appminne | jvm.memory.max | Byte | Representerar den maximala mängden minne som kan användas för minneshantering. Mängden använt och engagerat minne kommer alltid att vara mindre än eller lika med max om max definieras. En minnesallokering kan misslyckas om den försöker öka det använda minnet så att det används > som används även om det används <= max fortfarande skulle vara sant (till exempel när systemet har ont om virtuellt minne). |
>| Max tillgänglig datastorlek för gammal generation | jvm.gc.max.data.size jvm.gc.max.data.size jvm.gc.max.data.size jv | Byte | Den högsta minnesanvändningen för den gamla generationens minnespool sedan den virtuella Java-datorn startades. |
>| Datastorlek för gammal generering | jvm.gc.live.data.size | Byte | Storleken på den gamla generationens minnespool efter en fullständig GC. |
>| Befordra till datastorlek för gammal generation | jvm.gc.memory.promoted | Byte | Antal positiva ökningar i storleken på den gamla generationens minnespool före GC till efter GC. |
>| Främja till young generation datastorlek | jvm.gc.memory.allocated jvm.gc.memory.allocated jvm.gc.memory.allocated jv | Byte | Stegas för en ökning av storleken på den unga generationens minnespool efter en GC till före nästa. |
>| Antal GC-pauser | jvm.gc.pause (totalt antal) | Antal | Totalt antal GC efter detta JMV startade, inklusive Young och Old GC. |
>| GC Paus total tid | jvm.gc.pause (total tid) | Millisekunder | Totalt GC tid förbrukas efter detta JMV startade, inklusive Unga och Gamla GC. |

### <a name="request"></a>Förfrågan
>[!div class="mx-tdCol2BreakAll"]
>| Namn | Fjäder ställdon Metriska namn | Enhet | Information |
>|----|----|----|------------|
>| Tomcat Totalt antal skickade byte | tomcat.global.sent | Byte | Mängden data Tomcat webbserver skickas |
>| Tomcat totalt mottagna byte | tomcat.global.received | Byte | Mängden data tomcat webbserver emot |
>| Tomcat begäran total tid | tomcat.global.request (total tid) | Millisekunder | Total tid för Tomcat webbserver för att bearbeta begäranden |
>| Totalt antal antal för Tomcat-begäran | tomcat.global.request (totalt antal) | Antal | Totalt antal tomcat-webbserverbehandlade begäranden |
>| Tomcat begäran max tid | tomcat.global.request.max | Millisekunder | Maximal tid för Tomcat-webbservern att bearbeta en begäran |

### <a name="session"></a>Session
>[!div class="mx-tdCol2BreakAll"]
>| Namn | Fjäder ställdon Metriska namn | Enhet | Information |
>|----|----|----|------------|
>| Tomcat-session Max aktivt antal | tomcat.sessions.active.max | Antal | Maximalt antal sessioner som har varit aktiva samtidigt |
>| Tomcat Session Max Alive Tid | tomcat.sessions.alive.max | Millisekunder | Längsta tid (i sekunder) som en session som har gått ut hade varit vid liv |
>| Tomcat-session skapad räkning | tomcat.sessions.created | Antal | Antal sessioner som har skapats |
>| Tomcat-session har upphört att gälla | tomcat.sessions.expired | Antal | Antal sessioner som har upphört att gälla |
>| Tomcat-session avvisad räkning | tomcat.sessions.rejected | Antal | Antal sessioner som inte har skapats eftersom det maximala antalet aktiva sessioner har nåtts. |

## <a name="see-also"></a>Se även
* [Komma igång med Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analysera loggar och mått med diagnostikinställningar](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Nästa steg
* [Självstudiekurs: Övervaka Spring Cloud-resurser med aviseringar och åtgärdsgrupper](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Kvoter och serviceplaner för Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

