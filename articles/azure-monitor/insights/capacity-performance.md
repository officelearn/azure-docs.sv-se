---
title: Kapacitets- och prestandalösning i Azure Monitor | Microsoft-dokument
description: Använd lösningen Kapacitet och prestanda i Monitor för att förstå kapaciteten hos dina Hyper-V-servrar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 75c65cf9f76e711a3aeed764de8b92ed619bad2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666951"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Planera hyper-V virtuell datorkapacitet med lösning för kapacitet och prestanda (föråldrad)

![Symbol för kapacitet och prestanda](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Lösningen kapacitet och prestanda har inaktuell.  Kunder som redan har installerat lösningen kan fortsätta att använda den, men kapacitet och prestanda kan inte läggas till några nya arbetsytor.

Du kan använda lösningen Kapacitet och prestanda i Monitor för att hjälpa dig att förstå kapaciteten hos dina Hyper-V-servrar. Lösningen ger insikter i hyper-V-miljön genom att visa dig det totala utnyttjandet (CPU, minne och disk) för värdarna och de virtuella datorerna som körs på dessa Hyper-V-värdar. Mått samlas in för CPU, minne och diskar över alla dina värdar och de virtuella datorer som körs på dem.

Lösningen:

-   Visar värdar med högsta och lägsta CPU- och minnesanvändning
-   Visar virtuella datorer med högsta och lägsta CPU- och minnesanvändning
-   Visar virtuella datorer med högsta och lägsta IOPS- och dataflödesanvändning
-   Visar vilka virtuella datorer som körs på vilka värdar
-   Visar de översta diskarna med högt dataflöde, IOPS och svarstid i klusterdelade volymer
- Gör att du kan anpassa och filtrera baserat på grupper

> [!NOTE]
> Den tidigare versionen av kapacitets- och prestandalösningen Kapacitetshantering krävde både System Center Operations Manager och System Center Virtual Machine Manager. Den här uppdaterade lösningen har inte dessa beroenden.


## <a name="connected-sources"></a>Anslutna källor

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Support | Beskrivning |
|---|---|---|
| [Windows-agenter](../../azure-monitor/platform/agent-windows.md) | Ja | Lösningen samlar in information om kapacitets- och prestandadata från Windows-agenter. |
| [Linux-agenter](../../azure-monitor/learn/quick-collect-linux-computer.md) | Inga    | Lösningen samlar inte in information om kapacitets- och prestandadata från direkta Linux-agenter.|
| [SCOM:s ledningsgrupp](../../azure-monitor/platform/om-agents.md) | Ja |Lösningen samlar in kapacitets- och prestandadata från agenter i en ansluten SCOM-hanteringsgrupp. En direkt anslutning från SCOM-agenten till Log Analytics krävs inte.|
| [Azure-lagringskonto](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Inga | Azure-lagring innehåller inte kapacitets- och prestandadata.|

## <a name="prerequisites"></a>Krav

- Windows- eller Operations Manager-agenter måste vara installerade på Windows Server 2012 eller högre Hyper-V-värdar, inte virtuella datorer.


## <a name="configuration"></a>Konfiguration

Utför följande steg för att lägga till lösningen kapacitet och prestanda på arbetsytan.

- Lägg till lösningen kapacitet och prestanda på arbetsytan Log Analytics med hjälp av processen som beskrivs i [Lägg till logganalyslösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Hanteringspaket

Om din SCOM-hanteringsgrupp är ansluten till din Log Analytics-arbetsyta installeras följande hanteringspaket i SCOM när du lägger till den här lösningen. Det krävs ingen konfigurering eller underhåll av dessa hanteringspaket.

- Microsoft.IntelligencePacks.CapacityPerformance

1201-evenemanget liknar:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

När lösningen Kapacitet och prestanda uppdateras ändras versionsnumret.

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till lösningen kapacitet och prestanda på arbetsytan läggs kapacitet och prestanda till på översiktsinstrumentpanelen. Den här panelen visar antalet aktiva Hyper-V-värdar och antalet aktiva virtuella datorer som övervakades under den valda tidsperioden.

![Kapacitet och prestandapanel](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Granska utnyttjande

Klicka på panelen Kapacitet och prestanda för att öppna instrumentpanelen Kapacitet och prestanda. Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn listar upp till tio objekt som matchar denna kolumns kriterier för specificerat omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster genom att klicka på **Se alla** längst ned i kolumnen eller genom att klicka på kolumnrubriken.

- **Värdar**
    - **Processoranvändning för värd** Visar en grafisk trend för CPU-användningen av värddatorer och en lista över värdar, baserat på den valda tidsperioden. Hovra över linjediagrammet om du vill visa information för en viss tidpunkt. Klicka på diagrammet om du vill visa mer information i loggsökningen. Klicka på ett värdnamn för att öppna loggsökning och visa CPU-räknarinformation för värdbaserade virtuella datorer.
    - **Värdminnesanvändning** Visar en grafisk trend för minnesanvändningen av värddatorer och en lista över värdar, baserat på den valda tidsperioden. Hovra över linjediagrammet om du vill visa information för en viss tidpunkt. Klicka på diagrammet om du vill visa mer information i loggsökningen. Klicka på ett värdnamn om du vill öppna information om loggsökning och visa minnesräknare för värdbaserade virtuella datorer.
- **Virtuella datorer**
    - **VM CPU-användning** Visar en grafisk trend för CPU-användningen av virtuella datorer och en lista över virtuella datorer, baserat på den valda tidsperioden. Hovra över linjediagrammet om du vill visa information om en viss tidpunkt för de tre översta virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggsökningen. Klicka på ett vm-namn om du vill öppna loggsökning och visa aggregerade CPU-räknardetaljer för den virtuella datorn.
    - **Vm-minnesanvändning** Visar en grafisk trend för minnesanvändningen av virtuella datorer och en lista över virtuella datorer, baserat på den valda tidsperioden. Hovra över linjediagrammet om du vill visa information om en viss tidpunkt för de tre översta virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggsökningen. Klicka på ett vm-namn om du vill öppna loggsökning och visa aggregerad minnesräknare för den virtuella datorn.
    - **IOPS** för virtuell dator totalt disk Visar en grafisk trend för den totala disken iOPS för virtuella datorer och en lista över virtuella datorer med IOPS för varje, baserat på den valda tidsperioden. Hovra över linjediagrammet om du vill visa information om en viss tidpunkt för de tre översta virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggsökningen. Klicka på ett VM-namn om du vill öppna loggsökning och visa aggregerade disk-IOPS-räknaredetaljer för den virtuella datorn.
    - **Vm totalt diskutflöde** Visar en grafisk trend för det totala diskgenomflödet för virtuella datorer och en lista över virtuella datorer med det totala diskdataflödet för varje, baserat på den valda tidsperioden. Hovra över linjediagrammet om du vill visa information om en viss tidpunkt för de tre översta virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggsökningen. Klicka på ett vm-namn om du vill öppna loggsökning och visa aggregerade totala diskdataflödesräknaredetaljer för den virtuella datorn.
- **Klustrade delade volymer**
    - **Totalt totalt dataflöde** Visar summan av både läsningar och skrivningar på klustrade delade volymer.
    - **Totalt IOPS** Visar summan av in-/utdataåtgärder per sekund på klustrade delade volymer.
    - **Total latens** Visar den totala svarstiden för klustrade delade volymer.
- **Värddensitet** Den övre panelen visar det totala antalet värdar och virtuella datorer som är tillgängliga för lösningen. Klicka på den övre panelen om du vill visa ytterligare information i loggsökningen. Visar också alla värdar och antalet virtuella datorer som finns. Klicka på en värd för att öka detaljnivån i den virtuella datorn resulterar i en loggsökning.


![Bladet Hosts på instrumentpanelen](./media/capacity-performance/dashboard-hosts.png)

![instrumentpanelen virtuella datorer blad](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Utvärdera prestanda

Produktionsdatorer skiljer sig mycket från en organisation till en annan. Kapacitets- och prestandaarbetsbelastningar kan också bero på hur dina virtuella datorer körs och vad du anser vara normalt. Specifika procedurer som hjälper dig att mäta prestanda skulle förmodligen inte gälla för din miljö. Så, mer generaliserad föreskrivande vägledning är bättre lämpad att hjälpa. Microsoft publicerar en mängd olika artiklar om normativ vägledning som hjälper dig att mäta prestanda.

Sammanfattningsvis samlar lösningen in kapacitets- och prestandadata från en mängd olika källor, inklusive prestandaräknare. Använd de kapacitets- och prestandadata som presenteras på olika ytor i lösningen och jämför dina resultat med resultaten vid artikeln [Mätprestanda på Hyper-V.](https://msdn.microsoft.com/library/cc768535.aspx) Även om artikeln publicerades för en tid sedan är måtten, övervägandena och riktlinjerna fortfarande giltiga. Artikeln innehåller länkar till andra användbara resurser.


## <a name="sample-log-searches"></a>Exempel på loggsökningar

I följande tabell visas exempelloggsökningar efter kapacitets- och prestandadata som samlas in och beräknas av den här lösningen.


| Söka i data | Beskrivning |
|:--- |:--- |
| Alla värdminneskonfigurationer | Perf &#124; där ObjectName == "Kapacitet och prestanda" och CounterName == "Host Assigned Memory MB" &#124; sammanfatta MB = avg(CounterValue) efter InstanceName |
| Alla VM-minneskonfigurationer | Perf &#124; där ObjectName == "Kapacitet och prestanda" och CounterName == "VM Assigned Memory MB" &#124; sammanfatta MB = avg(CounterValue) efter InstanceName |
| Uppdelning av totalt disk-IOPS för alla virtuella datorer | Perf &#124; där ObjectName == "Kapacitet och prestanda" och (CounterName == "VHD Läser/s" eller CounterName == "VHD-skrivningar/s") &#124; sammanfatta AggregatedValue = avg(CounterValue) efter lagerplats(TimeGenerated, 1h), CounterName, InstanceName |
| Uppdelning av totalt diskflöde för alla virtuella datorer | Perf &#124; där ObjectName == "Kapacitet och prestanda" och (CounterName == "VHD Read MB/s" eller CounterName == "VHD Write MB/s") &#124; sammanfatta AggregatedValue = avg(CounterValue) efter lagerplats(TimeGenerated, 1h), CounterName, InstanceName |
| Uppdelning av totalt IOPS för alla CSV:er | Perf &#124; där ObjectName == "Kapacitet och prestanda" och (CounterName == "CSV Läser/s" eller CounterName == "CSV-skrivningar/s") &#124; sammanfatta AggregatedValue = avg(CounterValue) efter lagerplats(TimeGenerated, 1h), CounterName, InstanceName |
| Uppdelning av totalt dataflöde för alla CSV:er | Perf &#124; där ObjectName == "Kapacitet och prestanda" och (CounterName == "CSV Läser/s" eller CounterName == "CSV-skrivningar/s") &#124; sammanfatta AggregatedValue = avg(CounterValue) efter lagerplats(TimeGenerated, 1h), CounterName, InstanceName |
| Uppdelning av total latens för alla CSV:er | Perf &#124; där ObjectName == "Kapacitet och prestanda" och (CounterName == "CSV Läsfördröjning" eller CounterName == "CSV Skrivfördröjning") &#124; sammanfatta AggregatedValue = avg(CounterValue) efter lagerplats(TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>Nästa steg
* Använd [Loggsökningar i Logganalys](../../azure-monitor/log-query/log-query-overview.md) för att visa detaljerade kapacitets- och prestandadata.
