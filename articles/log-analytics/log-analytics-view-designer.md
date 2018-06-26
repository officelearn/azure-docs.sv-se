---
title: Skapa vyer för att analysera data i Azure Log Analytics | Microsoft Docs
description: Med hjälp av Vydesigner i logganalys kan du skapa anpassade vyer som visas i Azure-portalen och innehåller en mängd olika datavisualiseringar i logganalys-arbetsytan. Den här artikeln innehåller en översikt över Vydesigner och visar procedurer för att skapa och redigera anpassade vyer.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 91d4efcd7fabc2f284078d752ea68778a9bd8d86
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752047"
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Skapa anpassade vyer med hjälp av Vydesigner i logganalys
Med hjälp av Vydesigner i [Azure logganalys](log-analytics-overview.md), kan du skapa en mängd olika anpassade vyer i Azure-portalen som kan hjälpa dig att visualisera data i logganalys-arbetsytan. Den här artikeln visar en översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.

Mer information om Vydesigner finns:

* [Panelen referens](log-analytics-view-designer-tiles.md): innehåller en referens guide till inställningarna för var och en av de tillgängliga panelerna i anpassade vyer.
* [Referens för visualisering del](log-analytics-view-designer-parts.md): innehåller en referens guide till inställningarna för visualisering delar som är tillgängliga i din anpassade vyer.


## <a name="concepts"></a>Begrepp
Vyer visas på den **översikt** sidan i logganalys-arbetsytan i Azure-portalen. Panelerna i varje vy visas i alfabetisk ordning och paneler för lösningarna som installeras i samma arbetsyta.

![Översiktssidan](media/log-analytics-view-designer/overview-page.png)

De vyer som du skapar med Vydesigner innehålla element som beskrivs i följande tabell:

| En del | Beskrivning |
|:--- |:--- |
| Paneler | Visas på logganalys-arbetsytan **översikt** sidan. Varje sida vid sida visar en visuell översikt över den anpassade vyn representerar. Varje typ av panelen innehåller en annan visualisering av posterna. Du kan välja en panel för att visa en anpassad vy. |
| Anpassad vy | Visas när du väljer en panel. Varje vy innehåller en eller flera visualiseringen delar. |
| Visualiseringen delar | Presentera en visualisering av data i logganalys-arbetsytan baserat på en eller flera [logga sökningar](log-analytics-log-searches.md). De flesta delar är ett huvud som ger en övergripande visualisering, och en lista som visar de bästa resultaten. Varje Deltypen innehåller en annan visualisering av posterna i logganalys-arbetsytan. Du kan välja element del vill utföra en sökning i loggen som ger detaljerad poster. |


## <a name="work-with-an-existing-view"></a>Arbeta med en befintlig vy
Vyer som skapats med Vydesigner visas följande alternativ:

![Översikt över menyn](media/log-analytics-view-designer/overview-menu.png)

Alternativen beskrivs i följande tabell:

| Alternativ | Beskrivning |
|:--|:--|
| Uppdatera   | Uppdaterar vy med den senaste informationen. | 
| Analytics | Öppnar den [Advanced Analytics portal](log-analytics-log-search-portals.md#advanced-analytics-portal) att analysera data med loggen sökningar. |
| Redigera       | Öppnar vyn i Vydesigner att redigera dess innehåll och konfiguration.  |
| Klona      | Skapar en ny vy och öppnas i View Designer. Namnet på den nya vyn är samma som det ursprungliga namnet, men *kopiera* läggs till den. |
| Datumintervall | Ange datum och tid intervallet filter för de data som ingår i vyn. |
| +          | Definiera ett anpassat filter som definieras för vyn. |


## <a name="create-a-new-view"></a>Skapa en ny vy
Du kan skapa en ny vy i Vydesigner genom att välja **Vydesigner** på menyn i logganalys-arbetsytan.

![Visa Designer panelen](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Arbeta med Vydesigner
Du kan använda View Designer för att skapa nya vyer eller redigera befintliga. 

Vydesigner har tre rutor: 
* **Design**: innehåller den vy som du skapar eller redigerar. 
* **Kontroller**: innehåller paneler och delar som du lägger till den **Design** fönstret. 
* **Egenskaper för**: Visar egenskaper för paneler eller markerade delar.

![Vydesigner](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurera panelen Visa
En anpassad vy kan ha endast en bricka. Om du vill visa den aktuella panelen eller välj ett alternativ, Välj den **panelen** fliken i den **kontrollen** fönstret. Den **egenskaper** fönstret Egenskaper för den aktuella panelen. 

Du kan konfigurera egenskaper för sida vid sida enligt informationen i den [panelen referens](log-analytics-view-designer-tiles.md) och klicka sedan på **tillämpa** spara ändringarna.

### <a name="configure-the-visualization-parts"></a>Konfigurera visualiseringen delar
En vy kan innehålla valfritt antal visualiseringen delar. Lägg till delar i en vy, Välj den **visa** och välj sedan en visualiseringen del. Den **egenskaper** fönstret Egenskaper för den markerade delen. 

Du kan konfigurera Vyegenskaper enligt informationen i den [visualiseringen del referens](log-analytics-view-designer-parts.md) och klicka sedan på **tillämpa** spara ändringarna.

Vyer har bara en rad med visualiseringen delar. Du kan ändra de befintliga webbdelar genom att dra dem till en ny plats.

Du kan ta bort en del av visualiseringen från vyn genom att välja den **X** överst höger i delen.


### <a name="menu-options"></a>Menyalternativ
Alternativ för att arbeta med vyer i redigeringsläge beskrivs i följande tabell.

![Redigera-menyn](media/log-analytics-view-designer/edit-menu.png)

| Alternativ | Beskrivning |
|:--|:--|
| Spara        | Sparar ändringarna och stänger vyn. |
| Avbryt      | Tar bort dina ändringar och stänger vyn. |
| Ta bort vy | Tar bort vyn. |
| Exportera      | Exporterar vyn till en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md) som du kan importera till en annan arbetsyta. Namnet på filen är namnet på vyn och har en *omsview* tillägg. |
| Importera      | Import av *omsview* filen som du exporterade från en annan arbetsyta. Den här åtgärden skriver över konfigurationen av den befintliga vyn. |
| Klona       | Skapar en ny vy och öppnas i View Designer. Namnet på den nya vyn är samma som det ursprungliga namnet, men *kopiera* läggs till den. |

## <a name="next-steps"></a>Nästa steg
* Lägg till [paneler](log-analytics-view-designer-tiles.md) till den anpassade vyn.
* Lägg till [visualiseringen delar](log-analytics-view-designer-parts.md) till den anpassade vyn.
