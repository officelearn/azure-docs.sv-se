---
title: "Skapa vyer för att analysera data i Azure Log Analytics | Microsoft Docs"
description: "Vydesigner i logganalys kan du skapa anpassade vyer som visas i Azure-portalen och som innehåller olika visuella data i logganalys-arbetsytan. Den här artikeln innehåller en översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: a84f40503c1b9778c496461ebbf6864f99bd1c4b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Använd vyn Designer för att skapa anpassade vyer i logganalys
Vydesigner i [logganalys](log-analytics-overview.md) kan du skapa anpassade vyer i Azure-portalen som innehåller olika visuella data i logganalys-arbetsytan. Den här artikeln innehåller en översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.

Andra artiklar som är tillgängliga för Vydesigner är:

* [Panelen referens](log-analytics-view-designer-tiles.md) -referens i inställningarna för vart och ett av rutor tillgänglig för användning i din anpassade vyer.
* [Referens för visualisering del](log-analytics-view-designer-parts.md) -referens i inställningarna för vart och ett av rutor tillgänglig för användning i din anpassade vyer.

>[!NOTE]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), och sedan frågor i alla visningslägen måste skrivas den [nya frågespråket](https://go.microsoft.com/fwlink/?linkid=856078).  Alla vyer som har skapats innan arbetsytan har uppgraderats kommer att automtically konverteras.

## <a name="concepts"></a>Begrepp
Vyer visas på den **översikt** sidan i logganalys-arbetsytan i Azure-portalen.  Panelen för varje anpassad vy visas i alfabetisk ordning med panelerna för lösningarna som installerats i samma arbetsyta.

![Översiktssidan](media/log-analytics-view-designer/overview-page.png)

Vyer som skapats med Vydesigner innehålla element i följande tabell.

| En del | Beskrivning |
|:--- |:--- |
| Panel |Visas på översiktssidan för logganalys-arbetsytan.  Innehåller en visuell sammanfattning av informationen i den anpassade vyn.  Olika typer av panelen ange olika visuella av poster.  Klicka på rutan öppnas den anpassade vyn. |
| Anpassad vy |Visas när användaren klickar på ikonen.  Innehåller en eller flera visualiseringen delar. |
| Visualiseringen delar |Visualisering av data i logganalys-arbetsytan baserat på en eller flera [logga sökningar](log-analytics-log-searches.md).  De flesta delar innehåller ett huvud som anger en hög nivå visualisering och en lista över de bästa resultaten.  En annan deltyper ange olika visuella av poster i logganalys-arbetsytan.  Klicka på elementen i del till söka loggen att tillhandahålla detaljerade poster. |


## <a name="work-with-an-existing-view"></a>Arbeta med en befintlig vy
När du öppnar en vy som skapades med Vydesigner har en meny med alternativ i följande tabell.

![Översikt över menyn](media/log-analytics-view-designer/overview-menu.png)


| Alternativ | Beskrivning |
|:--|:--|
| Uppdatera   | Uppdatera vyn med den senaste informationen. | 
| Analys | Öppna den [Advanced Analytics portal](log-analytics-log-search-portals.md#advanced-analytics-portal) att analysera data med loggen sökningar. () log-Analytics-log-Search-Portals.MD#Advanced-Analytics-Portal). |
| Filter    | Ange en tidsfilter för data som ingår i vyn. |
| Redigera      | Öppna vyn i Vydesigner att redigera dess innehåll och konfiguration.   |
| Klona     | Skapa en ny vy och öppna den i View Designer.  Den nya vyn har samma namn som ursprungligt med ”kopiera” läggs till i slutet av dokumentet. |


## <a name="create-a-new-view"></a>Skapa en ny vy
Skapa en ny vy i den **Vydesigner** genom att klicka på panelen Vydesigner på översiktssidan för logganalys-arbetsytan i Azure-portalen.

![Visa Designer panelen](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="working-with-view-designer"></a>Arbeta med Vydesigner
Om du skapar en ny vy eller redigerar en befintlig ska du arbeta med View Designer.  

Vydesigner har tre rutor.  Den **Design** rutan innehåller den vy som du skapar eller redigerar.  Lägg till paneler och delar från den **kontrollen** att den **Design** fönstret.  Den **egenskaper** rutan visar egenskaperna för sida vid sida eller markerad del.

![Vydesigner](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Konfigurera vyn sida vid sida
En anpassad vy kan ha endast en bricka.  Välj den **panelen** fliken i den **kontrollen** fönstret för att visa den aktuella panelen eller välj ett alternativ.  Den **egenskaper** rutan visar egenskaperna för den aktuella panelen.  Konfigurera panelen Egenskaper enligt den detaljerade informationen i den [panelen referens](log-analytics-view-designer-tiles.md) och på **tillämpa** spara ändringarna.

### <a name="configure-visualization-parts"></a>Konfigurera visualiseringen delar
En vy kan innehålla valfritt antal visualiseringen delar.  Välj den **visa** fliken och sedan en visualiseringen del för att lägga till vyn.  Den **egenskaper** rutan visar egenskaperna för den markerade delen.  Konfigurera Vyegenskaper enligt den detaljerade informationen i den [visualiseringen del referens](log-analytics-view-designer-parts.md) och på **tillämpa** spara ändringarna.

Vyer kan bara ha en rad med visualiseringen delar.  Ordna om befintliga delar i vyn genom att klicka och dra dem till en ny plats.

Du kan ta bort en del av visualiseringen från vyn genom att klicka på den **X** -knappen i det övre högra hörnet av del.


### <a name="menu-options"></a>Menyalternativ
När du arbetar med en vy i redigeringsläge har menyalternativen i följande tabell.

![Redigera-menyn](media/log-analytics-view-designer/edit-menu.png)

| Alternativ | Beskrivning |
|:--|:--|
| Spara        | Spara dina ändringar och Stäng vyn. |
| Avbryt      | Ta bort dina ändringar och Stäng vyn. |
| Ta bort vy | Ta bort vyn. |
| Exportera      | Exportera vyn till en [Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md) som du kan importera till en annan arbetsyta.  Namnet på filen kommer att namnet på vyn med tillägget *omsview*. |
| Importera      | Importera en *omsview* filen som du exporterade från en annan arbetsyta.  Konfigurationen av den befintliga vyn skrivs. |
| Klona       | Skapa en ny vy och öppna den i View Designer.  Den nya vyn har samma namn som ursprungligt med ”kopiera” läggs till i slutet av dokumentet. |
| Publicera     | Exportera vyn till en JSON-fil som kan infogas i en [skötseln lösning](../operations-management-suite/operations-management-suite-solutions-resources-views.md).  Namnet på filen kommer att namnet på vyn med tillägget *json*. En andra fil skapas med tillägget *resjson* som innehåller värden för resurser som definierats i JSON-filen.

## <a name="next-steps"></a>Nästa steg
* Lägg till [paneler](log-analytics-view-designer-tiles.md) till den anpassade vyn.
* Lägg till [visualiseringen delar](log-analytics-view-designer-parts.md) till den anpassade vyn.
