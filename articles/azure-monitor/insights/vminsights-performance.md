---
title: Diagramprestanda med Azure Monitor för virtuella datorer
description: Prestanda är en funktion i Azure Monitor för virtuella datorer som automatiskt identifierar programkomponenter på Windows- och Linux-system och mappar kommunikationen mellan tjänster. Den här artikeln innehåller information om hur du använder den i en mängd olika scenarier.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: a50ba39777e6a9d3d609e584c0c7d872f2a65f35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283726"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Diagramprestanda med Azure Monitor för virtuella datorer

Azure Monitor för virtuella datorer innehåller en uppsättning prestandadiagram som riktar sig till flera kpi:er (Key Performance Indicators) som hjälper dig att avgöra hur bra en virtuell dator presterar. Diagrammen visar resursutnyttjande över en tidsperiod så att du kan identifiera flaskhalsar, avvikelser eller växla till ett perspektiv som visar varje dator för att visa resursutnyttjande baserat på det valda måttet. Det finns många element att tänka på när det gäller prestanda, men Azure Monitor för virtuella datorer övervakar prestandaindikatorer för viktiga operativsystem relaterade till processor, minne, nätverkskort och diskanvändning. Prestanda kompletterar hälsoövervakningsfunktionen och hjälper till att exponera problem som indikerar ett eventuellt systemkomponentfel, stöder justering och optimering för att uppnå effektivitet eller stöder kapacitetsplanering.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Multi-VM perspektiv från Azure Monitor

Från Azure Monitor ger prestandafunktionen en vy över alla övervakade virtuella datorer som distribueras över arbetsgrupper i dina prenumerationer eller i din miljö. Gör följande för att komma åt från Azure Monitor. 

1. Välj **Övervaka**i Azure-portalen . 
2. Välj **Virtuella datorer** i avsnittet **Lösningar.**
3. Välj fliken **Prestanda.**

![Vyn Prestanda för vm-statistik Top N List](media/vminsights-performance/vminsights-performance-aggview-01.png)

Om du har mer än en Log Analytics-arbetsyta på fliken **Topp N-diagram** väljer du den arbetsyta som är aktiverad med lösningen från **arbetsytans** väljare högst upp på sidan. **Gruppväljaren** returnerar prenumerationer, resursgrupper, [datorgrupper](../platform/computer-groups.md)och skaluppsättningar för virtuella datorer som är relaterade till den valda arbetsytan och som du kan använda för att ytterligare filtrera resultat som presenteras i diagrammen på den här sidan och på de andra sidorna. Ditt val gäller endast prestandafunktionen och förs inte över till Hälsa eller Karta.  

Som standard visar diagrammen de senaste 24 timmarna. Med **timerange-väljaren** kan du fråga efter historiska tidsintervall på upp till 30 dagar för att visa hur resultatet såg ut tidigare.

De fem kapacitetsutnyttjandediagram som visas på sidan är:

* CPU-användning % - visar de fem bästa maskinerna med det högsta genomsnittliga processorutnyttjandet 
* Tillgängligt minne - visar de fem bästa datorerna med den lägsta genomsnittliga mängden tillgängligt minne 
* Logiskt diskutrymme används % - visar de fem bästa maskinerna med det högsta genomsnittliga diskutrymmet som används % för alla diskvolymer 
* Överförd kurs för byte - visar de fem bästa maskinerna med det högsta genomsnittet av skickade byte 
* Bytes mottagningsfrekvens - visar de fem bästa maskinerna med det högsta medelvärdet av mottagna byte 

Om du klickar på pin-ikonen i det övre högra hörnet i något av de fem diagrammen fästs det valda diagrammet på den senaste Azure-instrumentpanelen som du senast visade.  Från instrumentpanelen kan du ändra storlek på och flytta diagrammet. Om du väljer diagrammet från instrumentpanelen omdirigeras du till Azure Monitor för virtuella datorer och läser in rätt omfång och vy.  

Om du klickar på ikonen till vänster om pin-ikonen på något av de fem diagrammen öppnas **vyn Topp N-lista.**  Här ser du resursutnyttjandet för det prestandamåttet efter enskild virtuell dator i en listvy och vilken dator som trendar högst.  

![Topp-N-listvy för ett valt prestandamått](media/vminsights-performance/vminsights-performance-topnlist-01.png)

När du klickar på den virtuella datorn expanderas **fönstret Egenskaper** till höger för att visa egenskaperna för det valda objektet, till exempel systeminformation som rapporteras av operativsystemet, egenskaperna för Azure VM osv. Om du klickar på något av alternativen under avsnittet **Snabblänkar** omdirigeras du till den funktionen direkt från den valda virtuella datorn.  

![Egenskapsfönstret För virtuella datorer](./media/vminsights-performance/vminsights-properties-pane-01.png)

Växla till fliken **Aggregerade diagram** om du vill visa prestandamått som filtrerats efter medel- eller percentilmått.  

