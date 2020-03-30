---
title: Övergångsguide för Azure Monitor-vydesigner till arbetsböcker
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658701"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Övergångsguide för Azure Monitor-vydesigner till arbetsböcker
[View designer](view-designer.md) är en funktion i Azure Monitor som låter dig skapa anpassade vyer som hjälper dig att visualisera data i din Log Analytics arbetsyta, med diagram, listor och tidslinjer. De håller på att fasas ut och ersättas med arbetsböcker som ger ytterligare funktioner. Den här artikeln innehåller en översikt över processen för att konvertera befintliga vyer till arbetsböcker.

## <a name="workbooks-overview"></a>Översikt över arbetsböcker
[Arbetsböcker](../insights/vminsights-workbooks.md) kombinerar text, [loggfrågor,](../log-query/query-language.md)mått och parametrar i avancerade interaktiva rapporter. Gruppmedlemmar med samma åtkomst till Azure-resurser kan också redigera arbetsböcker.

Arbetsböcker är användbara för scenarier som:

-   Utforska användningen av din virtuella dator när du inte vet de mått av intresse i förväg: CPU-användning, diskutrymme, minne, nätverksberoenden, etc. Till skillnad från andra verktyg för användningsanalys kan du med arbetsböcker kombinera flera typer av visualiseringar och analyser, vilket gör dem bra för den här typen av frihandsutforskning.
-   Förklara för ditt team hur en nyligen etablerad virtuell dator presterar genom att visa mått för nyckelräknare och andra logghändelser.
-   Dela resultaten av ett storleksändringsexperiment av din virtuella dator med andra medlemmar i ditt team. Du kan förklara målen för experimentet med text och sedan visa varje användningsmått och analysfrågor som används för att utvärdera experimentet, tillsammans med tydliga länktexter för om varje mått var över eller under målet.
-   Rapportera effekten av ett avbrott på användningen av den virtuella datorn, kombinera data, textförklaring och en diskussion om nästa steg för att förhindra avbrott i framtiden.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Varför konvertera visa instrumentpaneler för vydesigner till arbetsböcker?

View designer erbjuder möjligheten att generera olika frågebaserade vyer och visualiseringar. Men många anpassningar på hög nivå är dock begränsade, som till exempel formatering av rutnät och ikoner eller val av alternativ grafik för att representera data. Vydesignern är begränsad till totalt nio olika paneler för att representera dina data.

Arbetsböcker är en plattform som ger dig tillgång till den fulla potentialen av dina data. arbetsböcker behåller inte bara alla funktioner, utan stöder också ytterligare funktioner via text, mått, parametrar och mycket mer. Arbetsböcker gör det till exempel möjligt för användare att konsolidera täta rutnät och lägga till sökfält för att enkelt filtrera och analysera data. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Fördelar med att använda arbetsböcker över View Designer

* Stöder både loggar och mått.
* Tillåter både personliga vyer för individuell åtkomstkontroll och delade arbetsböcker vyer.
* Anpassade layoutalternativ med flikar, storleksändring och skalningskontroller.
* Stöd för frågor över flera Log Analytics-arbetsytor, Programinsiktsprogram och prenumerationer.
* Aktiverar anpassade parametrar som dynamiskt uppdaterar associerade diagram och visualiseringar.
* Stöd för mallgalleri från offentliga GitHub.

Den här guiden innehåller enkla steg för att direkt återskapa flera av de vanliga vydesignervyerna, men arbetsböcker ger användarna friheten att skapa och utforma någon av sina egna anpassade visualiseringar och mätvärden. Följande skärmbild kommer från [användningsmallen för arbetsyta](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) och visar ett exempel på vilka arbetsböcker som kan skapa:


![Exempel på arbetsböcker](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Så här börjar du använda arbetsböcker
Öppna arbetsböcker från arbetsböckerna är aktiverade i Log Analytics-arbetsytor som ett objekt i sidonavigeringsfältet, direkt under visningsdesignerns plats.

![Navigering i arbetsböcker](media/view-designer-conversion-overview/workbooks-nav.png)

När du har valt det här alternativet visas ett galleri med alla sparade arbetsböcker och mallar för arbetsytan.

![Galleri för arbetsböcker](media/view-designer-conversion-overview/workbooks-gallery.png)

Om du vill starta en ny arbetsbok kan du välja mallen **Tom** under **Snabbstart**eller ikonen **Nytt** i det övre navigeringsfältet. Om du vill visa mallar eller återgå till sparade arbetsböcker markerar du objektet i galleriet eller söker efter namnet i sökfältet.

Om du vill spara en arbetsbok måste du spara rapporten med en viss rubrik, prenumeration, resursgrupp och plats.
Arbetsboken fylls automatiskt på samma inställningar som LA-arbetsytan, med samma prenumeration, resursgrupp, men användarna kan ändra dessa rapportinställningar. Arbetsböcker sparas som standard i *Mina rapporter*, som endast är tillgängliga för den enskilda användaren. De kan också sparas direkt i delade rapporter eller delas senare.

![Arbetsböcker spara](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Nästa steg

- [Konverteringsalternativ](view-designer-conversion-options.md)
