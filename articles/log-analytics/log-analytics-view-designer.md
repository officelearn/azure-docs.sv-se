---
title: "Skapa vyer för att analysera data i OMS Log Analytics | Microsoft Docs"
description: "Vydesigner i logganalys kan du skapa anpassade vyer som visas i OMS och Azure portal och innehålla olika visuella data i OMS-databasen. Den här artikeln innehåller en översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer."
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
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: e3c463d749dc4179df58286b9bb75584880a6bc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Använd vyn Designer för att skapa anpassade vyer i logganalys
Vydesigner i [logganalys](log-analytics-overview.md) kan du skapa anpassade vyer i OMS-konsolen som innehåller olika visuella data i OMS-databasen. Den här artikeln innehåller en översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.

Andra artiklar som är tillgängliga för Vydesigner är:

* [Panelen referens](log-analytics-view-designer-tiles.md) -referens i inställningarna för vart och ett av rutor tillgänglig för användning i din anpassade vyer.
* [Referens för visualisering del](log-analytics-view-designer-parts.md) -referens i inställningarna för vart och ett av rutor tillgänglig för användning i din anpassade vyer.

>[!NOTE]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), och sedan frågor i alla visningslägen måste skrivas den [nya frågespråket](https://go.microsoft.com/fwlink/?linkid=856078).  Alla vyer som har skapats innan arbetsytan har uppgraderats kommer att automtically konverteras.

## <a name="concepts"></a>Koncept
Vyer som skapats med Vydesigner innehålla element i följande tabell.

| En del | Beskrivning |
|:--- |:--- |
| sida vid sida |Visas på huvudinstrumentpanelen Log Analytics Översikt.  Innehåller en visuell sammanfattning av informationen i den anpassade vyn.  Olika typer av panelen ange olika visuella poster i OMS-databasen.  Klicka på rutan öppnas den anpassade vyn. |
| Anpassad vy |Visas när användaren klickar på ikonen.  Innehåller en eller flera visualiseringen delar. |
| Visualiseringen delar |Visualisering av data i databasen OMS baserat på en eller flera [logga sökningar](log-analytics-log-searches.md).  De flesta delar innehåller ett huvud som anger en hög nivå visualisering och en lista över de bästa resultaten.  Annan deltyper ange olika visuella poster i OMS-databasen.  Klicka på elementen i del till söka loggen att tillhandahålla detaljerade poster. |

![Översikt över Designer](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>Lägg till Vydesigner på din arbetsyta
Vydesigner är i förhandsvisning, måste du lägga till din arbetsyta genom att välja **förhandsgranskningsfunktioner** i den **inställningar** avsnitt i OMS-portalen.

![Aktivera förhandsgranskning](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Skapa och redigera vyer
### <a name="create-a-new-view"></a>Skapa en ny vy
Öppna en ny vy i den **Vydesigner** genom att klicka på panelen Vydesigner i OMS huvudinstrumentpanelen.

![Visa Designer panelen](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Redigera en befintlig vy
Öppna vyn genom att klicka på dess panelen i OMS huvudinstrumentpanelen om du vill redigera en befintlig vy i View Designer.  Klicka på den **redigera** för att öppna vyn i View Designer.

![Redigera en vy](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Klona en befintlig vy
När du klonar en vy, skapar en ny vy och öppnas i View Designer.  Den nya vyn har samma namn som ursprungligt med ”kopiera” läggs till i slutet av dokumentet.  Öppna den befintliga vyn genom att klicka på dess panelen i OMS huvudinstrumentpanelen för att klona en vy.  Klicka på den **klona** knappen för att öppna vyn i View Designer.

![Klona en vy](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Ta bort en befintlig vy
Öppna vyn genom att klicka på dess panelen i OMS huvudinstrumentpanelen för att ta bort en befintlig vy.  Klicka på den **redigera** om du vill öppna vyn i View Designer och klickar på **ta bort vy**.

![Ta bort en vy](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Exportera en befintlig vy
Du kan exportera en vy till en JSON-fil som du kan importera till en annan arbetsyta eller använda i en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md).  Öppna vyn genom att klicka på dess panelen i OMS huvudinstrumentpanelen om du vill exportera en befintlig vy.  Klicka på den **exportera** för att skapa en fil i webbläsarens Hämtningsmappen.  Namnet på filen kommer att namnet på vyn med tillägget *omsview*.

![Exportera en vy](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Importera en befintlig vy
Du kan importera en *omsview* filen som du exporterade från en annan hanteringsgrupp.  Om du vill importera en befintlig vy måste du först skapa en ny vy.  Klicka på den **importera** och välj den *omsview* fil.  Konfigurationen i filen kopieras till den befintliga vyn.

![Exportera en vy](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Arbeta med Vydesigner
Vydesigner har tre rutor.  Den **Design** rutan som representerar den anpassade vyn.  När du lägger till paneler och delar från den **kontrollen** att den **Design** fönstret läggs de till vyn.  Den **egenskaper** rutan visar egenskaperna för sida vid sida eller markerad del.

![Vydesigner](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Konfigurera vyn sida vid sida
En anpassad vy kan ha endast en bricka.  Välj den **panelen** fliken i den **kontrollen** fönstret för att visa den aktuella panelen eller välj ett alternativ.  Den **egenskaper** rutan visar egenskaperna för den aktuella panelen.  Konfigurera panelen Egenskaper enligt den detaljerade informationen i den [panelen referens](log-analytics-view-designer-tiles.md) och på **tillämpa** spara ändringarna.

### <a name="configure-visualization-parts"></a>Konfigurera visualiseringen delar
En vy kan innehålla valfritt antal visualiseringen delar.  Välj den **visa** fliken och sedan en visualiseringen del för att lägga till vyn.  Den **egenskaper** rutan visar egenskaperna för den markerade delen.  Konfigurera Vyegenskaper enligt den detaljerade informationen i den [visualiseringen del referens](log-analytics-view-designer-parts.md) och på **tillämpa** spara ändringarna.

### <a name="delete-a-visualization-part"></a>Ta bort en del för visualisering
Du kan ta bort en del av visualiseringen från vyn genom att klicka på den **X** -knappen i det övre högra hörnet av del.

### <a name="rearrange-visualization-parts"></a>Ändra visualiseringen delar
Vyer kan bara ha en rad med visualiseringen delar.  Ordna om befintliga delar i vyn genom att klicka och dra dem till en ny plats.

## <a name="next-steps"></a>Nästa steg
* Lägg till [paneler](log-analytics-view-designer-tiles.md) till den anpassade vyn.
* Lägg till [visualiseringen delar](log-analytics-view-designer-parts.md) till den anpassade vyn.
