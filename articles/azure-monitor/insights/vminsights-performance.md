---
title: Så här diagramerar du prestanda med Azure Monitor for VMs (förhands granskning) | Microsoft Docs
description: Prestanda är en funktion i Azure Monitor for VMs som automatiskt identifierar program komponenter i Windows-och Linux-system och mappar kommunikationen mellan tjänsterna. Den här artikeln innehåller information om hur du använder den i flera olika scenarier.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 245d5c0fb0a54a6d129a193deaa9445bc8fefbfb
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670703"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms-preview"></a>Så här diagramerar du prestanda med Azure Monitor for VMs (för hands version)

Azure Monitor for VMs innehåller en uppsättning prestanda diagram som riktar sig mot flera nyckeltal (KPI: er) för att hjälpa dig att avgöra hur väl en virtuell dator fungerar. Diagrammen visar resursutnyttjande under en viss tids period, så att du kan identifiera Flask halsar, avvikelser eller växla till ett perspektiv som visar varje dator för att Visa resursutnyttjande baserat på det mått som valts. Även om det finns många element som ska övervägas vid hantering av prestanda, Azure Monitor for VMs övervaka viktiga prestanda indikatorer för operativ systemet som rör processor, minne, nätverkskort och disk användning. Prestanda kompletterar hälso övervaknings funktionen och hjälper till att exponera problem som indikerar ett möjligt fel i system komponenten, stöder justering och optimering för att uppnå effektivitet eller stöd för kapacitets planering.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspektiv för flera virtuella datorer från Azure Monitor

Från Azure Monitor tillhandahåller prestanda funktionen en vy över alla övervakade virtuella datorer som distribuerats mellan arbets grupper i dina prenumerationer eller i din miljö. Utför följande steg för att komma åt från Azure Monitor. 

1. I Azure Portal väljer du **övervaka**. 
2. Välj **Virtual Machines (för hands version)** i avsnittet **lösningar** .
3. Välj fliken **prestanda** .

![Prestanda för VM Insights Top N List](./media/vminsights-performance/vminsights-performance-aggview-01.png)

Välj den arbets yta som är aktive rad med lösningen från **arbets ytans** väljare överst på sidan om du har fler än en Log Analytics arbets yta på fliken **x översta diagram** . **Grupp** väljaren returnerar prenumerationer, resurs grupper, [dator grupper](../platform/computer-groups.md)och virtuella datorers skalnings uppsättningar för datorer som är relaterade till den valda arbets ytan som du kan använda för att ytterligare filtrera resultat som visas i diagrammen på den här sidan och på de andra sidorna. Ditt val gäller bara för prestanda funktionen och överförs inte över till hälsan eller kartan.  

Som standard visar diagrammen de senaste 24 timmarna. Med hjälp av **TimeRange** -Väljaren kan du fråga efter historiska tidsintervaller på upp till 30 dagar för att visa hur prestanda som har visats tidigare.

De fem kapacitets användnings diagram som visas på sidan är:

* PROCESSOR användning% – visar de fem främsta datorerna med den högsta genomsnittliga processor användningen 
* Tillgängligt minne – visar de fem främsta datorerna med den lägsta genomsnittliga mängden tillgängligt minne 
* Använt logiskt disk utrymme% – visar de fem främsta datorerna med det högsta genomsnittliga disk utrymme som använts% på alla disk volymer 
* Antal skickade byte – visar de fem främsta datorerna med högst genomsnitt byte skickade 
* Antal mottagna byte – visar de fem främsta datorerna med högst genomsnitt mottagna byte 

Om du klickar på ikonen fäst i det övre högra hörnet i något av de fem diagrammen fästs det valda diagrammet på den sista Azure-instrumentpanelen som du senast visade.  På instrument panelen kan du ändra storlek på och flytta diagrammet. Genom att välja diagrammet på instrument panelen kommer du att omdirigera dig till Azure Monitor for VMs och läsa in rätt omfång och vy.  

Om du klickar på ikonen som finns till vänster om PIN-ikonen i något av de fem diagrammen öppnas den **översta List** visningen.  Här kan du se resursutnyttjande för den prestanda måtten för enskilda virtuella datorer i en listvy och vilken dator som är mest trendad.  

