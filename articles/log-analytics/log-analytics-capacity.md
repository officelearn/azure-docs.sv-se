---
title: Lösning för kapacitet och prestanda i Azure Log Analytics | Microsoft Docs
description: Använd lösningen kapacitet och prestanda i logganalys som hjälper dig att förstå kapaciteten för Hyper-V-servrar.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: magoedte
ms.openlocfilehash: 99c29afec7d06a458ed6d34071f1b6acbba1f03b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>Planera kapaciteten för Hyper-V-virtuella datorer med kapacitet och prestanda för lösning (förhandsgranskning)

![Symbolen kapacitet och prestanda](./media/log-analytics-capacity/capacity-solution.png)

Du kan använda lösningen kapacitet och prestanda i logganalys som hjälper dig att förstå kapaciteten för Hyper-V-servrar. Lösningen ger insikter om Hyper-V-miljö genom att visa den totala användningen av värdarna och de virtuella datorerna körs på dessa Hyper-V-värdar (CPU, minne och disk). Mått har samlats in för CPU, minne och diskar mellan alla värdar och de virtuella datorerna körs på dem.

Lösning:

-   Visar med högsta och lägsta CPU och minne användning
-   Visar virtuella datorer med högsta och lägsta CPU och minne användning
-   Visar virtuella datorer med högsta och lägsta IOPS och genomströmning användning
-   Visar vilka virtuella datorer som körs på vilka värdar
-   Visar översta diskar med hög genomströmning, IOPS och svarstid i klusterdelade volymer
- Du kan anpassa och filtrera baserat på grupper

> [!NOTE]
> Den tidigare versionen av kapacitet och prestanda för lösningen kallas kapacitet Management krävs både System Center Operations Manager och System Center Virtual Machine Manager. Den här uppdaterade lösningen har inte dessa beroenden.


## <a name="connected-sources"></a>Anslutna källor

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Support | Beskrivning |
|---|---|---|
| [Windows-agenter](log-analytics-windows-agent.md) | Ja | Lösningen samlar in information om kapacitet och prestanda från Windows-agenter. |
| [Linux-agenter](log-analytics-linux-agents.md) | Nej    | Lösningen samlar inte in information om kapacitet och prestanda från direkt Linux-agenter.|
| [SCOM-hanteringsgrupp](log-analytics-om-agents.md) | Ja |Lösningen samlar in data om kapacitet och prestanda från agenter i en ansluten SCOM-hanteringsgrupp. En direkt anslutning från SCOM-agent till logganalys krävs inte.|
| [Azure Storage-konto](log-analytics-azure-storage.md) | Nej | Azure storage innehåller inte data kapacitet och prestanda.|

## <a name="prerequisites"></a>Förutsättningar

- Windows- eller Operations Manager-agenter måste installeras på Windows Server 2012 eller högre Hyper-V-värdar, inte virtuella datorer.


## <a name="configuration"></a>Konfiguration

Utför följande steg för att lägga till kapacitet och prestanda för lösningen till din arbetsyta.

- Lägga till kapacitet och prestanda för lösningen i logganalys-arbetsytan med processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).

## <a name="management-packs"></a>Hanteringspaket

Om din SCOM-hanteringsgrupp är ansluten till logganalys-arbetsytan, sedan installeras följande hanteringspaket i SCOM när du lägger till den här lösningen. Det krävs ingen konfigurering eller underhåll av dessa hanteringspaket.

- Microsoft.IntelligencePacks.CapacityPerformance

Händelsen 1201 liknar:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

När lösningen Kapacitets- och uppdateras ändras versionsnumret.

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](log-analytics-om-agents.md).

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till lösningen kapacitet och prestanda för din arbetsyta, har kapacitet och prestanda lagts till i instrumentpanelens Översikt. Den här panelen visar antalet aktiva Hyper-V-värdar och valt antal aktiva virtuella datorer som har övervakas för tidsperioden.

![Panelen kapacitet och prestanda](./media/log-analytics-capacity/capacity-tile.png)


### <a name="review-utilization"></a>Granska användning

Klicka på panelen kapacitet och prestanda för att öppna instrumentpanelen kapacitet och prestanda. Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn listar upp till tio objekt som matchar denna kolumns kriterier för specificerat omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster genom att klicka på **Se alla** längst ned i kolumnen eller genom att klicka på kolumnrubriken.

- **Värdar**
    - **Värd för processoranvändningen** visar en grafisk trend över CPU-utnyttjande på värddatorer och en lista över värdar, baserat på den valda tidsperioden. Hovra över linjediagram för att visa detaljer för en specifik tidpunkt. Klicka på diagrammet om du vill visa mer information i loggen sökningen. Klicka på alla värdnamn för att öppna loggen Sök och visa information om CPU prestandaräknare för värdbaserade virtuella datorer.
    - **Värd för minnesanvändning** visar en grafisk trend över minnesanvändning värddatorer och en lista över värdar, baserat på den valda tidsperioden. Hovra över linjediagram för att visa detaljer för en specifik tidpunkt. Klicka på diagrammet om du vill visa mer information i loggen sökningen. Klicka på alla värdnamn för att öppna loggen sökning och visa information om prestandaräknare för värdbaserade virtuella datorer.
