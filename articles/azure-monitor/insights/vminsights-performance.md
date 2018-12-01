---
title: Så här diagrammets prestanda med Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Prestanda är en funktion i Azure Monitor för virtuella datorer som automatiskt identifierar programkomponenter i Windows och Linux-system och mappar kommunikationen mellan olika tjänster. Den här artikeln innehåller information om hur du använder det på en mängd olika scenarier.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: cfee8295fc344b1210010a463612fe7d10de280d
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682919"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms-preview"></a>Hur du diagrammet prestanda med Azure Monitor för virtuella datorer (förhandsversion)
Azure Monitor för virtuella datorer innehåller en uppsättning prestandadiagram som är inriktade på flera nyckeltal (KPI: er) för att bestämma hur bra en virtuell dator fungerar. Diagrammen visar Resursanvändning under en viss tid så att du kan identifiera flaskhalsar, avvikelser, eller växla till ett perspektiv som lista varje dator om du vill visa resursanvändningen baserat på mått som har valts. Det finns ett stort antal element att överväga när du hanterar prestanda, fokuserar Azure Monitor för virtuella datorer på operativsystemet så som detta visas via processor, minne, nätverkskort och diskar. Prestanda kompletterar hälsotillstånd övervakningsfunktionen och hjälper till att exponera problem som indikerar ett möjligt system komponentfel, support justering och optimering för att uppnå effektivitet eller stöd för kapacitetsplanering.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Multi-VM perspektiv från Azure Monitor
Från Azure Monitor ger funktionen prestanda en vy för flera virtuella datorer för alla övervakade virtuella datorer distribuerade över resursgrupper i dina prenumerationer eller i din miljö.  Utför följande steg för att komma åt från Azure Monitor. 

1. I Azure-portalen väljer du **övervakaren**. 
2. Välj **virtuella datorer (förhandsversion)** i den **lösningar** avsnittet.
3. Välj den **prestanda** fliken.

![VM insikter prestanda Top N listvy](./media/vminsights-performance/vminsights-performance-aggview-01.png)

På den **Top N diagram** fliken om du har mer än en Log Analytics-arbetsyta, väljer arbetsytan aktiverad med lösningen från den **arbetsytan** Väljaren överst på sidan. Den **grupp** väljare returnerar prenumerationer, resursgrupper, [datorgrupper](../../azure-monitor/platform/computer-groups.md), och VM scale sets med datorer som är relaterade till den valda arbetsytan som du kan använda för att ytterligare filtrera resultat som visas i diagrammen på den här sidan och andra sidor. Ditt val har endast gäller för funktionen prestanda och inget hälsotillstånd eller kartan.  

Som standard visar i diagrammen de senaste 24 timmarna. Med hjälp av den **TimeRange** Väljaren, som du kan fråga efter historiska tidsintervall på upp till 30 dagar att visa hur prestanda tittat i förflutna.   

De fem kapacitet användning diagrammen som visas på sidan är:

* Processorförbrukning i % – visar de översta fem datorerna med den högsta genomsnittliga processoranvändningen 
* Tillgängligt minne – visar de översta fem datorerna med den lägsta genomsnittliga mängden tillgängligt minne 
* Logisk diskutrymme används % - visar de översta fem datorerna med det högsta genomsnittliga diskutrymmet används % över alla volymer på diskar 
* Byte visar skickade – de fem främsta datorerna med högsta medelvärde för byte som skickats 
* Byte visar Receive - de fem främsta datorerna med högsta medelvärde för byte som skickats 

När du klickar på det övre högra hörnet i någon av de fem diagram öppnas **topp-N** vy.  Här kan du se resursanvändningen för den prestandamått av enskild virtuell dator i en listvy och vilken dator trendar högsta.  