![Vyn Samlad datorstatistiksprestanda](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Följande kapacitetsutnyttjandediagram tillhandahålls:

* CPU-användning % - standardvärden som visar medelvärdet och den översta 95:e percentilen 
* Tillgängligt minne - standardvärden som visar medelvärdet, topp 5 och 10:e percentilen 
* Logiskt diskutrymme används % - standardvärden som visar medelvärdet och den 95:e percentilen 
* Överförd kurs för byte – standardvärden som visar genomsnittligt antal skickade byte 
* Mottagningsfrekvens för byte – standardvärden som visar genomsnittligt antal mottagna byte

Du kan också ändra diagrammens kornighet inom tidsintervallet genom att välja **Medel,** **Min**, **Max**, **50,** **90:e**och **95:e** i percentilväljaren.

Om du vill visa resursutnyttjandet per enskild virtuell dator i en listvy och se vilken dator som trendar med högst utnyttjande väljer du fliken **Topp N-lista.**  Sidan **Topp N-lista** visar de 20 bästa datorerna sorterade efter den mest använda med 95:e percentilen för det metriska *PROCESSOR-utnyttjandet %*.  Du kan se fler datorer genom att välja **Läs in mer**och resultaten expanderas för att visa de 500 bästa datorerna. 

>[!NOTE]
>Listan kan inte visa fler än 500 datorer åt gången.  
>

![Exempel på sidan Topplista](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Om du vill filtrera resultaten på en viss virtuell dator i listan anger du dess datornamn i textrutan **Sök efter namn.**  

Om du hellre vill visa användning från **Metric** ett annat prestandamått väljer du **Tillgängligt minne**, **Logiskt diskutrymme som används %**, **Nätverksåtertagen byte/s**eller **Nätverksskickad byte/s** och listuppdateringar för att visa utnyttjande begränsat till det måttet.  

Om du väljer en virtuell dator i listan öppnas **egenskapspanelen** till höger på sidan och härifrån kan du välja **Prestandadetalj**.  Sidan Detalj på **virtuella** datorer öppnas och är begränsad till den virtuella datorn, liknande erfarenhet när du öppnar VM Insights Performance direkt från Den virtuella azure-datorn.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Visa prestanda direkt från en virtuell Azure-dator

Gör följande för att komma direkt från en virtuell dator.

1. I Azure-portalen väljer du **Virtuella datorer**. 
2. Välj en virtuell dator i listan och välj **Insikter**i avsnittet **Övervakning** .  
3. Välj fliken **Prestanda.** 

Den här sidan innehåller inte bara prestandautnyttjandediagram, utan även en tabell som visar för varje logisk disk som upptäckts, dess kapacitet, användning och totalt genomsnitt för varje mått.  

Följande kapacitetsutnyttjandediagram tillhandahålls:

* CPU-användning % - standardvärden som visar medelvärdet och den översta 95:e percentilen 
* Tillgängligt minne - standardvärden som visar medelvärdet, topp 5 och 10:e percentilen 
* Logiskt diskutrymme används % - standardvärden som visar medelvärdet och den 95:e percentilen 
* Logisk disk IOPS - standardvärden som visar medelvärdet och den 95:e percentilen
* Logisk disk MB/s - standardvärden som visar medelvärdet och den 95:e percentilen
* Max logisk disk används % - standardvärden som visar medelvärdet och 95:e percentilen
* Överförd kurs för byte – standardvärden som visar genomsnittligt antal skickade byte 
* Mottagningsfrekvens för byte – standardvärden som visar genomsnittligt antal mottagna byte

Om du klickar på pin-ikonen i det övre högra hörnet i något av diagrammen fäster det markerade diagrammet till den senaste Azure-instrumentpanelen som du visade. Från instrumentpanelen kan du ändra storlek på och flytta diagrammet. Om du väljer diagrammet från instrumentpanelen omdirigeras du till Azure Monitor för virtuella datorer och prestandadetaljvyn för den virtuella datorn läses in.  

![VM-insikter Prestanda direkt från vm-vyn](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Visa prestanda direkt från en azure-skalningsuppsättning för virtuella datorer

Om du vill komma åt direkt från en Azure-skalningsuppsättning för virtuella datorer utför du följande steg.

1. I Azure-portalen väljer du **Skaluppsättningar för virtuella datorer**.
2. Välj en virtuell dator i listan och välj **Insikter** i avsnittet **Övervakning** för att visa fliken **Prestanda.**

Den här sidan läser in prestandavyn för Azure Monitor, begränsad till den valda skalningsuppsättningen. På så sätt kan du se De översta N-instanserna i skalningsuppsättningen över uppsättningen övervakade mått, visa den sammanlagda prestandan i skalningsuppsättningen och se trenderna för valda mått i de enskilda instanserna n skalningsuppsättningen. Om du väljer en instans i listvyn kan du läsa in kartan eller navigera till en detaljerad prestandavy för den instansen.

Om du klickar på pin-ikonen i det övre högra hörnet i något av diagrammen fäster det markerade diagrammet till den senaste Azure-instrumentpanelen som du visade. Från instrumentpanelen kan du ändra storlek på och flytta diagrammet. Om du väljer diagrammet från instrumentpanelen omdirigeras du till Azure Monitor för virtuella datorer och prestandadetaljvyn för den virtuella datorn läses in.  

![VM-insikter Prestanda direkt från skalningsuppsättningsvyn för virtuella datorer](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Du kan också komma åt en detaljerad prestandavy för en viss instans från instansvyn för skalningsuppsättningen. Navigera till **instanser** under avsnittet **Inställningar** och välj sedan **Insikter**.



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [arbetsböcker](vminsights-workbooks.md) som ingår i Azure Monitor för virtuella datorer för att ytterligare analysera prestanda och nätverksmått.  

- Mer information om identifierade programberoenden finns i [Visa Azure Monitor för virtuella datorer Karta](vminsights-maps.md).