- **Virtual Machines**
    - **VM processoranvändningen** visar en grafisk trend över CPU-användning av virtuella datorer och en lista över virtuella datorer baserat på den valda tidsperioden. Hovra över linjediagram för att visa detaljer för en specifik tidpunkt för de översta 3 virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggen sökningen. Klicka på alla VM-namn för att öppna loggen Sök och visa sammanställs CPU information om prestandaräknare för den virtuella datorn.
    - **VM minnesanvändning** visar en grafisk trend över minnesanvändning för virtuella datorer och en lista över virtuella datorer baserat på den valda tidsperioden. Hovra över linjediagram för att visa detaljer för en specifik tidpunkt för de översta 3 virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggen sökningen. Klicka på alla namn på virtuell dator att öppna loggen Sök och visa information om prestandaräknare aggregerade minne för den virtuella datorn.
    - **VM totala Disk-IOPS** visar en grafisk trend över totala disken IOPS för virtuella datorer och en lista över virtuella datorer med IOPS för varje, baserat på den valda tidsperioden. Hovra över linjediagram för att visa detaljer för en specifik tidpunkt för de översta 3 virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggen sökningen. Klicka på alla VM-namn för att öppna loggen Sök- och aggregerade disk IOPS prestandaräknaren information för den virtuella datorn.
    - **VM totala Diskgenomflödet** visar en grafisk trend över det totala genomflödet för virtuella datorer och en lista över virtuella datorer med det totala genomflödet för varje baserat på den valda tidsperioden. Hovra över linjediagram för att visa detaljer för en specifik tidpunkt för de översta 3 virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggen sökningen. Klicka på alla VM-namn för att öppna loggen Sök och visa sammanställs totala genomflödet räknaren diskinformation för den virtuella datorn.
- **Klusterdelade volymer**
    - **Totalt antal genomströmning** visar summan av både läsningar och skrivningar på klusterdelade volymer.
    - **Totalt antal IOPS** visar summan av i/o-åtgärder per sekund på klusterdelade volymer.
    - **Total svarstid** visar den totala svarstiden på klusterdelade volymer.
- **Värd för densitet** övre panelen visar det totala antalet värdar och virtuella datorer som är tillgängliga för lösningen. Klicka på övre panelen om du vill visa mer information i loggen sökningen. Visar också alla värdar och antalet virtuella datorer som finns. Klicka på en värd till VM-resultat i en logg sökning mer detaljerat.


![instrumentpanelen värdar bladet](./media/log-analytics-capacity/dashboard-hosts.png)

![instrumentpanelen bladet för virtuella datorer](./media/log-analytics-capacity/dashboard-vms.png)


### <a name="evaluate-performance"></a>Utvärdera prestanda

Produktion datormiljöer avsevärt skiljer sig åt en organisation. Dessutom kapacitet och prestanda för arbetsbelastningar kan beror på hur dina virtuella datorer som körs, och vad du normalt. Specifika procedurer som hjälper dig att mått prestanda förmodligen inte gäller för din miljö. Så mer generaliserad normativ vägledning passar bättre för att. Microsoft publicerar en mängd olika vägledningsartiklar för att du mäta prestanda.

Sammanfattningsvis, lösningen samlar in kapacitet och prestandadata från olika källor, till exempel prestandaräknare. Kapacitets- och data som visas i olika underlag i lösningen och jämför resultaten till webbplatser på den [mäta prestanda på Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) artikel. Även om artikeln publicerades för en tid sedan, är mått, överväganden och riktlinjer fortfarande giltiga. Artikeln innehåller länkar till andra användbara resurser.


## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel loggen söker efter Kapacitets- och data som samlas in och beräknas av den här lösningen.


| Fråga | Beskrivning |
|:--- |:--- |
| Alla värden minneskonfigurationer | Perf &#124; där ObjectName == ”kapacitet och prestanda” och CounterName == ”värd tilldelat minne MB” &#124; sammanfatta MB = avg(CounterValue) av instansnamn |
| Alla konfigurationer av Virtuellt minne | Perf &#124; där ObjectName == ”kapacitet och prestanda” och CounterName == ”VM tilldelat minne MB” &#124; sammanfatta MB = avg(CounterValue) av instansnamn |
| Uppdelning av totala Disk-IOPS över alla virtuella datorer | Perf &#124; där ObjectName == ”kapacitet och prestanda” och (CounterName == ”VHD Diskläsningar/s- eller CounterName ==” VHD skrivningar/s ”) &#124; sammanfatta AggregatedValue = avg(CounterValue) av bin (TimeGenerated 1 tim), CounterName, InstanceName |
| Uppdelning av totala Diskgenomflödet över alla virtuella datorer | Perf &#124; där ObjectName == ”kapacitet och prestanda” och (CounterName == ”VHD läsa MB/s” eller CounterName == ”VHD Write MB/s”) &#124; sammanfatta AggregatedValue = avg(CounterValue) av bin (TimeGenerated 1 tim), CounterName, InstanceName |
| Uppdelning av IOPS totalt över alla CSV: er | Perf &#124; där ObjectName == ”kapacitet och prestanda” och (CounterName == ”CSV Diskläsningar/s- eller CounterName ==” CSV skrivningar/s ”) &#124; sammanfatta AggregatedValue = avg(CounterValue) av bin (TimeGenerated 1 tim), CounterName, InstanceName |
| Uppdelning av totala genomflödet över alla CSV: er | Perf &#124; där ObjectName == ”kapacitet och prestanda” och (CounterName == ”CSV Diskläsningar/s- eller CounterName ==” CSV skrivningar/s ”) &#124; sammanfatta AggregatedValue = avg(CounterValue) av bin (TimeGenerated 1 tim), CounterName, InstanceName |
| Uppdelning av totala svarstiden i alla CSV: er | Perf &#124; där ObjectName == ”kapacitet och prestanda” och (CounterName == ”CSV Läs latens” eller CounterName == ”CSV skriva latens”) &#124; sammanfatta AggregatedValue = avg(CounterValue) av bin (TimeGenerated 1 tim), CounterName, InstanceName |


## <a name="next-steps"></a>Nästa steg
* Använd [logga sökningar i logganalys](log-analytics-log-search.md) att visa detaljerad information om kapacitet och prestanda.
