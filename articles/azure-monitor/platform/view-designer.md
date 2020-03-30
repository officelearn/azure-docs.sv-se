---
title: Skapa vyer för att analysera loggdata i Azure Monitor | Microsoft-dokument
description: Genom att använda View Designer i Azure Monitor kan du skapa anpassade vyer som visas i Azure-portalen och innehåller en mängd olika visualiseringar på data på log analytics-arbetsytan. Den här artikeln innehåller en översikt över Visa designer och innehåller procedurer för att skapa och redigera anpassade vyer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/10/2019
ms.openlocfilehash: 9a7521f61dc59bd954629a05638c159ab0e70556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658497"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Skapa anpassade vyer med Hjälp av Visa designer i Azure Monitor
Genom att använda View Designer i Azure Monitor kan du skapa en mängd anpassade vyer i Azure-portalen som kan hjälpa dig att visualisera data på din Log Analytics-arbetsyta. Den här artikeln innehåller en översikt över Visa designer och procedurer för att skapa och redigera anpassade vyer.

> [!IMPORTANT]
> Vyer i Azure Monitor håller på att fasas ut och ersättas med [arbetsböcker](workbooks-overview.md) som ger ytterligare funktioner. Mer information om hur du konverterar befintliga vyer till arbetsböcker finns i [Azure Monitor-vydesignern i övergångsguiden för arbetsböcker.](view-designer-conversion-overview.md)

Mer information om View Designer finns i:

* [Panelreferens:](view-designer-tiles.md)Innehåller en referensguide till inställningarna för var och en av de tillgängliga panelerna i dina anpassade vyer.
* [Visualiseringsdelreferens:](view-designer-parts.md)Innehåller en referensguide till inställningarna för de visualiseringsdelar som är tillgängliga i dina anpassade vyer.


## <a name="concepts"></a>Begrepp
Vyer visas på sidan Översikt **över** Azure Monitor i Azure-portalen. Öppna den här sidan från **Azure Monitor-menyn** genom att klicka på **Mer** under avsnittet **Insikter.** Panelerna i varje anpassad vy visas i alfabetisk ordning och panelerna för övervakningslösningarna installeras på samma arbetsyta.

![Översiktssida](media/view-designer/overview-page.png)

Vyerna som du skapar med Visa designer innehåller de element som beskrivs i följande tabell:

| Del | Beskrivning |
|:--- |:--- |
| Ikoner | Visas på sidan Översikt **över** Azure Monitor. Varje panel visar en visuell sammanfattning av den anpassade vy som den representerar. Varje paneltyp ger en annan visualisering av dina poster. Du väljer en panel för att visa en anpassad vy. |
| Anpassad vy | Visas när du väljer en panel. Varje vy innehåller en eller flera visualiseringsdelar. |
| Visualisering delar | Presentera en visualisering av data på log analytics-arbetsytan baserat på en eller flera [loggfrågor](../log-query/log-query-overview.md). De flesta delar innehåller ett huvud, som ger en visualisering på hög nivå, och en lista som visar de bästa resultaten. Varje deltyp ger en annan visualisering av posterna på log analytics-arbetsytan. Du väljer element i delen för att utföra en loggfråga som innehåller detaljerade poster. |

## <a name="required-permissions"></a>Nödvändiga behörigheter
Du behöver minst [behörighet på deltagarnivå](manage-access.md#manage-access-using-azure-permissions) på arbetsytan Logganalys för att skapa eller ändra vyer. Om du inte har den här behörigheten visas inte alternativet Visa designer på menyn.


## <a name="work-with-an-existing-view"></a>Arbeta med en befintlig vy
Vyer som har skapats med Visa designer visar följande alternativ:

![Översiktsmeny](media/view-designer/overview-menu.png)

Alternativen beskrivs i följande tabell:

| Alternativ | Beskrivning |
|:--|:--|
| Uppdatera   | Uppdaterar vyn med de senaste data. | 
| Loggar      | Öppnar [Logganalysen](../log-query/portals.md) för att analysera data med loggfrågor. |
| Redigera       | Öppnar vyn i Visa designer för att redigera dess innehåll och konfiguration.  |
| Klona      | Skapar en ny vy och öppnar den i View Designer. Namnet på den nya vyn är detsamma som det ursprungliga namnet, men med *Kopiera* bifogat. |
| Datumintervall | Ange datum- och tidsintervallfiltret för de data som ingår i vyn. Det här datumintervallet tillämpas före alla datumintervall som anges i frågor i vyn.  |
| +          | Definiera ett anpassat filter som har definierats för vyn. |


## <a name="create-a-new-view"></a>Skapa en ny vy
Du kan skapa en ny vy i Visa designer genom att välja **Visa designer** på menyn på log analytics-arbetsytan.

![Visa panelen Visa designer](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Arbeta med View Designer
Du kan använda Visa designer för att skapa nya vyer eller redigera befintliga vyer. 

View Designer har tre fönsterrutor: 
* **Design**: Innehåller den anpassade vy som du skapar eller redigerar. 
* **kontroller:** innehåller de paneler och delar som du lägger till i **designfönstret.** 
* **Egenskaper**: Visar egenskaperna för panelerna eller de markerade delarna.

![Vydesigner](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurera vypanelen
En anpassad vy kan bara ha en enda panel. Om du vill visa den aktuella panelen eller välja en alternativ väljer du fliken Sida vid **sida** i **kontrollfönstret.** I fönstret **Egenskaper** visas egenskaperna för den aktuella panelen. 

Du kan konfigurera panelegenskaperna enligt informationen i [referensen Panel](view-designer-tiles.md) och sedan klicka på **Använd** för att spara ändringarna.

### <a name="configure-the-visualization-parts"></a>Konfigurera visualiseringsdelarna
En vy kan innehålla valfritt antal visualiseringsdelar. Om du vill lägga till delar i en vy markerar du fliken **Visa** och väljer sedan en visualiseringsdel. I fönstret **Egenskaper** visas egenskaperna för den markerade delen. 

Du kan konfigurera vyegenskaperna enligt informationen i [visualiseringsdelreferensen](view-designer-parts.md) och sedan klicka på **Använd** för att spara ändringarna.

Vyer har bara en rad visualiseringsdelar. Du kan ordna om de befintliga delarna genom att dra dem till en ny plats.

Du kan ta bort en visualiseringsdel från vyn genom att välja **X** längst upp till höger i delen.


### <a name="menu-options"></a>Menyalternativ
Alternativen för att arbeta med vyer i redigeringsläge beskrivs i följande tabell.

![Redigera-menyn](media/view-designer/edit-menu.png)

| Alternativ | Beskrivning |
|:--|:--|
| Spara        | Sparar ändringarna och stänger vyn. |
| Avbryt      | Ignorerar ändringarna och stänger vyn. |
| Ta bort vy | Tar bort vyn. |
| Exportera      | Exporterar vyn till en [Azure Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) som du kan importera till en annan arbetsyta. Namnet på filen är namnet på vyn, och den har en *omsview-förlängning.* |
| Importera      | Importerar *omsview-filen* som du exporterade från en annan arbetsyta. Den här åtgärden skriver över konfigurationen av den befintliga vyn. |
| Klona       | Skapar en ny vy och öppnar den i View Designer. Namnet på den nya vyn är detsamma som det ursprungliga namnet, men med *Kopiera* bifogat. |

## <a name="next-steps"></a>Nästa steg
* Lägg till [paneler](view-designer-tiles.md) i din anpassade vy.
* Lägg till [visualiseringsdelar](view-designer-parts.md) i den anpassade vyn.
