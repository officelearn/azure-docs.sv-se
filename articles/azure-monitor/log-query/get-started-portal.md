---
title: Kom igång med Azure Monitor Log Analytics | Microsoft Docs
description: Den här artikeln innehåller en själv studie kurs om hur du använder Log Analytics i Azure Portal för att skriva frågor.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: bwren
ms.openlocfilehash: 18e6e162764ce190a1953a4126e4f9ef2eb717f5
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899520"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Kom igång med Log Analytics i Azure Monitor

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här självstudien får du lära dig hur du använder Log Analytics i Azure Portal för att skriva Azure Monitor logg frågor. Du får lära dig att:

- Använd Log Analytics för att skriva en enkel fråga
- Förstå schemat för dina data
- Filtrera, sortera och gruppera resultat
- Använd ett tidsintervall
- Skapa diagram
- Spara och Läs in frågor
- Exportera och dela frågor

En själv studie kurs om hur du skriver logg frågor finns i [Kom igång med logg frågor i Azure Monitor](get-started-queries.md).<br>
Mer information om logg frågor finns i [Översikt över logg frågor i Azure Monitor](log-query-overview.md).

## <a name="meet-log-analytics"></a>Möta Log Analytics
Log Analytics är ett webb verktyg som används för att skriva och köra Azure Monitor logg frågor. Öppna den genom att välja **loggar** på Azure Monitor-menyn. Den börjar med en ny tom fråga.