![Vyn överst N för ett valt prestanda mått](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

När du klickar på den virtuella datorn expanderas fönstret **Egenskaper** till höger för att visa egenskaperna för det valda objektet, till exempel system information som rapporteras av operativ systemet, egenskaperna för den virtuella Azure-datorn osv. Om du klickar på ett av alternativen under avsnittet **snabb länkar** omdirigeras du till funktionen direkt från den valda virtuella datorn.  

![Egenskaps fönstret för virtuell dator](./media/vminsights-performance/vminsights-properties-pane-01.png)

Växla till fliken **aggregerade diagram** för att visa de prestanda mått som filtrerats efter medelvärde eller percentiler.  

![Sammanställd vy för prestanda för VM Insights](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Följande diagram för kapacitets användning har angetts:

* PROCESSOR användning%-standardvärden som visar genomsnitts-och topp 95-percentilen 
* Tillgängligt minne – standard visar genomsnitt, 5 främsta och tionde percentilen 
* Använt logiskt disk utrymme%-standard visar genomsnitts-och 95-percentilen 
* Skickade byte hast-standard visar Genomsnittligt antal byte som skickats 
* Antal mottagna byte-meddelanden: standard visar Genomsnittligt antal mottagna byte

Du kan också ändra granularitet för diagrammen inom tidsintervallet genom att välja **AVG**, **min**, **Max**, **50**, **nittionde**och **95** i percentils väljaren.

Om du vill visa resursutnyttjande för enskilda virtuella datorer i en listvy, och se vilken dator som är trendad med högsta användning, väljer du fliken **överst N List** .  Sidan **topp N lista** visar de 20 främsta datorerna sorterade efter den mest använda 95 percentilen för måttet *CPU-användning*.  Du kan se fler datorer genom att välja **Läs in mer**och resultaten expanderas för att visa de översta 500 datorerna. 

>[!NOTE]
>Listan kan inte Visa mer än 500 datorer i taget.  
>

![Exempel på översta N List Sidan](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Om du vill filtrera resultaten på en enskild virtuell dator i listan anger du dess dator namn i text rutan **Sök efter namn** .  

Om du hellre vill visa användning från ett annat prestanda mått går du till List rutan **mått** och väljer **tillgängligt minne**, **logiskt disk utrymme som används%** , **mottagna byte/s i nätverket**eller **skickade byte/s** och listan uppdateras för att Visa användnings område för det måttet.  

Om du väljer en virtuell dator i listan öppnas panelen **Egenskaper** på sidans högra sida och härifrån kan du välja **prestanda information**.  Sidan **information om virtuell dator** öppnas och är begränsad till den virtuella datorn, ungefär som i erfarenhet när du får åtkomst till VM Insights-prestanda direkt från den virtuella Azure-datorn.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Visa prestanda direkt från en virtuell Azure-dator

Utför följande steg för att komma åt direkt från en virtuell dator.

1. I Azure Portal väljer du **Virtual Machines**. 
2. I listan väljer du en virtuell dator och i avsnittet **övervakning** väljer du **insikter (för hands version)** .  
3. Välj fliken **prestanda** . 

På den här sidan ingår inte bara prestanda användnings diagram, utan även en tabell som visar för varje logisk disk som identifieras, dess kapacitet, användning och total genomsnitt per mått.  

Följande diagram för kapacitets användning har angetts:

* PROCESSOR användning%-standardvärden som visar genomsnitts-och topp 95-percentilen 
* Tillgängligt minne – standard visar genomsnitt, 5 främsta och tionde percentilen 
* Använt logiskt disk utrymme%-standard visar genomsnitts-och 95-percentilen 
* Logisk disk IOPS-standardvärden visar genomsnitts-och 95 percentilen
* Logisk disk MB/s-standardvärden som visar genomsnitts-och 95 percentilen
* Maximal logisk disk som används%-standard visar genomsnitts-och 95-percentilen
* Skickade byte hast-standard visar Genomsnittligt antal byte som skickats 
* Antal mottagna byte-meddelanden: standard visar Genomsnittligt antal mottagna byte

Om du klickar på ikonen fäst i det övre högra hörnet i något av diagrammen, går det markerade diagrammet till den sista Azure-instrumentpanelen som du visade. På instrument panelen kan du ändra storlek på och flytta diagrammet. Genom att välja diagrammet på instrument panelen omdirigeras du till Azure Monitor for VMs och läser in vyn prestanda information för den virtuella datorn.  

![Prestanda för VM Insights direkt från vyn VM](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Visa prestanda direkt från en skalnings uppsättning för virtuella Azure-datorer

Utför följande steg för att komma åt direkt från en skalnings uppsättning för virtuella Azure-datorer.

1. I Azure Portal väljer du **skalnings uppsättningar för virtuella datorer**.
2. I listan väljer du en virtuell dator och i avsnittet **övervakning** väljer du **insikter (för hands version)** för att visa fliken **prestanda** .

Den här sidan läser in vyn Azure Monitor prestanda som är begränsad till den valda skalnings uppsättningen. På så sätt kan du se de x främsta instanserna i skalnings uppsättningen över de övervakade måtten, Visa den sammanställda prestandan i skalnings uppsättningen och se trender för valda mått i de enskilda instanserna N skalnings uppsättningen. Genom att välja en instans i listvyn kan du läsa in dess karta eller navigera till en detaljerad prestanda vy för den instansen.

Om du klickar på ikonen fäst i det övre högra hörnet i något av diagrammen, går det markerade diagrammet till den sista Azure-instrumentpanelen som du visade. På instrument panelen kan du ändra storlek på och flytta diagrammet. Genom att välja diagrammet på instrument panelen omdirigeras du till Azure Monitor for VMs och läser in vyn prestanda information för den virtuella datorn.  

![Prestanda för VM Insights direkt från den virtuella datorns skal uppsättnings vy](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Du kan också få åtkomst till en detaljerad prestanda vy för en angiven instans från vyn instanser för din skalnings uppsättning. Navigera till **instanserna** under avsnittet **Inställningar** och välj sedan **insikter (för hands version)** .

## <a name="alerts"></a>Aviseringar  

Prestanda mått som aktive ras som en del av Azure Monitor for VMs inte innehåller förkonfigurerade aviserings regler. Det finns [hälso aviseringar](vminsights-health.md#alerts) som motsvarar prestanda problem som upptäckts på din virtuella Azure-dator, till exempel hög processor användning, lite tillgängligt minne, ont om disk utrymme osv.  Dessa hälso aviseringar gäller dock endast för alla virtuella datorer som är aktiverade för Azure Monitor for VMs. 

Vi kan dock bara samla in och lagra en delmängd av de prestanda mått du behöver i Log Analytics arbets ytan. Om övervaknings strategin kräver analys eller aviseringar som innehåller andra prestanda mått för att effektivt utvärdera kapaciteten eller hälsan hos den virtuella datorn, eller om du behöver flexibiliteten att ange egna aviserings kriterier eller logik, kan du konfigurera [insamling av dessa prestanda räknare](../platform/data-sources-performance-counters.md) i Log Analytics och definiera [logg aviseringar](../platform/alerts-log.md). Samtidigt som Log Analytics kan du utföra komplexa analyser med andra data typer och ge längre kvarhållning för att stödja trend analys, men måtten å andra sidan, är lätta och kan stödja i nära real tids scenarier. De samlas in av [Azure Diagnostics-agenten](../../virtual-machines/windows/monitor.md) och lagras i Azure Monitor Metrics Store, så att du kan skapa aviseringar med lägre latens och till en lägre kostnad.

Granska översikten över [insamling av mått och loggar med Azure Monitor](../platform/data-platform.md) för att ytterligare förstå de grundläggande skillnaderna och andra överväganden innan du konfigurerar insamling av dessa ytterligare mått och varnings regler.  

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [arbets böcker](vminsights-workbooks.md) som ingår i Azure Monitor for VMS för att ytterligare analysera prestanda-och nätverks mått.  

- Information om identifierade program beroenden finns i [visa Azure Monitor for VMS karta](vminsights-maps.md).