![Övre N listvyn för en vald prestandamått](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

När du klickar på den virtuella datorn, den **egenskaper** fönstret utökas till höger för att visa egenskaperna för objekt som har valts, till exempel systeminformation som rapporteras av operativsystemet, egenskaper för virtuell Azure-dator, osv. När du klickar på något av alternativen under den **snabblänkar** avsnittet omdirigeras du till funktionen direkt från den valda virtuella datorn.  

![Egenskapsrutan för virtuell dator](./media/vminsights-performance/vminsights-properties-pane-01.png)

Växla till den **aggregeras diagram** fliken för att visa prestandavärden filtreras efter medelvärde eller percentilerna mått.  

![Sammanställda prestanda vy för VM-insikter](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Följande kapacitet användning diagrammen tillhandahålls:

* Processorförbrukning i % - standard som visar den genomsnittliga och högsta 95: e percentilen 
* Tillgängligt minne - standardinställningar som visar den genomsnittliga, översta 5 och 10: e percentilen 
* Logisk diskutrymme används % - standardinställningar som visar den genomsnittliga och den 95: e percentilen 
* Byte skickade Rate – standardvärden som visar Genomsnittligt antal byte som skickats 
* Byte Receive Rate – standardvärden som visar Genomsnittligt antal byte som tagits emot

Du kan också ändra Granulariteten för diagram inom tidsintervallet genom att välja **genomsnittlig**, **Min**, **Max**, **50**,  **90**, och **95: e** i Väljaren: e percentilen.   

För att visa resursanvändningen av en enskild virtuell dator i en listvy och se vilka datorn trendar med största användning, Välj den **topp-N** fliken.  Den **topp-N** sidan visas de översta 20 datorerna sorterade efter den mest utnyttjad av 95: e percentilen för måttet *processoranvändning %*.  Du kan se flera datorer genom att välja **belastningen mer**, och resultaten utökas för att visa de översta 500 datorerna. 

>[!NOTE]
>Listan kan inte visa högst 500 datorer i taget.  
>

![Övre N sidan exempel](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Om du vill filtrera resultatet för en specifik virtuell dator i listan, anger du dess datornamn i den **Sök efter namn** textrutan.  

Om du i stället skulle visa användning från ett mått med olika prestanda, från den **mått** listrutan Välj **tillgängligt minne**, **logisk diskutrymme används %**,  **Network mottagna Byte/s**, eller **nätverk skickade Byte/s** och listan uppdateras för att visa användning tillhöra det måttet.  

Om du väljer en virtuell dator i listan öppnas den **egenskaper** panelen till höger på sidan och härifrån kan du välja **prestandainformation**.  Den **information om virtuella datorer** öppnas och är begränsad till den virtuella datorn, ungefär som i upplevelse vid åtkomst till Insights-prestanda för virtuella datorer direkt från Azure-VM.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Visa prestanda direkt från en Azure-dator
Om du vill komma åt direkt från en virtuell dator måste du utföra följande.

1. I Azure-portalen väljer du **virtuella datorer**. 
2. I listan, väljer en virtuell dator och i den **övervakning** i avsnittet **Insights (förhandsversion)**.  
3. Välj den **prestanda** fliken. 

Den här sidan inte bara innehåller prestandadiagram för användning, men också en tabell som visar för varje logisk disk kan identifieras, sin kapacitet, användning, och total genomsnittlig per varje mått.  

Följande kapacitet användning diagrammen tillhandahålls:

* Processorförbrukning i % - standard som visar den genomsnittliga och högsta 95: e percentilen 
* Tillgängligt minne - standardinställningar som visar den genomsnittliga, översta 5 och 10: e percentilen 
* Logisk diskutrymme används % - standardinställningar som visar den genomsnittliga och den 95: e percentilen 
* Logisk Disk IOPS - standardinställningar som visar den genomsnittliga och den 95: e percentilen
* Logisk Disk MB/s - standard som visar den genomsnittliga och den 95: e percentilen
* Maximalt antal logiska disken används % - standardinställningar som visar den genomsnittliga och den 95: e percentilen
* Byte skickade Rate – standardvärden som visar Genomsnittligt antal byte som skickats 
* Byte Receive Rate – standardvärden som visar Genomsnittligt antal byte som tagits emot

![VM insikter prestanda direkt visa från virtuella datorn](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="alerting-and-alert-management"></a>Hantering av varningar och aviseringar 
Prestandamått aktiverad som en del av Azure Monitor för virtuella datorer inkluderar inte förkonfigurerade Varningsregler. Det finns [hälsovarningar](vminsights-health.md#alerting-and-alert-management) motsvarar prestandaproblem som upptäckts på din Azure-dator, till exempel hög processoranvändning, lågt minne, diskens i/o, ont om ledigt diskutrymme osv., dessa hälsovarningar tillämpas endast på alla virtuella datorer ansluten till samma Log Analytics-arbetsytan aktiverad för Azure Monitor för virtuella datorer. 

Vi kan endast samla in och lagra en delmängd av prestandamått som du behöver i Log Analytics-arbetsytan. Om din övervakningsstrategi kräver analys eller aviseringar som innehåller andra prestandamått för att effektivt utvärdera kapacitet eller hälsotillståndet för den virtuella datorn eller om du behöver flexibiliteten att ange egna avisering kriterier eller logik, kan du Konfigurera [insamling av dessa prestandaräknare](../../azure-monitor/platform/data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json) i Log Analytics och definiera [loggaviseringar](../../monitoring-and-diagnostics/alert-log.md?toc=/azure/azure-monitor/toc.json). Med Log Analytics kan du utföra komplexa analyser med andra datatyper och ange längre kvarhållning för att stödja trendanalys, mått å andra sidan, är enkel och kan stödja scenarier i nästan realtid. De samlas in av den [Azure-diagnostikagenten](../../virtual-machines/windows/monitor.md) och lagras i Azure Monitor metrics store, så att du kan skapa aviseringar med kortare svarstider och till en lägre kostnad.

Granska en översikt över [uppsättning mått och loggar med Azure Monitor](../../azure-monitor/platform/data-collection.md?toc=/azure/azure-monitor/toc.json) att ytterligare förstå viktiga skillnader och annat att tänka på innan du konfigurerar insamling av dessa ytterligare mått och Varningsregler.  

## <a name="next-steps"></a>Nästa steg
Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer Health](vminsights-health.md), eller om du vill visa identifierade programberoenden, se [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md). 