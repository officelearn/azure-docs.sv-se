---
title: Skapa vyer för att analysera data i Azure Log Analytics | Microsoft Docs
description: Du kan skapa anpassade vyer som visas i Azure-portalen och innehåller en mängd olika visualiseringar i Log Analytics-arbetsytan med hjälp av Vydesigner i Log Analytics. Den här artikeln innehåller en översikt över Vydesigner och presenterar procedurer för att skapa och redigera anpassade vyer.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: bbf38d17f2f411fde240a67f6666953b275fb788
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642475"
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Skapa anpassade vyer med hjälp av Vydesigner i Log Analytics
Med hjälp av Vydesigner i [Azure Log Analytics](../../log-analytics/log-analytics-queries.md), du kan skapa en mängd olika anpassade vyer i Azure portal som kan hjälpa dig att visualisera data i Log Analytics-arbetsytan. Den här artikeln presenteras en översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.

Mer information om Vydesigner finns:

* [Panelen referens](view-designer-tiles.md): ger en referensguide i inställningarna för var och en av de tillgängliga panelerna i dina anpassade vyer.
* [Referens för visualiseringsdel](view-designer-parts.md): ger en referensguide i inställningarna för visualisering delarna som är tillgängliga i dina anpassade vyer.


## <a name="concepts"></a>Begrepp
Vyer visas på den **översikt** för Log Analytics-arbetsytan i Azure-portalen. Paneler på varje anpassad vy visas i alfabetisk ordning och paneler för lösningarna som är installerade på samma arbetsyta.

![Översiktssidan](media/view-designer/overview-page.png)

De vyer som du skapar med Vydesigner innehålla de delar som beskrivs i följande tabell:

| En del | Beskrivning |
|:--- |:--- |
| Paneler | Visas på Log Analytics-arbetsytan **översikt** sidan. Varje panel visar en visuell översikt över den anpassade vyn som representerar. Varje Paneltyp av innehåller en annan visualisering av posterna. Du väljer en panel för att visa en anpassad vy. |
| Anpassad vy | Visas när du väljer en panel. Varje vy innehåller en eller flera delar av visualiseringen. |
| Delar av visualiseringen | Presentera en visualisering av data i Log Analytics-arbetsytan baserat på en eller flera [loggsökningar](../../log-analytics/log-analytics-queries.md). De flesta delar är en rubrik som ger en övergripande visualisering, och en lista som visar de bästa resultaten. Varje Deltyp av en innehåller en annan visualisering av posterna i Log Analytics-arbetsytan. Du kan välja element i delen att utföra en loggsökning som ger detaljerad poster. |


## <a name="work-with-an-existing-view"></a>Arbeta med en befintlig vy
Vyer som skapats med Vydesigner visas följande alternativ:

![Menyn översikt](media/view-designer/overview-menu.png)

Alternativen beskrivs i följande tabell:

| Alternativ | Beskrivning |
|:--|:--|
| Uppdatera   | Uppdaterar vy med den senaste informationen. | 
| Analytics | Öppnar den [Advanced Analytics-portalen](../../log-analytics/log-analytics-log-search-portals.md) att analysera data med loggfrågor. |
| Redigera       | Öppnar vyn i Vydesigner redigera dess innehåll och konfiguration.  |
| Klona      | Skapar en ny vy och öppnar den i View Designer. Namnet på den nya vyn är samma som det ursprungliga namnet, men med *kopia* läggas till den. |
| Datumintervall | Ange filter för datum och tid intervallet för de data som ingår i vyn. Den här datumintervall kopplas före eventuella datumintervall som anges i frågor i vyn.  |
| +          | Definiera en anpassad filter som har definierats för vyn. |


## <a name="create-a-new-view"></a>Skapa en ny vy
Du kan skapa en ny vy i Vydesigner genom att välja **Vydesigner** på menyn i Log Analytics-arbetsytan.

![Visa Designer panelen](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Arbeta med Vydesigner
Du kan använda View Designer för att skapa nya vyer eller redigera befintliga. 

Vydesigner har tre fönster: 
* **Design**: innehåller den vy som du skapar eller redigerar. 
* **Kontroller**: innehåller paneler och delar som du lägger till den **Design** fönstret. 
* **Egenskaper för**: visas egenskaperna för paneler eller valda delar.

![Vydesigner](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurera visa panelen
En anpassad vy kan ha bara en enda panel. Om du vill visa den aktuella panelen eller välj ett alternativ, Välj den **panelen** fliken i den **kontroll** fönstret. Den **egenskaper** fönstret visas egenskaperna för den aktuella panelen. 

Du kan konfigurera panelen Egenskaper enligt informationen i den [panelen referens](view-designer-tiles.md) och klicka sedan på **tillämpa** att spara ändringarna.

### <a name="configure-the-visualization-parts"></a>Konfigurera visualisering-delar
En vy kan innehålla valfritt antal delar av visualiseringen. Om du vill lägga till delar i en vy, Välj den **visa** fliken och välj sedan en del av visualiseringen. Den **egenskaper** fönstret visas egenskaperna för den markerade delen. 

Du kan konfigurera Vyegenskaper enligt informationen i den [referens för Visualiseringsdel](view-designer-parts.md) och klicka sedan på **tillämpa** att spara ändringarna.

Vyer har bara en rad av delar av visualiseringen. Du kan ändra de befintliga delarna genom att dra dem till en ny plats.

Du kan ta bort en visualisering del från vyn genom att välja den **X** längst upp höger på delen.


### <a name="menu-options"></a>Menyalternativ
Alternativ för att arbeta med vyer i redigeringsläge beskrivs i följande tabell.

![Redigera-menyn](media/view-designer/edit-menu.png)

| Alternativ | Beskrivning |
|:--|:--|
| Spara        | Sparar dina ändringar och stänger vyn. |
| Avbryt      | Tar bort dina ändringar och stänger vyn. |
| Ta bort vy | Tar bort vyn. |
| Exportera      | Exporterar vyn till att en [Azure Resource Manager-mall](../../azure-resource-manager/resource-group-authoring-templates.md) som du kan importera till en annan arbetsyta. Filens namn är namnet på vyn och den har en *omsview* tillägget. |
| Importera      | Importerar de *omsview* -fil som du har exporterat från en annan arbetsyta. Den här åtgärden skriver över konfigurationen av den befintliga vyn. |
| Klona       | Skapar en ny vy och öppnar den i View Designer. Namnet på den nya vyn är samma som det ursprungliga namnet, men med *kopia* läggas till den. |

## <a name="next-steps"></a>Nästa steg
* Lägg till [paneler](view-designer-tiles.md) till den anpassade vyn.
* Lägg till [visualisering delar](view-designer-parts.md) till den anpassade vyn.
