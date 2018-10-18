---
title: Så här diagrammets prestanda med Azure Monitor för virtuella datorer | Microsoft Docs
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
ms.date: 10/16/2018
ms.author: magoedte
ms.openlocfilehash: 63549768f616e60e92c853047525c18cefdaddb4
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386288"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Hur du diagrammet prestanda med Azure Monitor för virtuella datorer
Azure Monitor för virtuella datorer innehåller en uppsättning prestandadiagram som är inriktade på flera nyckeltal (KPI: er) för att bestämma hur bra en virtuell dator fungerar. Diagrammen visar Resursanvändning under en viss tid så att du kan identifiera flaskhalsar, avvikelser, eller växla till ett perspektiv som lista varje dator om du vill visa resursanvändningen baserat på mått som har valts. Det finns ett stort antal element att överväga när du hanterar prestanda, fokuserar Azure Monitor för virtuella datorer på operativsystemet så som detta visas via processor, minne, nätverkskort och diskar. Prestanda kompletterar hälsotillstånd övervakningsfunktionen och hjälper till att exponera problem som indikerar ett möjligt system komponentfel, support justering och optimering för att uppnå effektivitet eller stöd för kapacitetsplanering.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Multi-VM perspektiv från Azure Monitor
Från Azure Monitor ger funktionen prestanda en vy för flera virtuella datorer för alla övervakade virtuella datorer distribuerade över resursgrupper i dina prenumerationer eller i din miljö.  Om du vill komma åt från Azure Monitor kan du utföra följande. 

1. I Azure-portalen väljer du **övervakaren**. 
2. Välj **virtuella datorer (förhandsversion)** i den **lösningar** avsnittet.
3. Välj den **prestanda** fliken.

![VM insikter prestanda Top N listvy](./media/monitoring-vminsights-performance/vminsights-performance-aggview-01.png)

På den **Top N diagram** fliken om du har mer än en Log Analytics-arbetsyta, väljer arbetsytan aktiverad med lösningen från den **arbetsytan** Väljaren överst på sidan. Den **grupp** väljare returnerar prenumerationer, resursgrupper, [datorgrupper](../log-analytics/log-analytics-computer-groups.md), och VM scale sets med datorer som är relaterade till den valda arbetsytan som du kan använda för att ytterligare filtrera resultaten i diagrammen på den här sidan och andra sidor. Ditt val har endast gäller för funktionen prestanda och inget hälsotillstånd eller kartan.  

Som standard visar i diagrammen de senaste 24 timmarna. Med hjälp av den **TimeRange** Väljaren, som du kan fråga efter historiska tidsintervall på upp till 30 dagar att visa hur prestanda tittat i förflutna.   

De fem kapacitet användning diagrammen som visas på sidan är:

* Processorförbrukning i % – visar de 5 främsta datorerna med den högsta genomsnittliga processoranvändningen 
* Tillgängligt minne – visar de översta 5 datorerna med den lägsta genomsnittliga mängden tillgängligt minne 
* Logisk diskutrymme används % - visar de översta 5 datorerna med det högsta genomsnittliga diskutrymmet används % över alla volymer på diskar 
* Byte visar skickade – de 5 främsta datorerna med högsta medelvärde för byte som skickats 
* Byte visar Receive - de 5 främsta datorerna med högsta medelvärde för byte som skickats 

När du klickar på det övre högra hörnet i någon av de fem diagram öppnas **topp-N** vy.  Här kan du se resursanvändningen för den prestandamått av enskild virtuell dator i en listvy och vilken dator trendar högsta.  

![Övre N listvyn för en vald prestandamått](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

När du klickar på den virtuella datorn, den **egenskaper** fönstret utökas till höger för att visa egenskaperna för objekt som har valts, till exempel systeminformation som rapporteras av operativsystemet, egenskaper för virtuell Azure-dator, osv. När du klickar på något av alternativen under den **snabblänkar** avsnittet omdirigeras du till funktionen direkt från den valda virtuella datorn.  

![Egenskapsrutan för virtuell dator](./media/monitoring-vminsights-performance/vminsights-properties-pane-01.png)

Växla till den **aggregeras diagram** fliken för att visa prestandavärden filtreras efter medelvärde eller percentilerna mått.  

![Sammanställda prestanda vy för VM-insikter](./media/monitoring-vminsights-performance/vminsights-performance-aggview-02.png)

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

![Övre N sidan exempel](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

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

![VM insikter prestanda direkt visa från virtuella datorn](./media/monitoring-vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="alerting-and-alert-management"></a>Hantering av varningar och aviseringar 
Prestandamått aktiverad som en del av Azure Monitor för virtuella datorer inkluderar inte förkonfigurerade Varningsregler. Det finns hälsovarningar för prestandaproblem som upptäckts på din Azure-VM, låg, till exempel hög processoranvändning, lågt minne, diskutrymme osv, dessa hälsovarningar tillämpas endast på de virtuella datorerna är anslutna till samma Log Analytics-arbetsytan integrerad med Azure Monitor för virtuella datorer. Om du behöver flexibiliteten att ange dina egna villkor eller logic kan du skapa anpassade aviseringsregler genom att följa [skapa, visa och hantera aviseringar med hjälp av Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Nästa steg
Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer Health](monitoring-vminsights-health.md), eller om du vill visa identifierade programberoenden, se [visa Azure Monitor för virtuella datorer kartan](monitoring-vminsights-maps.md). 