![Startsida](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Brand Väggs krav
Om du vill använda Log Analytics måste webbläsaren ha åtkomst till följande adresser. Om webbläsaren har åtkomst till Azure Portal via en brand vägg måste du aktivera åtkomst till dessa adresser.

| URI | IP-adress | Portar |
|:---|:---|:---|
| portal.loganalytics.io | Dynamisk | 80,443 |
| api.loganalytics.io | Dynamisk | 80,443 |
| docs.loganalytics.io | Dynamisk | 80,443 |

## <a name="basic-queries"></a>Grundläggande frågor
Frågor kan användas för att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserade på dina data. Börja med en grundläggande fråga:

```Kusto
Event | search "error"
```

Den här frågan söker i _händelse_ tabellen efter poster som innehåller term _felet_ i vilken egenskap som helst.

Frågor kan inledas med antingen ett tabell namn eller ett [Sök](/azure/kusto/query/searchoperator) kommando. Exemplet ovan börjar med tabell namn _händelsen_, som hämtar alla poster från händelse tabellen. Pipe-tecknet (|) separerar kommandon, så utdata från den första fungerar som indata för följande kommando. Du kan lägga till ett valfritt antal kommandon i en enskild fråga.

Ett annat sätt att skriva samma fråga är:

```Kusto
search in (Event) "error"
```

I det här exemplet är **sökningen** begränsad till _händelse_ tabellen, och alla poster i tabellen genomsöks efter termen _fel_.

## <a name="running-a-query"></a>Köra en fråga
Kör en fråga genom att klicka på knappen **Kör** eller trycka på **SKIFT + RETUR**. Tänk på följande information som avgör vilken kod som ska köras och vilka data som returneras:

- Rad brytningar: En enda rast gör det lättare att läsa frågan. Flera rad brytningar delas upp i separata frågor.
- Pekaralternativ Placera markören någonstans i frågan för att köra den. Den aktuella frågan anses vara koden upp tills en tom rad har hittats.
- Tidsintervall – ett tidsintervall under de _senaste 24 timmarna_ anges som standard. Om du vill använda ett annat intervall använder du tids väljaren eller lägger till ett explicit tidsintervall filter i frågan.


## <a name="understand-the-schema"></a>Förstå schemat
Schemat är en samling tabeller som är visuellt grupperade under en logisk kategori. Flera av kategorierna är från övervaknings lösningar. Kategorin _LogManagement_ innehåller vanliga data som Windows-och Syslog-händelser, prestanda data och agent pulsslag.

![Schema](media/get-started-portal/schema.png)

I varje tabell ordnas data i kolumner med olika data typer som anges av ikoner bredvid kolumn namnet. Till exempel den _händelse_ tabellen som visas i skärmbilden innehåller kolumner som _datorn_ som är text, _EventCategory_ som är ett tal och _TimeGenerated_ som är datum/tid.

## <a name="filter-the-results"></a>Filtrera resultaten
Börja med att hämta allt i _händelse_ tabellen.

```Kusto
Event
```

Log Analytics automatiskt omfångs resultat efter:

- Tidsintervall:  Som standard är frågorna begränsade till de senaste 24 timmarna.
- Antal resultat: Resultaten är begränsade till högst 10 000 poster.

Den här frågan är mycket allmän och returnerar för många resultat för att vara användbar. Du kan filtrera resultaten antingen via tabell elementen eller genom att uttryckligen lägga till ett filter i frågan. Filtrering av resultat via tabell elementen gäller för den befintliga resultat uppsättningen, medan ett filter till själva frågan returnerar en ny filtrerad resultat uppsättning och kan därför skapa mer exakta resultat.

### <a name="add-a-filter-to-the-query"></a>Lägg till ett filter i frågan
Det finns en pil till vänster om varje post. Klicka på pilen för att öppna informationen för en viss post.

Hovra ovanför ett kolumn namn för ikonerna "+" och "-" som ska visas. Om du vill lägga till ett filter som bara kommer att returnera poster med samma värde klickar du på tecknet "+". Klicka på "-" om du vill utesluta poster med det här värdet och klicka sedan på **Kör** för att köra frågan igen.

![Lägg till filter i fråga](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrera genom tabell elementen
Nu ska vi fokusera på händelser med allvarlighets graden _fel_. Detta anges i en kolumn med namnet _EventLevelName_. Du måste bläddra till höger för att se den här kolumnen.

Klicka på filter ikonen bredvid kolumn rubriken och välj värden som _börjar med_ text _felet_i popup-fönstret:

![Filter](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Sortera och gruppera resultat
Resultaten begränsas nu till att endast innehålla fel händelser från SQL Server som skapats under de senaste 24 timmarna. Resultaten sorteras dock inte på något sätt. Om du vill sortera resultaten efter en speciell kolumn, till exempel _timestamp_ , klickar du på kolumn rubriken. Ett klick sorteras i stigande ordning medan en andra klickar sorteras i fallande ordning.

![Sorterings kolumn](media/get-started-portal/sort-column.png)

Ett annat sätt att ordna resultaten är av grupper. Om du vill gruppera resultat efter en speciell kolumn drar du bara kolumn rubriken ovanför de andra kolumnerna. Om du vill skapa under grupper drar du även andra kolumner till det övre fältet.

![Grupper](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Välj kolumner som ska visas
Resultat tabellen innehåller ofta många kolumner. Du kanske upptäcker att några av de returnerade kolumnerna inte visas som standard, eller att du vill ta bort några kolumner som visas. Klicka på knappen kolumner om du vill välja kolumner som ska visas:

![Välja kolumner](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Välj ett tidsintervall
Som standard använder Log Analytics de _senaste 24 timmarna_ tidsintervallet. Om du vill använda ett annat intervall väljer du ett annat värde via tids väljaren och klickar på **Kör**. Förutom de förinställda värdena kan du använda alternativet _anpassat tidsintervall_ för att välja ett absolut intervall för frågan.

![Tids väljare](media/get-started-portal/time-picker.png)

När du väljer ett anpassat tidsintervall är de valda värdena i UTC, vilket kan skilja sig från din lokala tidszon.

Om frågan uttryckligen innehåller ett filter för _TimeGenerated_, visas rubriken för tid väljaren _i frågan_. Manuellt val kommer att inaktive ras för att förhindra en konflikt.


## <a name="charts"></a>Diagram
Förutom att returnera resultat i en tabell, kan frågeresultaten visas i visuella format. Använd följande fråga som exempel:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Som standard visas resultaten i en tabell. Klicka på _diagram_ för att visa resultatet i en grafisk vy:

![Stapeldiagram](media/get-started-portal/bar-chart.png)

Resultatet visas i ett liggande stapeldiagram. Klicka på _staplad kolumn_ och välj _cirkel_ för att visa en annan vy av resultaten:

![Cirkeldiagram](media/get-started-portal/pie-chart.png)

Olika egenskaper för vyn, till exempel x-och y-axlar, eller grupperings-och delnings inställningar, kan ändras manuellt från kontroll fältet.

Du kan också ställa in den önskade vyn i själva frågan med operatorn Render.

### <a name="smart-diagnostics"></a>Smart diagnostik
Om det finns en plötslig insamling eller ett steg i dina data på en timechart kan du se en markerad punkt på raden. Detta anger att _Smart diagnostik_ har identifierat en kombination av egenskaper som filtrerar bort den plötsliga ändringen. Klicka på punkten för att få mer information om filtret och för att se den filtrerade versionen. Detta kan hjälpa dig att identifiera vad som orsakade ändringen:

![Smart diagnostik](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Fäst vid instrumentpanelen
Om du vill fästa ett diagram eller en tabell till någon av dina delade Azure-instrumentpaneler klickar du på ikonen fäst. Observera att den här ikonen har flyttats överst i Log Analytics-fönstret, som skiljer sig från skärm bilden nedan.

![Fäst vid instrumentpanelen](media/get-started-portal/pin-dashboard.png)

Vissa förenklingar tillämpas på ett diagram när du fäster det på en instrument panel:

- Tabell kolumner och rader: För att fästa en tabell på instrument panelen måste den ha fyra eller färre kolumner. Endast de sju översta raderna visas.
- Tids begränsning: Frågor begränsas automatiskt till de senaste 14 dagarna.
- Begränsning av lager plats antal: Om du visar ett diagram som har många diskreta lager platser grupperas mindre fyllda lager platser automatiskt till en _annan_ lager plats.

## <a name="save-queries"></a>Spara frågor
När du har skapat en användbar fråga kanske du vill spara den eller dela med andra. Ikonen **Spara** är i det översta fältet.

Du kan spara hela fråge sidan eller en enskild fråga som en funktion. Functions är frågor som även kan refereras till av andra frågor. För att kunna spara en fråga som en funktion måste du ange ett funktions Ali Aset, vilket är namnet som används för att anropa den här frågan när andra frågor refererar till den.

![Spara funktion](media/get-started-portal/save-function.png)

>[!NOTE]
>Följande tecken stöds – `a–z, A–Z, 0-9, -, _, ., <space>, (, ), |` i fältet **namn** när du sparar eller redigerar den sparade frågan.

Log Analytics frågor sparas alltid på en vald arbets yta och delas med andra användare av arbets ytan.

## <a name="load-queries"></a>Läs in frågor
Ikonen i Query Explorer är längst upp till höger. Här visas alla sparade frågor efter kategori. Du kan också markera vissa frågor som favoriter för att snabbt hitta dem i framtiden. Dubbelklicka på en sparad fråga för att lägga till den i det aktuella fönstret.

![Frågeutforskaren](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Exportera och dela som länk
Log Analytics stöder flera export metoder:

- Excel: Spara resultatet som en CSV-fil.
- Power BI: Exportera resultaten till Power BI. Mer information finns i [importera Azure Monitor loggdata till Power BI](../../azure-monitor/platform/powerbi.md) .
- Dela en länk: Själva frågan kan delas som en länk som sedan kan skickas och köras av andra användare som har åtkomst till samma arbets yta.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att [skriva Azure Monitor logg frågor](get-started-queries.md).
